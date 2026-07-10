---
type: project
statut: actif
tags: [ia, automation]
cree: 2026-07-03
---

# Second Brain

## Objectif
Construire le second brain Obsidian d'Hector : structure PARA + journal + espace AI, avec Claude comme architecte & concierge (règles dans [[CLAUDE]]).

## Statut
Phase 1 terminée le 2026-07-03 : structure, CLAUDE.md, templates, guide d'usage. Prochaines phases : remplissage/capture, puis Phase 3 (migrations éventuelles), Phase 5 (routines).

## Next actions
- [x] Hector valide [[contexte-hector]] (fait le 2026-07-04, sans correction)
- [x] Hector valide les Objectifs/Statuts des 5 autres notes pivot (fait le 2026-07-04)
- [ ] Hector configure le plugin core « Templates » d'Obsidian (dossier : `Templates/`)
- [ ] Phase 4 : runbook ingestion wiki (tri inbox du 2026-07-10)
- [ ] Phase 6 : AGENTS.md miroir Hermes/Codex (tri inbox du 2026-07-10)

## Décisions
- 2026-07-03 — Trading-Bots-Vault reste un vault séparé ; import éventuel en Phase 3.
- 2026-07-03 — 6 projets confirmés · `05-Journal/` séparé · wiki dans `03-Resources/wiki/`.
- 2026-07-03 — Chaque phase se termine par un commit git.
- 2026-07-04 — Objectifs/Statuts des 5 pivots validés ; extraction des runbooks du doc v4.0 reportée après la Phase 5.
- 2026-07-10 — Tri de [[idees-videos-second-brain]] : /grille-moi, R6 et critère evergreen actés et appliqués ; runbook ingestion wiki → Phase 4 ; AGENTS.md miroir → Phase 6 ; note archivée.

## Ressources liées
- [[guide-d-usage-des-notes]]
- [[Les 5 niveaux d'un second brain avec Claude]]
- [[Les 5 niveaux d'utilisation de Claude]]

## Journal du projet
### 2026-07-03
- Phase 1 exécutée : git init + commit baseline, création de la structure complète, CLAUDE.md, 6 templates, guide d'usage, notes pivot. Log : `AI/logs/2026-07-03-phase1.md`.

### 2026-07-04
- Contexte d'Hector distillé dans [[contexte-hector]] (doc v4.0 + corrections) ; Objectifs/Statuts proposés sur les 5 autres pivots (R2 — à valider). Log : `AI/logs/2026-07-04-session.md`.
- Objectifs et Statuts validés par Hector ; Statuts remplacés par l'état réel au 04/07, mentions de proposition retirées.

### 2026-07-10
- 2 vidéos Nate Herk (méthode second brain, niveaux de Claude) fichées dans `03-Resources/videos/` ; 5 idées à trier dans [[idees-videos-second-brain]] (GO attendu). Log : `AI/logs/2026-07-10-session.md`.
- Tri acté par Hector : R6 + critère evergreen dans `CLAUDE.md`, commande `/grille-moi` créée, Phases 4 et 6 au backlog, note d'idées annotée et archivée (premier item d'Inbox traité). Log : session 2 du même log.
