---
type: resource
tags: [ia, alternance, automation]
cree: 2026-07-12
source: VPS openclaw (ssh, lecture seule 2026-07-12) — /home/veillebot/alternance
---

# Agent Alternance — décisions et leçons (côté serveur)

> Extraites le 2026-07-12 du doc de déploiement `alternance_setup.md` (v1.0, 03/07) et des unités systemd. 1 à 3 lignes chacune.

## Décisions
- **Crons script-only : 0 token, 0 LLM, 0 décision autonome** — la leçon Curator appliquée noir sur blanc dès la première ligne du doc de déploiement ; le brief matinal est un script Python déterministe.
- **HITL absolu** : Hermes ne peut qu'exécuter `report.py` (lecture) ; l'envoi de candidature reste 100 % humain — jamais d'envoi automatique.
- **La data vit hors de l'arbre de l'agent** (`~/alternance/` vs `~/.hermes/skills/`) : l'agent lit, il ne possède pas — même principe que le code de prod hors de l'arbre modifiable ([[contexte-hector]]).
- **Le VPS fait foi dès le déploiement** : `offres.json` serveur = source de vérité, le Mac n'est plus l'original.
- **L'ordre des timers encode le pipeline** : Perplexity 07h00 (lundi) → France Travail 07h15 → brief 07h45 — les dépendances sont dans les horaires, pas dans un orchestrateur.
- **Dead-man's switch dès la conception** : ping `HEARTBEAT_URL` après chaque brief réussi — même famille que le freeze-detector des bots et UptimeRobot chez autonomous.

## Leçons
- **`flock` partagé entre collecteurs** (FT + Perplexity, timeout 600 s sur `.offres.lock`) : deux crons qui écrivent le même fichier finissent par se marcher dessus — sérialiser d'entrée.
- **Backups datés avant chaque étape risquée** : `offres.json.bak`, `-20260709`, `-20260709-perplexity` — l'ajout d'une nouvelle source de collecte s'est fait avec filet.
- **Un canal d'entrée manuel reste nécessaire** : `inbox/` avec collages `=== OFFRE ===` — tout ne vient pas des APIs, le pipeline accepte l'humain comme source.

## Liens
- [[_agent-alternance]]
- [[01-Projects/agent-alternance/fiche-technique|fiche-technique]]
