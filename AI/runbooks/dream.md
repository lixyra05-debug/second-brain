---
type: resource
tags: [ia, automation]
cree: 2026-07-31
source: interne — mise en place du déclenchement automatique de /dream, 2026-07-31
---

# Runbook — `/dream` automatique (launchd, dimanche 20 h)

Le rêve hebdomadaire tourne tout seul et prévient par notification macOS. **Un rêve que personne ne lit ne sert à rien** : c'est la notification qui ferme la boucle, pas le fichier.

Ce qu'il fait : lit tout le vault, écrit `AI/dreams/YYYY-MM-DD-dream.md` en six sections, écrit son log de session, **et s'arrête là**. Il n'applique rien, ne commite rien, ne pousse rien.

> ⚠️ **UNE ACTION MANUELLE RESTE À FAIRE — sans elle, le rêve automatique ne tournera pas.**
> Voir *« Le blocage macOS »* ci-dessous. Tout le reste est en place et testé.

## Les pièces

| Pièce | Chemin | Versionnée ? |
|---|---|---|
| La commande | `.claude/commands/dream.md` | **oui** (dans le vault) |
| Le wrapper | `~/.local/bin/dream-weekly.sh` | non — hors vault, contenu recopié ci-dessous |
| Le déclencheur | `~/Library/LaunchAgents/com.hectorvolant.secondbrain.dream.plist` | non — hors vault |
| Le journal d'exécution | `~/Library/Logs/second-brain-dream.log` | non |

Label launchd : **`com.hectorvolant.secondbrain.dream`**

## Le blocage macOS — à lever une fois

**Constaté au test du 2026-07-31.** macOS protège `~/Documents` (mécanisme TCC). Un process lancé par **launchd** n'y a **aucun accès** par défaut, même en étant le bon utilisateur. Le vault y vivant, le rêve automatique ne peut rien lire tant que ce n'est pas levé.

Preuve du test : `git status` dans le vault renvoie `fatal: Unable to read current working directory: Operation not permitted` depuis launchd, alors que **le même script lancé depuis le Terminal fonctionne parfaitement** — c'est bien une question de permission, pas de code.

**Le correctif, à faire à la main :**
> Réglages Système › Confidentialité et sécurité › **Accès complet au disque** › `+` › `⌘⇧G` › taper `/bin/zsh` › Ouvrir › activer l'interrupteur.

Puis vérifier : `launchctl kickstart -p gui/$(id -u)/com.hectorvolant.secondbrain.dream` doit afficher `préflight: lecture du vault OK` dans le log.

**Le compromis, à connaître** : accorder l'accès complet au disque à `/bin/zsh` le donne à **tout script zsh lancé par launchd**, pas seulement à celui-ci. C'est la méthode standard pour un agent launchd qui touche un dossier protégé, mais c'est large. Les alternatives sont pires : déplacer le vault hors de `~/Documents`, ou renoncer à l'automatisation.

## Pourquoi launchd et pas cron

`StartCalendarInterval` **rattrape le créneau manqué** : si le Mac était éteint ou endormi le dimanche à 20 h, launchd déclenche dès le réveil. Un `cron` classique aurait simplement perdu la semaine — inacceptable pour un rituel hebdomadaire sur une machine portable.

## Tester à la main

```sh
# 1. Déclencher le job comme launchd le ferait (le plus fidèle)
launchctl kickstart -p gui/$(id -u)/com.hectorvolant.secondbrain.dream

# 2. Suivre le déroulé
tail -f ~/Library/Logs/second-brain-dream.log

# 3. Ou lancer le wrapper seul, sans passer par launchd
#    ⚠️ ce chemin-là NE teste PAS le blocage TCC : depuis le Terminal, l'accès est accordé.
~/.local/bin/dream-weekly.sh
```

Un run réussi laisse dans le log : le binaire `claude` résolu, `préflight: lecture du vault OK`, `claude exit=0`, `périmètre OK`, `OK — .../AI/dreams/<date>-dream.md`. La notification arrive à la fin.

## Désactiver

```sh
# Suspendre sans rien supprimer (réversible immédiatement)
launchctl bootout gui/$(id -u)/com.hectorvolant.secondbrain.dream

# Réactiver
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.hectorvolant.secondbrain.dream.plist

# Vérifier l'état et la dernière sortie
launchctl print gui/$(id -u)/com.hectorvolant.secondbrain.dream | grep -E "state = |runs =|last exit"

# Désinstaller pour de bon
launchctl bootout gui/$(id -u)/com.hectorvolant.secondbrain.dream
rm ~/Library/LaunchAgents/com.hectorvolant.secondbrain.dream.plist ~/.local/bin/dream-weekly.sh
```

## Garde-fous inscrits dans le wrapper

