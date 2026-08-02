---
type: resource
tags: [ia, dev, automation]
cree: 2026-08-02
source: local — ~/dream-viewer (git init, aucun remote au 2026-08-02)
---

# DREAM CONSOLE — fiche technique

> Distillée le 2026-08-02 depuis `~/dream-viewer` (31 fichiers, 5 726 lignes, 9 commits) et depuis le VPS en **lecture seule**. Zéro code copié : pointeurs et connaissance.

## Stack

- **Next.js 16.2.12** (App Router, Turbopack), **React 19.2.4**, **TypeScript 5**, **Tailwind v4** (CSS-first, `@theme` dans `globals.css` — plus de `tailwind.config`).
- **Framer Motion 12** — orchestration des transitions (4 fichiers). **react-markdown + remark-gfm** — rendu des rêves, tableaux GFM inclus.
- **Rendu graphique : SVG + Canvas 2D uniquement.** Pas de Three.js, pas de WebGL : le graphe du vault (48 nœuds, 283 arêtes) tient en Canvas 2D à 60 fps, les tracés et réticules sont en SVG, tout le reste en `transform`/`opacity` CSS.
- **Typographie** via `next/font` : Chakra Petch (chrome d'instrument), IBM Plex Mono, Inter, Newsreader.
- **Aucune base de données.** Le vault est lu au rendu par `fs` + un parseur de frontmatter maison (~20 lignes) ; `gray-matter`, prévu au départ, a été installé puis **retiré le 02/08** — inutile pour un frontmatter aussi plat, et une dépendance non importée est une dette.
- **Collecteur VPS** : Python 3 **stdlib seule** (`http.server`, `sqlite3`, `subprocess`) — rien d'installé sur le serveur.

## Architecture

Deux sources, deux régimes. **Cinq modules lisent le vault au rendu** : en `npm run dev`, Next réexécute le Server Component à chaque requête, donc le vault est relu intégralement à chaque rafraîchissement ; `next build` fige l'état du jour. **Un sixième module est en direct**, alimenté en SSE par une sonde déployée sur le VPS et jointe par tunnel SSH (`ssh -N -L 8787:127.0.0.1:8787`).

Les cinq modules du vault partagent **un axe unique de 30 jours** : un même `x` à l'écran désigne toujours la même date, dans DREAMS comme dans MIROIR comme dans FRAÎCHEUR. Le module HERMES a délibérément **sa propre échelle** (la minute) et le dit à l'écran — un timer de 60 s serait invisible sur un axe mensuel.

Côté VPS, le cloisonnement vit dans l'unité systemd, pas dans le code : `ProtectHome=read-only`, `ProtectSystem=strict`, `NoNewPrivileges`, `IPAddressDeny=any` + `IPAddressAllow=localhost`. Le routeur n'expose que `GET` ; toute autre méthode répond 405 sans que le corps soit lu.

## Features principales

- **DREAMS** — timeline des rêves, six sections dépliables, tableaux GFM et `[[wikilinks]]` vivants. Gère l'empilement de `/dream` (plusieurs rêves dans un fichier) et le décalage de profondeur des titres qu'il provoque.
- **FRAÎCHEUR** — les 8 pivots sur l'axe partagé, âge de l'instantané `## Statut` (jamais le mtime du fichier), seuil des 7 jours matérialisé, règle `pause` respectée.
- **BOUCLES OUVERTES** — les décisions non prises, compteurs **recalculés à chaque rendu** depuis la date d'origine dérivée du texte du rêve.
- **MIROIR** — deux pistes parallèles machine / humain, mêmes graduations, plus les relevés officiels des sections Miroir.
- **VAULT** — graphe de liens, layout figé au build (simulation déterministe, graine fixe), pulsations dont la fréquence encode le degré entrant ; régions mortes rendues comme des cadres pré-imprimés et vides.
- **HERMES** (direct) — 20 timers systemd avec leur phase de cycle, divergence `state.db` / `sessions.json`, santé du gateway ; onglet **ATLAS** : positions, PnL, et ce que le non-choix a produit depuis le verdict A/B.

## État de prod

**Local uniquement, jamais déployé** — c'est une contrainte du projet, pas un manque. `npm run dev` sur le Mac, tunnel SSH vers le VPS pour le module HERMES. Build vert, lint sans avertissement, 21 assertions de non-régression (`npm run smoke`) et 12 captures de contrôle automatisées (`npm run shots`, Playwright, remontée des erreurs console).

Le collecteur, lui, **est déployé** : `hermes-collector.service`, unité système `enabled`/`active`, ~20 Mo, écoute sur `127.0.0.1:8787` et aucun port public.

## Pointeurs

- **Code** : `~/dream-viewer` sur le Mac (git local, **aucun remote au 2026-08-02** — pas de backup hors machine).
- **Collecteur** : `~/hermes-collector/hermes_collector.py` sur le VPS · unité dans `/etc/systemd/system/hermes-collector.service` · source versionnée dans `~/dream-viewer/collector/`.
- **Doc du collecteur** : `~/dream-viewer/collector/README.md` — filtres, garanties, vérifications passées.
- **Règles du projet** : `~/dream-viewer/AGENTS.md`, section `dream-console`.
- **Accès** : `ssh -N -L 8787:127.0.0.1:8787 root@178.104.159.57`, puis `npm run dev`.

## Liens

- [[_dream-console]]
- [[01-Projects/dream-console/decisions-et-lecons|decisions-et-lecons]]
- [[_second-brain]] — la source de données des cinq premiers modules
- [[_hermes-polymarket]] — la source du sixième
- [[documenter-un-projet]]
