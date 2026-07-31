---
type: wiki
tags: [dev]
cree: 2026-07-12
---

# Inventaire des projets Claude Code

> Recensement du 2026-07-12, lecture seule. Sources croisées : dossiers de sessions `~/.claude/projects/`, chemins projets de `~/.claude.json` (clés uniquement — aucun transcript lu), `find ~ -maxdepth 4 -name .git` (hors Library/node_modules/.Trash), [[contexte-hector]] et les 6 pivots. **Tri confirmé par Hector le 2026-07-12** — verdicts annotés dans les tableaux ; autonomous reclassé **actif**.
>
> **Correction du 2026-07-31** (arbitrage du rêve du 31/07) : « livré » n'est pas un statut. `legitvision` et `vivo` portent `statut: actif` en frontmatter et c'est correct — leurs lignes passent à « actif — en production » et « actif — en cours de publication ». La règle est désormais dans la constitution (§4) : quand une catégorie d'inventaire contredit le frontmatter d'un pivot, **c'est l'inventaire qui s'aligne**.

## Projets construits

| Projet | Chemin | Commits | Dernière activité | Catégorie proposée | Note |
|---|---|---|---|---|---|
| legitvision | `~/legitvision` | 110 | 2026-06-24 | **actif — en production** | SaaS en prod — documenté le 10/07 ✅ |
| vivo | `~/projects/vivo` | 34 | 2026-07-10 | **actif — en cours de publication** | feature-complete, RevenueCat intégré (`d4bc7d3`) — fiche + pivot rafraîchis le 12/07 ✅ |
| autonomous | `~/autonomous` | 49 | 2026-05-31 | **actif** (GO 12/07) | SaaS outbound **en prod** : autonomous.coldsend.app — pivot créé, documenté et ajouté à [[contexte-hector]] le 12/07 ✅ |
| remedes-naturels | `~/projects/remedes-naturels` | 9 | 2026-05-04 | **pause — produit livré, pas de travail actif** | ebooks via Stripe — documenté le 12/07 ✅ |
| second-brain | `~/Documents/second-brain` | 11 | 2026-07-10 | **actif** | ce vault — auto-documenté ([[_second-brain]]) |
| hermes-polymarket | VPS — `~veillebot/.hermes/skills` | 3 (repo skills) | 2026-06-17 (commit) · ops le 12/07 | **actif** | 2 bots paper + cockpit, 14 unités systemd ; revue validation Atlas le 17/07 — documenté le 12/07 ✅ |
| agent-alternance | VPS — `~veillebot/alternance` | pas de git | 2026-07-12 (offres du matin) | **actif** | pipeline script-only FT/Perplexity, brief 7h45, HITL absolu — documenté le 12/07 ✅ |
| ads-studio | VPS — `~veillebot/ads-studio` | 0 (git init **sans commit**) | 2026-06-24 (mtime) | **actif** | code trouvé sur le VPS le 12/07, pointé dans le pivot (pas de fiche, GO) ; ⚠️ jamais commité = zéro backup |
| molty-openclaw | user `openclaw` (VPS, vide) + `~/clawd` (Mac) | 0 | 2026-04-07 | **expérimental** (constat 12/07) | rien de déployé : user VPS vide (dotfiles d'avril), workspace identité local seulement ; la veille réelle = `veillebot-weekly` (Hermes, lundi 7h) |
| bot-automobile | **code introuvable** (Mac + VPS vérifiés 12/07) | — | — | **non localisé** | scraping Playwright + alertes Telegram ([[contexte-hector]]) — aucune trace trouvée ; à pointer par Hector s'il existe encore quelque part |
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
