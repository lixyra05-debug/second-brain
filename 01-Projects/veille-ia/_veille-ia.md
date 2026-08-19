---
type: project
statut: actif
tags: [ia, agents, automation]
cree: 2026-08-19
---

# Veille IA (Hermes VeilleBot)

## Objectif

Une veille IA hebdomadaire **entièrement automatisée** : chaque lundi 7 h, le VPS scrape des sources whitelistées, filtre au LLM, et pousse le meilleur dans le notebook NotebookLM **« Veille IA Hector — Q3 2026 (Pro) »** (id `9d6812e4-4764-4ad6-9d3d-f335c7f4dca0`, compte Google `ndrcn937@gmail.com`), avec récap Telegram. En service depuis le 2026-06-03 ; pivot créé le 2026-08-19 à l'issue de la reconnaissance NotebookLM (P4).

## La chaîne complète

```
veillebot-weekly.timer (systemd user veillebot — lundi 07:00 Europe/Paris, Persistent=true)
  └─ ~/veillebot/scripts/main.py            (python du venv ~/notebooklm-veillebot/venv)
       1. sources.yaml      → quoi suivre (chaînes YouTube FR/EN, queries + repos GitHub)
       2. scrapers.py       → YouTube via Apify ; GitHub via API directe (GITHUB_TOKEN, gratuit)
       3. state_db.py       → dédoublonnage SQLite (~/veillebot/state.db, hash d'URL)
       4. filter_kimi.py    → scoring 0-10 par Kimi K2.5 (OpenRouter), seuil 7 ; les
                              focus_topics du yaml nourrissent le prompt du filtre
       5. curator_kimi.py   → sélection top 15 + résumés + tags
       6. notebooklm_pusher → push via le CLI notebooklm-py (NON officiel, v0.6.0, auth
                              cookies — maintenue en vie par le run hebdo lui-même ;
                              alerte Telegram dédiée si elle expire)
       7. telegram_notify   → récap du run
  └─ ~/veillebot/logs/YYYY-MM-DD.json       (un log JSON par run : compteurs + items poussés)
```

Secrets dans `~/.hermes/.env` (Apify, OpenRouter, GitHub, Telegram). Twitter : section présente dans le yaml mais **le code est commenté** dans `scrapers.py` — la réactiver demande les deux. Le venv est partagé avec les timers Hermes — « un seul environnement à maintenir, un seul point de casse » (cf. [[_hermes-polymarket]]).

## Source de vérité hors NotebookLM

**Le notebook est un débouché, pas la mémoire.** Tout ce qui y entre est reconstructible depuis le VPS :
- `sources.yaml` — quoi suivre (versionné par sauvegardes datées `sources.yaml.bak-YYYY-MM-DD`) ;
- `state.db` — tout ce qui a été vu, avec score Kimi, tags et `notebooklm_source_id` des items poussés ;
- `logs/*.json` — chaque run : compteurs et liste titre/URL/score/tags des items poussés.

Si NotebookLM casse (API non officielle, cookies), on perd le chat et les artefacts — **pas la veille**.

## Statut

**Au 2026-08-19 — chaîne saine, v1.1 du yaml déployée ce jour.** 12 runs depuis le 04/06, 0 erreur, 75 items poussés ; dernier run le 17/08 (94 scrapés → 31 nouveaux → 8 filtrés → 5 poussés, 7 min 33). Le notebook contient 79 sources (75 pipeline + 4 manuelles). Corrections du 19/08 appliquées sur le VPS et validées à blanc (queries testées contre la vraie API GitHub — 10 repos rendus par chacune des 5 nouvelles ; releases speech-to-speech captées : v0.2.10 → v0.2.12 ; zéro Apify ni Kimi dépensé au test). Premier run en conditions réelles : **lundi 24/08**. Le soir du 19/08, le `SYSTEM_PROMPT` du filtre a été aligné à son tour (voir Décisions) : la v1.1 partira lundi avec un profil d'août, plus de juin.

## Next actions

