---
type: project
statut: actif
tags: [sante, dev, business]
cree: 2026-07-03
---

# Vivo

## Objectif
App mobile (React Native / Expo) de scan nutrition + cosmétique : barcode + OCR d'étiquettes via Claude Vision, scoring multi-critères (additifs, NOVA, packaging, naturalité — sources EFSA/EMA/ANSES/CIRC), traçage de propriété des marques via Wikidata, 40 plantes médicinales, mode famille, deux tiers Premium/Expert.

## Statut
Au 2026-07-12 : **feature-complete, RevenueCat fait** (achats Premium/Expert + restore, `d4bc7d3` du 10/07). **Bloqueur restant : compte Apple Developer** → EAS/TestFlight → screenshots → soumission.

## Next actions
- [x] ~~Compte Apple Developer → EAS/TestFlight → screenshots → soumission~~ — **reporté sans date le 2026-08-03**, décision assumée. Voir Décisions. L'app reste feature-complete à 700+ tests, non publiée.

## Décisions
- **2026-08-03 — Compte Apple Developer : REPORTÉ SANS DATE.** Décision assumée d'Hector : l'app reste **feature-complete et non publiée**. Le pivot garde `statut: actif` — « en cours de publication » décrit toujours l'intention, pas un chantier en cours. **À traiter comme ÉCARTÉE, pas comme « sans réponse »** : c'est un choix explicite, au même titre que la `pause` de [[_remedes-naturels]], et une date qui vieillit n'en fait pas une dérive.
- 2026-07-10 — **RevenueCat intégré** (`d4bc7d3`) : achats Premium/Expert et restore.
- 2026-07-31 — **« livrée » retiré** : Vivo n'est pas publiée, elle est **en cours de publication**. Correction actée par Hector, propagée à [[contexte-hector]] et [[inventaire-projets]].

## Ressources liées
- [[01-Projects/vivo/fiche-technique|fiche-technique]] — stack, architecture, features, état (feature-complete, 723 tests verts), pointeurs
- [[01-Projects/vivo/decisions-et-lecons|decisions-et-lecons]] — décisions produit/tech et leçons durcies du projet

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-10
- Import via [[documenter-un-projet]] : fiche technique et décisions/leçons distillées du repo local `~/projects/vivo` (lecture seule, aucun code copié). Log : `AI/logs/2026-07-10-session.md` (session 4).

### 2026-07-12
- Statut rafraîchi sur ordre d'Hector : RevenueCat intégré le 10/07 (`d4bc7d3`, détecté par l'inventaire) ; fiche technique mise à jour. Log : `AI/logs/2026-07-12-session.md` (lot C).
