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
**Au 2026-07-31** *(premier instantané daté de ce pivot — le précédent avait **28 jours** et décrivait un vault qui n'existe plus : le pivot du vault était devenu la note la moins à jour du vault, relevé par les deux rêves)*
- **Le vault vit désormais à `~/second-brain`**, hors de `~/Documents`. macOS y bloquait launchd par TCC ; déplacer le dossier évitait d'accorder l'accès complet au disque à `/bin/zsh`. Git a voyagé intact (74 fichiers suivis, remote joignable, `.git` sans chemin absolu). **Ne jamais le remettre dans un dossier protégé** (`~/Documents`, `~/Desktop`, `~/Downloads`).
- **`/dream` v1.5 livré** — la commande de consolidation lit tout le vault, **n'applique rien**, et produit `AI/dreams/YYYY-MM-DD-dream.md` en **six** sections : Digest · **Boucles non fermées** · Statuts périmés · Consolidations · Graines de wiki · Miroir. Deux affinages nés du premier run : un pivot `pause` n'est plus périmé par la seule date, et deux rêves le même jour **s'empilent** au lieu de s'écraser.
- **Automatisé** — agent launchd `com.hectorvolant.secondbrain.dream`, **dimanche 20 h**, avec rattrapage si le Mac était éteint. Wrapper à préflight, chien de garde 40 min, contrôle de périmètre sur le delta git, et notification macOS à la fin. **Bout en bout validé le 31/07** (`exit 0`, rêve écrit, notification reçue). Runbook : [[dream]]. Premier créneau réel : **dimanche 20 h**.
- **Phases** : 1, 2 et 6 faites. La **Phase 5 n'est plus un chantier** — §6 acte que `/dream` tient la revue périodique ; restent à définir « revue du jour » et « traite l'inbox ». Ouvertes : **Phase 4** (runbook d'ingestion wiki — `03-Resources/wiki/` toujours vide) et l'extraction des runbooks du doc v4.0, **orpheline** depuis que sa porte (la Phase 5) a été fermée sans reparentage.
- La constitution compte désormais **R1-R7**, `AI/dreams/` est cité en §2, §7 et §8, et `/grille-moi` **n'a toujours jamais servi** — ce qui gèle son portage en skill Codex.

---

Phase 1 terminée le 2026-07-03 : structure, CLAUDE.md, templates, guide d'usage. Prochaines phases : remplissage/capture, puis Phase 3 (migrations éventuelles), Phase 5 (routines).

## Next actions
- [x] Hector valide [[contexte-hector]] (fait le 2026-07-04, sans correction)
- [x] Hector valide les Objectifs/Statuts des 5 autres notes pivot (fait le 2026-07-04)
- [x] Hector configure le plugin core « Templates » d'Obsidian (dossier : `Templates/`) — fait, constaté dans `.obsidian/templates.json` le 2026-07-12 (daily notes câblées aussi : `05-Journal` + `tpl-daily`)
- [ ] Phase 4 : runbook ingestion wiki (tri inbox du 2026-07-10) — **3 graines de wiki identifiées par le rêve du 31/07, à créer en Phase 4 ; rien n'a été créé** :
  - « **Garde-fous des agents autonomes** » — sources : [[contexte-hector]] (incident Curator) + les `decisions-et-lecons` de hermes-polymarket, agent-alternance et autonomous + [[_ads-studio]]. Les mêmes 5 règles reviennent dans 4 projets indépendants.
  - « **Anatomie du VPS Hetzner** » — sources : les 3 fiches techniques VPS (hermes, alternance, autonomous) + [[_ads-studio]] + [[inventaire-projets]]. Un seul serveur, 4 projets, 19 timers ; la carte n'existe nulle part.
  - « **Le vault comme mémoire partagée entre agents** » — sources : `AGENTS.md` §8-§9 + [[codex-cli-reference]] + [[MCP 2026-07-28 — cœur stateless]] + [[OpenWorker — agent open-source model-independent]].
- [x] Phase 6 : AGENTS.md miroir Hermes/Codex (tri inbox du 2026-07-10) — fait le 2026-07-12 : AGENTS.md = constitution, CLAUDE.md = `@AGENTS.md`, import vérifié sur session fraîche
- [x] Documenter hermes-polymarket + agent-alternance, et localiser bot-automobile / molty-openclaw / code ads-studio (repos VPS, ssh lecture seule — session dédiée ; fiches selon trouvaille) — fait le 2026-07-12 (fiches créées ; bot-automobile introuvable ; molty non déployé ; ads-studio localisé puis backupé)
- [ ] Porter /grille-moi en skill Codex ($grille-moi) — gaté derrière la première utilisation réelle côté Claude Code

## Décisions
- 2026-07-03 — Trading-Bots-Vault reste un vault séparé ; import éventuel en Phase 3.
- 2026-07-03 — 6 projets confirmés · `05-Journal/` séparé · wiki dans `03-Resources/wiki/`.
- 2026-07-03 — Chaque phase se termine par un commit git.
- 2026-07-04 — Objectifs/Statuts des 5 pivots validés ; extraction des runbooks du doc v4.0 reportée après la Phase 5.
- 2026-07-10 — Tri de [[idees-videos-second-brain]] : /grille-moi, R6 et critère evergreen actés et appliqués ; runbook ingestion wiki → Phase 4 ; AGENTS.md miroir → Phase 6 ; note archivée.
- 2026-07-12 — Inventaire des projets ([[inventaire-projets]]) trié par Hector : autonomous (actif, découvert par l'inventaire) et remedes-naturels (pause) entrent dans 01-Projects ; expérimentaux/abandonnés restent sans fiche ; mission VPS élargie (bot-automobile, code ads-studio).
- 2026-07-12 — **Phase 6 exécutée** (GO Hector) : AGENTS.md devient la constitution du vault, règles généralisées à tout agent (« l'agent » au lieu de « Claude », R6 pointe AGENTS.md) + sections Spécifique Claude Code / Spécifique Codex ; CLAUDE.md réduit à l'import officiel `@AGENTS.md`. Import vérifié : session fraîche sans outils récite R1-R6 et le critère evergreen.
- 2026-07-12 — Référence Codex ingérée ([[codex-cli-reference]]), §9 de la constitution rempli ; **Memories Codex volontairement désactivées — le vault est l'unique mémoire partagée entre agents** ; `~/.codex/AGENTS.md` global créé (il n'existait pas), pointant vers la constitution.
- 2026-07-31 — **`/dream` v1 créée** : commande de consolidation de mémoire en lecture seule (lit tout le vault, ne modifie rien, produit `AI/dreams/YYYY-MM-DD-dream.md` en 5 sections, zéro application automatique).
- 2026-07-31 — **Arbitrage du premier rêve** ([[2026-07-31-dream]]). *Appliqué* : `AI/dreams/` et `/dream` entrent dans la constitution (§2, §8) ; **§6 acté — `/dream` tient la revue périodique, « revue du jour » et « traite l'inbox » restent à définir, la Phase 5 n'est plus un chantier ouvert** ; « livré » n'est pas un statut (§4) et l'inventaire s'aligne sur le frontmatter des pivots ; [[Kimi K3 — rapport technique]] promue d'Inbox en fiche resource ; sections vides des pivots marquées « (à remplir) » ; statuts d'[[_agent-alternance]] et d'[[_ads-studio]] rafraîchis après reconnaissance VPS en lecture seule. *Non appliqué* : les 3 graines de wiki (→ Phase 4, ci-dessus) et les autres rafraîchissements de statut — **Hector les fournit, l'agent ne les devine pas**.

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
- Premier runbook créé ([[documenter-un-projet]], généralisé du pilote LegitVision) et appliqué à Vivo ; VPS au backlog. Log : session 4 du même log.

### 2026-07-12
- Inventaire complet des projets Claude Code (3 sources croisées) puis Phase 2 sur GO : autonomous et remedes-naturels documentés (2 nouveaux pivots — 8 dossiers projets désormais), vivo rafraîchi, contexte-hector corrigé, mission VPS élargie. Log : `AI/logs/2026-07-12-session.md`.
- Backup GitHub privé du vault (`lixyra05-debug/second-brain`) puis journal + config Obsidian versionnés ; mission VPS (fiches hermes-polymarket + agent-alternance, code ads-studio localisé puis backupé sur `lixyra05-debug/ads-studio`) ; **Phase 6** : constitution AGENTS.md + CLAUDE.md = `@AGENTS.md` (import vérifié). Log : sessions 2-5 du même log.

### 2026-07-31
- **`/dream` v1** créée et **premier rêve** produit ([[2026-07-31-dream]]) : 7 pivots périmés sur 8, 1 capture Inbox à promouvoir, 1 incohérence de vocabulaire, 5 boucles ouvertes chronométrées, 3 graines de wiki, et un miroir (39 notes machine / 0 note écrite de la main d'Hector). Log : `AI/logs/2026-07-31-session.md`.
- **Arbitrage rendu le jour même** : les 3 changements structurels appliqués (§2, §6, §8 + §4 sur le vocabulaire de statut), Kimi K3 promue, sections vides des pivots rendues visibles, et 2 statuts rafraîchis après reconnaissance VPS lecture seule (agent-alternance : 58 offres dont 45 jamais scorées ; ads-studio : bloqueur crédits levé, 11 générations, 10 commits). Graines de wiki renvoyées en Phase 4.
- **`/dream` v1.5** : règle `pause` (plus de faux positif sur un projet en pause), section **Boucles non fermées** (le rêve rend des comptes sur ses propres propositions), règle anti-écrasement (deux rêves le même jour s'empilent). **Automatisé** par launchd le dimanche à 20 h, avec préflight, chien de garde et notification macOS.
- **Vault déplacé en `~/second-brain`** pour contourner TCC sans élargir de permission — 11 références de chemin corrigées dans 9 fichiers, git intact. **Bout en bout du rêve automatique validé** le soir même : `exit 0`, rêve empilé sous « Rêve 2 », notification reçue. Les deux affinages v1.5 se sont vérifiés d'eux-mêmes en conditions réelles.
- **Constitution : R7 ajoutée** (« un log ne se réécrit jamais ») et leçon sur le moment d'écrire un `## Statut` (en fin de session). Pointeur global `~/.claude/CLAUDE.md` créé, miroir de `~/.codex/AGENTS.md`.
