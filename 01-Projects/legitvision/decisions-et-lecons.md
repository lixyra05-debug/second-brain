---
type: resource
tags: [ia, dev]
cree: 2026-07-10
source: https://github.com/lixyra05-debug/legitvision
---

# LegitVision — décisions et leçons

> Extraites le 2026-07-10 du CLAUDE.md du repo, des messages de commit (110) et de [[contexte-hector]]. 1 à 3 lignes chacune, avec le commit quand il parle.

## Décisions produit
- **Jamais « certifié authentique »** — toujours « estimation », « probabilité », « score de confiance » (règle critique n°1 du repo). C'est le bouclier légal du produit.
- **Montres écartée volontairement** : catégorie ajoutée puis retirée le même cycle (`3b3ff42` → revert `15dc3c0` « non souhaitée »). Scope tenu : sneakers, sacs, vêtements.
- **Monétisation stricte** : zéro crédit par défaut (`33737fe`, migration 016) et paywall dès l'entrée du flow d'analyse (`b02ebdf`) — pas d'analyse gratuite.
- **Garde-fous sur le verdict** : score 40-60 ou confiance basse → flag expert review ; `confidence_level` de l'IA intégré jusqu'au skip du débit si photos insuffisantes (`1223044`).
- **SEO programmatique comme canal d'acquisition** : 3 sprints (`51c477d`, `f268acf`, `c63ebd6`) ≈ 150 pages générées depuis les données marques/modèles — le catalogue EST le contenu.
- **Design sobre premium (StockX/GOAT), mobile-first** : deux refontes ambitieuses (Farfetch×GOAT, bento glassmorphism) tentées puis revertées le même jour (`09e445b`/`36856d6`, `e0d4094`/`e201a7a`).

## Décisions techniques
- **Prompts IA spécialisés par marque** (`20432d9`) + protocoles photo et points d'authentification par modèle en JSONB — la qualité d'analyse vient de la spécialisation, pas d'un prompt générique.
- **RLS partout, service_role seul pour les colonnes sensibles** : trigger `guard_profiles` qui verrouille `role`/`credits_remaining`/`subscription_plan`/`stripe_*` (migration 017).
- **Le CLAUDE.md du repo est la vraie doc** : le README est resté le boilerplate create-next-app ; tout vit dans CLAUDE.md, tenu à jour après chaque audit (`f0f462b`).

## Leçons durcies
- **Une policy RLS UPDATE sans restriction de colonnes = escalade** (P0-3) : un user pouvait se donner `role='admin'` et des crédits illimités. RLS ligne ≠ RLS colonne — verrouiller les colonnes privilégiées par trigger.
- **Le webhook Stripe n'était pas enregistré** (P0-1) : les clients payaient sans recevoir leurs crédits, en silence. Leçon : tester le happy path paiement **en prod, en réel** (cf. règle « MCP Stripe en LIVE → tester avec un prix 1 € » de [[contexte-hector]]) ; vérifié en vrai le 2026-06-22.
- **Un ID de modèle IA hardcodé finit par mourir** (P0-2) : `claude-sonnet-4-20250514` retiré → 404 sur chaque analyse en prod. Surveiller les deprecations ; les modèles 4.6+ rejettent `budget_tokens` → `thinking: adaptive` (`4eab45a`).
- **Idempotence et atomicité sur l'argent** : race condition sur le débit de crédits (TOCTOU, `f4ae344`), webhook idempotent via `stripe_events` (`d74fdb1`, migration 013), upgrade Pro↔Business qui modifiait l'abonnement au lieu d'en créer un deuxième (`5084c4f`, `a09b7bb`).
- **iOS casse en premier** (80 % d'usage mobile) : paiement en boucle sur iOS Safari → navigation top-level vers `/checkout` (`74b4d6f`) ; photos iPhone HEIC illisibles → conversion heic2any (`1a49f18`).
- **Les P0 ont été trouvés par des audits, pas par les users** : QA multi-agents 94/100 (2026-05-07) puis audit du 2026-06-21 — auditer à date fixe avant que les clients ne trouvent à ta place.

## Liens
- [[_legitvision]]
- [[fiche-technique]]
