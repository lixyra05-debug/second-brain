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

**Au 2026-08-02** *(premier instantané — projet créé et documenté le jour même)*
- **Six modules livrés et vérifiés** : DREAMS · FRAÎCHEUR · BOUCLES OUVERTES · MIROIR · VAULT · HERMES (avec onglet ATLAS). 9 commits, build vert, lint sans avertissement, 21 assertions de non-régression, 12 captures de contrôle automatisées.
- **Les cinq premiers modules lisent le vault au rendu** : en `npm run dev`, le vault est relu intégralement à chaque rafraîchissement. Aucune base de données.
- **Le sixième est en direct** : `hermes-collector`, sonde **lecture seule** déployée sur le VPS (unité **système**, `User=veillebot`), SSE sur `127.0.0.1:8787`, jointe par tunnel SSH. Aucun port public, routeur `GET` uniquement, aucun secret ni contenu de conversation dans le flux.
- **Ce que la sonde a trouvé en se branchant** : `~/.hermes/state.db` est gelée depuis le 13/07 — **non-usage réel, pas une panne** (0 message entrant au log, 0 erreur SQLite, base tenue ouverte en écriture). Mais le 14/07 le gateway a journalisé « Session expiry done: 1 finalized », écrit `sessions.json` et **jamais `ended_at` en base** : 7 h 47 d'écart entre deux magasins, aucun signal. Capturé dans `00-Inbox/`.
- **Ce que le panneau ATLAS montre** : depuis le verdict A/B du 17/07, **139 clôtures de plus** — 1,5× l'échantillon sur lequel il a été rendu — pour **−0,55 $**, contre −17,14 $ sur les 92 premières. Le verdict de juillet n'est plus la meilleure information disponible sur Atlas.
- **Risque non couvert** : `~/dream-viewer` est en git **local sans remote**. Le projet n'a aucun backup hors du Mac.

## Next actions

- [ ] Pousser `~/dream-viewer` sur un dépôt privé — c'est le seul projet actif sans filet.
- [ ] Trancher le sort de l'exception `ReadWritePaths` du collecteur (mesurée, documentée, réversible en une ligne).
- [ ] Décider si `polymarket.db` (5,9 Go) entre dans le collecteur, et sous quelles requêtes bornées.

## Décisions

- **2026-08-01 — direction visuelle VERRIÈRE**, arbitrée entre quatre propositions jugées sur trois axes, avec la loi typographique d'ÉPHÉMÉRIDE greffée.
- **2026-08-02 — unité systemd SYSTÈME et non `--user`**, sur preuve : en `--user`, ni `ProtectHome` ni `IPAddressDeny` ne s'appliquent sur cet hôte. Consigne initiale corrigée par le test.
- **2026-08-02 — `polymarket.db` (5,9 Go) et `bot3.db` (1,7 Go) hors périmètre** du collecteur tant que les agrégats ne sont pas vérifiés sur index.

## Ressources liées

- [[01-Projects/dream-console/fiche-technique|fiche-technique]] — stack, architecture, features, pointeurs
- [[01-Projects/dream-console/decisions-et-lecons|decisions-et-lecons]] — dont « une propriété de sécurité non testée n'existe pas »

## Journal du projet

### 2026-08-01
- Lecture du vault, audit d'exploitabilité module par module, quatre directions visuelles proposées et jugées. VERRIÈRE retenue. Couche de données et cinq modules livrés (lots 1 à 3). Log : `AI/logs/2026-08-01-session.md`.

### 2026-08-02
- Collecteur Hermes déployé sur le VPS en lecture seule, module HERMES puis panneau ATLAS (lots 4 à 7). Import du projet dans le vault via [[documenter-un-projet]] — **aucune modification du dépôt `~/dream-viewer` pendant l'import**. Log : `AI/logs/2026-08-02-session.md`.