- [ ] **Lundi 24/08 — vérifier le récap Telegram du premier run v1.1** : les nouvelles queries injectent ~50 candidats d'un coup (rattrapage d'historique voulu), le plafond `max_items_per_run: 15` fait le tri.
- [x] ~~**Surveiller sur 3 runs si les items voix passent le filtre** : les focus_topics sont alignés, mais le `SYSTEM_PROMPT` de `filter_kimi.py` décrit encore un Hector sans jarvis-voice ni ads-studio (« LegitVision, Vivo, Autonomous ») — s'il massacre la voix au scoring, c'est lui qu'il faudra mettre à jour (décision à part, non prise le 19/08).~~ — **tranché le 2026-08-19 au soir, sans attendre les runs : le `SYSTEM_PROMPT` est mis à jour le jour même.** « Attendre 3 runs pour confirmer ce qu'on sait déjà, c'est trois semaines de veille mal filtrée avant lundi. » Voir Décisions ; la preuve reste due au run du 24/08.
- [ ] **2026-09-07 — revue Polymarket** (clause écrite dans le yaml) : si 0 item retenu en 3 runs, retirer la query **et** les focus_topics « polymarket » / « prediction market ».
- [ ] **2026-11-01 — revue du profil du filtre** (`SYSTEM_PROMPT` de `filter_kimi.py`), **ou dès qu'un nouveau chantier majeur démarre** : ce prompt a tenu deux mois et demi sans relecture alors qu'il décide de tout ce qui entre dans la veille — sinon on redécouvre le même décalage en novembre. *(Ajoutée le 2026-08-19, demande d'Hector.)*
- [ ] (option) Réinvestir le budget Apify libéré par Instagram — voir décision du 19/08 ci-dessous.

## Décisions

- **2026-08-19 — Voix ajoutée, le trou le plus coûteux comblé.** Suivi direct des releases de `huggingface/speech-to-speech` (nouveau mécanisme `github.repos` + `scrape_github_releases()` dans `scrapers.py` — n'existait pas ; dédoublonnage par URL de release, donc chaque release future arrive une fois) + 3 queries STT / TTS / voice agent. Motif d'Hector : le socle vocal a subi le retrait de ses modèles le 16/08 ; « deux mois d'avance aurait tout changé ».
- **2026-08-19 — Polymarket : une query qui crée le flux, avec clause de mort datée.** Le focus_topic seul scorait un flux qui n'en contenait jamais (0 source en 12 runs). Revue au 07/09 après 3 runs : 0 item → on retire query et focus_topics. « Un critère qui ne rencontre jamais rien est pire qu'absent — il donne l'illusion d'une couverture. »
- **2026-08-19 — Instagram désactivé** (bloc commenté dans le yaml, daté) : 8 comptes scrapés à chaque run, 0 source retenue en 12 runs. Où va le budget libéré : les ajouts GitHub sont **gratuits** (API directe) ; Twitter reste bloqué par le plan Apify **et** par du code commenté, pas par le budget ; le seul réinvestissement Apify utile serait des chaînes YouTube supplémentaires (voix ?) — non décidé.
- **2026-08-19 — Ads-tech** : query `"meta ads" OR "marketing api" in:readme stars:>20` (validée à blanc : 10 repos).
- **2026-08-19 — Les trous assumés sont écrits dans le yaml** pour ne pas être redécouverts comme des oublis dans trois mois : jobboards (agent-alternance a `lba-fetch` sur ce VPS), ingestion média (media-vault a sa chaîne locale sur le Mac), stack produit Expo/Supabase/RevenueCat (hors périmètre par conception — cette veille couvre l'outillage d'agents, pas la stack des produits).
- **2026-08-19 (soir) — Le `SYSTEM_PROMPT` de `filter_kimi.py` mis à jour le jour même, sans attendre les 3 runs.** Arbitrage d'Hector : « Attendre 3 runs pour confirmer ce qu'on sait déjà, c'est trois semaines de veille mal filtrée avant lundi. » Le prompt datait du 03/06, jamais retouché, et décrivait un Hector de juin (« studio IA/no-code », stack Trigger.dev/FastAPI, trois projets — ni jarvis-voice ni ads-studio). Seul le bloc « Profil de Hector » change, reconstruit depuis les pivots du vault, avant/après validé en session ; mission, barème 0-10 et seuil 7 inchangés au mot près. Focus alignés sur les 13 focus_topics v1.1 ; agent-alternance laissé hors du profil (trou assumé jobboards) ; Polymarket maintenu jusqu'à la clause du 07/09. Sauvegarde `scripts/filter_kimi.py.bak-2026-08-19` ; md5 `ad7c6cb9…` → `2ab2e3ae…` ; `py_compile` + test d'import passés (venv réel, user veillebot, import seul — sans effet de bord).
  - **La ligne « stack produit HORS périmètre » est le seul ajout négatif du prompt, et le plus important** : « un profil qui ne dit que ce qu'il veut laisse passer tout ce qui y ressemble » (Hector). C'est elle qui empêche le tuto Supabase/Expo de passer le seuil en ressemblant au reste.
  - **Deux mois et demi sans relecture, pour le texte qui décide de TOUT ce qui entre dans la veille** : d'où la next action datée du 2026-11-01 — revue du profil, ou avant si un nouveau chantier majeur démarre.

## Ressources liées

- [[_hermes-polymarket]] — le venv partagé `notebooklm-veillebot/venv` et la culture d'audit du VPS
- [[_jarvis-voice]] — premier bénéficiaire des ajouts voix du 19/08
- [[2026-08-19-session]] — la reconnaissance NotebookLM (P4) qui a mené à ce pivot : connecteurs claude.ai morts, CLI non officiel vivant côté VPS, inventaire des 79 sources

## Journal du projet

### 2026-08-19
- Pivot créé (le projet tournait depuis le 03/06 sans note dans le vault).
- `sources.yaml` passé en v1.1 sur le VPS, sauvegardes datées (`sources.yaml.bak-2026-08-19`, `scripts/scrapers.py.bak-2026-08-19`) : voix (releases + 3 queries), Polymarket (query + clause de revue au 07/09), Instagram désactivé (0/12 runs), ads-tech, focus_topics alignés, trous assumés commentés.
- `scrapers.py` : ajout de `scrape_github_releases()` (releases, tags en pis-aller) — seul changement de code, compilé et testé à blanc via l'API GitHub réelle.
- Le soir, sur GO après avant/après : bloc « Profil de Hector » du `SYSTEM_PROMPT` de `filter_kimi.py` réécrit depuis les pivots (sauvegarde `filter_kimi.py.bak-2026-08-19`, md5 `ad7c6cb9…` → `2ab2e3ae…`, `py_compile` + import OK) ; le reste du prompt inchangé.
- Rapport automatique du premier run posé sur le Mac (demande d'Hector : « dis-moi lundi soir si la voix score ») : one-shot launchd `com.hectorvolant.secondbrain.veille-rapport-2408` — lun 24/08 20 h 04, rattrapage mar 25/08 9 h 33 — collecte VPS en lecture seule, rapport ajouté à `AI/logs/2026-08-24-session.md` + notification macOS avec le verdict, puis auto-désinstallation. Testé à blanc le 19/08.
