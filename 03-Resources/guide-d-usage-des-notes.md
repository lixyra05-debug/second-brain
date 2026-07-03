---
type: resource
tags: []
cree: 2026-07-03
source: interne — Phase 1 du second brain
---

# Guide d'usage des notes

Le mode d'emploi du vault, version humaine. Les règles complètes pour Claude sont dans `CLAUDE.md`.

## Quel template quand ?

| Tu veux… | Template | Où va la note |
|---|---|---|
| Écrire ta note du jour | `tpl-daily` | `05-Journal/2026-07-03.md` |
| Lancer ou documenter un projet | `tpl-project` | `01-Projects/<projet>/_<projet>.md` |
| Garder la trace d'un cours, article, vidéo, doc | `tpl-resource` | `03-Resources/cours/`, `articles/`, `videos/` ou `docs-techniques/` |
| Capturer UNE idée qui tient debout toute seule | `tpl-permanent` | là où elle vit (area, projet…) — sinon `00-Inbox/` en attendant |
| Faire la synthèse d'un sujet (hub) | `tpl-wiki` | `03-Resources/wiki/` |
| (Claude) Logger une session | `tpl-log-session` | `AI/logs/` |

Pour insérer un template : plugin core **Templates** d'Obsidian → « Insert template » (configurer le dossier de templates sur `Templates/` dans les réglages).

## Comment capturer

1. **Ne réfléchis pas au rangement au moment de la capture.** Tout ce qui n'a pas de place évidente va dans `00-Inbox/` — une note par idée, titre clair.
2. En cours de journée, le plus simple : la section **Captures → Inbox** de ta daily. Tu (ou Claude, sur « traite l'inbox ») transformeras chaque ligne en vraie note plus tard.
3. **1 idée = 1 note.** Si ta note contient deux idées, c'est deux notes. Le titre doit dire l'idée, pas la ranger (« Les agents perdent le contexte entre deux sessions » plutôt que « Notes IA #4 »).

## Comment lier

- Mets des `[[liens]]` **au moment où tu écris**, dès qu'un mot renvoie à un projet, un sujet ou une autre note : c'est le maillage qui rend le vault utile, pas le rangement.
- Un lien vers une note qui n'existe pas encore est **une bonne chose** : il marque une note à écrire.
- Chaque ressource devrait pointer vers au moins un projet ou une page wiki ; chaque note permanente vers son origine et ses voisines.
- Quand plusieurs notes tournent autour d'un même sujet, crée une page `tpl-wiki` qui les rassemble — c'est le hub, les notes sont les rayons.

## Les tags (vocabulaire fermé)

`ia` · `agents` · `trading` · `ads` · `alternance` · `ynov` · `business` · `automation` · `dev` · `finance` · `sante` · `idee`

On n'en invente pas de nouveaux : douze suffisent, c'est ce qui les rend cherchables. Les liens `[[...]]` font le travail fin, les tags font le travail large.
