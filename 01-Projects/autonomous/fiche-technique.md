---
type: resource
tags: [ia, business, automation]
cree: 2026-07-12
source: https://github.com/lixyra05-debug/autonomous
---

# Autonomous — fiche technique

> Distillée le 2026-07-12 depuis le repo local (lecture seule) : CLAUDE.md du repo (1 697 lignes, sprints 2A → 3W), package.json, structure, migrations, git log (49 commits). Connaissance + pointeurs uniquement (critère evergreen).

## Stack
- **Next.js 14** (App Router, TypeScript), next-intl (FR défaut + EN), Tailwind + design system maison « AURORA » (light iCloud, surfaces glass sur fond aurora animé), framer-motion.
- **Supabase** (eu-west-1) : Auth, PostgreSQL RLS, realtime — migrations 0013 → 0022 appliquées à la main.
- **Trigger.dev v4 self-hosted** sur VPS Hetzner (9 conteneurs Docker, Caddy TLS — trigger.coldsend.app) : tâches `analyze-icp` / `find-prospects` / `generate-emails` / `send-email` / `orchestrate-campaign` + 2 crons (dispatch `*/5`, poll-batch `*/10`).
- **Scrapling + FastAPI** sur le même VPS (scraping stealth — scrapling.coldsend.app) · **Explorium** REST (prospection B2B, hard caps crédits) · **Anthropic** (Opus forced-tool pour l'ICP, Sonnet pour les emails, Batch API −50 %) · **Resend** (envoi, DKIM/SPF/DMARC sur coldsend.app, webhooks svix).
- **Ops** : Vercel (front, edge Paris), Sentry + PostHog, UptimeRobot (3 monitors), script d'audit env sanitisé (GREEN/YELLOW/RED).

## Architecture (en bref)
Front sur Vercel (landing bilingue + 4 pages vs concurrents + dashboard auth-gated) ; tout le métier vit dans des tâches Trigger.dev self-hosted sur le VPS.
Pipeline : URL d'entreprise → scrape Scrapling (stealth) → `analyze-icp` (Opus forced-tool → ICP JSON validé Zod) → `find-prospects` (Explorium REST : businesses → prospects → bulk enrich, upsert `leads`) → `generate-emails` (Sonnet forced-tool, copy-rules outbound-engine gelées dans le prompt) → scheduler cron → `send-email` (Resend, allowlist + caps) → webhook bounces.
Les tâches enfants possèdent leurs transitions de statut (pending → scraping → analyzing → prospecting → generating → completed) ; le parent ne pose que failed/completed.
Garde-fous d'envoi : SEND_MODE progressif (warming), caps daily/per-domain/weekly, auto-chain derrière feature flag (OFF par défaut).
La doc opérateur vit dans `docs/` (~30 fichiers, dont un playbook self-hosted de 558 lignes).

## Features principales
- **Campagne en un input** : une URL → ICP inféré → prospects qualifiés (caps warming : 10/run) → emails personnalisés ≤ 3 phrases → planification par draft et envoi progressif.
- **Dashboard** : lancement, suivi temps réel, planification, export CSV, settings ; signup/login/logout + emails transactionnels (welcome, campaign-ready, password reset).
- **Landing SEO** : Lighthouse 98-100, JSON-LD, hreflang, OG 1200×630, 4 pages comparatives bilingues (Apollo, Lemlist, Instantly, Smartlead).
- **Légal FR complet** dès le Sprint 2A : CGV, mentions légales, privacy, refund (L221-18, LCEN, RGPD).
- **Observabilité** : Sentry, PostHog, UptimeRobot, audit d'env sanitisé, un doc par sprint dans `docs/`.

## État de prod
- **En production** : https://autonomous.coldsend.app (Vercel + VPS Trigger.dev/Scrapling derrière Caddy TLS).
- **Au 2026-07-12** (Hector) : premier run Trigger.dev **SUCCESS en prod confirmé**, après une double panne infra (image jamais transférée sur le VPS + env store silencieusement vide) ; UptimeRobot actif (3 monitors). Prochaine étape : recharge Explorium → test E2E stripe.com → DM 5 warm leads ; SIRET/SEPA en attente pour encaisser.
- Dernier commit : `3710f53` (2026-05-31, sprint 3W follow-up) — 49 commits, ~23 sprints tous chroniqués dans le CLAUDE.md du repo.

## Pointeurs
- **Prod** : https://autonomous.coldsend.app · Vercel `lixyra05-debugs-projects/autonomous`
- **Repo local** : `/Users/volanthector/autonomous` (pnpm)
- **GitHub** : https://github.com/lixyra05-debug/autonomous
- **VPS** : Trigger.dev self-hosted + Scrapling (trigger. / scrapling.coldsend.app) — playbook : `docs/trigger-dev-self-hosted-playbook.md`
- **Dans le repo** : `CLAUDE.md` (la doc de référence, sprint par sprint), `docs/`, `supabase/migrations/`, `trigger/` (les tâches)

## Liens
- [[_autonomous]]
- [[01-Projects/autonomous/decisions-et-lecons|decisions-et-lecons]]
- [[documenter-un-projet]]
