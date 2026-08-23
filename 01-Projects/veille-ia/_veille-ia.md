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

**Au 2026-08-23 — chaîne à l'arrêt : le push NotebookLM est mort, timer désactivé avant le run du 24/08.** Google a renommé NotebookLM en « Gemini Notebook » et migré sur `notebook.google.com` ; `notebooklm.google.com` renvoie désormais un **301 permanent**. Le CLI du VPS est en **0.6.0** (publiée le 2026-05-29) : **27 occurrences de l'ancien host codées en dur dans le paquet installé, 0 du nouveau**. Mesuré sur le serveur le 23/08 en lecture seule : `Token fetch ✗ fail`, `notebooklm list` → « Authentication expired or invalid ». **Ce n'est pas l'expiration des cookies** — sur le Mac, des cookies frais obtenus par login navigateur le 23/08 à 12:41 (63 cookies, valides jusqu'en 2027) échouent identiquement en 0.3.4. Se reconnecter ne répare rien ; seule la version le peut — correctif upstream en **0.8.0** (2026-08-03, bascule du host par défaut) puis **0.8.1** (2026-08-14, reconnaissance du host après le rebrand). Le timer `veillebot-weekly` a été **stoppé et désactivé le 23/08** (voir Décisions). Dernier run sain : **17/08** — 94 scrapés → 31 nouveaux → 8 filtrés → 5 curés → **5 poussés**, `push_failed: 0`, `errors: []`. `state.db` au 23/08 : 458 items, 75 poussés. L'instantané du 19/08 est conservé au-dessous.

**Au 2026-08-19 — chaîne saine, v1.1 du yaml déployée ce jour.** 12 runs depuis le 04/06, 0 erreur, 75 items poussés ; dernier run le 17/08 (94 scrapés → 31 nouveaux → 8 filtrés → 5 poussés, 7 min 33). Le notebook contient 79 sources (75 pipeline + 4 manuelles). Corrections du 19/08 appliquées sur le VPS et validées à blanc (queries testées contre la vraie API GitHub — 10 repos rendus par chacune des 5 nouvelles ; releases speech-to-speech captées : v0.2.10 → v0.2.12 ; zéro Apify ni Kimi dépensé au test). Premier run en conditions réelles : **lundi 24/08**. Le soir du 19/08, le `SYSTEM_PROMPT` du filtre a été aligné à son tour (voir Décisions) : la v1.1 partira lundi avec un profil d'août, plus de juin.

## Next actions

- [ ] 🔴 **LE CHANTIER RÉEL — `mark_seen` doit se déplacer après le push, ou `pushed_to_notebooklm` doit entrer dans le filtre.** Ce n'est pas la version, c'est la conception : **même en 0.8.1, la prochaine panne de push brûlera silencieusement une cohorte entière.** `main.py` appelle `mark_seen()` au **STEP 3 (ligne 118)**, sur chaque item scrapé, bien avant `push_curated_items()` au **STEP 6 (ligne 162)**. Le branchement `auth_expired` (ligne 178) ne fait que journaliser et alerter — **aucun rollback, aucun démarquage, aucun `DELETE` dans tout le fichier**. Et le filtre du run suivant ne consulte jamais l'état de push : `is_seen()` et `filter_new_urls()` testent la seule existence de la ligne. Conséquence : un item curé mais non poussé est **exclu définitivement**, et comme `kimi_score` et `tags` ne sont écrits que par `mark_pushed()` (donc au succès seul), **il devient indiscernable d'un item recalé au scoring**. Les deux corrections possibles sont dans le titre ; la seconde est la moins invasive. *(Découvert le 2026-08-23.)*
- [ ] **Réactiver le timer — SEULEMENT quand la chaîne est réparée.** Commande exacte : `systemctl --user enable --now veillebot-weekly.timer` (user `veillebot`). ⚠️ **`Persistent=true` : systemd rattrapera le tir manqué IMMÉDIATEMENT à la réactivation, il n'attendra pas le lundi suivant.** Réactiver = déclencher un run dans la minute. Ne pas le faire avant que le push fonctionne, sous peine de reproduire exactement la perte qu'on vient d'éviter.
- [ ] **Réparer le push : monter le CLI de 0.6.0 à 0.8.1 sur le VPS** — avec deux précautions. (1) Le venv `notebooklm-veillebot/venv` est **partagé avec les timers Hermes** (« un seul environnement à maintenir, un seul point de casse ») : la montée les touche aussi, à tester à blanc. (2) La version seule ne suffira pas — il faudra ré-authentifier, et sur un VPS headless cela passe par le **master-token headless auth** introduit en 0.8.0. Dispositif neuf, à configurer, pas un `pip install`.
- [x] ~~**Lundi 24/08 — vérifier le récap Telegram du premier run v1.1** : les nouvelles queries injectent ~50 candidats d'un coup (rattrapage d'historique voulu), le plafond `max_items_per_run: 15` fait le tri.~~ — **caduque : le run du 24/08 n'aura pas lieu**, timer désactivé le 23/08. Le rattrapage d'historique reste intact et sera injecté au premier run après réparation. *(À noter : le one-shot launchd `com.hectorvolant.secondbrain.veille-rapport-2408` sur le Mac, lui, tirera bien le 24/08 à 20 h 04 — il trouvera un run absent, puis s'auto-désinstallera comme prévu. Le mécanisme de rapport n'existera donc plus lors de la réactivation.)*
- [x] ~~**Surveiller sur 3 runs si les items voix passent le filtre** : les focus_topics sont alignés, mais le `SYSTEM_PROMPT` de `filter_kimi.py` décrit encore un Hector sans jarvis-voice ni ads-studio (« LegitVision, Vivo, Autonomous ») — s'il massacre la voix au scoring, c'est lui qu'il faudra mettre à jour (décision à part, non prise le 19/08).~~ — **tranché le 2026-08-19 au soir, sans attendre les runs : le `SYSTEM_PROMPT` est mis à jour le jour même.** « Attendre 3 runs pour confirmer ce qu'on sait déjà, c'est trois semaines de veille mal filtrée avant lundi. » Voir Décisions ; la preuve reste due au run du 24/08.
- [ ] **2026-09-07 — revue Polymarket** (clause écrite dans le yaml) : si 0 item retenu en 3 runs, retirer la query **et** les focus_topics « polymarket » / « prediction market ».
- [ ] **2026-11-01 — revue du profil du filtre** (`SYSTEM_PROMPT` de `filter_kimi.py`), **ou dès qu'un nouveau chantier majeur démarre** : ce prompt a tenu deux mois et demi sans relecture alors qu'il décide de tout ce qui entre dans la veille — sinon on redécouvre le même décalage en novembre. *(Ajoutée le 2026-08-19, demande d'Hector.)*
- [ ] (option) Réinvestir le budget Apify libéré par Instagram — voir décision du 19/08 ci-dessous.

## Décisions

- **2026-08-23 — Timer coupé plutôt que CLI mis à jour, à quinze heures du run.** Arbitrage d'Hector : « une montée en 0.8.1 à quinze heures du run, sans test, c'est le genre de geste qui casse deux systèmes au lieu d'un. Un run décalé d'une semaine ne coûte rien. » Le venv est partagé avec les timers Hermes. Couper le timer arrête la chaîne **avant l'étape 3** : zéro budget Apify, zéro OpenRouter, zéro ligne écrite dans `state.db`, rien à récupérer ensuite. C'est le seul geste qui laisse l'état intact.
- **2026-08-23 — `stop` + `disable`, et non `mask` : systemd a refusé le masquage.** `systemctl --user mask` écrit son lien vers `/dev/null` dans `~/.config/systemd/user/` — exactement là où vit l'unité. Refus : « File /home/veillebot/.config/systemd/user/veillebot-weekly.timer already exists. » **Le fichier d'unité a survécu intact** (md5 `95b2ec9c…` identique avant et après, 560 octets, mtime Jun 4 08:54 — vérifié dans les deux sens). Retenu à la place : `stop` puis `disable`, qui retire le lien de `timers.target.wants/`. État vérifié : `is-enabled: disabled`, `is-active: inactive`, absent de `list-timers --all`, et aucune autre unité ne déclenche `veillebot-weekly.service`. État antérieur à restaurer : `enabled` + `active`.
- **2026-08-23 — Le vrai défaut n'est pas la version, c'est l'ordre d'écriture — et il aurait survécu à la mise à jour.** C'est la leçon de la session : on a cherché une panne d'authentification, trouvé un renommage de domaine, et découvert au passage un défaut de conception qui, lui, était là depuis le 03/06 et n'attendait qu'une panne de push pour se manifester. **Un dédoublonnage qui marque « vu » avant de savoir si l'item est arrivé transforme chaque incident de push en perte silencieuse.** Le schéma `seen_items` distingue pourtant bien `first_seen` de `pushed_to_notebooklm` — la colonne existe, elle n'est simplement jamais consultée pour décider ce qui est nouveau. Voir la next action en tête de liste.
- **2026-08-23 — « La sortie n'est pas la réponse », deux fois dans la même session.** `notebooklm source list --json` a rendu `{"sources": []}` avec un **code de sortie 0** sur une session expirée : sans passage en sortie brute, on concluait « notebook vide ». Et `notebooklm profile list` affichait `authenticated` sur un `storage_state.json` du 3 juin — les quatre contrôles qui passent (fichier existe, JSON valide, cookies présents, cookie SID) sont structurels ; **seul `auth check --test` touche le réseau, et c'est la seule ligne qui fait foi**. À se rappeler pour tout contrôle de cette chaîne.

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

### 2026-08-23
- **Panne découverte de biais.** La session partait d'une question sans rapport (inventaire des outils vidéo open source de la veille). L'interrogation du notebook a buté sur « Authentication expired » ; le diagnostic a remonté jusqu'au renommage de NotebookLM en « Gemini Notebook » et à la migration sur `notebook.google.com` (301 permanent mesuré sur l'ancien host).
- **Le VPS était déjà en panne, pas seulement exposé.** Diagnostic en lecture seule stricte (user `veillebot`, GO d'Hector) : CLI en 0.6.0, 27 occurrences de l'ancien host dans le paquet installé et 0 du nouveau, `Token fetch ✗ fail`. Intégrité vérifiée dans les deux sens — `storage_state.json` md5 `b91d9b4c…` et mtime `2026-08-19 09:33:51` **identiques avant et après** les commandes. Rien écrit sur le serveur pendant le diagnostic.
- **Point 1 tranché avant tout le reste, à la demande d'Hector** (« c'est lui qui décide si demain est risqué ou juste décalé ») : le « vu » est écrit au scrape. Demain était donc **risqué**, pas décalé. C'est ce résultat qui a déclenché la coupure.
- **Timer `veillebot-weekly` stoppé et désactivé** (GO d'Hector, seule écriture de la session sur le VPS). `mask` refusé par systemd, fichier d'unité intact. Vérifications complètes en Décisions.
- **Défaut de conception consigné en tête des next actions** : `mark_seen` au STEP 3 contre le push au STEP 6, sans rollback ni consultation de `pushed_to_notebooklm` dans le filtre.
- Chemin de retour documenté, avec l'avertissement `Persistent=true` : **réactiver, c'est déclencher un run dans la minute.**
