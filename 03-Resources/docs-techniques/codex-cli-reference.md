---
type: resource
tags: [ia, agents, dev]
cree: 2026-07-12
source: recherche Perplexity du 2026-07-12 (docs OpenAI — developers.openai / learn.chatgpt)
---

# Codex CLI — référence pour le vault

## Source
Recherche Perplexity du 2026-07-12, sourcée sur les docs officielles OpenAI (Codex CLI, ChatGPT Learn) et des guides 2025-2026. Ingérée pour préparer l'arrivée d'agents Codex dans le vault (Phase 6 — [[_second-brain]]).

## Résumé (5 lignes max)
Ce qu'il faut savoir pour faire opérer Codex dans le vault : le sandbox `workspace-write` laisse **lire partout** mais n'autorise écriture et exécution que dans le workspace (+ writable roots ajoutés par `--add-dir`) ; les instructions viennent des AGENTS.md **concaténés du global (`~/.codex/`) au projet**, plafonnés à 32 KiB par défaut ; les commandes custom modernes sont des **skills** `SKILL.md` invoquées par `$nom` ; la mémoire persistante (Memories) est off par défaut, en SQLite interne, **non mappable** sur une note du vault.

## Points clés
- **Sandbox `workspace-write`** : lecture partout, écriture + exécution limitées au workspace ; réseau et éditions hors workspace passent par approval (preset recommandée : `--sandbox workspace-write --ask-for-approval on-request`).
- **`--add-dir <chemin>` = writable roots, écriture uniquement** — ne change rien à la lecture (déjà libre). Écrire dans le vault depuis un autre repo : `codex --sandbox workspace-write --add-dir ~/second-brain`.
- **Commandes custom, deux systèmes** : `~/.codex/prompts/*.md` (ancien — invocation `/prompts:nom`, placeholders `$ARGUMENTS`/`$1..$9`, pas de sous-dossiers) vs **skills** (moderne — dossier + `SKILL.md` dans `~/.codex/skills/`, invocation `$nom` ou activation implicite par description, gestion via `skills.config` de `config.toml`).
- **AGENTS.md** : à chaque niveau Codex prend `AGENTS.override.md` sinon `AGENTS.md` (un seul fichier par répertoire, fichiers vides ignorés), concatène du global vers le cwd — **le plus proche du répertoire courant domine**.
- **Limite 32 KiB par défaut** (`project_doc_max_bytes`) sur la concaténation : au-delà, troncature ; augmentable dans `config.toml`, noms de repli possibles (`project_doc_fallback_filenames`).
- **Pièges documentés** : un `AGENTS.override.md` plus haut écrase le local · `CODEX_HOME` peut pointer sur un autre profil · redémarrer Codex après toute modif de config.
- **Memories** : off par défaut (`features.memories`), consolidation pilotée par `memories.*`, stockage **SQLite interne** (`sqlite_home`) — **aucun réglage pour pointer vers un fichier markdown** ; l'écriture explicite dans le vault passe par une skill ou un MCP filesystem.
- **ChatGPT Desktop ↔ vault** : pas de connecteur Obsidian first-party — passer par un MCP filesystem (dossier exposé, read-only par défaut) ou le combo plugin « Obsidian Local REST API » + serveur `mcp-obsidian`.

## Liens
- [[_second-brain]]
- [[AGENTS|AGENTS.md §9 — Spécifique Codex]] (la version opérationnelle courte)
