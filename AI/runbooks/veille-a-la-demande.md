---
type: resource
tags: [ia, agents, automation]
cree: 2026-07-30
source: interne — premier run agent-reach du 2026-07-30
---

# Runbook — Veille à la demande (Agent Reach)

Balayer 2-3 canaux sur un sujet, filtrer à l'evergreen, restituer en chat pour arbitrage, puis matérialiser dans le vault. Validé sur le premier run agent-reach (2026-07-30 : sujet agents IA / Claude Code / automatisation, fenêtre 7 jours → 2 fiches + 1 capture).

## 1. Prérequis — AVANT toute commande
- **La fenêtre Chrome du profil « Scraping » doit être OUVERTE ET AU PREMIER PLAN.** Twitter et Reddit passent par OpenCLI, qui rejoue la session de ce navigateur ; si la fenêtre est fermée ou reléguée en arrière-plan, le service worker de l'extension s'endort et tout tombe en `BROWSER_CONNECT` / « login required ».
- Confirmer la connexion : `opencli doctor` → attendre `Extension: connected`. Sinon, réveiller l'extension (voir Pannes connues) **avant** de balayer.
- Vérifier le bon profil : `opencli profile list` → un seul contexte, aliasé `scraping default`. Si un profil perso apparaît, **STOP** : jamais scraper depuis un compte principal.

## 2. Canaux
- **Twitter/X** (OpenCLI, session Scraping) : `opencli twitter search "<requête>" -f yaml`. Une requête par thème (agents IA, Claude Code, MCP, sorties de modèles).
- **Reddit** (OpenCLI, session Scraping) : `opencli reddit subreddit <sub> -f yaml` (listing hot), `opencli reddit search "<requête>" -f yaml`, puis `opencli reddit read <POST_ID> -f yaml` pour un thread. Pas de chemin anonyme : le `.json` public est en 403.
- **Exa** (sémantique) : `mcporter call 'exa.web_search_exa(query: "...", numResults: 6)' --timeout 120000` — **seulement avec une clé Exa perso** (la clé gratuite partagée est rate-limitée, voir Pannes). Sans clé : sauter Exa et le dire dans le log.
- **Appels séquentiels**, un canal à la fois : enchaîner des appels OpenCLI en parallèle a coïncidé avec la chute de l'extension sur ce run. Un thème = un appel = on attend le résultat.

## 3. Filtrer — critère evergreen
- Question unique : **« encore utile dans un an ? »** Si non, la donnée reste dans son outil source (le vault ne garde que le pointeur).
- **5 items max.** Écarter : annonces jetables, hype, threads d'opinion. Garder : specs/protocoles, outils open-source réutilisables, méthodes, rapports techniques.
- Un artefact durable enrobé de hype (rapport technique relayé par des comptes à engagement) → garder l'artefact, jeter l'enrobage. Résoudre les liens `t.co` vers l'URL canonique : `curl -s -o /dev/null -w "%{redirect_url}\n" <t.co>`.

## 4. Restituer en chat — puis STOP
- Présenter les ≤ 5 items : **2 lignes chacun** (quoi + pourquoi ça compte pour Hector), avec le lien.
- Proposer un classement et les candidats à couper. **STOP : Hector arbitre** ce qui est retenu et sa destination. Rien n'est écrit dans le vault avant son GO.

## 5. Matérialiser (après GO)
- **Fiche evergreen → `03-Resources/articles/`** : template `tpl-resource`, tags du vocabulaire fermé, `source:` = URL canonique, `[[liens]]` vers les pivots concernés. Résumé ≤ 5 lignes ; le détail concret va dans « Points clés ».
- **Actionnable mais pas encore evergreen → `00-Inbox/`** : note brute (≈ 3 lignes), tag `idee`, le lien, une phrase « à trancher ». Pas de fiche — le tampon Inbox nourrit le prochain « traite l'inbox ».
- **Écarté** : ne rien créer ; une ligne dans le log suffit comme trace.

## 6. Clore (R4)
- Log de session `AI/logs/YYYY-MM-DD-session.md` : canaux balayés, items retenus + destination, écartés (une ligne), décisions en attente.
- Commit vault : `veille: <sujet> (<n> fiches, <n> captures)` + push. **Ne jamais happer dans ce commit un fichier non suivi préexistant étranger au run** — le désindexer (`git restore --staged <f>`) et le laisser à Hector.

## Pannes connues
- **Service worker MV3 qui s'endort.** L'extension OpenCLI est un service worker Manifest V3 : Chrome le suspend dès que la fenêtre Scraping perd le focus ou reste inactive → `Extension: not connected`, `BROWSER_CONNECT`. `opencli daemon restart` seul **ne suffit pas** si la fenêtre n'est pas là. Correctif : ramener une fenêtre du profil Scraping **au premier plan** (ouvrir un onglet neuf réveille le worker), puis re-confirmer `opencli doctor`. Sur ce run, la connexion a chuté dès la première recherche et n'est revenue qu'après réouverture manuelle de la fenêtre.
- **Rate limit Exa sur la clé gratuite partagée.** Le backend Exa par défaut tape une clé MCP mutualisée → `HTTP 429 "You've hit Exa's free MCP rate limit"`, vite épuisée. Correctif : clé Exa perso (dashboard.exa.ai) passée en header `Authorization: Bearer <clé>` ou en `?exaApiKey=<clé>` sur l'URL MCP, **stockée hors du vault**. Sans clé perso : Exa hors périmètre, balayage limité à Twitter + Reddit, mentionné dans le log.

## Liens
- [[documenter-un-projet]]
- [[_second-brain]]
