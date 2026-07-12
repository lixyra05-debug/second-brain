---
type: resource
tags: [business, dev]
cree: 2026-07-12
source: https://github.com/lixyra05-debug/remedes-naturels
---

# Remèdes naturels — décisions et leçons

> Extraites le 2026-07-12 du git log (9 commits — le repo n'a pas de doc interne) et de [[contexte-hector]]. 1 à 3 lignes chacune.

## Décisions
- **Vente en direct, zéro plateforme** : landing + Stripe Checkout maison plutôt que Gumroad & co — marge pleine sur une stack déjà maîtrisée.
- **Zéro base de données** : l'état vit chez Stripe, les PDF en statique — le projet entier tient en 9 commits.
- **Double canal de livraison** : email automatique (webhook + Resend, `d5ffbb0`) ET page `/telecharger` avec vérification serveur de la session (`55d46f1`) — si l'email n'arrive pas, le client a un second chemin.
- **Nettoyage conformité avant la vente** (`934aa35`) : retrait des mentions de remboursement, vraies couvertures à la place des mockups, suppression des témoignages — pas de fausses preuves sociales, cohérent avec les règles contenu de [[contexte-hector]].
- **Un seul compte Stripe LYXIRIA pour tous les produits** (`cbcefe2`) — le même que LegitVision.
- **Réutilisation d'actifs entre projets** : *Le médecin des pauvres* (1912) est à la fois l'ebook vendu ici et la source cross-référencée des cartes éducatives de Vivo ([[01-Projects/vivo/decisions-et-lecons|decisions-et-lecons (Vivo)]]).

## Leçons
- **Le test LIVE à 1 € est écrit dans l'historique** (`885debe` bascule → achat réel → `296f15e` restauration 19,99 €) : la règle d'or « Stripe en LIVE → tester avec un prix 1 € » appliquée littéralement — même famille que la leçon webhook de [[01-Projects/legitvision/decisions-et-lecons|LegitVision]] : le paiement se teste en vrai.
- **CLAUDE.md = `@AGENTS.md`** : le pattern miroir agent-agnostique était déjà en place ici — exactement l'idée 5 du tri d'inbox (backlog Phase 6 de [[_second-brain]]).
- **Limite à vérifier si les ventes reprennent** : les PDF vivent dans `public/downloads/` (servis statiquement par Next) — la page de téléchargement vérifie la session, mais l'URL directe des fichiers reste devinable.

## Liens
- [[_remedes-naturels]]
- [[01-Projects/remedes-naturels/fiche-technique|fiche-technique]]
