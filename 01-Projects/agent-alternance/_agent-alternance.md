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
Au 2026-07-04 : 19 offres ingérées et scorées, **4 candidatures envoyées** — Vista IA (91/100), Docaposte (86), Nextories (80), Citeo (68, à finaliser sur leur site). Lead Mediawan en attente ; **relance de Muriel Caillet (Ynov) prioritaire**. CV et LM v3 (posture apprentissage) validés.

## Next actions
- [ ] 

## Décisions
- 

## Ressources liées
- [[01-Projects/agent-alternance/fiche-technique|fiche-technique]] — pipeline serveur (France Travail, Perplexity, brief 7h45, bilan dimanche), pointeurs VPS
- [[01-Projects/agent-alternance/decisions-et-lecons|decisions-et-lecons]] — décisions et leçons du déploiement serveur

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-12
- Import VPS via [[documenter-un-projet]] (ssh lecture seule — aucun fichier modifié sur le serveur). Pipeline vivant : offres rafraîchies le matin même (scrape France Travail 07h15). Log : `AI/logs/2026-07-12-session.md` (session 3).
