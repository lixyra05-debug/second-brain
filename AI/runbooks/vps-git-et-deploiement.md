---
type: resource
tags: [automation, dev, agents]
cree: 2026-08-03
source: interne — séquence rejouée 4 fois (ads-studio 12/07, alternance 31/07 ×2, alternance 03/08)
---

# Runbook — Mettre un repo VPS sous git, et y déployer un correctif

> Écrit après la **quatrième** exécution de la même séquence. Elle ne vivait que dispersée dans trois logs ; à partir d'ici elle a un nom.

## Doctrine — la lecture seule d'abord

**Une session sur le VPS commence toujours en lecture seule, et le reste jusqu'à ce qu'un GO nomme précisément ce qui doit changer.** Ce n'est pas de la prudence décorative : trois des quatre exécutions de cette séquence ont commencé par une reconnaissance qui a changé le plan.

- Commandes de reconnaissance : `ls`, `find`, `stat`, `cat`/`head`/`sed -n`, `grep`, `wc`, `git log`/`status`, `systemctl --user status`/`list-timers`/`list-units`, `journalctl --user -u`, `lsof`, `df`/`du`, et `sqlite3 -readonly`.
- **Jamais** de `.env`, jamais de credentials — ils ne servent à rien pour comprendre, et les lire crée une exposition sans contrepartie.
- Sur SQLite : requêtes **bornées et indexées**. `MIN/MAX(rowid)` est en O(1), `COUNT(*)` sur une table de plusieurs Go ne l'est pas.
- Ce qu'on ne peut pas prouver en lecture seule se **note comme non prouvé**, on ne l'écrit pas comme un fait.

## A. Mettre un repo sous git et le sauvegarder

1. **`.gitignore` d'abord, `git init` ensuite.** L'ordre compte : un `git add -A` avant le `.gitignore` fait entrer les secrets dans l'historique, et l'historique ne s'oublie pas.
   Y entrent au minimum : `.env*`, les données de production, les fichiers de PII, les sorties d'agent, les bases.
2. **Scan anti-secrets ET anti-PII, en deux passes, avec abandon automatique.**
   - passe 1 : motifs de secrets — `sk-`, `ghp_`, `xox`, `BEGIN … PRIVATE KEY`, `api_key=`, `password=`, `Bearer ` ;
   - passe 2 : motifs de données personnelles propres au projet — noms, adresses, identifiants de conversation, numéros.
   - **un seul hit = on s'arrête** et on corrige le `.gitignore`. Le scan tourne **avant chaque commit**, pas seulement le premier.
3. **Baseline** : premier commit, on note le hash court **et le nombre de fichiers suivis**. C'est ce couple qui sera cité dans le pivot — et c'est lui qui permet de détecter qu'une note s'est mise à mentir. *(Le 31/07, le pivot annonçait 15 fichiers pour un dépôt qui en comptait 16 : la contradiction n'a été vue que parce que le compte était écrit.)*
4. **Remote privé**, jamais public. Vérifier la visibilité après création, pas seulement à la commande.
5. **Reporter dans le vault** : pivot du projet → « Ressources liées » (URL, hash, nombre de fichiers, date) et [[inventaire-projets]]. Une ligne d'inventaire qui dit « pas de git » alors que le dépôt existe est **plus dangereuse qu'une date qui vieillit** : c'est un avertissement devenu faux.

## B. Déployer un correctif sur un script en production

1. **md5 avant.** On note l'empreinte du fichier tel qu'il tourne.
2. **`.bak` daté à côté** : `script.py.bak-YYYYMMDD`. Il reste sur le serveur ; c'est le rollback à une commande.
3. **Appliquer le correctif**, puis **`py_compile` immédiatement**. En cas d'échec : **restaurer depuis le `.bak` dans le même souffle**, ne jamais laisser un fichier cassé en place.
4. **md5 après**, et on le note. Deux empreintes encadrent le changement.
5. **`flock` sur le verrou du projet** pour toute opération qui touche aux données pendant qu'un timer peut tirer (ici `~/alternance/.offres.lock`, `-w 600`).
6. **Test fonctionnel qui n'a pas d'effet de bord.** Importer le module et appeler la fonction ajoutée vaut mieux que lancer le script : `brief_matin.py --force` **enverrait un Telegram**. Un test ne doit jamais produire l'effet qu'il vérifie.
7. **Commit dans le repo du projet**, avec le message qui dit *pourquoi*, pas seulement *quoi*.
8. **Vérification au run suivant, avec un critère écrit d'avance.** C'est le point le plus souvent sauté et le seul qui prouve quelque chose : `exit 0` + la ligne attendue au journal. Un correctif déployé n'est pas un correctif vérifié.

## Ce qui a été appris à l'usage

- **Un correctif « non prouvé » se note comme tel.** La dédup secondaire du 31/07 est déployée et testée sur 21 assertions, mais **jamais encore observée sur un doublon réel en production** : le critère est écrit, il attend un run qui en collecte un. C'est une boucle ouverte assumée, pas un succès.
- **Un test ne doit pas pouvoir toucher la production.** Un script de maintenance qui accepte une cible en paramètre doit vérifier que la cible est bien celle de production **avant** d'arrêter un service. *(Défaut trouvé le 03/08 sur `bot3_trim` : pointer une copie arrêtait quand même le vrai collecteur.)*
- **Un `except` muet qui rend une liste vide est une panne silencieuse.** Journaliser la cause coûte une ligne et fait gagner des heures.
- **Le cloisonnement se vérifie, il ne se lit pas.** Une directive systemd acceptée n'est pas une directive appliquée — cf. [[01-Projects/dream-console/decisions-et-lecons|dream-console — decisions-et-lecons]].

## Liens

- [[documenter-un-projet]] — l'autre moitié : importer la *connaissance* d'un projet, pas son code
- [[_agent-alternance]] · [[_ads-studio]] · [[_dream-console]]
- [[contexte-hector]] — règles sur les agents autonomes, dont le dead-man's switch externe
