---
type: resource
tags: [trading, automation, dev]
cree: 2026-08-23
source: VPS openclaw (ssh, lecture seule 2026-08-23) — relevés systemd, db.py, report.py, collector.yaml
---

# Agrandissement disque — déplacer `bot3.db` sur un volume Hetzner (préparé, NON exécuté)

> **Rien n'a été exécuté. Aucun volume n'a été créé.** Tous les relevés ci-dessous sont datés du 2026-08-23 en lecture seule. Les commandes sont écrites pour être copiées telles quelles, et **chaque phase a son inverse**, jusqu'au point de non-retour n° 1 qui est signalé en toutes lettres.
>
> Contexte et chiffrage : next action « PLAN D'AGRANDISSEMENT HETZNER » de [[_hermes-polymarket]]. Discipline générale : [[vps-git-et-deploiement]].

## Ce qui est décidé avant de commencer

| | |
|---|---|
| Machine | Hetzner **Cloud** vServer, instance `126039211`, `nbg1-dc3` — volumes éligibles |
| Volume | **300 Go** — ~12 €/mois HT *(prix à confirmer en console : les pages Hetzner ne le rendent pas)* |
| Ce qui bouge | `~/bot3_btc5min/data/` **en entier** — `bot3.db` (16,70 Gio) + `backups/` (0,86 Gio) |
| Comment | volume monté sur `/mnt/hc-bot3`, puis **symlink** `data → /mnt/hc-bot3/data` |
| Fenêtre d'arrêt | **~4 min mesurées, budget 8, plafond dur 14** (au-delà, healthchecks.io alerte pour de vrai) |
| Ce qui NE bouge PAS | le code, `collector.yaml`, `bot3.env`, les unités systemd, `polymarket.db` |

**Aucune modification de configuration n'est nécessaire.** `DB_PATH = ROOT / "data" / "bot3.db"` (`db.py:16`) et `backup.dir: "data/backups"` (`collector.yaml`) sont tous deux **relatifs à la racine du projet** : le symlink suffit, et rien dans le code n'a besoin d'être touché.

### Les deux raisons de préférer le symlink au montage direct

1. **Un montage direct sur `~/bot3_btc5min/data` est un piège silencieux.** Si le volume ne monte pas au redémarrage, le répertoire d'origine resté *dessous* redevient visible : le collecteur écrirait dans une base périmée **sans rien signaler**. Avec le symlink, un volume absent fait crasher le service au démarrage — la panne est bruyante.
2. **Le sous-répertoire `data/` À L'INTÉRIEUR du volume est ce qui rend la panne bruyante.** Si le symlink pointait sur la racine du montage (`/mnt/hc-bot3`), un volume non monté donnerait un répertoire **existant et vide** → silence. En pointant sur `/mnt/hc-bot3/data`, un volume non monté donne un **symlink cassé** → erreur immédiate. Ce détail d'un niveau est toute la différence.

### Relevé de référence (2026-08-23, à re-vérifier le jour J)

```
bot3.db              17 935 065 088 o   (16,70 Gio)
bot3.db-wal              31 386 192 o   (29,9 Mio)
data/ total                17,59 Gio
triggers R5                       16
user_version                       3    (= db.SCHEMA_VERSION)
tables                             8    book_snapshots collector_events cycle_outcomes
                                        cycles hb_opportunities report_daily rtds_ticks trades
/ libre                     5,75 Gio
veillebot                   uid=1001 gid=1001   XDG_RUNTIME_DIR=/run/user/1001
```

---

## PHASE 0 — hors fenêtre, aucun service arrêté, entièrement réversible

### 0.1 — Créer et attacher le volume (console Hetzner, geste d'Hector)

Console → Volumes → Create Volume → **300 GB**, même localisation que le serveur (`nbg1`), attacher à `openclaw`, **et surtout : NE PAS cocher le formatage/montage automatique**. On le fait à la main pour choisir `-m 0` et le point de montage.

Noter l'**ID du volume** affiché par la console : il sert de `<VOLUME_ID>` ci-dessous.

