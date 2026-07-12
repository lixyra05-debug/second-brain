---
type: resource
tags: [ia, business, automation]
cree: 2026-07-12
source: https://github.com/lixyra05-debug/autonomous
---

# Autonomous — décisions et leçons

> Extraites le 2026-07-12 du CLAUDE.md du repo (sprints 2A → 3W) et du git log (49 commits). 1 à 3 lignes chacune, commit ou sprint cité quand il parle.

## Décisions produit
- **Un seul input : l'URL de l'entreprise** — ICP, prospects et emails sont inférés derrière. Positionné frontalement contre Apollo/Lemlist/Instantly/Smartlead via 4 pages comparatives bilingues (`d05bb60`).
- **Warming discipliné** : SEND_MODE progressif (test → warming_week_0), caps quotidien/hebdo/par-domaine, auto-chain derrière un feature flag OFF, activation gated sur métriques réelles (bounce < 2 %, complaint < 0,05 %).
- **Copy rules gelées dans le prompt** (skill outbound-engine embarquée verbatim) : jamais « I/We », ouverture par le nom d'entreprise ou une observation marché, ≤ 3 phrases, soft CTA, aucun lien au premier contact, sujet 3-7 mots.
- **Coûts pilotés au sprint près** : Opus pour l'ICP (~0,38 $/analyse), Sonnet pour les emails (~0,012 $/email), Batch API −50 % ; hard caps Explorium (10 prospects/run, 30 crédits/tentative).
- **Légal FR avant le produit** : CGV/mentions/privacy/refund livrées dès le Sprint 2A (`cb35f07`).

## Décisions techniques
- **Infra hybride** : front Vercel, métier sur VPS Hetzner (Trigger.dev v4 self-hosted 9 conteneurs + Scrapling FastAPI + Caddy TLS) — contrôle des coûts et des données contre du tout-serverless.
- **Forced-tool partout** : `tool_choice` forcé + schéma → JSON garanti, validé Zod ; **invariant de parité** Zod ↔ JSON Schema ↔ colonne jsonb — à éditer ensemble, sinon casse silencieuse (`lib/schemas/icp.ts`).
- **REST direct plutôt que SDK/MCP** (Explorium) ; **idempotence double couche** sur l'envoi : UNIQUE(draft_id, provider) en DB + idempotencyKey Resend, les deux ancrées sur le même draftId.
- **devops-safety / AJEV** (Announce → Justify → Execute → Verify) : backup avant chaque sed, budget de cycles par classe d'erreur, valeurs de secrets jamais échoées au terminal.

## Leçons durcies
- **Chaque fix révèle l'hypothèse cassée de la couche suivante** (chaîne de hotfixes 3J → 3J.1 → 3K → 3K.1 → 3L.1) : avant de fermer un port ou changer un host, auditer TOUS les `*_ORIGIN`/`*_URL`/`*_HOST` — d'où le script d'audit sanitisé.
- **Un grep trop large a fui 6 secrets dans le transcript** → rotation immédiate de 7 champs, protocole de grep chirurgical ; et toute rotation d'`ENCRYPTION_KEY` doit purger les tokens chiffrés avec l'ancienne clé, sinon l'auth CLI casse en silence.
- **Docker bypasse UFW** (DNAT en PREROUTING) : fermer un port d'un conteneur = bind `127.0.0.1` dans le compose, pas une règle firewall.
- **Le compose de Trigger.dev est une whitelist d'env explicite** (pas d'`env_file`) : une variable ajoutée à `.env` n'atteint jamais le conteneur sans mapping `KEY: ${KEY}`.
- **`z.string().length(32)` = 32 caractères, pas 32 octets** : secrets générés en `openssl rand -hex 16`, pas `-hex 32`.
- **Les hooks projet peuvent être strippés par le harness** (PostToolUse jamais déclenché malgré la config) → `npx tsc --noEmit` manuel après chaque édition significative, plafond de 3 cycles spéculatifs.
- **Les webhooks reviennent** : un event Resend a rejoué ~19 h après l'envoi — receivers idempotents obligatoires (`ON CONFLICT DO NOTHING`).
- **Le self-hosted meurt en silence** — la double panne de juillet (image jamais transférée sur le VPS, env store vide) n'a été vue qu'en la cherchant → monitoring externe UptimeRobot, cohérent avec la règle « zéro panne *silencieuse* » de [[contexte-hector]].

## Liens
- [[_autonomous]]
- [[01-Projects/autonomous/fiche-technique|fiche-technique]]
