---
type: resource
tags: [ia, dev, business]
cree: 2026-07-10
source: https://github.com/lixyra05-debug/legitvision
---

# LegitVision — fiche technique

> Distillée le 2026-07-10 depuis le repo local (lecture seule) : CLAUDE.md du repo, structure, package.json, migrations, git log (110 commits), rapport QA. Connaissance + pointeurs uniquement — le code reste dans le repo (critère evergreen).

## Stack
- **Next.js 14.2** (App Router, TypeScript strict, Server Components), Tailwind CSS, composants UI maison (@base-ui/react — deps shadcn retirées), pnpm.
- **Supabase** : Auth (email + OAuth Google), PostgreSQL avec RLS sur toutes les tables, Storage (bucket `analysis-photos`).
- **Stripe** : achat unique + abonnements Pro/Business, système de crédits, webhook idempotent (table `stripe_events`), compte LYXIRIA en LIVE.
- **IA** : Claude Vision API (`claude-opus-4-8`, thinking adaptive) via `@anthropic-ai/sdk`, prompts spécialisés par marque.
- **Autour** : Upstash Redis (rate-limit), Zod (validation), Resend (emails de confirmation de paiement), heic2any/sharp (photos), Playwright (E2E), Vercel (`/api/analyze` à 60 s max).

## Architecture (en bref)
App Router en trois groupes de routes : `(public)` (landing + ~150 pages SEO programmatiques), `(auth)`, `(dashboard)` (analyse en stepper : catégorie → marque/modèle → upload guidé 8-12 photos selon protocole par marque).
La route `/api/analyze` orchestre tout : vérif crédits → fetch photos Storage → payload Claude Vision → parse JSON + scoring pondéré (`lib/ai/scoring.ts`) → sauvegarde → rapport `/check/[id]`.
6 tables cœur (`profiles`, `brands`, `models`, `analyses`, `analysis_photos`, `credits_transactions`) construites par 17 migrations SQL ; le webhook Stripe crédite les comptes.
Un dossier `remotion/` (vidéos) vit dans le repo, plus un repo séparé `~/legitvision-ads` (créas Remotion — à rapprocher de [[_ads-studio]]).

## Features principales
- Analyse d'authenticité par photos : score pondéré 0-100 + sous-scores + findings, vocabulaire prudent (« estimation », jamais « certifié »), flag expert review si score 40-60 ou confiance basse, skip du débit si photos insuffisantes.
- Catalogue 47 marques / 342+ modèles — sneakers, sacs, vêtements (montres volontairement écartée) — avec variantes, collabs et points d'authentification par modèle (JSONB).
- Monétisation stricte : zéro crédit par défaut, paywall dès l'entrée du flow, upgrade Pro↔Business sans double facturation.
- SEO programmatique : ~150 pages générées depuis les données marques/modèles (60 « acheter-authentique », 87 « legit check », guides signal×marque), JSON-LD, images OG dynamiques.
- i18n FR/EN intégral, chatbot support, page résiliation, pages légales.

## État de prod
- **En production, tiers payants actifs** — dernier commit du repo : 2026-06-24 (SEO P2/P3).
- QA Production Readiness **94/100** (2026-05-07, audit multi-agents) ; audit du 2026-06-21 : les 3 P0 (webhook Stripe absent, modèle Vision retiré, faille RLS `profiles`) **résolus et vérifiés en prod**, 0 compte compromis.
- Pas encore : admin panel, HITL expert review. (Le CLAUDE.md du repo liste aussi les emails transactionnels comme stub, mais la confirmation de paiement via Resend a été branchée depuis — commit `cd28a3f`.)
- Dernier cycle (juin) : durcissement sécurité (rate-limit, validation photos côté serveur, Zod, sanitization des erreurs) + optimisation SEO.

## Pointeurs
- **Prod** : https://legitvision.vercel.app (projet Vercel `legitvision`)
- **Repo local** : `/Users/volanthector/legitvision` (`pnpm dev` / `pnpm build`)
- **GitHub** : https://github.com/lixyra05-debug/legitvision
- **Dans le repo** : `CLAUDE.md` (la vraie doc — schéma DB, règles critiques, flux, commandes), `QA_LEGITVISION_REPORT.md`, `PRD-SEO-Programmatic.md`, `supabase/migrations/` (17 fichiers)
- **Annexe** : `/Users/volanthector/legitvision-ads` — créas vidéo Remotion (1 commit)

## Liens
- [[_legitvision]]
- [[01-Projects/legitvision/decisions-et-lecons|decisions-et-lecons]]
- [[documenter-un-projet]]
