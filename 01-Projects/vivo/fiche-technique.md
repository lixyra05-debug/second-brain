---
type: resource
tags: [sante, dev, ia]
cree: 2026-07-10
source: https://github.com/lixyra05-debug/vivo
---

# Vivo — fiche technique

> Distillée le 2026-07-10 depuis le repo local (lecture seule) : CLAUDE.md du repo (382 lignes, lu en entier), PRD, package.json/app.json/eas.json, migrations, git log (33 commits). **Mise à jour le 2026-07-12** : RevenueCat intégré (`d4bc7d3`). Connaissance + pointeurs uniquement (critère evergreen).

## Stack
- **Expo SDK 54 / React Native 0.81** (React 19, New Architecture), TypeScript strict, Expo Router (file-based), NativeWind + React Native Paper, Zustand + TanStack Query, expo-camera ; npm (installs via `npx expo install` uniquement).
- **Supabase** (West EU Irlande) : Auth, PostgreSQL RLS, Storage, Edge Function `analyze-ingredients` (OCR Claude Vision — la clé Anthropic reste côté serveur).
- **Données produits** : Open Food Facts v2 (`fr.openfoodfacts.org`) + Open Beauty Facts en cascade food→cosmétique, caches locaux ; Wikidata SPARQL pour la maison-mère des marques.
- **Monétisation & ops** : RevenueCat (IAP iOS/Android, **intégré** — achats Premium/Expert + restore, `d4bc7d3`) + Stripe web, Sentry (filtre PII strict), PostHog (reporté), EAS Build/Submit (dev/preview/production, autoIncrement).
- **Tests** : Jest + RNTL — **723 tests verts**, compteur suivi commit par commit, `npx expo export` avant chaque commit.

## Architecture (en bref)
Routing file-based dans `app/` (~25 groupes d'écrans), toute la logique dans `src/lib`.
Le cœur est un moteur de scoring **pur TypeScript** (`src/lib/scoring/engine.ts`) : score = 100 − pénalités toxicologiques, sans compensation ; autour : base de 57 additifs sourcés, classification NOVA, détection huiles de graines, NLP clean labeling, moteur de compatibilité (allergènes EU, FODMAP, 6 presets profils).
Un premium gate central (`premium-gate.ts`) gère **25 features sur 2 tiers** (10 Premium / 15 Expert), hiérarchie de tiers, table `subscriptions` comme source de vérité.
L'univers plantes (40 fiches, 30 recettes, 5 protocoles de 21 jours) est en **data statique embarquée** + AsyncStorage pour la progression — zéro serveur.
15 migrations SQL idempotentes appliquées à la main (workflow SQL Editor documenté dans le repo) ; gamification, stats avancées, export PDF et mode famille (4 profils, triggers SQL) complètent le tout.

## Features principales
- **Scan** barcode alimentaire + cosmétique (cascade OFF→OBF) et **OCR d'étiquettes par photo** via Claude Vision (Edge Function) ; scan, score et filtres allergènes **toujours gratuits**.
- **Score 0-100 par pénalités toxicologiques** (« un poison reste un poison », pas le Nutri-Score), adapté au profil santé ; mode « ce que je peux manger ».
- **Contenu sourcé agences officielles uniquement** (EFSA/ANSES/IARC/EMA/Cochrane/ANSM) avec tests garde-fous anti-sources militantes ; 31 cartes éducatives ; écran méthodologie transparent.
- **Premium (29,99 €/an)** : alternatives dynamiques, classements enseignes, recap mensuel partageable, scan choc viral, mode famille, export PDF médecin, stats avancées, historique illimité.
- **Expert (49,99 €/an)** : encyclopédie 40 plantes, chercheur de remèdes, protocoles 21 jours, herbier, rappels de cure, recettes bien-être.
- **Gratuit aussi** : risque packaging (14 matériaux sourcés) + traçage maison-mère Wikidata ; gamification (streaks, 12 badges, synthèse hebdo).
- **Conformité** : RGPD (consentement CGU versionné, droit à l'effacement, `deletion_requests`), App Store (disclaimers, auto-renouvellement, suppression de compte, attribution OFF).

## État de prod
- **Feature-complete, non publiée** — 723+ tests verts, conformité App Store faite (`63395af`) ; derniers ajouts : OCR étiquettes (`4c3b979`), packaging + maison-mère (`15ca83a`), **RevenueCat intégré** (achats Premium/Expert + restore, `d4bc7d3`, 2026-07-10).
- **Bloqueur restant : compte Apple Developer** → EAS/TestFlight → screenshots → soumission (statut du 2026-07-12, cf. [[_vivo]]).
- Reportés en attendant : PostHog, OTA updates.

## Pointeurs
- **Repo local** : `/Users/volanthector/projects/vivo`
- **GitHub** : https://github.com/lixyra05-debug/vivo
- **Bundle** : `com.lyxiria.vivo` (iOS + Android) — pas d'URL publique (app non soumise) ; domaine prévu vu dans le code : vivo.lyxiria.com
- **Dans le repo** : `CLAUDE.md` (la vraie doc — sprints, règles absolues R1-R10, leçons), `PRD.md`, `supabase/migrations/` (15 fichiers), `supabase/functions/analyze-ingredients/`

## Liens
- [[_vivo]]
- [[01-Projects/vivo/decisions-et-lecons|decisions-et-lecons]]
- [[documenter-un-projet]]
