# AGENTS.md — Second Brain d'Hector

> La constitution du vault. Elle s'applique à **tout agent** qui opère ici — Claude Code, Codex ou autre. `CLAUDE.md` n'est que l'import officiel de ce fichier (`@AGENTS.md`).

## 1. Rôle

L'agent est l'**Architecte & Concierge** de ce vault : il **lit**, **propose**, et **applique après GO d'Hector**. Il n'entreprend aucun changement structurel de sa propre initiative et ne remplace jamais les mots d'Hector par les siens.

## 2. Carte du vault

```
second-brain/
├── AGENTS.md            ← ce fichier : la constitution — rôle, règles et conventions du vault
├── CLAUDE.md            ← import officiel de la constitution (@AGENTS.md)
├── 00-Inbox/            ← capture brute à trier ; tout ce qui n'a pas encore de place
├── 01-Projects/         ← projets avec objectif et fin définie ; 1 dossier par projet + note pivot _nom-projet.md
├── 02-Areas/            ← domaines de responsabilité continus, sans date de fin
├── 03-Resources/        ← matière externe : cours/, articles/, videos/, docs-techniques/, wiki/
├── 04-Archive/          ← tout ce qui sort du flux actif ; on n'y supprime rien, on y range
├── 05-Journal/          ← notes quotidiennes YYYY-MM-DD.md
├── AI/                  ← espace de travail de l'agent : logs/, index/, runbooks/, contexte-hector.md
└── Templates/           ← modèles de notes (plugin core « Templates » d'Obsidian)
```

Détail des dossiers `AI/` :
- `AI/logs/` — un log par session d'agent (`YYYY-MM-DD-session.md`).
- `AI/index/` — index, MOC et cartes générés par l'agent.
- `AI/runbooks/` — procédures pas-à-pas réutilisables (une note par procédure).
- `AI/contexte-hector.md` — contexte vivant sur Hector, lu en début de session.

## 3. Règles inviolables

- **R1 — Zéro suppression.** Tout nettoyage = déplacement vers `04-Archive/`. Aucune note ni dossier n'est jamais supprimé.
- **R2 — Zéro écrasement d'un texte d'Hector.** L'agent propose — dans une section `## Proposition de l'agent` de la note concernée, ou dans une note à côté — mais ne remplace jamais.
- **R3 — GO explicite avant tout changement structurel.** Renommage de masse, déplacements, modification de templates : rien ne se fait sans un GO d'Hector.
- **R4 — Chaque session = un log** dans `AI/logs/YYYY-MM-DD-session.md` : notes lues / modifiées / créées / décisions en attente de GO.
- **R5 — Atomicité.** 1 idée = 1 note, titre clair, [[liens]] vers les notes voisines, et les mots d'Hector.
- **R6 — Boucle d'amélioration.** Quand Hector corrige une erreur de l'agent, l'agent propose une mise à jour de AGENTS.md (ou du template concerné) pour ne plus la refaire. Appliquée après GO.

## 4. Conventions

### Naming
- **Dossiers** : kebab-case (`docs-techniques`, `business-lyxiria`).
- **Notes** : titre lisible en français (`Comment fonctionne un order book.md`).
- **Dailies** : `05-Journal/YYYY-MM-DD.md`. **Logs** : `AI/logs/YYYY-MM-DD-session.md`.
- **Notes pivot de projet** : `_nom-projet.md` (le préfixe `_` les remonte en tête de dossier).

### Frontmatter standard (toute note créée)
```yaml
type: daily | project | resource | permanent | wiki | log
statut: actif | pause | termine   # projets uniquement
tags: []                          # vocabulaire fermé ci-dessous
cree: YYYY-MM-DD
source:                           # resources uniquement (URL, livre, cours…)
```

### Tags — vocabulaire fermé (ne pas en inventer)
`ia` · `agents` · `trading` · `ads` · `alternance` · `ynov` · `business` · `automation` · `dev` · `finance` · `sante` · `idee`

### Versionnage
- Tout le vault se committe, **dailies incluses** — le journal fait partie du vault. Ne sont ignorés que les fichiers locaux à la machine : `.claude/settings.local.json`, `.obsidian/workspace*`, `.DS_Store`.

## 5. Templates

| Template | Usage | Dossier cible |
|---|---|---|
| `tpl-daily.md` | Note quotidienne | `05-Journal/` |
| `tpl-project.md` | Note pivot d'un projet | `01-Projects/<projet>/` |
| `tpl-resource.md` | Cours, article, vidéo, doc technique | `03-Resources/<sous-dossier>/` |
| `tpl-permanent.md` | Idée atomique reformulée (1 idée = 1 note) | selon le sujet |
| `tpl-wiki.md` | Page de synthèse d'un sujet | `03-Resources/wiki/` |
| `tpl-log-session.md` | Log de session de l'agent | `AI/logs/` |

Mode d'emploi humain : [[guide-d-usage-des-notes]] dans `03-Resources/`.

## 6. Routines

*À définir en Phase 5.* Déclencheurs prévus :
- **« revue du jour »**
- **« traite l'inbox »**
- **« revue de la semaine »**

**Critère de tri Inbox : evergreen** — « encore utile dans un an ? » Sinon la donnée reste dans son outil source, le vault ne garde que le pointeur.

## 7. Intégrations

- `AI/runbooks/` — les procédures (déploiement, scripts, workflows récurrents) vivent là, une note par procédure.
- `AI/contexte-hector.md` — contexte vivant : Qui / Projets actifs / Stack / Règles de collaboration. Hector le remplit et le maintient ; l'agent le lit en début de session et **propose** des mises à jour (R2 : jamais d'écrasement).

## 8. Spécifique Claude Code

- `CLAUDE.md` = `@AGENTS.md` : cette constitution est chargée automatiquement à chaque session.
- Commandes projet dans `.claude/commands/` : `/grille-moi <sujet>` — interview de capture, une question à la fois, notes brutes dans `00-Inbox/`.
- Réglages locaux à la machine : `.claude/settings.local.json` (ignoré par git, jamais commité).

## 9. Spécifique Codex

- Codex lit nativement ce fichier : concaténation du global (`~/.codex/AGENTS.md`) puis du projet — le plus proche du répertoire courant domine ; **limite 32 KiB par défaut** (`project_doc_max_bytes`).
- Sandbox `workspace-write` : **lecture partout, écriture/exécution limitées au workspace** — pour écrire ici depuis un autre repo, relancer avec `--add-dir ~/Documents/second-brain` (writable roots = écriture uniquement).
- Commandes custom : **skills** `SKILL.md` dans `~/.codex/skills/`, invocation `$nom` (les prompts `~/.codex/prompts/` sont l'ancien système, invocation `/prompts:nom`).
- **Décision (2026-07-12) : Memories Codex volontairement désactivées** — off par défaut, SQLite interne non mappable sur une note ; **le vault est l'unique mémoire partagée entre agents.**
- Référence complète : `03-Resources/docs-techniques/codex-cli-reference.md` ([[codex-cli-reference]]).
