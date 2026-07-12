---
type: project
statut: actif
tags: [trading, agents, automation]
cree: 2026-07-03
---

# Hermes Polymarket

## Objectif
Système de trading autonome sur Polymarket, 24/7 sur le VPS Hetzner : la décision de trade vit dans des **moteurs Python déterministes**, Hermes Agent n'est que le cockpit (monitoring/ops, lecture seule). Roadmap 3 bots — arbitrage, copy-trading (Atlas), BTC-5m (gaté) — en paper-first avec HITL Telegram ; passage en réel seulement après gates de validation.

## Statut
Au 2026-07-04 : **3 GO en attente** — config A/B Flipadelphia + Kickstand7 (25 $ chacun, remplace Sharkbets), fix de la persistance recency du finder, sanity-check des timestamps de verdicts API. Formule d'edge du Bot 1 corrigée (Σbid − 1). Bot 3 toujours gaté.

## Next actions
- [ ] 

## Décisions
- 

## Ressources liées
- [[01-Projects/hermes-polymarket/fiche-technique|fiche-technique]] — bots, cadences systemd, cockpit, pointeurs VPS
- [[01-Projects/hermes-polymarket/decisions-et-lecons|decisions-et-lecons]] — décisions d'architecture et leçons durcies
- **Satellite** : Trading-Bots-Vault — vault Obsidian séparé des bots (`~/Downloads/Trading-Bots-Vault` sur le Mac ; décision du 03/07 : import éventuel en Phase 3)

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-12
- Import VPS via [[documenter-un-projet]] (ssh lecture seule, user veillebot — aucun fichier modifié sur le serveur). À noter : la revue de fin de validation paper d'Atlas (fenêtre A/B v2) est programmée par timer one-shot le **2026-07-17 18:30 UTC**. Log : `AI/logs/2026-07-12-session.md` (session 3).
