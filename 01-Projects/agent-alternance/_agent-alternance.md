---
type: project
statut: actif
tags: [ia, agents, alternance, automation]
cree: 2026-07-03
---

# Agent Alternance

## Objectif
Décrocher une **alternance de 12 mois** en agents IA / automatisation pour la rentrée Ynov de septembre 2026, via un pipeline semi-automatisé : scraping des jobboards, scoring des offres par Claude, envoi des candidatures **validé à la main** — HITL obligatoire, aucun envoi automatique.

## Statut
**Au 2026-07-31 — fin de journée** *(rectifie l'instantané du même jour ci-dessous, devenu faux dans l'heure ; relevé par le rêve 2 à **0 jour d'âge**)*
- **5ᵉ doublon archivé** — `francetravail-752a178f` (G ET F, Paris 2e) est passé en `archivee` sur GO. L'instantané précédent le dit encore « laissé en `analysee` » : **5 doublons archivés au total**, pas 4.
- **Le repo est sous git et sauvegardé** : `lixyra05-debug/alternance`, privé, **16 fichiers suivis** (et non 15). Le code seul y figure — ni `.env`, ni `offres.json`/`.csv`, ni `profil.json`/`.md`, ni `candidatures/`, ni `claude_out/`. Scan anti-secrets **et anti-PII** passé avant chaque commit, 0 hit.
- **`scoring.json` extrait** : poids des 5 critères, bandes de classement et **nature** des filtres durs vivent désormais dans un fichier **versionné et sans PII**, surchargé clé par clé par `profil.json > scoring` qui reste hors dépôt (`common.charger_scoring()`). Le dépôt est reconstructible sans jamais contenir de donnée personnelle.
- **Dédup secondaire déployée** dans `common.py` : clé `(entreprise, titre)` normalisés **en plus** de l'URL, qui rattrape les republications France Travail sous un nouvel id. Validée sur les 5 doublons connus, **0 faux positif** sur les 59 offres. Rollback : `scripts/common.py.bak-20260731` (le seul filet avant la mise sous git).
- Le reste de l'instantané ci-dessous tient : 59 offres, 46 scorées, **0 en bande `top`**, gisement pauvre.

---

**Au 2026-07-31** *(ssh lecture seule, user veillebot — aucune écriture, aucun `.env` lu ; log session 31/07)*
- **Le pipeline tourne, sans une seule panne.** `alternance-scrape` (quotidien 05:15 UTC = 07:15 Paris) et `alternance-brief` (05:45 UTC = 07:45 Paris) ont fini en `status=0/SUCCESS` le 30/07 ; `alternance-bilan` a tiré le dimanche 26/07, `perplexity-fetch` le lundi 27/07. Aucune unité alternance en échec.
- **59 offres dans `offres.json`** après le run du 31/07 05:15 UTC (« +1 nouvelles, 0 rafraîchies, 59 au total »), contre 19 annoncées au 04/07. Sources : France Travail 33, HelloWork 11, WTTJ 4, site entreprise 4, Grimp 3, autre 2, LinkedIn 1.
- **Les 46 non scorées l'ont été le 31/07** (session Claude, méthode `profil.json > scoring` retrouvée et rejouée à l'identique — formule vérifiée à l'unité près sur les 5 offres qui portaient un `score_detail`). Résultat : **0 en bande `top`**, 16 `a_surveiller`, 38 `a_eviter` sur les 59. *(Divergence non résolue : l'instantané du 04/07 annonçait 19 offres « ingérées et scorées », 13 en portaient un.)*
- **Le gisement est pauvre, et c'est le vrai enseignement.** La meilleure des 46 sort à **74**, sous les quatre offres qu'Hector a trouvées à la main en juillet (91, 86, 84, 80). Composition du lot : **~20 entonnoirs d'école** (ISCOD ×8, Scholia ×4, IMC ×2, IFCV ×2, NOTOS, H3 Campus — l'alternance y est adossée à LEUR diplôme, donc `filtre_dur: ECHEC`, précédent Alegria tranché le 09/07) · 7 annonces génériques « collectée par La bonne alternance » sans contenu réel · 6 hors domaine (bruit du mot-clé « développeur ») · 4 doublons archivés · 2 offres sans employeur nommé, donc non candidatables. **Il reste une douzaine d'offres réellement exploitables, dont 5 sans description.**
- **4 doublons archivés** (Scholia ×2, IFCV, HappyCab) : mêmes annonces republiées sous un nouvel id France Travail, donc une nouvelle URL — **la dédup de `common.py` se fait par URL et ne les voit pas**. Un 5ᵉ doublon (G ET F, Paris 2e) a été détecté et laissé en `analysee`, hors périmètre du GO.
- **Le scoring n'est pas en panne — il n'a jamais été automatisé, par design.** Diagnostic du 31/07 : aucune unité systemd de scoring (les 4 unités alternance sont `scrape`, `brief`, `bilan`, `perplexity-fetch`), aucun script de scoring dans `scripts/`, et **toutes les occurrences de `score` dans le code sont en lecture** — `report.py` l'affiche, `common.py` le préserve au merge. Le seul écrivain de `statut` est `common.py:106` → `offer["statut"] = "nouvelle"` : rien ne fait jamais avancer une offre d'un cran. `alternance_setup.md` l'assume dans une section « **Volontairement HORS de cette livraison** » ; le service du brief s'appelle « script-only, **0 token** ». Le scoring était une **étape manuelle en session Claude** (artefacts dans `claude_out/`), **arrêtée le 09/07**. Le cycle `nouvelle → analysee → …` de `schema_offre.md` est spécifié mais non implémenté.
- **Effet de bord du scoring manquant : le brief ne voyait plus rien de neuf.** `report.py` sélectionne les offres actionnables avec `statut in ("nouvelle","analysee") and classement == "top"`, et `classement` n'est posé que par l'étape manuelle. Entre le 09/07 et le 31/07, **aucune offre entrante ne pouvait y apparaître** : le brief de 07h45 tournait, sortait vert, et ne recyclait plus que **Publicis Media** (84), seule ancienne encore `analysee` + `top`. Depuis le scoring du 31/07 il en voit **2** — Publicis et le lead Mediawan. Le scoring n'a promu **aucune** nouvelle offre en `top` : aucune ne le méritait.
- **Candidatures : toujours les 4 du 02/07**, relancées le 09/07, dont **1 refus** enregistré. Aucune nouvelle lettre dans `candidatures/` depuis le 03/07. Scores des 4 envoyées : 91, 86, 80, 68.
- **Requêtes France Travail — pas de panne non plus.** Historique sur 19 jours (13 → 31/07) : `développeur` 14 jours avec résultats / 39 offres · `intelligence artificielle` **13 / 22** · `automatisation` **2 / 2**. Les `HTTP 204` sont le régime normal de `publieeDepuis=1` (chaque run ne demande que les offres publiées dans les dernières 24 h, filtrées `natureContrat=E2,FS` sur Paris/IDF) — le dimanche 19/07 et le lundi 27/07, les **trois** requêtes rentrent vides. Seule vraie anomalie : **`automatisation`, 2 offres en 19 jours**, un mot-clé stérile qui consomme un tiers du budget de requêtes (~3 par run).

