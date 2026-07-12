---
type: wiki
tags: []
cree: 2026-07-03
---

# Contexte Hector

> Distillé par Claude depuis le « prompt de téléportation » v4.0 (juin 2026), avec les corrections d'Hector du 2026-07-04. **Relu et validé par Hector le 2026-07-04.** Hector maintient ; Claude le lit en début de session et **propose** des mises à jour (R2 — jamais d'écrasement). Le doc v4.0 complet (comptes/accès, workflows détaillés, templates de projet) vit hors vault.

## Qui

- **Hector Volant, 20 ans, Paris.** BTS NDRC, puis **Ynov Campus — 3e année Bachelor IA, rentrée septembre 2026** (la piste Efrei n'est plus d'actualité).
- **Recherche active d'une alternance de 12 mois** (agents IA / automatisation) → [[_agent-alternance]].
- Fondateur de **LYXIRIA** (micro-entreprise). Produits livrés : [[_legitvision]], [[_vivo]], remedes-naturels (vente d'ebooks via Stripe).
- **Style de travail** : hands-on, itératif, voix-vers-texte (messages longs et décousus — c'est normal), résultats concrets > théorie, veut des commandes exactes à copier-coller. Délègue l'implémentation à Claude Code, **garde les décisions stratégiques**. Déteste le fluff et le groveling. Tendance « objet brillant »/scope creep assumée : le recadrer vers le shipping, sans lourdeur.
- Outillage de tête : Claude = orchestrateur principal, Perplexity = recherche.

## Projets actifs

- [[_agent-alternance]] — décrocher l'alternance via un pipeline semi-auto : scraping jobboards, scoring Claude, envoi validé à la main (HITL obligatoire).
- [[_hermes-polymarket]] — trading autonome sur Polymarket : moteurs Python **déterministes** qui décident, Hermes Agent en simple cockpit. 2 bots en paper (validation en cours), Bot 3 gaté.
- [[_ads-studio]] — usine à créas pub semi-auto (Claude Code + Higgsfield + Meta Ads, orchestrée par Hermes). Aucune exécution ni dépense sans GO d'Hector.
- [[_autonomous]] — SaaS outbound B2B : une URL d'entreprise → 50 prospects qualifiés + emails personnalisés, en prod sur autonomous.coldsend.app. *(Ajouté le 2026-07-12 — trou découvert par l'inventaire des projets, ajout demandé par Hector.)*
- [[_legitvision]] — SaaS d'authentification luxe (livré).
- [[_vivo]] — app scanner nutrition/cosmétique (livrée, 700+ tests).
- [[_second-brain]] — ce vault, Claude en architecte & concierge.
- **En fond** : remedes-naturels (livré), bot automobile (scraping Python/Playwright + alertes Telegram), **Molty/OpenClaw** — agent de veille 24/7 sur le VPS, distinct du cockpit Hermes (ne pas les confondre). *(Correction du 2026-07-12, mission VPS : Molty n'est pas déployé — user `openclaw` vide sur le serveur, seul le workspace local `~/clawd` existe ; la veille réelle qui tourne est `veillebot-weekly` sous Hermes, lundi 7h.)*

## Stack

- **Frontend** : Next.js 14–16 (App Router, TypeScript), React Native / Expo SDK 54, Tailwind + shadcn/ui (web), Framer Motion / Reanimated (animations).
- **Backend** : Supabase (Auth, PostgreSQL, RLS, Edge Functions Deno, Realtime), Stripe (checkout, webhooks, abonnements), RevenueCat (IAP mobile, à configurer).
- **IA & agents** : Claude Code (l'« ouvrier » principal — prompts XML structurés obligatoires pour le code), Claude Vision API (OCR étiquettes, scoring, analyse d'images), Hermes Agent (Kimi-K2.5 via OpenRouter) en cockpit lecture seule, OpenClaw/Molty (veille), n8n (workflows cloud).
- **Infra** : Vercel (web, déploiement auto GitHub), EAS Build (mobile), VPS Hetzner Ubuntu 24.04 (`openclaw`) + systemd services/timers, pnpm (web) / npm (mobile Expo — `npx expo install`, jamais `npm install`).
- **Data & automatisation** : Python + Playwright (scraping multi-sources), SQLite append-only (DBs des bots), Wikidata SPARQL (traçage de propriété des marques).

## Règles de collaboration

**Dans ce vault** : R1–R5 de `CLAUDE.md` — zéro suppression, zéro écrasement, GO explicite, log de session, atomicité.

**Sur le code** (règles d'or v4.0, condensées) :
- Prompt XML structuré pour toute mission ; docs (Context7) **avant** de coder ; `npx tsc --noEmit` + build avant commit ; Jest en phase finale uniquement.
- Agent Teams : jamais 2 agents sur le même fichier, briefs auto-portants, compaction entre agents.
- Sécurité : clés API jamais côté client, `console.log` purgés avant commit, MCP Stripe connecté en LIVE par défaut (tester avec un prix 1 €).
- Santé/contenu : jamais de claim thérapeutique (« contribuer à », « favoriser »), sources EFSA/EMA/ANSES/CIRC uniquement, disclaimer médical sur les écrans santé.

**Sur les agents autonomes** (leçons durcies par l'incident Curator, 18–19/06/2026) :
- Un LLM ne décide **jamais** les trades : cerveau déterministe, LLM = cockpit/ops en lecture seule.
- Paper-first + HITL + gates de validation qui mesurent **la vraie chose** (le winrate seul est trompeur pour l'arb/copy).
- Code de prod **hors** de l'arbre modifiable de l'agent ; seules les barrières OS protègent vraiment ; auto-amélioration coupée, ou dans un labo sandboxé séparé de la prod.
- Dead-man's switch **externe** contre les pannes silencieuses — objectif : zéro panne *silencieuse*, pas zéro panne.
