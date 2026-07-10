---
type: resource
tags: [sante, dev, ia]
cree: 2026-07-10
source: https://github.com/lixyra05-debug/vivo
---

# Vivo — décisions et leçons

> Extraites le 2026-07-10 du CLAUDE.md du repo, du PRD, des messages de commit (33) et de [[contexte-hector]]. 1 à 3 lignes chacune, commit cité quand il parle.

## Décisions produit
- **« Yuka, mais scientifiquement honnête »** (PRD) : score par pénalités toxicologiques **sans compensation** — un nutriment positif ne rachète jamais un poison. C'est le différenciateur.
- **Le scan, le score et les filtres allergènes restent TOUJOURS gratuits** — « leçon Exposr/MyFitnessPal » codifiée en règle absolue ; Yuka met les allergènes derrière le paywall, pas Vivo. Garantie affichée sur chaque paywall.
- **Sources officielles uniquement** (EFSA/ANSES/IARC/EMA/Cochrane/ANSM) : les listes militantes (Gouget, Beauvillard) servent de piste, puis tout est cross-référencé et **seules les agences sont citées** — des tests automatisés interdisent de citer ces auteurs.
- **Langage santé R5** : jamais « soigne/guérit/traite », toujours « favorise/contribue à » + disclaimer médical sur chaque écran plantes — aligné sur les règles santé de [[contexte-hector]], garde-fou regex dans les tests.
- **2 tiers** : Premium 29,99 €/an + Expert Plantes 49,99 €/an (`684a2eb`) ; écran abonnement **sans bouton « S'abonner »** tant que l'IAP n'est pas branché (App Store Guideline 3.1.1).
- **La transparence est gratuite** : packaging risk + maison-mère Wikidata non gatés (`15ca83a`) — même principe que les allergènes.
- **France first** : tout en français, marché FR d'abord, internationalisation en phase 3 (PRD).

## Décisions techniques
- **Moteur de scoring en TDD, compteur de tests public** : 288 → 723 verts suivi sprint par sprint dans le CLAUDE.md du repo ; `npx expo export` avant chaque commit.
- **Les tests comme contrat éditorial** : garde-fous automatisés sur les sources (allowlist d'URLs d'agences), le langage (regex anti-allégation) et les IDs canoniques — la ligne éditoriale est exécutable, pas juste écrite.
- **Data statique embarquée + AsyncStorage (R10)** pour tout l'univers plantes/protocoles/herbier — zéro dépendance serveur pour les features Expert.
- **Sentry avec PII strippée par design (R8)** : `beforeSend` supprime email/IP/username, drop des events contenant un barcode et des routes santé — le crash reporting ne voit jamais la donnée sensible.
- **Migrations SQL idempotentes appliquées à la main** : Claude écrit le fichier, Hector l'exécute dans le SQL Editor et vérifie « Success » — workflow documenté dans le repo.
- **Règles numérotées R1-R10 + section « Leçons de LegitVision (ne pas répéter) » dans le CLAUDE.md du repo** — le transfert de leçons inter-projets était déjà en place côté repos ; le vault le centralise désormais.
- **Multi-agents à périmètres disjoints** (Agents A/B/C par sprint, jamais 2 agents sur le même fichier) — règle d'or v4.0 appliquée sur tous les gros sprints.

## Leçons durcies
- **`world.openfoodfacts.org` bloque les requêtes anonymes (503/429)** → tous les appels passent par `fr.openfoodfacts.org` (`e7bdfc6`) ; seuls les liens canoniques visibles utilisateur gardent `world.`.
- **Hermes n'est pas le web** : `AbortSignal.any` non garanti → listener manuel dans le wrapper réseau maison (timeout 8 s + 2 retries + backoff + respect de Retry-After).
- **`\b` ASCII casse sur le français** (« blé », « céleri », « sésame ») → lookarounds Unicode `(?<!\p{L})…(?!\p{L})` pour le matching d'allergènes (B-002).
- **L'audit trouve avant les users** : audit global 62/100 → Sprint 1 (RLS oubliée sur 4 tables publiques, FK trop stricte qui bloquait les cosmétiques, RGPD consentement/effacement) → ~80/100 débloque TestFlight. Même pattern que LegitVision.
- **`react-native-view-shot`/`expo-sharing` incompatibles Expo Go** → dev client obligatoire pour tester les features de partage.
- **Perf listes RN** : `getItemLayout` + `windowSize` + `removeClippedSubviews`, et jamais de FlatList `scrollEnabled={false}` dans une ScrollView (B-015).
- **Wikidata en prod** : pas de récursion SPARQL (timeouts) — parent immédiat seulement, cache mémoire y compris des résultats négatifs, User-Agent poli, timeout court sans retry.
- **Héritées de LegitVision et codifiées dans le repo** : service_role dans les Edge Functions (RLS bloque sinon), build vérifié avant commit, logos toujours en local, jamais laisser une autre IA toucher le repo ou la DB.

## Liens
- [[_vivo]]
- [[01-Projects/vivo/fiche-technique|fiche-technique]]
- [[01-Projects/legitvision/decisions-et-lecons|decisions-et-lecons (LegitVision)]]
