---
type: project
statut: actif
tags: [ia, agents, alternance, automation]
cree: 2026-07-03
---

# Agent Alternance

## Objectif
Décrocher une **alternance de 12 mois** en agents IA / automatisation pour la rentrée Ynov de septembre 2026, via un pipeline semi-automatisé : scraping des jobboards, scoring des offres par Claude, envoi des candidatures **validé à la main** — HITL obligatoire, aucun envoi automatique.

## Statut
**Au 2026-07-31** *(ssh lecture seule, user veillebot — aucune écriture, aucun `.env` lu ; log session 31/07)*
- **Le pipeline tourne, sans une seule panne.** `alternance-scrape` (quotidien 05:15 UTC = 07:15 Paris) et `alternance-brief` (05:45 UTC = 07:45 Paris) ont fini en `status=0/SUCCESS` le 30/07 ; `alternance-bilan` a tiré le dimanche 26/07, `perplexity-fetch` le lundi 27/07. Aucune unité alternance en échec.
- **59 offres dans `offres.json`** après le run du 31/07 05:15 UTC (« +1 nouvelles, 0 rafraîchies, 59 au total »), contre 19 annoncées au 04/07. Sources : France Travail 33, HelloWork 11, WTTJ 4, site entreprise 4, Grimp 3, autre 2, LinkedIn 1.
- **46 de ces 59 sont au statut `nouvelle` — jamais scorées.** Seules 13 portent un score (9 `analysee`, 3 `envoyee`, 1 `refus`). *(Divergence non résolue : l'instantané du 04/07 annonçait 19 offres « ingérées et scorées ».)*
- **Le scoring n'est pas en panne — il n'a jamais été automatisé, par design.** Diagnostic du 31/07 : aucune unité systemd de scoring (les 4 unités alternance sont `scrape`, `brief`, `bilan`, `perplexity-fetch`), aucun script de scoring dans `scripts/`, et **toutes les occurrences de `score` dans le code sont en lecture** — `report.py` l'affiche, `common.py` le préserve au merge. Le seul écrivain de `statut` est `common.py:106` → `offer["statut"] = "nouvelle"` : rien ne fait jamais avancer une offre d'un cran. `alternance_setup.md` l'assume dans une section « **Volontairement HORS de cette livraison** » ; le service du brief s'appelle « script-only, **0 token** ». Le scoring était une **étape manuelle en session Claude** (artefacts dans `claude_out/`), **arrêtée le 09/07**. Le cycle `nouvelle → analysee → …` de `schema_offre.md` est spécifié mais non implémenté.
- **Effet de bord : le brief quotidien est aveugle aux nouvelles offres.** `report.py` sélectionne les offres actionnables avec `statut in ("nouvelle","analysee") and classement == "top"`, or `classement` n'est posé que par l'étape manuelle. Aucune offre entrée depuis le 09/07 ne peut apparaître dans la section « top » du brief de 07h45 : il tourne, il est vert, et il ne peut recycler que les 13 anciennes.
- **Candidatures : toujours les 4 du 02/07**, relancées le 09/07, dont **1 refus** enregistré. Aucune nouvelle lettre dans `candidatures/` depuis le 03/07. Scores des 4 envoyées : 91, 86, 80, 68.
- **Requêtes France Travail — pas de panne non plus.** Historique sur 19 jours (13 → 31/07) : `développeur` 14 jours avec résultats / 39 offres · `intelligence artificielle` **13 / 22** · `automatisation` **2 / 2**. Les `HTTP 204` sont le régime normal de `publieeDepuis=1` (chaque run ne demande que les offres publiées dans les dernières 24 h, filtrées `natureContrat=E2,FS` sur Paris/IDF) — le dimanche 19/07 et le lundi 27/07, les **trois** requêtes rentrent vides. Seule vraie anomalie : **`automatisation`, 2 offres en 19 jours**, un mot-clé stérile qui consomme un tiers du budget de requêtes (~3 par run).

---

Au 2026-07-04 : 19 offres ingérées et scorées, **4 candidatures envoyées** — Vista IA (91/100), Docaposte (86), Nextories (80), Citeo (68, à finaliser sur leur site). Lead Mediawan en attente ; **relance de Muriel Caillet (Ynov) prioritaire**. CV et LM v3 (posture apprentissage) validés.

## Next actions
- [ ] (à remplir)

## Décisions
- (à remplir)

## Ressources liées
- [[01-Projects/agent-alternance/fiche-technique|fiche-technique]] — pipeline serveur (France Travail, Perplexity, brief 7h45, bilan dimanche), pointeurs VPS
- [[01-Projects/agent-alternance/decisions-et-lecons|decisions-et-lecons]] — décisions et leçons du déploiement serveur

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-12
- Import VPS via [[documenter-un-projet]] (ssh lecture seule — aucun fichier modifié sur le serveur). Pipeline vivant : offres rafraîchies le matin même (scrape France Travail 07h15). Log : `AI/logs/2026-07-12-session.md` (session 3).

### 2026-07-31
- Reconnaissance VPS **lecture seule** (arbitrage du rêve du 31/07) : `offres.json` / `offres.csv`, `candidatures/`, `claude_out/`, statut des services `alternance-scrape`, `alternance-brief` et `alternance-bilan`. Statut mis à jour (instantané « Au 2026-07-31 ») ; celui du 04/07 conservé au-dessous. Aucune écriture sur le serveur, aucun `.env` lu. Log : `AI/logs/2026-07-31-session.md`.