### 0.2 — Le volume est-il bien vu par le système ?

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT
ls -la /dev/disk/by-id/ | grep HC_Volume
```

**Contrôle** : un nouveau disque d'environ 300 Go, `FSTYPE` **vide**, et un lien `scsi-0HC_Volume_<VOLUME_ID>`.
**Si `FSTYPE` n'est pas vide → S'ARRÊTER** : le volume n'est pas neuf, ne rien formater.

### 0.3 — Formater et monter

```bash
mkfs.ext4 -m 0 -L bot3data /dev/disk/by-id/scsi-0HC_Volume_<VOLUME_ID>
mkdir -p /mnt/hc-bot3
mount -o discard,defaults /dev/disk/by-id/scsi-0HC_Volume_<VOLUME_ID> /mnt/hc-bot3
chown veillebot:veillebot /mnt/hc-bot3
df -h /mnt/hc-bot3
```

`-m 0` supprime les 5 % de blocs réservés à root — sur 300 Go cela rend **15 Go** qui ne servaient à rien ici.

**Contrôle** : `df` montre ~275 Gio disponibles et le point de montage `/mnt/hc-bot3`.

### 0.4 — Persister au redémarrage, avec `nofail`

```bash
cp -p /etc/fstab /etc/fstab.bak-2026-08-23
printf '%s\n' '/dev/disk/by-id/scsi-0HC_Volume_<VOLUME_ID>  /mnt/hc-bot3  ext4  discard,nofail,defaults  0 0' >> /etc/fstab
systemctl daemon-reload
umount /mnt/hc-bot3 && mount -a && df -h /mnt/hc-bot3
```

**`nofail` est délibéré** : un volume injoignable ne doit **pas** bloquer le démarrage du serveur. C'est le symlink cassé — donc le service qui crashe — qui portera l'alerte, pas un boot en rade.

**Contrôle** : après `umount` puis `mount -a`, le volume est remonté. Si ce n'est pas le cas, **corriger fstab avant d'aller plus loin**.

### 0.5 — Le volume tient-il l'écriture ? (avant de lui confier 18 Go)

```bash
dd if=/dev/zero of=/mnt/hc-bot3/.essai bs=8M count=256 oflag=direct conv=fsync status=progress
rm -f /mnt/hc-bot3/.essai
```

**Contrôle** : 2 Gio écrits sans erreur. Le débit affiché **sert à dimensionner la fenêtre** : 18,9 Go ÷ débit = durée de copie. À moins de 100 Mo/s, la fenêtre dépasse 5 min — en tenir compte.

> **↩︎ Inverse de la phase 0** : `umount /mnt/hc-bot3`, retirer la ligne de `/etc/fstab` (ou restaurer `/etc/fstab.bak-2026-08-23`), détacher et supprimer le volume en console. **Aucun service n'a été touché, aucune donnée n'a bougé.**

---

## PHASE 1 — la fenêtre d'arrêt commence

> ⏱️ **Noter l'heure de début.** Plafond dur : **14 minutes** — au-delà, healthchecks.io déclenche une vraie alerte sur le dead-man's switch (grace de 14 min). Ce serait légitime, mais autant le savoir.

### 1.1 — Arrêter les écrivains

```bash
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 systemctl --user stop \
  bot3-collector.service bot3-backfill.timer bot3-backfill.service \
  bot3-heartbeat.timer bot3-heartbeat.service bot3-report.timer bot3-report.service'
