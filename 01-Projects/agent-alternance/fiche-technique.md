---
type: resource
tags: [ia, alternance, automation]
cree: 2026-07-12
source: VPS openclaw (ssh, lecture seule 2026-07-12) — /home/veillebot/alternance
---

# Agent Alternance — fiche technique (côté serveur)

> Distillée le 2026-07-12 par ssh en **lecture seule** : unités systemd, `alternance_setup.md` (doc de déploiement v1.0 du 03/07), structure de `~/alternance/`. Le `.env` (secrets Telegram) n'a pas été lu.

## Stack
- **Python scripts-only** — aucun LLM dans les crons : `fetch_francetravail.py` (API France Travail), `fetch_perplexity.py` (Perplexity Sonar, 1 appel/semaine), `brief_matin.py` (0 token), `report.py` + `notify.py` (Telegram).
- **systemd user timers** (4) + `flock` sur `.offres.lock` (timeout 600 s) pour sérialiser les écritures.
- **Data plate** dans `~/alternance/` : `offres.json` (source de vérité) + `offres.csv`, backups datés, `profil.json`/`profil.md`, `schema_offre.md`, `plan_scraping.md`.
- Hermes en cockpit lecture seule (skill `alternance-ops` : exécution de `report.py` uniquement) ; scoring Claude hors crons (sorties dans `claude_out/`).

## Architecture (une journée du pipeline)
- **07h00 lundi** : Perplexity Sonar collecte les offres hebdo — volontairement **avant** France Travail.
- **07h15 chaque jour** : scrape API France Travail (`--depuis 1`), sous flock.
- **07h45 chaque jour** : brief matinal Telegram — script déterministe, **0 token, 0 LLM, 0 décision autonome** (la leçon Curator, écrite dans le doc de déploiement).
- **18h00 dimanche** : bilan hebdo (`report.py bilan | notify.py`).
- Flux humain : `inbox/` reçoit les collages manuels `=== OFFRE ===`, `candidatures/` contient les LM générées — **aucun envoi automatique, HITL absolu**.
- **Dead-man's switch** prévu dès la conception : ping `HEARTBEAT_URL` (healthchecks.io / UptimeRobot) après chaque brief réussi.

## Séparation des responsabilités (du doc de déploiement)
- La data vit **hors de l'arbre de l'agent** : tout dans `~/alternance/`, jamais dans `~/.hermes/skills/` — seul le SKILL.md (des instructions, pas de la prod) va chez Hermes.
- Dès le déploiement, **le VPS fait foi** : `offres.json` serveur est la source de vérité, plus le Mac.

## État
- **Pipeline vivant** : `offres.json` rafraîchi le matin même de cette fiche (12/07, 07h15 Paris) ; backups datés des étapes risquées (`-20260709`, `-perplexity`).
- Côté candidatures (pivot au 04/07) : 19 offres scorées, 4 envoyées, relance Muriel Caillet (Ynov) prioritaire.
- Pas de repo git : le dossier serveur est le déploiement, la trace vit dans les docs (`alternance_setup.md`, `plan_scraping.md`, `mission_diagnostic.md`).

## Pointeurs
- **VPS** : `root@178.104.159.57` puis `su - veillebot` — `~/alternance/` (data + scripts) ; timers dans `~/.config/systemd/user/alternance-*` et `perplexity-fetch.*`
- **Docs serveur** : `alternance_setup.md` (architecture et règles), `plan_scraping.md`, `schema_offre.md`

## Liens
- [[_agent-alternance]]
- [[01-Projects/agent-alternance/decisions-et-lecons|decisions-et-lecons]]
- [[documenter-un-projet]]
