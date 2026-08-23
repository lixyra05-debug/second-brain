---
type: project
statut: actif
tags: [ia, business, automation]
cree: 2026-07-12
---

# Autonomous

## Objectif
SaaS outbound B2B — une URL d'entreprise → 50 prospects qualifiés + emails personnalisés (autonomous.coldsend.app).

## Statut
Au 2026-07-12 : **premier run Trigger.dev SUCCESS en prod confirmé**, après une double panne infra (image jamais transférée sur le VPS + env store silencieusement vide). Monitoring UptimeRobot actif (3 monitors). Prochaine étape : recharge Explorium → test E2E sur stripe.com → DM 5 warm leads. Clarification SIRET/SEPA en attente pour encaisser.

## Next actions
- [x] ~~🔴 **La pile Trigger.dev tourne parfaitement et n'exécute plus rien du métier depuis trois mois.**~~ — **ARBITRÉ ET ARRÊTÉE le 2026-08-23.** Décision d'Hector : on n'agrandit pas le disque, on arrête Trigger.dev. **Étape 1 seule exécutée — `docker compose stop`, aucun conteneur, volume ni image supprimé.** Les 8 conteneurs sont `Exited`, et **Postgres comme ClickHouse sortent en code 0** : arrêt propre, intégrité préservée. *(`supervisor` sort en 143 = SIGTERM et `registry` en 2 : tous deux sans état, sans conséquence.)* Volumes intacts (8,9 + 4,7 Gio), 10 images, 5 827 conteneurs, **0 octet libéré — c'est attendu, un `stop` ne libère rien.** Politique `unless-stopped` : ils ne reviendront pas seuls, même après reboot. **Retour arrière en une commande** — `docker compose -p trigger --env-file /opt/trigger/hosting/docker/.env -f …/webapp/docker-compose.yml -f …/worker/docker-compose.yml start`.
  - **Ce que l'étape 1 achète : la pente, pas le disque.** Elle arrête l'ordonnanceur qui tirait toutes les 5 min à vide, donc **~0,15 Go/j** et l'accumulation des runners. Sur l'échéance du disque, cela vaut **une demi-journée** — le poste dominant reste `bot3.db`. Voir [[_hermes-polymarket]].
  - ⚠️ **`https://trigger.coldsend.app` répond désormais 502** (Caddy est actif, le webapp derrière ne l'est plus). Attendu.
  - 🔑 **PRÉALABLE AUX ÉTAPES 2 à 5 : exporter les 21 variables du dashboard.** Elles vivent chiffrées dans le volume Postgres et **ne sont mirrorées nulle part** — le dépôt documente leurs *noms* et leurs *rôles*, jamais leurs valeurs de production (le commit `3710f53` dit lui-même « dashboard confirmed »). Les clés d'API se retrouvent chez leurs émetteurs ; **les réglages de warming, non — ce sont des décisions.** Procédure en fin de note. **L'export exige de relancer la pile** : le dashboard est éteint.
- [x] ~~*(analyse d'origine, conservée)* La pile n'exécute plus rien du métier — 14,1 Go de volumes pour ça.~~ Mesuré le 2026-08-23 en base, pas au statut. ⚠️ **Le chiffre de 14,1 Go était faux, et il a fallu trois mesures pour l'établir** : `du -x` excluait les montages overlay ; puis `du` sans `-x` traversait les overlays montés et **comptait deux fois** les couches (19 Gio) ; **la vraie empreinte, mesurée conteneurs arrêtés, est de 21 Gio** — volumes 14 Gio + couches d'images 6,5 Gio dans `/var/lib/containerd` (le driver `overlayfs` ne les range pas sous `/var/lib/docker`) + 517 Mio de journaux de conteneurs. **C'est l'arrêt lui-même qui a rendu la mesure honnête**, en démontant les overlays. **49 118 runs depuis le 07/05, ~456 par jour, et sur les 7 derniers jours : 3 024 runs, tous `COMPLETED_SUCCESSFULLY`, zéro échec.** Le problème n'est pas la santé de la pile — c'est ce qu'elle exécute :

    | tâche | runs 7 j | total | dernier run |
    |---|---|---|---|
    | `dispatch-scheduled-sends` | 2 016 (une / 5 min) | 32 726 | **maintenant** |
    | `poll-batch` | 1 008 (une / 10 min) | 16 341 | **maintenant** |
    | `send-email` | 0 | 12 | 2026-05-13 |
    | `find-prospects` | 0 | 11 | **2026-05-21** |
    | `analyze-icp` | 0 | 10 | **2026-05-21** |
    | `orchestrate-campaign` | 0 | 8 | **2026-05-21** |
    | `generate-emails` | 0 | 8 | **2026-05-21** |

  **Les deux seules tâches vivantes sont l'ordonnanceur qui se réveille pour ne rien trouver.** Les quatre tâches qui *sont* le produit — `analyze-icp`, `find-prospects`, `generate-emails`, `orchestrate-campaign` — n'ont pas tourné depuis le **21/05**. Le `## Statut` du 12/07 (« premier run SUCCESS en prod ») reste vrai et n'a jamais eu de suite.
  - **Ce que ça coûte, mesuré** : **14,10 Go de volumes** (ClickHouse **8,8 Go** de télémétrie, Postgres **4,7 Go**) + **6,6 Go d'images**. Dans Postgres, `TaskEvent` pèse **1,4 Go pour 921 198 lignes** et `TaskRunExecutionSnapshot` 278 Mo — **c'est la trace de 49 000 exécutions qui n'ont rien produit.** S'y ajoutent **5 799 conteneurs `runner-*` arrêtés** jamais nettoyés, le plus vieux datant de 13 jours.
  - **Et ce n'est PAS récupérable par du ménage** : `docker system df` donne **0 B réclamable sur les volumes** (ils sont attachés à des conteneurs vivants). Un `docker system prune` ne rendrait que ~255 Mo (231,6 Mo d'images + 23,7 Mo de conteneurs). **Les 14,1 Go ne se libèrent que par une décision sur la pile elle-même**, pas par un nettoyage.
  - **Le lien avec l'urgence du serveur** : le disque sature vers le **27/08** ([[_hermes-polymarket]]), et **Docker est la seule réserve assez grosse pour débloquer `bot3_trim.py`**, dont la garde d'espace exige 23,3 Go libres. Libérer les caches ne suffit pas ; libérer Docker, oui.
  - **Rapport seul, aucune action prise** — consigne d'Hector du 23/08. La question posée était « est-ce que je paie 14 Go pour une pile morte ? » : **la pile est vivante, c'est le produit au-dessus qui est à l'arrêt.** *(À noter : `trigger-electric-1`, arrêté le 03/08, n'a rien à voir — il ne portait que l'API Realtime, voir [[01-Projects/autonomous/fiche-technique|fiche-technique]].)*
- [ ] Recharge Explorium
- [ ] Test E2E sur stripe.com
- [ ] DM 5 warm leads
- [ ] Clarification SIRET/SEPA (pour encaisser)

## Arrêt de Trigger.dev — ce qui est sauf, ce qui meurt, et comment sauver le reste

*(Relevé le 2026-08-23, étape 1 exécutée, étapes 2 à 5 en attente de l'export.)*

### ✅ Vérifié, pas supposé : le code est en sécurité

`~/autonomous` sur le Mac, branche `main`, remote **`github.com/lixyra05-debug/autonomous` — dépôt PRIVÉ**, poussé le 2026-05-31, **0 commit local non poussé** (seul `.gitignore` est modifié). **Les 8 tâches y sont toutes**, et ce sont exactement les 8 que Postgres connaît : `analyze-icp` · `dispatch-scheduled-sends` · `find-prospects` · `generate-emails` · `orchestrate-campaign` · `ping-pong` · `poll-batch` · `send-email`.

Deux choses qui allègent beaucoup le chemin de retour :
- **`trigger.config.ts:4` → `project: process.env.TRIGGER_PROJECT_REF ?? "proj_REPLACE_ME"`.** Le project ref est **déjà externalisé** : un nouvel ID après réinstallation coûte une variable, pas une modification de code.
- **`docs/trigger-dev-self-hosted-playbook.md` fait 581 lignes**, `docs/trigger-setup.md` 157. Le chemin de retour est déjà écrit.

Et la config de la pile survit aux volumes : `/opt/trigger` est un clone git (commit `6e8b039`), **`/opt/trigger/hosting/docker/.env` existe** (6 858 o).

### ⚠️ Ce qui meurt à l'étape 5

**La télémétrie** — 8,9 Gio de ClickHouse, 1,4 Gio de `TaskEvent` (921 198 lignes), 49 136 runs (dernier : 2026-08-23 15:20:00). **Sans valeur** : 3 024 runs sur 7 jours, tous réussis, tous de l'ordonnancement à vide.

**Et la vraie perte : les 42 variables d'environnement** (21 clés × 2 environnements), chiffrées dans le volume Postgres :

```
ANTHROPIC_API_KEY · ANTHROPIC_USE_BATCH · DAILY_SEND_CAP · EXPLORIUM_API_KEY
MAX_DRAFTS_PER_CAMPAIGN · NEXT_PUBLIC_POSTHOG_HOST · NEXT_PUBLIC_POSTHOG_KEY
NEXT_PUBLIC_SITE_URL · NEXT_PUBLIC_SUPABASE_ANON_KEY · NEXT_PUBLIC_SUPABASE_URL
PER_DOMAIN_DAILY_CAP · RESEND_API_KEY · SCHEDULER_TIMEZONE · SCRAPLING_URL
SEND_AUTO_CHAIN · SENDER_EMAIL · SEND_MODE · SEND_TRANSACTIONAL
SUPABASE_SERVICE_ROLE_KEY · TEST_MODE_ALLOWED_RECIPIENTS · WEEKLY_SEND_CAP
```

Les clés se retrouvent chez leurs émetteurs. **Les réglages de warming — `DAILY_SEND_CAP`, `WEEKLY_SEND_CAP`, `PER_DOMAIN_DAILY_CAP`, `SEND_MODE`, `TEST_MODE_ALLOWED_RECIPIENTS` — sont des décisions d'Hector, pas des valeurs dérivables.** Le dépôt en documente les noms et les rôles, jamais les valeurs de production.

### 🔑 Procédure d'export — à faire par Hector AVANT les étapes 2 à 5

**1. Rallumer la pile** (le dashboard est éteint depuis l'étape 1) :
```bash
ssh root@178.104.159.57 'docker compose -p trigger \
  --env-file /opt/trigger/hosting/docker/.env \
  -f /opt/trigger/hosting/docker/webapp/docker-compose.yml \
  -f /opt/trigger/hosting/docker/worker/docker-compose.yml start'
```
Attendre que `https://trigger.coldsend.app` réponde (Caddy est déjà actif ; ~1 à 2 min le temps que le webapp passe `healthy`).

**2. Où cliquer.** `https://trigger.coldsend.app` → connexion (magic link sur l'email du compte) → sélectionner le projet **Autonomous** → menu latéral **Environment Variables**. Basculer entre les onglets **Production** et **Development** : les deux jeux existent, d'où 42 valeurs pour 21 clés. Les valeurs sont masquées — l'icône œil de chaque ligne les révèle, une par une.

**3. Dans quel format.** Un simple `CLÉ=valeur` par ligne, deux blocs séparés `# --- production ---` et `# --- development ---`. Pas de JSON : ce format se recolle tel quel dans le dashboard au retour, et se compare d'un coup d'œil.

**4. Où les ranger — et surtout, séparément.**
- **Les 12 réglages non secrets** → `~/autonomous/docs/trigger-env-values.md`, commité dans le dépôt privé. Ce sont : `ANTHROPIC_USE_BATCH`, `DAILY_SEND_CAP`, `WEEKLY_SEND_CAP`, `PER_DOMAIN_DAILY_CAP`, `MAX_DRAFTS_PER_CAMPAIGN`, `SCHEDULER_TIMEZONE`, `SCRAPLING_URL`, `SEND_AUTO_CHAIN`, `SEND_MODE`, `SEND_TRANSACTIONAL`, `SENDER_EMAIL`, `TEST_MODE_ALLOWED_RECIPIENTS`, plus les `NEXT_PUBLIC_*` qui sont publiques par construction.
- **Les 5 secrets** → gestionnaire de secrets, **jamais dans le dépôt** : `ANTHROPIC_API_KEY`, `EXPLORIUM_API_KEY`, `RESEND_API_KEY`, `SUPABASE_SERVICE_ROLE_KEY`, `NEXT_PUBLIC_SUPABASE_ANON_KEY` *(celle-ci est anon donc publiable, mais elle voyage avec les autres — autant la ranger au même endroit)*.
- ⚠️ **Vérifier `.gitignore` avant de commiter** le fichier de docs, et **relire le diff** : c'est le moment exact où un secret entre dans un historique qui ne s'oublie pas. Règle du runbook [[vps-git-et-deploiement]] : scan anti-secrets **avant chaque commit**, pas seulement le premier.

**5. Re-stopper la pile** avec la même commande, `stop` au lieu de `start`.

**Contrôle de fin** : 21 clés × 2 environnements retrouvées, aucune valeur vide, et le fichier de docs ne contient **aucune** des 5 clés secrètes.

### Le chemin de retour, chiffré

| étape | effort |
|---|---|
| `docker compose up -d` depuis `/opt/trigger` (clone git et `.env` survivent) | ~30 min |
| Recréer compte + orga + projet (Postgres neuf = **nouveau `proj_`**) | ~15 min |
| Reporter le ref dans `TRIGGER_PROJECT_REF` (Vercel + local) | ~5 min — déjà externalisé |
| Ressaisir les 21 variables | **~20 min si exportées · plusieurs heures sinon** |
| `npx trigger.dev@latest deploy` — reconstruit l'image de tâche | ~15 min |
| Nouvelles clés prod → reconnecter le front Vercel | ~15 min |

**Une demi-journée si l'export est fait. Une à deux journées sinon**, à re-décider les plafonds de warming.

## Décisions
- **2026-08-23 — On n'agrandit pas le disque, on arrête Trigger.dev.** Décision d'Hector, prise après chiffrage des deux voies. Le volume Hetzner de 300 Go à ~12 €/mois achetait ~6 mois ; l'arrêt de la pile rend **21 Gio d'un coup**. Ce qui a emporté l'arbitrage n'est pas le prix : **la pile est en parfaite santé et n'exécute plus rien du métier depuis le 21/05** — payer pour du disque afin d'héberger la télémétrie de 49 000 runs à vide n'avait pas de sens. **L'étape 1 (`stop`) a été exécutée le jour même** ; les étapes 2 à 5, destructrices, attendent l'export des 21 variables. *« La décision d'archiver ou de garder ce produit m'appartient et elle ne se prend pas dans l'urgence du disque »* — d'où l'ordre choisi : arrêter d'abord, ce qui est gratuit et réversible, décider ensuite.
- **2026-08-03 — Clarification SIRET/SEPA : REPORTÉE AU 2026-09-15.** Décision d'Hector, datée et assumée — **pas un oubli**. Motif : la rentrée Ynov (septembre 2026) est prioritaire, et l'encaissement n'est pas bloquant avant cette date. La boucle est **fermée jusqu'au 15/09** ; elle doit ressortir à partir de là, et elle reste la seule du vault qui conditionne un encaissement réel.
- 2026-07-12 — Projet entré au vault suite à [[inventaire-projets]] : il n'avait ni pivot ni mention dans [[contexte-hector]] (corrigé le même jour).

## Ressources liées
- [[01-Projects/autonomous/fiche-technique|fiche-technique]] — stack, architecture, features, état de prod, pointeurs
- [[01-Projects/autonomous/decisions-et-lecons|decisions-et-lecons]] — décisions produit/tech et leçons durcies

## Journal du projet
### 2026-07-12
- Pivot créé + fiche technique + décisions/leçons via [[documenter-un-projet]] (repo `~/autonomous`, lecture seule, aucun code copié). Log : `AI/logs/2026-07-12-session.md`.