```

**Les `.service` sont arrêtés en plus des `.timer` : arrêter un timer n'arrête pas le service déjà en cours.** `bot3-collector` a `TimeoutStopSec=20`, il rend la main en 20 s au pire.

### 1.2 — Prouver que plus personne ne tient la base

```bash
fuser -v /home/veillebot/bot3_btc5min/data/bot3.db ; echo "fuser rc=$?"
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 systemctl --user is-active bot3-collector.service'
```

**Contrôle bloquant** : `fuser` ne doit lister **aucun** processus (`rc=1`), et `is-active` doit rendre `inactive`.
**S'il reste un processus → S'ARRÊTER, relancer les services, et comprendre avant de recommencer.** Copier une base tenue ouverte donne une copie déchirée.

### 1.3 — Replier le WAL dans le fichier principal

```bash
su - veillebot -c 'sqlite3 /home/veillebot/bot3_btc5min/data/bot3.db "PRAGMA wal_checkpoint(TRUNCATE);"'
ls -la /home/veillebot/bot3_btc5min/data/
```

**Contrôle** : `bot3.db-wal` fait **0 octet** (ou a disparu). Le fichier principal contient désormais tout.

### 1.4 — Relevé AVANT (tout est O(1), rien ne scanne les 18 Go)

```bash
cd /home/veillebot/bot3_btc5min/data
sha256sum bot3.db | tee /tmp/avant.sha256
sqlite3 "file:bot3.db?mode=ro" "PRAGMA quick_check;"
sqlite3 "file:bot3.db?mode=ro" "SELECT 'triggers', COUNT(*) FROM sqlite_master WHERE type='trigger';"
sqlite3 "file:bot3.db?mode=ro" "PRAGMA user_version;"
for t in book_snapshots collector_events cycle_outcomes cycles hb_opportunities report_daily rtds_ticks trades; do
  echo -n "  $t "; sqlite3 "file:bot3.db?mode=ro" "SELECT MAX(rowid) FROM \"$t\";"
done | tee /tmp/avant.rowids
ls -la ; du -sb .
```

**Contrôles attendus** : `quick_check` = `ok` · triggers = **16** · `user_version` = **3**.
`MAX(rowid)` et non `COUNT(*)` : le premier est en O(1), le second scannerait 8,6 Go sur `trades` et ferait exploser la fenêtre.

### 1.5 — La copie

```bash
time cp -a /home/veillebot/bot3_btc5min/data /mnt/hc-bot3/data
sync
```

`cp -a` préserve propriétaire, permissions et horodatages. `sync` force l'écriture réelle sur le volume avant de vérifier.

### 1.6 — Relevé APRÈS, sur la copie

```bash
cd /mnt/hc-bot3/data
sha256sum bot3.db | tee /tmp/apres.sha256
diff <(cut -d' ' -f1 /tmp/avant.sha256) <(cut -d' ' -f1 /tmp/apres.sha256) && echo "  SHA256 IDENTIQUE"
sqlite3 "file:bot3.db?mode=ro" "PRAGMA quick_check;"
sqlite3 "file:bot3.db?mode=ro" "SELECT 'triggers', COUNT(*) FROM sqlite_master WHERE type='trigger';"
sqlite3 "file:bot3.db?mode=ro" "PRAGMA user_version;"
for t in book_snapshots collector_events cycle_outcomes cycles hb_opportunities report_daily rtds_ticks trades; do
  echo -n "  $t "; sqlite3 "file:bot3.db?mode=ro" "SELECT MAX(rowid) FROM \"$t\";"
