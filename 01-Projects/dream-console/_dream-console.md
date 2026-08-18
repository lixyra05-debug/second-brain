---
type: project
statut: actif
tags: [ia, dev, automation]
cree: 2026-08-02
---

# DREAM Console

## Objectif

Une interface de commandement locale du second cerveau : ouvrir une page et voir, en une image, ce que le vault sait de lui-même — les rêves, la fraîcheur des pivots, les décisions non prises, l'équilibre mémoire machine / mémoire humaine, le graphe des liens — plus l'état vivant d'Hermes sur le VPS. **Locale, pour Hector seul, jamais déployée.**

## Statut

**Au 2026-08-18** — *le cockpit a un SEPTIÈME module en direct : la voix.*
- **Le pont vocal est branché** (phase 3 de [[_jarvis-voice]], lot 1) : le serveur vocal expose son état en SSE sur `127.0.0.1:8788`, la console l'affiche sur l'accueil. **Lecture seule dans les deux sens** — routeur `GET` uniquement, `405` sur tout le reste, aucun corps jamais lu : le cockpit affiche, il ne commande rien. Aucun extrait d'outil ne transite : la console apprend qu'un outil a servi et lequel, jamais ce qu'il a rapporté.
- **L'absence est le cas NOMINAL** et ne s'affiche pas : le serveur vocal n'est lancé que pour parler. Sans pont, l'accueil est exactement celui du 04/08 — vérifié, 60 fps, zéro erreur console, mouvement réduit toujours figé.
- **Validé à la voix le 18/08.** La machine à états était vérifiée (smoke 11/11), le rendu était vérifié (60 fps dans les quatre états) — seul le test à la voix fermait la boucle, et il est passé.

**Au 2026-08-02** *(premier instantané — projet créé et documenté le jour même)*
- **Six modules livrés et vérifiés** : DREAMS · FRAÎCHEUR · BOUCLES OUVERTES · MIROIR · VAULT · HERMES (avec onglet ATLAS). 9 commits, build vert, lint sans avertissement, 21 assertions de non-régression, 12 captures de contrôle automatisées.
- **Les cinq premiers modules lisent le vault au rendu** : en `npm run dev`, le vault est relu intégralement à chaque rafraîchissement. Aucune base de données.
- **Le sixième est en direct** : `hermes-collector`, sonde **lecture seule** déployée sur le VPS (unité **système**, `User=veillebot`), SSE sur `127.0.0.1:8787`, jointe par tunnel SSH. Aucun port public, routeur `GET` uniquement, aucun secret ni contenu de conversation dans le flux.
- **Ce que la sonde a trouvé en se branchant** : `~/.hermes/state.db` est gelée depuis le 13/07 — **non-usage réel, pas une panne** (0 message entrant au log, 0 erreur SQLite, base tenue ouverte en écriture). Mais le 14/07 le gateway a journalisé « Session expiry done: 1 finalized », écrit `sessions.json` et **jamais `ended_at` en base** : 7 h 47 d'écart entre deux magasins, aucun signal. Capturé dans `00-Inbox/`.
- **Ce que le panneau ATLAS montre** : depuis le verdict A/B du 17/07, **139 clôtures de plus** — 1,5× l'échantillon sur lequel il a été rendu — pour **−0,55 $**, contre −17,14 $ sur les 92 premières. Le verdict de juillet n'est plus la meilleure information disponible sur Atlas.
- **Sous filet depuis le 2026-08-03** : dépôt **privé** `lixyra05-debug/dream-viewer`, poussé après scan anti-secrets — ni IP, ni hôte, ni URL de tunnel, ni secret, ni chemin absolu du Mac dans les 49 fichiers suivis ; les captures d'écran (`.shots/`) sont ignorées et n'ont jamais été suivies. Deux éléments de contenu du vault retirés du code à cette occasion (une phrase du rêve recopiée en dur, les chiffres du verdict Atlas en repli).

## Next actions

- [x] ~~Trancher l'exception `ReadWritePaths` du collecteur~~ — **supprimée** le 03/08 : le collecteur recopie `atlas.db` dans son `/tmp` privé et lit la copie. `ReadWritePaths=` est vide, `~/.hermes` est strictement en lecture seule.
- [ ] Décider si `polymarket.db` (5,9 Go) entre dans le collecteur, et sous quelles requêtes bornées.
- [ ] **Dette assumée (v3, 04/08) — le contexte WebGL de l'accueil est détruit et recréé à chaque ouverture de module.** La sphère recule et s'estompe, puis la vue se déploie : le geste est le bon, mais la scène est reconstruite au retour. La conserver vivante derrière les vues de module exigerait de toucher à ces vues — arbitré « OK pour l'instant » par Hector, à reprendre le jour où les modules passeront en plein écran.
- [x] ~~**2026-08-09 (dimanche) — supprimer `~/dream-viewer.bak-20260804`** (950 Mo)~~ — **fait le 2026-08-15, sur GO d'Hector**, six jours après la date posée (retard relevé par la session du 15/08). Vérifié avant le geste : `~/dream-viewer` HEAD = `origin/main` (`c2fea2b`), arbre propre — le dépôt distant fait bien filet depuis la purge du 04/08, comme la next action le prévoyait. Log : `AI/logs/2026-08-15-session.md`.

## Décisions

