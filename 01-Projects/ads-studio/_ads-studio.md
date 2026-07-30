---
type: project
statut: actif
tags: [ads, ia, automation]
cree: 2026-07-03
---

# Ads Studio

## Objectif
Usine à créas publicitaires **semi-automatisée** : Claude Code + Higgsfield pour produire les créas, Meta Ads pour la diffusion, orchestration par Hermes. **Aucune exécution ni dépense sans validation d'Hector** (HITL).

## Statut
**Au 2026-07-31** *(ssh lecture seule, user veillebot — aucune écriture ; log session 31/07)*
- **Le bloqueur crédits est levé et le premier run créatif a eu lieu.** `prompts/higgsfield-notes.md` (30/07) documente un plan **illimité** : `seedance_2_0` illimité en web (720p, ~8 s max), `marketing_studio_video` jamais illimité et toujours facturé. **11 générations vidéo** `seedance_2_0` sur le produit `legitvision`, **coût 0**, entre le 29/07 08:01 et le 30/07 21:29 UTC.
- **Le repo est vivant** : **10 commits depuis la baseline `bd45ed9` du 12/07**, tous entre le 28 et le 30/07, arbre propre et aligné sur `origin/master` (le backup GitHub privé suit). Ajouts : commande `import` pour les générations hors pipeline, hygiène `.gitignore`, digest Hermes, Brand Brain, notes Higgsfield, règles de direction créative pour les vidéos 8 s.
- **`brand/brand-brain.md` (30/07)** — Brand Brain LegitVision : positionnement « **outil d'aide, pas autorité de certification** », formulations autorisées / interdites sans exception, ton, formats (motion design en principal), et la règle visuelle « nommer les marques : OUI · générer des produits de marque en IA : NON ».
- **Le garde-fou a mordu pour de vrai** : run `abandon_20260730_214210` — 3 créas en file **abandonnées**, hooks non conformes au Brand Brain (R6), « ne seront jamais générées ». `logs/generations.db` : 7 runs, 17 créas, 1 produit.
- **Nouvelle règle R7 dans le `CLAUDE.md` du repo**, née d'un incident réel : un test de sandbox lancé contre la vraie base a inséré une ligne dans `runs`, qui est append-only (R5) → purge des 2 lignes de test le 28/07, puis R7 « les tests n'écrivent JAMAIS dans la base de prod ». **C'est l'explication des 3 unités `run-uXXXX` en échec** relevées le 30/07 : des tests de confinement vérifiant que le kernel bloque l'écriture dans le repo.
- **Nouveau timer `ads-digest`** (28/07) : digest quotidien Telegram **en lecture seule**, `OnCalendar=07:00 Europe/Paris`, `Persistent=true`. **Jamais encore tiré** — premier passage attendu au matin du 31/07.
- **Meta Ads toujours coupé** : `config/limits.yaml` porte `meta.write_enabled: false` et `default_status: PAUSED` ; plafonds Higgsfield à 150 crédits et 30 créas par run, `preflight_cost` obligatoire. Aucune diffusion, aucune dépense Meta.

---

Au 2026-07-04 : première commande brief exécutée avec succès. **Bloqueur : recharge des crédits Higgsfield** (4.31 restants ; pack 500 crédits / 26 $ recommandé) avant le premier run créatif complet. Meta Ads ne sera câblé qu'après validation des étapes de génération.

## Next actions
- [ ] (à remplir)

## Décisions
- (à remplir)

## Ressources liées
- **Code** : VPS `/home/veillebot/ads-studio` — CLAUDE.md persona (« tu PROPOSES, Hector VALIDE ») + `brand/`, `briefs/`, `prompts/`, `ops/`, `reports/`, `advertising-masterclass.md`. **Backup GitHub privé** : https://github.com/lixyra05-debug/ads-studio (baseline du 2026-07-12).

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-12
- Mission VPS (ssh lecture seule) : le code vit sur le serveur (`~veillebot/ads-studio`, dernière activité 24/06) — pas de fiche (GO du 12/07 : le pivot fait foi), pointeur ci-dessus. Signalé : le repo n'a aucun commit. Log : `AI/logs/2026-07-12-session.md` (session 3).
- Backup exécuté (GO Hector, écriture VPS limitée au repo) : `.gitignore` posé (.env*, credentials, logs/, *.db, settings.local.json), scan anti-secrets **0 hit**, commit `bd45ed9` « baseline ads-studio » (8 fichiers), repo privé `lixyra05-debug/ads-studio` créé et poussé. Log : session 4.

### 2026-07-31
- Reconnaissance VPS **lecture seule** (arbitrage du rêve du 31/07) : `git log` (10 commits depuis la baseline), `briefs/generations.json`, `brand/brand-brain.md`, `prompts/higgsfield-notes.md`, `ops/hermes/`, `config/limits.yaml`, `logs/generations.db` (ouverte en `-readonly`), unités `run-uXXXX` en échec. Statut mis à jour (instantané « Au 2026-07-31 ») ; celui du 04/07 conservé au-dessous. Aucune écriture sur le serveur. Log : `AI/logs/2026-07-31-session.md`.
