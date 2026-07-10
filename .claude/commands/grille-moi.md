---
description: Interviewer Hector sur un sujet et déposer chaque idée en note brute dans 00-Inbox/
argument-hint: <sujet>
---

# /grille-moi — interview de capture

Sujet de l'interview : **$ARGUMENTS**

Tu interviewes Hector pour vider ce qu'il a en tête sur ce sujet vers le vault. Le goulot d'un second brain n'est pas la recherche, c'est la capture.

## Déroulé

1. Pose **une seule question à la fois** et attends la réponse avant de poser la suivante.
2. Questions **courtes et directes**, pensées pour une réponse dictée à la voix. Les réponses d'Hector peuvent être longues et décousues — c'est normal : creuse dedans, relance sur ce qui est flou ou contradictoire.
3. Commence large (« raconte-moi »), puis creuse : exemples concrets, chiffres, décisions prises, contraintes, prochaines étapes.
4. **Arrêt : 12 questions maximum**, ou immédiatement si Hector dit « stop », ou plus tôt si le sujet est épuisé.

## À la fin de l'interview

1. Chaque idée ou fait notable = **une note brute séparée** dans `00-Inbox/` :
   - Nom de fichier = l'idée en clair, en français lisible (le titre dit l'idée, il ne la range pas).
   - Frontmatter standard : `type: permanent` · `tags: [idee]` (+ un tag du vocabulaire fermé si évident) · `cree: <date du jour>`.
   - Corps : **2 à 5 lignes, les mots d'Hector (R2), zéro mise en forme** — pas de sections ni de gras ; c'est une capture brute, pas les sections de `tpl-permanent` (le tri viendra plus tard).
2. Liste ensuite à Hector les notes créées : chemin + une ligne chacune.
3. R4 : mentionne l'interview et les notes créées dans le log de session.
