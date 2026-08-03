---
type: resource
tags: [trading, agents, automation]
cree: 2026-07-12
source: VPS openclaw (ssh, lecture seule 2026-07-12) — /home/veillebot/.hermes/skills
---

# Hermes Polymarket — décisions et leçons

> Extraites le 2026-07-12 des unités systemd, des SKILL.md, du git log du repo skills et des docs d'audit du VPS. L'incident Curator (18-19/06) et ses leçons durcies sont **déjà dans [[contexte-hector]]** — pointés, pas dupliqués.

## Décisions d'architecture
- **Les moteurs déterministes décident, le LLM est un cockpit lecture seule** — la règle fondatrice du système (post-Curator, cf. [[contexte-hector]] § agents autonomes). Les trois SKILL.md la répètent : risk manager « Python-pure », Atlas « read-only », cockpit « strictement lecture seule ».
- **Le git comme rempart** : premier commit du repo skills = « baseline snapshot des skills Hermes (**protection anti-réécriture Curator**) » (`9bc45da`) — versionner pour détecter et annuler toute réécriture par un agent.
- **Les offsets de timers encodent les dépendances** (snapshot `:00` → brain `:01` → monitor `:03` ; tracker `:00` → atlas `:05`) : pas d'orchestrateur, le temps fait le séquencement — simple, inspectable, sans framework.
- **HITL en amont, pas en veto a posteriori** : le dispatcher n'alerte que les PASS, l'executor n'ouvre que sur un OUI Telegram explicite.
- **Fin d'expérience datée d'avance** : un timer one-shot (17/07 18:30 UTC) force la revue de fin de validation paper — pas de paper qui s'éternise « en attendant de voir ».
- **Observation sans clé** : la Brique 1 d'Atlas scrape le leaderboard (Apify) et observe les wallets — aucune clé privée sur le serveur tant que le paper n'est pas validé.

## Leçons durcies
- **Zéro panne silencieuse** : freeze-detector toutes les 30 min sur la santé Bot 1 + Atlas — le dead-man's switch externe est une pièce du système, pas une option (même famille que UptimeRobot chez [[01-Projects/autonomous/decisions-et-lecons|autonomous]]).
- **Auditer à date fixe, par écrit** : `audit_bots`, `bot1_terrain`, `atlas_source_ranking`, diagnostic de deadlock — les problèmes (deadlock Bot 1, qualité des sources Atlas) ont été trouvés par des audits datés en markdown, la même culture que LegitVision/Vivo.
- **Un venv partagé pour tous les timers** (`notebooklm-veillebot/venv`) : un seul environnement à maintenir — et un seul point de casse ; à garder en tête au premier conflit de dépendances.
- **La formule d'edge se corrige par écrit** (pivot du 04/07 : Σbid − 1) : les corrections de logique de trading passent par le pivot et les audits, jamais silencieusement.
- **Winrate n'est pas edge** (verdict Atlas, 17/07 — relu en ssh lecture seule le 30/07) : la fenêtre A/B paper ~14 j s'est close **net négative sur le miroir actif** — `0x44886115…` à 43 % de winrate mais **−$23.53** sur 53 clôtures (les pertes pèsent plus que les gains), tandis que `0xd1acd392…` sortait à +$8.66 sur 4 clôtures seulement (échantillon trop mince pour conclure). Copier des wallets « smart money » ne crée pas d'edge en soi : sans filtre de sélection ni sizing propres, l'asymétrie de payoff mange le taux de réussite. Conforme au design — le one-shot a envoyé les chiffres « à arbitrer », il n'a pas jugé (« un cron ne juge pas », cf. décision *Fin d'expérience datée d'avance*).

## Liens
- [[_hermes-polymarket]]
- [[01-Projects/hermes-polymarket/Hermes finalise une session sans l'écrire en base|Hermes finalise une session sans l'écrire en base]] — défaut de finalisation relevé le 2026-08-02 : `sessions.json` écrit, `ended_at` jamais posé en base, 7 h 47 d'écart entre les deux magasins
- [[01-Projects/hermes-polymarket/fiche-technique|fiche-technique]]
- [[contexte-hector]] — incident Curator + règles agents autonomes (la référence)
