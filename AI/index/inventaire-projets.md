---
type: wiki
tags: [dev]
cree: 2026-07-12
---

# Inventaire des projets Claude Code

> Recensement du 2026-07-12, lecture seule. Sources croisées : dossiers de sessions `~/.claude/projects/`, chemins projets de `~/.claude.json` (clés uniquement — aucun transcript lu), `find ~ -maxdepth 4 -name .git` (hors Library/node_modules/.Trash), [[contexte-hector]] et les 6 pivots. **Tri confirmé par Hector le 2026-07-12** — verdicts annotés dans les tableaux ; autonomous reclassé **actif**.

## Projets construits

| Projet | Chemin | Commits | Dernière activité | Catégorie proposée | Note |
|---|---|---|---|---|---|
| legitvision | `~/legitvision` | 110 | 2026-06-24 | **livré** | SaaS en prod — documenté le 10/07 ✅ |
| vivo | `~/projects/vivo` | 34 | 2026-07-10 | **livré** | feature-complete ; ⚠️ RevenueCat intégré le 10/07 (`d4bc7d3`), postérieur à la fiche → état de la fiche à rafraîchir |
| autonomous | `~/autonomous` | 49 | 2026-05-31 | **actif** (GO 12/07) | SaaS outbound **en prod** : autonomous.coldsend.app — pivot créé, documenté et ajouté à [[contexte-hector]] le 12/07 ✅ |
| remedes-naturels | `~/projects/remedes-naturels` | 9 | 2026-05-04 | **livré** (pivot : pause) | ebooks via Stripe — documenté le 12/07 ✅ |
| second-brain | `~/Documents/second-brain` | 11 | 2026-07-10 | **actif** | ce vault — auto-documenté ([[_second-brain]]) |
| hermes-polymarket | VPS (openclaw) | ? | ? | **actif** | moteurs Python + 2 bots paper — session ssh dédiée (déjà au backlog) |
| agent-alternance | VPS (openclaw) | ? | ? | **actif** | pipeline scraping/scoring/HITL — session ssh dédiée (déjà au backlog) |
| ads-studio | aucun repo local identifié | — | — | **actif** | orchestration Hermes + Higgsfield + Meta Ads ; pas de codebase sur ce Mac — GO 12/07 : pas de fiche, le pivot fait foi ; vérif VPS à venir |
| molty-openclaw | VPS + workspace `~/clawd` | 0 (local) | — | **actif** | agent de veille 24/7 ; `~/clawd` = workspace identité (SOUL/IDENTITY/HEARTBEAT…), git vide |
| bot-automobile | non localisé (VPS ?) | ? | ? | **actif ?** | scraping Playwright + alertes Telegram — GO 12/07 : ajouté à la mission VPS du backlog, fiche selon trouvaille |
| excuses-amandine | `~/Desktop/excuses-amandine` | pas de git | ~2026-06-30 | **expérimental** | one-shot perso (médias + page), sessions Claude Code |
| electricien-360 | `~/electricien-360` | 1 | 2026-01-18 | **abandonné** (confirmé 12/07) | create-next-app jamais développé (unique commit d'init, pas de déploiement) + zip dans le home |
| New project | `~/Documents/New project` | 0 | — | **abandonné** | scratch HTML/CSS/JS, git vide |

## Satellites

| Satellite | Chemin | Commits | Dernière activité | Parent | Note |
|---|---|---|---|---|---|
| legitvision-ads | `~/legitvision-ads` | 1 | 2026-05-06 | legitvision | créas Remotion — déjà pointé dans la fiche LegitVision ✅ |
| Trading-Bots-Vault | `~/Downloads/Trading-Bots-Vault` | pas de git | — | hermes-polymarket | vault Obsidian séparé (Bot1-Hermes / Bot2-Atlas / Bot3-BTC5m / Infra) — décision du 03/07 : import éventuel en Phase 3 |

## Outils (clones / infra — rien à documenter)

| Outil | Chemin | Commits | Dernière activité | Note |
|---|---|---|---|---|
| hermes-agent | `~/.hermes/hermes-agent` | 12 559 | 2026-06-22 | clone NousResearch/hermes-agent — le cockpit Hermes |
| agency-agents | `~/agency-agents` | 291 | 2026-04-11 | clone msitarzewski/agency-agents — la bibliothèque d'agents |
| notebooklm-py | `~/notebooklm-py` | 655 | 2026-04-06 | clone teng-lin/notebooklm-py |
| Open-Generative-AI | `~/Open-Generative-AI` | 151 | 2026-05-06 | clone Anil-matcha — alternative Higgsfield (utile à ads-studio) |
| fugu | `~/.fugu` | 75 | 2026-06-26 | clone SakanaAI/fugu — recherche |
| skills | `~/skills` | pas de git | — | répertoire de skills locaux (find-skills) |

## Exclusions
- `~/.claude.json` référence aussi `/Users/volanthector` et `~/Downloads` : dossiers de session, pas des projets.
- Internals ignorés : `~/.codex/*` (plugins/vendor), `~/.nvm`.

## Proposition de tri (Phase 2, après GO)
1. **Runbook complet** ([[documenter-un-projet]]) : `remedes-naturels` · `autonomous` (avec création du dossier `01-Projects/autonomous/` + pivot, il n'existe pas) · rafraîchissement d'état de la fiche `vivo` (RevenueCat intégré).
2. **VPS, session ssh dédiée** (déjà au backlog) : `hermes-polymarket` (+ pointeur Trading-Bots-Vault dans sa fiche) · `agent-alternance` · vérifier au passage `molty-openclaw` et `bot-automobile`.
3. **Pas de fiche — leur ligne d'inventaire est leur trace (evergreen)** : `excuses-amandine` · `electricien-360` · `New project`.
4. **Satellites** : `legitvision-ads` fait ✅ · `Trading-Bots-Vault` → pointeur dans la future fiche hermes-polymarket.
5. **Outils** : rien à faire.

## Questions pour le GO — réponses d'Hector (2026-07-12)
1. **autonomous** — **ACTIF**, pas seulement livré : pivot créé ([[_autonomous]]), documenté, ajouté aux projets actifs de [[contexte-hector]].
2. **ads-studio** — pas de fiche : rien à documenter sans repo, le pivot fait foi. On vérifiera sur le VPS si du code y vit.
3. **bot-automobile** — très probablement sur le VPS (scraper Python/Playwright 24/7, alertes Telegram) : ajouté à la mission VPS du backlog, fiche selon trouvaille.
4. **electricien-360** — abandonné confirmé.
