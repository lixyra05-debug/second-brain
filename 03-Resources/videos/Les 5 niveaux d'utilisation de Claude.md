---
type: resource
tags: [ia, agents]
cree: 2026-07-10
source: https://www.youtube.com/watch?v=ZRb7D6R64hM
---

# Les 5 niveaux d'utilisation de Claude

## Source
« Every Level of Claude Explained in 21 Minutes » — Nate Herk (YouTube, 21 min) : https://www.youtube.com/watch?v=ZRb7D6R64hM

## Résumé (5 lignes max)
Cinq niveaux d'utilisateur de Claude : enthousiaste (chat ponctuel), débutant (projets, connecteurs, artifacts, fichiers réels), intermédiaire (Co-work : accès disque, skills, tâches planifiées), avancé (Claude Code : CLAUDE.md, plan mode, sub-agents, worktrees) et architecte (routines cloud, hooks, channels — Claude devient une infrastructure). Chaque niveau a un « cheat code » d'entrée, et le mur du niveau 5 n'est pas technique : c'est la confiance, qui se construit par des routines à faible enjeu observées dans la durée.

## Points clés
- Cheat codes : créer un premier projet (→ N2), ouvrir Co-work (→ N3), structurer dossiers + règles (→ N4), automatiser sa tâche la plus répétitive (→ N5).
- CLAUDE.md : < 200 lignes, détails déportés dans des fichiers référencés ; chaque erreur → « mets à jour ton CLAUDE.md pour ne plus la refaire » (pratique interne Anthropic).
- Boucle de vérification : donner à Claude un moyen de tester son propre travail (navigateur, screenshots) — 2-3× la qualité selon Boris Cherny, créateur de Claude Code.
- Outillage : CLI d'abord (60-70 % de tokens en moins), puis API, puis skills, MCP en dernier recours.
- Contexte : /compact proactif (dérive passé ~50 % de la fenêtre), /context pour voir où partent les tokens, /insights mensuel sur ses propres usages.
- Parallélisme : sub-agents spécialisés (contextes isolés) + worktrees Git (3-4 sessions sans conflit) + commandes /custom pour les prompts répétés.
- N5 : routines cloud (planning, appel API ou événement GitHub), hooks (bloquer/formater/notifier), channels (Telegram, iMessage) ; headless mode + Agent SDK pour construire par-dessus.
- Confiance : commencer par une routine à faible enjeu qui ne sort pas de chez soi, l'observer des semaines ; confier d'abord le déterministe, garder l'humain sur l'agentique.

## Liens
- [[_second-brain]]
- [[Les 5 niveaux d'un second brain avec Claude]]
- [[idees-videos-second-brain]]
