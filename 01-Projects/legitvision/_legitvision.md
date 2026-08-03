---
type: project
statut: actif
tags: [ia, business, dev]
cree: 2026-07-03
---

# LegitVision

## Objectif
SaaS d'authentification d'articles de luxe par vision : Claude Vision API analyse les photos et rend un verdict, couverture 47 marques / 342+ modèles. Stack Next.js 14 / Supabase / Stripe / Vercel.

## Statut
Au 2026-07-04 : **en production**, tiers payants actifs — legitvision.vercel.app.

## Next actions
- [ ] **Revérifier l'instantané du 2026-07-04** — jamais revérifié depuis sa validation. Signal indirect du 30/07 : ads-studio a produit un `brand/brand-brain.md` LegitVision et 11 vidéos sur ce produit, donc la marque bouge pendant que son pivot l'ignore.

## Décisions
- 2026-07-10 — **Import pilote via [[documenter-un-projet]]** : fiche technique + décisions/leçons distillées du repo local en lecture seule, **pointeurs plutôt que copies**. C'est ce pilote qui a validé le runbook.
- 2026-07-31 — **« livré » n'est pas un statut** (AGENTS.md §4) : LegitVision reste `actif`, en production avec des tiers payants. Correction actée par Hector, propagée à [[inventaire-projets]] et [[contexte-hector]].

## Ressources liées
- [[01-Projects/legitvision/fiche-technique|fiche-technique]] — stack, architecture, features, état de prod, pointeurs (repo, GitHub, prod)
- [[01-Projects/legitvision/decisions-et-lecons|decisions-et-lecons]] — décisions produit/tech et leçons durcies du projet

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-10
- Import pilote : [[01-Projects/legitvision/fiche-technique|fiche-technique]] et [[01-Projects/legitvision/decisions-et-lecons|decisions-et-lecons]] distillées du repo local `~/legitvision` (lecture seule, aucun code copié). Log : `AI/logs/2026-07-10-session.md` (session 3).
