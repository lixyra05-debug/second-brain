---
type: resource
tags: [trading, agents, automation]
cree: 2026-07-12
source: VPS openclaw (ssh, lecture seule 2026-07-12) — /home/veillebot/.hermes/skills
---

# Hermes Polymarket — fiche technique

> Distillée le 2026-07-12 par ssh en **lecture seule** (user `veillebot`, aucun fichier modifié sur le serveur) : unités systemd user, SKILL.md des trois skills, git log du repo skills, docs d'audit à la racine du home. Aucun `.env` ni secret lu.

## Stack
- **Python 3** en venv (commun à tous les timers : `notebooklm-veillebot/venv`), **systemd user** (20 timers au 02/08), SQLite (`state.db`, `kanban.db`), Telegram (gateway + alertes).
- **+ 1 unité SYSTÈME** depuis le 2026-08-02 : `hermes-collector.service`, `User=veillebot` — sonde **lecture seule** d'Hermes pour [[_dream-console]]. Voir ci-dessous.
- **Hermes Agent** (clone NousResearch) : gateway messaging en daemon + cockpit ; les bots sont des **skills** dans `~/.hermes/skills/` (repo git dédié, 3 commits).
- **Bot 1 — `polymarket_trader`** : arbitrage d'incohérences logiques, signaux LLM, risk manager Python pur, validation HITL Telegram, mode paper (SKILL.md v0.1.0).
- **Bot 2 — `copy_trader` (Atlas)** : Smart Trader Finder — scrape du leaderboard via Apify, shortlist de wallets « smart money », **observation read-only, aucune clé privée** (SKILL.md v0.1.0).
- **Cockpit — `hermes-cockpit`** : brief matinal + détecteur de gel, « strictement lecture seule » (SKILL.md v1.0.0).

## Architecture (les cadences font l'orchestration)
**Bot 1** — chaîne séquencée par offsets de timers : snapshot `:00/5` (capture marchés + détection Tier-1) → brain `:01/5` (`inconsistency_detector` : signaux + shield) → dispatcher (chaque minute : signal PASS → alerte Telegram HITL) → listener (daemon : callbacks OUI/NON) → paper executor `:0/2` (ouvre les positions sur OUI) → position monitor `:3/5` (clôtures stop/take/time/résolution) → rapport quotidien 19h Paris + alerte STOP.
**Bot 2 (Atlas)** — copy-tracker `:00/15` (observation des wallets shortlist) → atlas `:05/15` (cycle entrées+sorties **paper**, 5 min après le tracker pour des données fraîches) → rapport paper 18h → **revue de fin de validation one-shot le 2026-07-17 18:30 UTC** (fenêtre A/B v2, timer dédié).
**Cockpit** — morning brief 07h Paris (Bot 1 + Atlas → Telegram), freeze-detector toutes les 30 min (dead-man's switch du pipeline), hermes-gateway en daemon.
**Bot 3 (BTC-5m)** — gaté : plan de 30 Ko posé le 06/07 (`~/veillebot/bot3_btc5min_plan_2026-07-06.md`), rien de déployé.

## Features principales
- Paper-first intégral : aucune clé de trading sur le serveur à ce stade ; Atlas est en pure observation.
- HITL Telegram : seuls les signaux PASS remontent en alerte, seul un OUI humain ouvre une position (paper).
- Garde-fous : shield dans le brain, clôtures automatiques (stop/take/time/résolution), alerte STOP dans le rapport quotidien.
- Supervision : brief matinal 07h, freeze-detector 30 min, rapports quotidiens 18h (Atlas) et 19h (Bot 1).
- Culture d'audit : rapports markdown datés à la racine du home VPS (`audit_bots_2026-06-25.md`, `bot1_terrain_2026-06-25.md`, `atlas_candidates_refresh_2026-07-01.md`, `RAPPORT_HERMES_COMPLET.md`, diagnostic deadlock…).

## Sonde externe — `hermes-collector` (2026-08-02)

Ajoutée par [[_dream-console]] : lit `state.db` (métadonnées seules), `~/.hermes/logs/`, les bases d'Atlas et l'état des timers ; expose un flux SSE **GET uniquement** sur `127.0.0.1:8787`, joint depuis le Mac par tunnel SSH. **Elle n'écrit rien et ne touche à aucun service Hermes.**

Elle est en unité **système** et non `systemctl --user` : sur cet hôte, une unité `--user` **n'applique ni `ProtectHome` ni `IPAddressDeny`** (`kernel.apparmor_restrict_unprivileged_userns=1`) — vérifié par test, cf. [[01-Projects/dream-console/decisions-et-lecons|dream-console — decisions-et-lecons]].

Deux constats faits à l'occasion, en lecture seule :
- **`state.db` est gelée depuis le 2026-07-13 20:16** — non-usage réel : 0 message entrant dans `gateway.log`, 0 erreur SQLite, base tenue ouverte en écriture par le gateway.
- **Divergence de finalisation** : le 14/07 le gateway a journalisé « Session expiry done: 1 finalized » et écrit `sessions.json`, mais `ended_at` est resté `NULL` en base — 7 h 47 d'écart. Capture dans `00-Inbox/`.

## État
- **2 bots en paper** (Bot 1 + Atlas), fenêtre de validation A/B v2 en cours — la revue est **programmée d'avance** pour le 17/07/2026 18:30 UTC.
- 3 GO en attente côté Hector (pivot au 04/07) ; Bot 3 toujours gaté (plan écrit le 06/07).
- Repo skills : dernier commit 2026-06-17 ; activité opérationnelle continue (DB et sessions modifiées le 12/07).

## Pointeurs
- **VPS** : `root@178.104.159.57` puis `su - veillebot` — code : `~/.hermes/skills/{polymarket_trader,copy_trader,hermes-cockpit}` (chacun avec README, docs/, tests/, config/)
- **Unités** : `~/.config/systemd/user/*.{timer,service}` (20 timers au 02/08 + services de test)
- **Sonde externe** (2026-08-02) : `/etc/systemd/system/hermes-collector.service` — unité **système** avec `User=veillebot`, code dans `~/hermes-collector/`, source versionnée dans `~/dream-viewer/collector/`
- **Audits** : `~/*.md` sur le VPS (racine du home veillebot)
- **Satellite** : Trading-Bots-Vault — vault Obsidian séparé, `~/Downloads/Trading-Bots-Vault` sur le Mac (Bot1-Hermes / Bot2-Atlas / Bot3-BTC5m / Infra)
- **Incident Curator et règles agents autonomes** : résumés dans [[contexte-hector]] — la référence, non dupliquée ici.

## Liens
- [[_hermes-polymarket]]
- [[01-Projects/hermes-polymarket/decisions-et-lecons|decisions-et-lecons]]
- [[_dream-console]] — le cockpit qui consomme la sonde
- [[documenter-un-projet]]