- **2026-08-01 — direction visuelle VERRIÈRE**, arbitrée entre quatre propositions jugées sur trois axes, avec la loi typographique d'ÉPHÉMÉRIDE greffée.
- **2026-08-02 — unité systemd SYSTÈME et non `--user`**, sur preuve : en `--user`, ni `ProtectHome` ni `IPAddressDeny` ne s'appliquent sur cet hôte. Consigne initiale corrigée par le test.
- **2026-08-02 — `polymarket.db` (5,9 Go) et `bot3.db` (1,7 Go) hors périmètre** du collecteur tant que les agrégats ne sont pas vérifiés sur index.
- **2026-08-18 — la règle « chaque effet encode une donnée nommable » est RÉTABLIE sur l'accueil, pour ce que la voix y ajoute.** Resserrage volontaire de la décision du 04/08, demandé par Hector — celle-ci suspendait la règle sur l'accueil et reste valable pour tout le reste de l'écran. Trois effets, trois phrases vérifiables : le **cœur** de la sphère s'éclaire à mesure que la phrase captée s'allonge (plafond 6 s) ; la **rotation** accélère à mesure que la réponse se fait attendre (plafond 3 s, ×4) ; le **souffle** passe de 9 s à 2,4 s de période pendant qu'elle parle. Ce dernier est **discret et non proportionnel**, faute d'amplitude audio sur le pont — on ne feint pas une grandeur qu'on n'a pas. Le fondu de 400 ms entre états n'encode rien et c'est écrit à côté. Sous **mouvement réduit** la sphère ne bouge pas, donc elle ne porte pas l'état : le mot est dans la ligne du bas, qui ne dépend d'aucune animation.
- **2026-08-18 — le pont vocal parle le dialecte du collecteur Hermes**, délibérément : mêmes routes (`/health`, `/snapshot`, `/events`), même événement SSE nommé, même politique d'origine locale. Un second dialecte aurait fait un second client à écrire et à maintenir. Trois différences assumées, chacune motivée dans `lib/voix.ts` : l'absence ne s'affiche pas, le flux pousse sur **changement** et non sur horloge, la reconnexion est plus espacée.
- **2026-08-03 — dépôt privé GitHub** après scan anti-secrets. Le code ne doit embarquer ni contenu du vault ni URL de tunnel : deux éléments retirés à cette occasion.
- **2026-08-03 — l'exception `ReadWritePaths` est supprimée, pas déplacée.** Recopie dans le `/tmp` privé du service (`PrivateTmp`) plutôt qu'une ouverture en écriture de `~/.hermes`.
- **2026-08-04 — pas de GC réclamé à GitHub** après la purge des captures : les objets ne sont plus atteignables, le dépôt est privé, GitHub collectera de lui-même. Décision d'Hector, prise en connaissance du délai de quelques jours.

## Ressources liées

- [[01-Projects/dream-console/fiche-technique|fiche-technique]] — stack, architecture, features, pointeurs
- [[01-Projects/dream-console/decisions-et-lecons|decisions-et-lecons]] — dont « une propriété de sécurité non testée n'existe pas »

## Journal du projet

### 2026-08-18
- **Le pont vocal branché sur l'accueil** (phase 3 lot 1 de [[_jarvis-voice]]). Trois fichiers neufs — `lib/voix.ts`, `components/voix-context.tsx`, `components/home/VoixBande.tsx` —, la sphère modulée, la ligne du bas augmentée d'un mot. Aucun keyframe ajouté : le projet en autorise quatre et pas un de plus, la voix pilote le WebGL et des transitions. La loi typographique décide des canaux et tombe juste : `.t-voice` — sérif italique, « ce qu'un humain a écrit » — porte la transcription. Commit `84ff806`, poussé (le dépôt distant était resté à `c2fea2b` depuis le 04/08). Log : `AI/logs/2026-08-18-session.md`, cinquième section.
- **À signaler à Hector** : le détecteur de frontière signale deux reprises dans `components/home/Home.tsx` — « chaque effet encode une donnée nommable », que le vault porte depuis le 03/08 (00:15) et le dépôt depuis le 04/08 (13:34). **Antérieures au travail du jour** (commit `c2fd9f1`, l'accueil v3). C'est la règle de conception du projet, pas de la matière de projet — mais les dates donnent raison au détecteur, et l'arbitrage est à Hector. Rien n'a été reformulé ni exempté.

### 2026-08-01
- Lecture du vault, audit d'exploitabilité module par module, quatre directions visuelles proposées et jugées. VERRIÈRE retenue. Couche de données et cinq modules livrés (lots 1 à 3). Log : `AI/logs/2026-08-01-session.md`.

### 2026-08-02
- Collecteur Hermes déployé sur le VPS en lecture seule, module HERMES puis panneau ATLAS (lots 4 à 7). Import du projet dans le vault via [[documenter-un-projet]] — **aucune modification du dépôt `~/dream-viewer` pendant l'import**. Log : `AI/logs/2026-08-02-session.md`.

### 2026-08-03
- Mise sous filet (dépôt privé, scan anti-secrets), suppression de l'exception `ReadWritePaths` du collecteur, **jauge disque avec projection de saturation** ajoutée au panneau HERMES. Log : `AI/logs/2026-08-03-session.md`.

### 2026-08-15
- Filet local `~/dream-viewer.bak-20260804` (950 Mo) **supprimé** sur GO d'Hector — la next action datée du 09/08 était en retard de six jours. Vérification préalable : HEAD local = `origin/main`, arbre propre, fetch frais. Le dépôt `~/dream-viewer` lui-même n'a pas été touché. Log : `AI/logs/2026-08-15-session.md`.