---

Au 2026-07-04 : 19 offres ingérées et scorées, **4 candidatures envoyées** — Vista IA (91/100), Docaposte (86), Nextories (80), Citeo (68, à finaliser sur leur site). Lead Mediawan en attente ; **relance de Muriel Caillet (Ynov) prioritaire**. CV et LM v3 (posture apprentissage) validés.

## Next actions
- [ ] Les **2 offres sans employeur nommé** (scores 73 et 68) — non candidatables en l'état
- [ ] Les **7 fiches « à vérifier »** de [[01-Projects/agent-alternance/cibles-directes|cibles-directes]]
- [ ] **Twitter/X à rebalayer** quand `search` refonctionnera — le canal où les fondateurs annoncent leurs recrutements
- [ ] **Prouver la dédup secondaire en production** : le critère est posé (une ligne `[dedup2]` au journal), il attend un run qui collecte un doublon réel

## Décisions
- 2026-07-09 — **Précédent Alegria** : une alternance adossée au diplôme de l'école qui la propose vaut `filtre_dur: ECHEC`. C'est ce filtre qui écarte les ~20 entonnoirs d'école du lot.
- 2026-07-31 — **Le scoring n'a jamais été automatisé, par design** : aucune unité systemd de scoring, aucun script, toutes les occurrences de `score` dans le code sont en lecture. `alternance_setup.md` l'assume dans une section « volontairement HORS de cette livraison ».
- 2026-07-31 — **`scoring.json` extrait de `profil.json`** : poids, bandes et nature des filtres deviennent une config **générique et versionnée**, surchargée par `profil.json > scoring` qui reste hors dépôt. Le dépôt est reconstructible sans jamais contenir de PII.
- 2026-07-31 — **Dédup secondaire déployée** : clé `(entreprise, titre)` normalisés **en plus** de l'URL, pour rattraper les republications France Travail sous un nouvel id. Validée sur les 5 doublons connus, 21 assertions, 0 faux positif.
- 2026-07-31 — **Le dépôt passe sous git et part en backup GitHub privé.** Le code seul y figure : ni `.env`, ni `offres.*`, ni `profil.*`, ni `candidatures/`.