- **Préflight TCC.** Le wrapper vérifie qu'il peut lire le vault **avant** de lancer `claude`. Sans ce test, `claude` démarre quand même et reste **pendu indéfiniment** — constaté au premier test du 31/07, le job tournait encore après plusieurs minutes sans rien produire. Un échec net en 2 secondes avec notification vaut infiniment mieux qu'un job pendu toute la semaine.
- **Chien de garde 40 min.** macOS n'a pas `timeout(1)` : le wrapper lance `claude` en arrière-plan et le tue au-delà du délai. Un rêve ne peut donc plus bloquer le créneau suivant.
- **Le chemin de `claude` n'est jamais codé en dur.** Le binaire peut vivre sous `~/.nvm/versions/node/<version>/bin/claude`, chemin qui **change à chaque montée de version de Node**. Le wrapper le résout à chaque run (`command -v`, puis repli sur un glob nvm, `~/.local/bin`, Homebrew) et abandonne avec une notification s'il ne le trouve pas.
- **Contrôle de périmètre après le run** : le wrapper photographie `git status` **avant** et **après**, et n'examine que le **delta**. Si le rêve a fait bouger autre chose que `AI/dreams/` ou `AI/logs/`, la notification devient « **à vérifier** » au lieu de « prêt ». Comparer le delta et non le status absolu évite la fausse alerte quand du travail non commité traîne déjà dans l'arbre — ce qui arrive un dimanche soir.
- **Aucun commit** (`COMMIT=0` en tête du script). Volontaire : un `git status` non vide est justement le rappel qu'un rêve attend d'être lu. Passer à `COMMIT=1` pour l'inverse.
- **Échec silencieux impossible** : chaque branche d'échec notifie et sort en `exit 1`, visible dans `launchctl print`.
- `--permission-mode acceptEdits` : nécessaire en headless, sinon le run se bloque sur une demande de permission que personne ne verra. Le vrai garde-fou reste le contrat de la commande (lecture seule, deux fichiers écrits) **plus** le contrôle de périmètre ci-dessus.

## Contenu du wrapper (recopié — le fichier est hors vault, donc hors backup)

```sh
#!/bin/zsh
set -u
VAULT="$HOME/Documents/second-brain"
COMMIT=0
TIMEOUT=2400
DATE="$(date +%Y-%m-%d)"
DREAM="$VAULT/AI/dreams/$DATE-dream.md"

notify() { /usr/bin/osascript -e "display notification \"$2\" with title \"$1\" sound name \"Glass\"" >/dev/null 2>&1 || true; }

echo "===== $(date '+%Y-%m-%d %H:%M:%S') — run /dream ====="

CLAUDE=""
if command -v claude >/dev/null 2>&1; then
  CLAUDE="$(command -v claude)"
else
  for c in "$HOME"/.nvm/versions/node/*/bin/claude(N) "$HOME"/.local/bin/claude(N) /opt/homebrew/bin/claude(N); do
    [ -x "$c" ] && CLAUDE="$c"
  done
fi
[ -z "$CLAUDE" ] && { notify "Rêve — échec" "Binaire claude introuvable."; exit 1; }

cd "$VAULT" 2>/dev/null || { notify "Rêve — échec" "Vault introuvable."; exit 1; }

# Preflight TCC : sans lui, claude demarre et reste pendu.
if ! git -C "$VAULT" status --porcelain >/dev/null 2>&1; then
  notify "Rêve — accès refusé" "launchd ne peut pas lire le vault. Accorder l'accès complet au disque à /bin/zsh."
  exit 1
fi

AVANT="$(git status --porcelain | sort)"

# Chien de garde : macOS n'a pas timeout(1).
"$CLAUDE" -p "/dream" --permission-mode acceptEdits &
CPID=$!
( sleep "$TIMEOUT"; kill -0 "$CPID" 2>/dev/null && { kill -TERM "$CPID" 2>/dev/null; sleep 5; kill -KILL "$CPID" 2>/dev/null; } ) &
WPID=$!
wait "$CPID"; RC=$?
kill "$WPID" 2>/dev/null

[ ! -f "$DREAM" ] && { notify "Rêve — échec" "Aucune note produite le $DATE (exit $RC)."; exit 1; }

APRES="$(git status --porcelain | sort)"
HORS=$(comm -13 <(printf '%s\n' "$AVANT") <(printf '%s\n' "$APRES") | grep -vE '(AI/dreams/|AI/logs/)' || true)
if [ -n "$HORS" ]; then
  notify "Rêve du $DATE — à vérifier" "Des fichiers hors AI/dreams et AI/logs ont bougé."
else
  notify "Rêve du $DATE prêt" "$(basename "$DREAM") — à arbitrer dans le vault."
fi

[ "$COMMIT" = "1" ] && { git add AI/dreams AI/logs && git commit -q -m "dream: rêve du $DATE"; }
exit 0
```

## Pièges connus

- **TCC / accès au vault** — le blocage principal, traité en haut de ce runbook. Signature : `Operation not permitted` sur `getcwd`.
- **Deux rêves le même jour** : le fichier porte la date, donc un second run écraserait le premier. La commande l'interdit — elle **empile** un bloc `## Rêve N (même jour)`, comme les logs empilent leurs sessions. Un rêve déjà arbitré est une trace, pas un brouillon.
- **`launchctl load` est obsolète** sur macOS récent : utiliser `bootstrap` / `bootout` / `kickstart`.
- **Notification absente** : vérifier Réglages › Notifications. Le canal a été testé le 31/07 depuis le Terminal **et** depuis launchd (la notification d'échec du préflight est bien partie) — il fonctionne.
- **Premier run après une mise à jour de Node** : c'est le moment de risque. Si le wrapper ne trouve plus `claude`, il notifie l'échec au lieu de rater en silence.

## Liens
- [[_second-brain]] — le projet du vault
- [[veille-a-la-demande]] — l'autre runbook d'automatisation, même discipline (canal indisponible = on saute et on le dit)
- `.claude/commands/dream.md` — la commande elle-même, versionnée dans le vault
