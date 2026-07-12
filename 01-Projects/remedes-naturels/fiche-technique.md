---
type: resource
tags: [business, dev]
cree: 2026-07-12
source: https://github.com/lixyra05-debug/remedes-naturels
---

# Remèdes naturels — fiche technique

> Distillée le 2026-07-12 depuis le repo local (lecture seule). Repo **sans doc interne** (README boilerplate, CLAUDE.md = `@AGENTS.md`) : tout vient du git log (9 commits) et de la structure. Connaissance + pointeurs uniquement.

## Stack
- **Next.js 16 / React 19** (App Router, TypeScript), Tailwind CSS 4, framer-motion — npm.
- **Stripe** (Checkout + webhook, compte LYXIRIA — le même que LegitVision, prix 19,99 €), **Resend** (livraison des ebooks par email).
- **Aucune base de données** : l'état vit chez Stripe ; les PDF sont servis en statique depuis `public/downloads/`.

## Architecture (en bref)
Landing one-page (`src/app/page.tsx`, sections dans `src/components/sections`) → `POST /api/checkout` (Stripe Checkout) → paiement → **deux canaux de livraison** : le webhook `/api/webhooks` envoie les ebooks par email (Resend), et la page `/telecharger` vérifie la session Stripe côté serveur avant de donner les fichiers.
Pages `/merci`, `/cgv`, `/mentions-legales` ; logique dans `src/lib/{checkout,constants}.ts`.
C'est le plus petit projet livré du portefeuille : 9 commits, zéro DB, zéro auth.

## Features principales
- 2 ebooks PDF : *La santé par les plantes* et *Le médecin des pauvres* (1912 — le même livre qui a servi de piste, cross-référencée, aux cartes éducatives de Vivo).
- Checkout Stripe en une page, livraison auto par email + téléchargement direct vérifié serveur.
- Conformité soignée avant la vente : mentions légales + CGV, retrait des mentions de remboursement, vraies couvertures, zéro témoignage inventé (`934aa35`).

## État
- **Livré, en pause** (GO Hector du 2026-07-12). Paiement vérifié **en LIVE** : bascule des prix à 1 €, achat réel, restauration à 19,99 € (`885debe` → `296f15e`, 2026-05-04).
- Déploiement non tracé dans le repo local (pas de `.vercel`) — l'URL de prod n'est pas déductible d'ici.

## Pointeurs
- **Repo local** : `/Users/volanthector/projects/remedes-naturels` (npm)
- **GitHub** : https://github.com/lixyra05-debug/remedes-naturels
- **Stripe** : compte LYXIRIA (`acct_1SITaCCMKNVmORd6` — cf. message du commit `cbcefe2`)
- **Ebooks** : `public/downloads/*.pdf`

## Liens
- [[_remedes-naturels]]
- [[01-Projects/remedes-naturels/decisions-et-lecons|decisions-et-lecons]]
- [[documenter-un-projet]]
