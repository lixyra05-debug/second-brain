---
type: wiki
tags: [ia, dev, agents]
cree: 2026-08-15
---

# Douze pannes silencieuses — l'instrument n'a pas échoué, il a menti

> **Proposition Claude — à valider.** Deuxième page de `03-Resources/wiki/`, écrite le 2026-08-15 depuis `results/PANNES-SILENCIEUSES.md` du chantier [[_jarvis-voice]] (11-15/08/2026). **Hector n'a pas relu.** La première page, [[La panne silencieuse]], catalogue cinq formes vues sur cinq systèmes en deux mois ; celle-ci ajoute douze cas d'un seul chantier en cinq jours — et **deux familles que la première n'avait pas**.

Aucune des douze n'a planté. Chacune a produit une sortie plausible, bien formée, souvent assortie d'un verdict affirmatif. Une panne qui se signale coûte le temps de la corriger ; **une panne qui se tait coûte tout ce qu'on a bâti dessus** — ici : 240 tours de mesure invalides, trois conclusions retirées après avoir été énoncées, un commit qui a failli partir sur un scan qui n'avait rien scanné, et un assistant vocal qui racontait une recherche que personne n'avait faite.

Les numéros suivent l'ordre de découverte, les familles regroupent par mécanisme.

## I. L'instrument mesure autre chose que ce qu'il prétend

*(n° 1, 2, 3, 8, 9, 10, 11)*

1. **Le TTS rapide qu'aucune statistique de signal ne condamne.** Le 4 bits, le plus rapide du balayage, audio propre — et un WER de relecture de 23,6 % contre 4,5 % : cinq fois pire, pour 52 ms. Variante perverse : un locuteur anglophone lisant du français — crêtes saines, WER 87,8 %. → *Un TTS qu'on n'a pas fait relire n'est pas un TTS qu'on a évalué.*
2. **Le veilleur mémoire qui juge sur 8 % de la fenêtre.** Verdict net et chiffré, calculé sur 15 s d'une passe qui en durait 190 — le chargement des modèles, aucun tour mesuré. → *Un instrument qui échantillonne publie sa couverture à côté de son verdict ; un verdict sans couverture est une opinion.*
3. **Le contrôle de disponibilité qu'un orphelin peut satisfaire.** 240 tours « en trois configurations », tous servis par un seul processus survivant : la sonde interrogeait le port, l'orphelin y répondait parfaitement. → *Sonder une ressource partagée ne dit jamais qui la détient.* Et : une stabilité inexpliquée est un signal, pas une récompense.
8. **L'audit d'écriture qui lisait le mode au mauvais rang.** `open()` prend le mode au rang 1, `Path.open()` au rang 0 ; l'audit AST lisait le rang 1 partout, trouvait un rang vide sur la forme pathlib, concluait « lecture » — et validait précisément l'erreur la plus probable du module qu'il gardait. → *Un audit s'éprouve sur ce qu'il doit attraper, pas seulement sur le code qu'il doit approuver.*
9. **Le scan anti-secrets qui répond « 0 hit » sans avoir cherché.** `zsh` ne découpe pas une variable non quotée : `grep` a reçu cinq chemins concaténés en un seul nom introuvable, et `|| echo "0 hit"` a traduit l'erreur en absence. → *Distinguer « rien trouvé » de « pas cherché » : un verdict négatif publie son dénominateur.* Et : un idiome shell éprouvé sous un interpréteur ne l'est pas sous un autre.
10. **Le critère de validation qu'on ne pouvait satisfaire qu'en inventant.** Le critère de recette exigeait un chiffre introuvable dans la source : le satisfaire, c'était halluciner — passer, c'était être cassé. Écrit par la personne qui connaît le mieux le vault, donc celle dont on vérifie le moins les chiffres. → *Un critère est un instrument : il se vérifie contre la source, avant de mesurer. Quand un critère cite un chiffre, ce chiffre a un fichier et une ligne, ou il n'a rien.*
11. **Le prédicat qui se désarme après le premier succès.** Le correctif d'appel d'outil ne tenait qu'au premier tour outillé : les résultats des tours *passés* se sérialisent aussi en `role:"tool"`, donc **chaque succès reclassait les appels suivants** — plus l'outil marchait, moins il était protégé. Le banc posait chaque question sur une conversation neuve : l'état exact où le prédicat est juste. → *Valider un correctif dans l'état où la panne vit, pas seulement dans l'état neuf.*

## II. Le garde-fou détruit ce qu'il devait protéger

*(n° 4, 5, 6 — première famille absente de [[La panne silencieuse]])*

Ici rien ne défaille : la protection fait exactement son travail, et son travail, mal borné, **supprime le phénomène qu'on voulait voir**. Le zéro qu'elle produit n'est pas un mensonge du système — c'est un artefact du garde.

4. **Le contrôle qui écartait exactement les événements à compter.** Une borne basse ajoutée par symétrie, par réflexe d'encadrement, excluait les tours à `audio_end_ms` trop court — la signature même du faux départ à mesurer. Taux rapporté : nul, par construction. → *Un garde-fou encadre la signature de la panne, pas la plage du « normal » — le normal contient ce qu'on cherche.*
5. **Le compteur qui prenait la spéculation interne pour un échec visible.** 83 % de faux départs annoncés sur le réglage par défaut ; en vrai 8 % — le compteur voyait les appels LLM spéculatifs que la garde jette avant l'utilisateur. C'était la garde qui fonctionnait, comptée comme une panne. → *Mesurer au niveau où le préjudice existe.* Et : un chiffre implausible sur une configuration par défaut accuse la mesure avant le logiciel.
6. **La latence qui récompensait la panne.** Le « meilleur » réglage devait sa médiane à dix tours sur vingt-quatre partis avant la fin de l'énoncé — l'anticipation comptée comme de la vitesse. Sur les seuls tours corrects, le classement s'inverse. → *Exclure les tours en échec avant d'agréger : une réponse anticipée n'est pas une réponse rapide.*

