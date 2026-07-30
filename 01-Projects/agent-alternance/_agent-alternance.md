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
- **58 offres dans `offres.json`** (CSV : 58 lignes) contre 19 annoncées au 04/07. Dernier merge du 30/07 : « +1 nouvelles, 3 rafraîchies, 58 au total ». Sources : France Travail 33, HelloWork 11, WTTJ 4, site entreprise 4, Grimp 3, autre 2, LinkedIn 1.
- **Mais 45 de ces 58 sont au statut `nouvelle` — jamais scorées.** Seules 13 offres portent un score (9 `analysee`, 3 `envoyee`, 1 `refus`). Le scoring est l'étape qui demande Claude, et `claude_out/` n'a rien produit depuis le **09/07** : la collecte avance toute seule, **l'analyse est à l'arrêt depuis 22 jours**. *(Divergence non résolue : l'instantané du 04/07 annonçait 19 offres « ingérées et scorées », 13 portent un score aujourd'hui.)*
- **Candidatures : toujours les 4 du 02/07**, relancées le 09/07, dont **1 refus** enregistré. Aucune nouvelle lettre dans `candidatures/` depuis le 03/07. Scores des 4 envoyées : 91, 86, 80, 68.
- **Signal à surveiller** — au scrape du 30/07, 2 des 3 requêtes France Travail rentrent vides : `intelligence artificielle` HTTP 204 · 0 résultat, `automatisation` HTTP 204 · 0 résultat ; seule `développeur` ramène 5 offres. L'entonnoir se réduit de fait à un seul axe de recherche.

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