## Ressources liées
- **Code** : VPS `/home/veillebot/alternance` — **backup GitHub privé** : https://github.com/lixyra05-debug/alternance (HEAD `d90b5d4` au 2026-08-03, **16 fichiers suivis** — la baseline `319be2f` du 31/07 en comptait 15, les sessions suivantes du même jour ont porté le dépôt à 16 ; vérifié sur le serveur). Code et docs seuls : la donnée (`offres.json`/`.csv`, `profil.*`, `candidatures/`, `claude_out/`) et les secrets (`.env`) restent sur le serveur.
- [[01-Projects/agent-alternance/fiche-technique|fiche-technique]] — pipeline serveur (France Travail, Perplexity, brief 7h45, bilan dimanche), pointeurs VPS
- [[01-Projects/agent-alternance/decisions-et-lecons|decisions-et-lecons]] — décisions et leçons du déploiement serveur
- [[01-Projects/agent-alternance/cibles-directes|cibles-directes]] — 20 entreprises IDF où un fondateur lit lui-même les candidatures (hors ATS, hors job boards) — 2026-07-31

## Journal du projet
### 2026-07-03
- Note pivot créée (Phase 1 du second brain).

### 2026-07-12
- Import VPS via [[documenter-un-projet]] (ssh lecture seule — aucun fichier modifié sur le serveur). Pipeline vivant : offres rafraîchies le matin même (scrape France Travail 07h15). Log : `AI/logs/2026-07-12-session.md` (session 3).

### 2026-07-31
- Reconnaissance VPS **lecture seule** (arbitrage du rêve du 31/07) : `offres.json` / `offres.csv`, `candidatures/`, `claude_out/`, statut des services `alternance-scrape`, `alternance-brief` et `alternance-bilan`. Statut mis à jour (instantané « Au 2026-07-31 ») ; celui du 04/07 conservé au-dessous. Aucune écriture sur le serveur, aucun `.env` lu. Log : `AI/logs/2026-07-31-session.md`.
- **Diagnostic du scoring** (lecture seule) : l'étage n'était pas en panne, il n'a **jamais été automatisé** — aucune unité systemd, aucun script, `alternance_setup.md` le met « volontairement hors livraison ». Requêtes France Travail : pas de panne non plus, les `HTTP 204` sont le régime normal de `publieeDepuis=1` ; seule `automatisation` est stérile (2 offres en 19 jours).
- **Liste cible construite** ([[01-Projects/agent-alternance/cibles-directes|cibles-directes]]) : 20 entreprises IDF de 2 à 26 personnes où le dirigeant est joignable **hors ATS**, réponse directe au constat que les grands groupes filtrent Bac+3 avant lecture humaine. Recherche web + sites officiels, chaque segment contre-vérifié ; **Twitter/X inutilisable** (`search` en HTTP 404). Aucun contact pris — HITL.
- **Scoring des 46 offres exécuté sur GO d'Hector** — **première écriture du vault sur `offres.json`**, sous le `flock` du scrape, via les fonctions de `common.py` (`save_offres` atomique + `export_csv`), avec sauvegarde datée `offres.json.bak-20260731-prescoring`. 46 offres reçoivent `score` / `score_detail` / `classement` / `notes` et passent en `analysee` ; 4 doublons en `archivee` ; Mediawan en `top` (override d'Hector, `score: null`). Vérification post-écriture : 0 écart arithmétique hors `filtre_dur`, les 4 candidatures envoyées intactes, CSV régénéré à 59 lignes. **Aucune candidature générée, aucun envoi — HITL intact.**
- **Dédup secondaire déployée** dans `scripts/common.py` : clé `(entreprise, titre)` normalisés en plus de l'URL. Écrite et testée **en local d'abord** (5 doublons rattrapés, 0 faux positif sur 59 offres), diff montré, puis déployée après md5 et `py_compile` vérifiés **avant** le swap, sous `flock`. Sauvegarde `scripts/common.py.bak-20260731`. 5ᵉ doublon archivé au passage.
- **Repo mis sous git + backup GitHub privé** `lixyra05-debug/alternance` — baseline 15 fichiers, puis 16. Scan anti-secrets en **deux passes** (fichiers candidats, puis contenu stagé, commit conditionné au résultat) : 0 hit. `.env`, `offres.json`/`.csv`, `profil.json`/`.md`, `candidatures/` et `claude_out/` volontairement exclus — le code seul.
- **`scoring.json` extrait** : config générique versionnée (poids, bandes, nature des filtres durs), surchargée par `profil.json > scoring` qui reste hors dépôt avec la PII. Scan pré-commit élargi au nom, à l'adresse et au contact : 0 hit. Le dépôt devient reconstructible sans donnée personnelle.

### 2026-08-01
- **Contrôle du run de 05:15 UTC — premier depuis le déploiement de la dédup secondaire.** `status=0/SUCCESS`, état **identique à la baseline** (59 offres · `analysee` 50 · `archivee` 5 · `envoyee` 3 · `refus` 1 · **5 collisions de clé, chacune avec une seule non archivée**), CSV à 59 lignes, `common.py` toujours en md5 `1cc01a58…`. **Non-régression confirmée.** ⚠️ Mais la dédup **n'est toujours pas prouvée en production** : les 3 requêtes n'ont ramené qu'une offre, écartée par le filtre (sans entreprise) → « Aucune offre collectée », donc **`merge_offres` n'a pas été appelé** et aucune ligne `[merge]` n'a été émise. Le chemin de code reste à exercer.
