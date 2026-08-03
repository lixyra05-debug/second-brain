---
type: permanent
tags: [idee, agents]
cree: 2026-08-02
---

# Hermes finalise une session sans l'écrire en base

## Développement

Le 2026-07-14 à 04:03, le gateway Hermes journalise `Session expiry: 1 sessions to finalize` puis `Session expiry done: 1 finalized`, et écrit bien `~/.hermes/sessions/sessions.json` — mais la session `20260713_201329_79e4c9c3` porte toujours `ended_at = NULL` dans `state.db`, dont le `-wal` n'a plus bougé depuis la veille 20:16. **Un `exit 0` pour une écriture faite à moitié : les deux magasins divergent de 7 h 47, et rien ne le signale.**

Conséquence pratique pour tout lecteur de cette base : une session ne peut jamais être dite « active » sur le seul `ended_at IS NULL` — celle-ci le serait pour toujours, alors qu'elle est close depuis 19 jours.

## Origine

Relevé le 2026-08-02 en lecture seule, pendant le branchement du collecteur Hermes sur DREAM CONSOLE (`~/dream-viewer/collector/`). Le gel de `state.db` depuis le 13/07 s'est révélé être un **non-usage réel** — 0 message entrant au log, 0 erreur SQLite, base tenue ouverte en écriture par le gateway — mais la vérification a fait tomber ce défaut-là à côté.

## Liens

- [[_hermes-polymarket]]
- [[01-Projects/hermes-polymarket/decisions-et-lecons|hermes — decisions-et-lecons]]
- [[contexte-hector]] — « dead-man's switch externe : zéro panne *silencieuse*, pas zéro panne »
- Matière pour la graine de wiki **« La panne silencieuse — quand le vert ne veut rien dire »** ([[2026-07-31-dream]], rêve 2) : c'est la **quatrième** occurrence du même motif, sur un quatrième projet — après le brief alternance vert et vide, la double panne infra d'autonomous, et le job pendu de `/dream`.