done | tee /tmp/apres.rowids
diff /tmp/avant.rowids /tmp/apres.rowids && echo "  ROWIDS IDENTIQUES"
ls -la ; du -sb .
```

**Contrôles bloquants** : SHA256 identique · `quick_check` = `ok` · 16 triggers · `user_version` = 3 · rowids identiques · `backups/` présent avec ses deux archives et leurs `.sha256`.
**Un seul écart → S'ARRÊTER**, `rm -rf /mnt/hc-bot3/data`, relancer les services, rien n'a bougé côté production.

### 1.7 — R5 prouvé par le test, jamais par la lecture

```bash
su - veillebot -c 'sqlite3 /mnt/hc-bot3/data/bot3.db "BEGIN; DELETE FROM trades WHERE rowid=(SELECT MIN(rowid) FROM trades); ROLLBACK;"' ; echo "rc=$?"
```

**Contrôle bloquant : la commande DOIT ÉCHOUER** — message `RAISE(ABORT)` et `rc` non nul. C'est exactement ce que fait `bot3_trim.py` à sa ligne 186.
**Si le `DELETE` PASSE, R5 ne tient plus sur la copie → S'ARRÊTER immédiatement**, supprimer la copie, relancer les services. Le `ROLLBACK` protège en dernier ressort, mais un `DELETE` qui passe signifie que les triggers n'ont pas suivi et la copie est inutilisable.

### 1.8 — Basculer (mise en place du symlink)

```bash
cd /home/veillebot/bot3_btc5min
mv data data.pre-volume-2026-08-23
su - veillebot -c 'ln -s /mnt/hc-bot3/data /home/veillebot/bot3_btc5min/data'
ls -la /home/veillebot/bot3_btc5min/ | grep -E '^l|data'
su - veillebot -c 'cd /home/veillebot/bot3_btc5min && PYTHONPATH=src ./venv/bin/python -c "
from bot3_collector import db
print(\"  DB_PATH   :\", db.DB_PATH)
print(\"  resolue   :\", db.DB_PATH.resolve())
print(\"  existe    :\", db.DB_PATH.exists())
import shutil; print(\"  FS de ROOT:\", round(shutil.disk_usage(db.ROOT).free/1024**3,2), \"Gio libres\")
"'
```

**Contrôles** : le symlink pointe sur `/mnt/hc-bot3/data`, `DB_PATH.resolve()` donne `/mnt/hc-bot3/data/bot3.db`, `exists()` = `True`.
**Et `FS de ROOT` doit maintenant afficher ~23 Gio libres** — c'est la preuve que `/` a été soulagé. *(`ROOT` reste sur `/` : `Path(__file__).resolve().parents[2]`, et `src/` n'est pas un symlink. Le `disk_free` du heartbeat continue donc de surveiller `/` et non le volume — vérifié le 23/08.)*

> **↩︎ Inverse jusqu'ici, gratuit** : `rm /home/veillebot/bot3_btc5min/data && mv data.pre-volume-2026-08-23 data`, puis relancer les services. **Aucune donnée n'a été écrite ailleurs.**

---

## ⛔ POINT DE NON-RETOUR n° 1 — le redémarrage du collecteur

**À partir de l'instant où `bot3-collector` redémarre, toute nouvelle écriture atterrit sur le volume.** Revenir en arrière reste techniquement possible — même commande qu'au-dessus — mais coûterait **les données écrites depuis le redémarrage** (~500 000 lignes `trades` par jour, soit ~350 lignes par minute).

**Ne franchir cette ligne que si les six contrôles de 1.6 et le test R5 de 1.7 sont tous verts.**

### 2.1 — Redémarrer

```bash
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 systemctl --user start \
  bot3-collector.service bot3-backfill.timer bot3-heartbeat.timer bot3-report.timer'
sleep 20
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 systemctl --user is-active \
  bot3-collector.service bot3-backfill.timer bot3-heartbeat.timer bot3-report.timer'
```

**Contrôle** : les quatre rendent `active`. ⏱️ **Fin de la fenêtre — noter l'heure.**

### 2.2 — Le collecteur écrit-il vraiment sur le volume ?

```bash
sleep 60
ls -la /mnt/hc-bot3/data/
fuser -v /mnt/hc-bot3/data/bot3.db
su - veillebot -c 'sqlite3 "file:/mnt/hc-bot3/data/bot3.db?mode=ro" "SELECT MAX(rowid) FROM trades;"'
```

**Contrôle** : `bot3.db-wal` est réapparu et grossit, `fuser` liste le processus du collecteur, et `MAX(rowid)` sur `trades` est **supérieur** au relevé de 1.4. C'est la preuve d'écriture, pas la présomption.

### 2.3 — Le heartbeat repasse-t-il au VERT ?

```bash
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 journalctl --user -u bot3-heartbeat.service -n 3 --no-pager -o cat'
```

**Contrôle attendu** : `heartbeat VERT` et `disk_free: ~23 G libres (mini 15 G)`. Il surveillait `/` avant, il surveille toujours `/` : le seuil de 15 Gio est repassé au-dessus.
**S'il reste ROUGE sur `disk_free`, c'est que `/` n'a pas été soulagé** — l'ancienne `data.pre-volume-*` occupe encore ses 17,6 Gio, c'est normal à ce stade (voir phase 3).

### 2.4 — Intégrité complète, maintenant qu'on est hors fenêtre

```bash
time su - veillebot -c 'sqlite3 "file:/mnt/hc-bot3/data/bot3.db?mode=ro" "PRAGMA integrity_check;"'
```

Lourd (plusieurs minutes sur 18 Go), c'est pourquoi il est **ici** et pas dans la fenêtre. **Contrôle** : `ok`.

---

## PHASE 3 — restaurer le filet, puis seulement libérer `/`

### 3.1 — Forcer une sauvegarde sans attendre 05:30

```bash
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 systemctl --user start bot3-report.service'
su - veillebot -c 'XDG_RUNTIME_DIR=/run/user/1001 journalctl --user -u bot3-report.service -n 60 --no-pager -o cat' | sed -n '/9. VOLUM/,$p'
```

Run réel (~2 min de mesures), **il envoie le rapport Telegram**. La garde relit `shutil.disk_usage()` à chaque passage : elle voit le volume et passe toute seule, sans modification de configuration.

**Contrôles attendus dans le bloc 9** :
- plus de `** BACKUP REFUSÉ **` ;
- une ligne `backup_ok` et une archive datée du jour dans `/mnt/hc-bot3/data/backups/` ;
- la mention du système de fichiers doit indiquer que base et backups sont **sur le même FS** (les deux sont sur le volume — donc `cout_db` reste actif, ce qui est correct et prévu : le volume a la place).

```bash
ls -la /mnt/hc-bot3/data/backups/
```

### ⛔ POINT DE NON-RETOUR n° 2 — définitif

### 3.2 — Libérer les 17,6 Gio de `/`, **et seulement maintenant**

**Critère écrit d'avance, à vérifier avant de taper quoi que ce soit :** une archive datée du jour existe dans `/mnt/hc-bot3/data/backups/`, son `.sha256` est présent, et le rapport a journalisé `backup_ok`. **Tant que ce n'est pas vrai, on ne touche pas à l'ancienne copie.**

```bash
# à ne lancer QUE si le critère ci-dessus est rempli
du -sh /home/veillebot/bot3_btc5min/data.pre-volume-2026-08-23
rm -rf /home/veillebot/bot3_btc5min/data.pre-volume-2026-08-23
df -h /
```

**Cette suppression est irréversible.** Elle n'est pas urgente : rien ne casse à la garder quelques jours de plus, et `/` ne se remplit plus du fait de bot3 dès la phase 2.

**Contrôle final** : `/` affiche ~23 Gio libres, et le heartbeat suivant passe `disk_free` au vert.

---

## Ce que ce plan ne couvre pas — à écrire dans le pivot après exécution

- **Personne ne surveille le volume.** Le `disk_free` du heartbeat regarde `db.ROOT`, c'est-à-dire `/`, et continuera de le faire. Le volume n'est surveillé qu'indirectement, par la garde du backup, **une fois par jour à 05:30**. Un second check `disk_free` sur `/mnt/hc-bot3` est à ajouter — sinon on aura déplacé la panne sans déplacer l'alarme.
- **La pente reste.** Voir la revue datée au **2026-09-14** dans [[_hermes-polymarket]] : l'alarme de `/` resonne vers le 21/09 sans autre geste.
- **`polymarket.db` (7,89 Go) reste sur `/`.** Le même volume pourrait l'accueillir plus tard ; ce n'est pas dans ce plan.
- **`bot3_trim` redevient exécutable** une fois la place revenue — mais son `--dry-run` reste piégé tant que la garde d'espace ne le couvre pas. Voir la next action dédiée.

## Liens

- [[_hermes-polymarket]] — chiffrage, next actions, revue du 14/09
- [[vps-git-et-deploiement]] — la discipline dont ce plan est une instance : md5 avant/après, `.bak` daté, test sans effet de bord, critère de vérification écrit d'avance
- [[La panne silencieuse]] — les deux pièges évités ici sont de sa famille : le montage qui masque un répertoire, et l'alarme qui change de cible sans le dire
- [[01-Projects/hermes-polymarket/mise-en-pause-atlas-procedure|mise-en-pause-atlas-procedure]] — même format : préparé, non exécuté, chaque commande a son inverse