## III. La panne se déguise en résultat

*(n° 7 — cousine de la forme n° 1 de la première page : un succès de façade)*

7. **Le repli d'erreur qui se fait passer pour de la vitesse.** 33 % des réponses — jusqu'à 85 % sur une passe — étaient le texte de repli du fournisseur, qui revient *vite* et *améliore* donc la médiane publiée. Rien dans la trace ne distingue un repli d'une vraie réponse, sauf le contenu — qu'on ne lit pas quand on agrège des millisecondes. → *Compter les réponses dégradées séparément ; refuser d'agréger une latence sans le taux de succès qui va avec.*

## IV. La correction maquille la sortie, l'état interne continue de mentir

*(n° 12 — seconde famille absente de [[La panne silencieuse]])*

12. **Le filtre qui corrigeait la voix mais pas la mémoire.** Quand le modèle écrivait sa syntaxe d'appel au lieu de l'exécuter, un filtre devant le TTS faisait prononcer une phrase honnête — à l'oreille, le tour était propre. Mais le texte assistant était persisté **en amont** du TTS : l'historique gardait la syntaxe brute, et au tour suivant le modèle la relisait comme une recherche accomplie — puis brodait dessus (« je n'ai pas d'informations sur Vivo dans mon second cerveau », sans avoir jamais cherché). Le contrefactuel mesure le mécanisme des deux côtés : historique honnête → **6/6** rappellent réellement l'outil ; historique contaminé → **0/6**. Ce que l'historique raconte pilote entièrement le tour suivant.
→ *Corriger à l'endroit où l'état s'écrit, pas à l'endroit où il se montre. Un filtre de sortie qui ne corrige pas la source fabrique un système dont la parole et la mémoire divergent — et la mémoire gagne toujours au tour suivant.*

C'est une famille à part : les onze autres pannes mentaient **à l'observateur** ; celle-ci fait mentir le système **à lui-même**. Un point de sortie a l'air idéal pour corriger — rien d'inaudible n'y échappe — mais c'est un point de sortie, pas un point de vérité : on y corrige ce que l'utilisateur perçoit en laissant intact ce que le système croit. La divergence est invisible au tour où elle naît et n'explose qu'au suivant, sous une forme — une hallucination — qui ne ressemble plus à sa cause.

## Le motif commun, et les cinq réflexes

Les douze partagent une signature : **un verdict affirmatif sur une réalité non observée.** Cinq réflexes, tous bon marché :

- **Mesurer la chose, pas son proxy.** Le WER de relecture teste l'intelligibilité ; la crête teste l'amplitude.
- **Publier la couverture avec le verdict** — échantillons, durée, fichiers réellement ouverts. « Rien trouvé » et « pas cherché » rendent la même sortie.
- **Encadrer la signature de la panne, pas la plage du normal.**
- **Séparer les tours en échec avant d'agréger** — et compter les dégradés.
- **Se méfier des résultats trop propres** : une stabilité inexpliquée, un chiffre implausible sur une configuration par défaut, une médiane qui s'améliore quand on dégrade le système — des indices, pas des récompenses.

## La chaîne de vérification remonte plus haut qu'on ne l'avait posée

**Sept des douze — 4, 5, 6, 8, 9, 11 et 12 — sont des pannes des instruments et garde-fous écrits pour attraper les autres.** Un instrument neuf n'est pas plus fiable que le système qu'il observe ; il est simplement moins testé. Les n° 11 et 12 durcissent le constat : deux pannes **du même correctif**, trouvées par le seul test que le banc ne faisait pas — une vraie conversation, avec un historique qui a vécu.

La n° 10 déborde du cadre : elle n'est ni dans le système ni dans les instruments, elle est dans le **critère**, écrit avant tout le reste par la personne la mieux placée pour le connaître. On vérifie donc le système avec des instruments, les instruments avec des jeux d'essai — **et les critères contre la source**. Sinon tout le reste est parfaitement rigoureux, au service d'une cible fausse.

Rapprochement avec la forme n° 5 de la première page (*une propriété qu'on a lue au lieu de la tester*) : les pannes 8 et 9 en sont la version instrumentale — le garde-fou existait, tournait, répondait, et n'avait jamais été vu mordre. **Un garde-fou qu'on n'a jamais vu mordre n'est pas un garde-fou vérifié, c'est une hypothèse.**

## Liens

- [[La panne silencieuse]] — la page sœur : cinq formes, cinq systèmes, deux mois ; ici douze cas, un chantier, cinq jours
- [[_jarvis-voice]] — le chantier d'origine ; source : `results/PANNES-SILENCIEUSES.md` du dépôt (hors vault)
- [[contexte-hector]] — la règle mère : zéro panne *silencieuse*, pas zéro panne
- [[Spec Bot 3 — un bloc daté du 14 août dans un fichier du 1er]] — même famille de soupçon : là ce n'est pas un vert qui ment, c'est une date
