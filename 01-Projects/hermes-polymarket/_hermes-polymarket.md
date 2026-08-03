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
**Au 2026-07-30** *(ssh lecture seule, verdict Atlas relu — aucune écriture VPS ; log session 30/07)*
- **Verdict Atlas (Bot 2)** — le one-shot de fin de fenêtre A/B paper (~14 j) a bien tiré le **2026-07-17 18:30 UTC** (`atlas-validation-review`, exit 0, poussé Telegram). Chiffres « à arbitrer », le cron ne juge pas :
  - wallet `0xd1acd392…08d5` : 4 clôturées, winrate 25%, **total +$8.66** — échantillon trop mince pour conclure ;
  - wallet `0x44886115…e319` : 53 clôturées, winrate 43%, **total −$23.53** — le miroir actif perd (pertes > gains). Combiné ≈ **−$15** sur bankroll notionnel $50.
  - Arbitrage keep/adjust/stop = décision d'Hector en conversation. **Observé au 30/07 : Atlas toujours en paper** (timers `atlas`/`copy-tracker`/`atlas-report` actifs, DB fraîches) → keep-running de facto, aucune clé réelle.
- **Bot 1 (`polymarket_trader`)** — **pleinement opérationnel en paper** : chaîne snapshot→brain→dispatcher→executor→monitor firing à l'heure (dernier cycle ~11:3x UTC le 30/07), aucun timer Bot 1 en échec.
- **Gate Bot 3 (BTC-5m)** — **a bougé** : de « gaté, plan seul (06/07) » à un **collecteur record-only** en construction dans `~/bot3_btc5min` (repo git dédié, 2 commits : squelette record-only épinglé → dead-man's switch T1/T2 passés). Heartbeat minute **VERT** vers healthchecks.io (db 8 tables / 16 triggers, 31,5 G libres). **Toujours zéro trade** : la gate d'exécution reste fermée, seule la fondation collecte + surveillance tourne.
- *Nota : 3 unités `run-uXXXX` en échec dans `--failed` = transients d'un test de sandbox ads-studio, sans rapport avec Hermes.*

---

Au 2026-07-04 : **3 GO en attente** — config A/B Flipadelphia + Kickstand7 (25 $ chacun, remplace Sharkbets), fix de la persistance recency du finder, sanity-check des timestamps de verdicts API. Formule d'edge du Bot 1 corrigée (Σbid − 1). Bot 3 toujours gaté.

## Next actions
- [x] ~~Arbitrage Atlas keep / adjust / stop~~ — **TRANCHÉ le 2026-08-03 : KEEP.** Voir Décisions. La boucle est fermée ; elle ne doit plus être comptée « sans réponse ».
- [ ] **Réexamen d'Atlas au 2026-09-30** (post-rentrée) — point de revue posé avec la décision de keep
- [ ] **Bot 3** : la gate d'exécution reste fermée — collecteur record-only, zéro trade

## Décisions
- **2026-08-03 — ATLAS : KEEP DÉCIDÉ.** Décision d'Hector, **pas un report** : la boucle ouverte depuis le verdict du 17/07 est fermée. **Aucune unité n'est arrêtée** — `atlas`, `copy-tracker`, `atlas-report` et le one-shot `atlas-validation-review` restent `enabled`/`active`, la collecte continue d'accumuler de la matière. Les trois granularités de pause préparées le 03/08 ([[01-Projects/hermes-polymarket/mise-en-pause-atlas-procedure|mise-en-pause-atlas-procedure]]) **restent non exécutées**.
  **Motif : coût nul.** Aucune clé privée n'existe sur le serveur (vérifié le 03/08), tout est en `paper` : l'exposition financière est nulle quoi qu'il arrive. *(Précision au motif énoncé : il reste **10 positions ouvertes pour 25,00 USDC en paper**, la plus ancienne depuis le 11/07 — et non zéro. Le fait est corrigé, la conclusion tient : en paper et sans clé, 10 positions ouvertes ne coûtent pas davantage que zéro.)*
  **Ce keep n'est PAS une réhabilitation.** La thèse du copy-trading reste **invalidée par le verdict du 17/07** ; c'est un maintien sans enjeu, pas un feu vert. Point de revue : **réexamen au 2026-09-30**, post-rentrée.
- 2026-07-04 — **Formule d'edge du Bot 1 corrigée** (Σbid − 1).
- 2026-07-17 — **La fenêtre A/B v2 est close par un one-shot programmé d'avance** (`atlas-validation-review`) qui produit des chiffres et ne juge pas : l'arbitrage reste humain.
- 2026-07-30 — **Bot 3 passe à un collecteur record-only** : repo git dédié, heartbeat minute vers healthchecks.io, gate d'exécution fermée, **zéro trade**.
- 2026-08-02 — **Une sonde externe en lecture seule** (`hermes-collector`, [[_dream-console]]) lit Hermes sans jamais y écrire ; elle a relevé le gel de `state.db` depuis le 13/07 et le défaut de finalisation du 14/07.

## Ressources liées
- [[01-Projects/hermes-polymarket/mise-en-pause-atlas-procedure|mise-en-pause-atlas-procedure]] — préparée le 03/08, **non exécutée** : Hector arbitre A/B/C
- [[01-Projects/hermes-polymarket/fiche-technique|fiche-technique]] — bots, cadences systemd, cockpit, pointeurs VPS
- [[01-Projects/hermes-polymarket/decisions-et-lecons|decisions-et-lecons]] — décisions d'architecture et leçons durcies
- **Satellite** : Trading-Bots-Vault — vault Obsidian séparé des bots (`~/Downloads/Trading-Bots-Vault` sur le Mac ; décision du 03/07 : import éventuel en Phase 3)

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-12
- Import VPS via [[documenter-un-projet]] (ssh lecture seule, user veillebot — aucun fichier modifié sur le serveur). À noter : la revue de fin de validation paper d'Atlas (fenêtre A/B v2) est programmée par timer one-shot le **2026-07-17 18:30 UTC**. Log : `AI/logs/2026-07-12-session.md` (session 3).

### 2026-07-30
- Refresh post-verdict Atlas via ssh **lecture seule** (user veillebot ; aucune écriture, aucun `.env` lu). Lu : unité + journal du one-shot `atlas-validation-review` (verdict A/B du 17/07), `systemctl --user list-timers`, `git log ~/bot3_btc5min`. Statut mis à jour (instantané « Au 2026-07-30 ») ; leçon « winrate ≠ edge » ajoutée à [[decisions-et-lecons]]. Log : `AI/logs/2026-07-30-session.md`.
