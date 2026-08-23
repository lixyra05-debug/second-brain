---
type: wiki
tags: [agents, automation, ia]
cree: 2026-08-03
---

# La panne silencieuse — quand le vert ne veut rien dire

> Première page de `03-Resources/wiki/`, ouverture de la Phase 4. Écrite le 2026-08-03 à partir de la graine des rêves des 31/07 et 02/08, **relue et validée par Hector le 2026-08-03**.

Une panne bruyante se répare. Une panne silencieuse s'installe.

Le principe est déjà dans le vault, hérité de l'incident Curator : *« dead-man's switch **externe** — objectif : zéro panne **silencieuse**, pas zéro panne »* ([[contexte-hector]]). Ce qui manquait, c'est le **catalogue des formes** que prend le mensonge. Cinq systèmes indépendants ont produit la même panne en deux mois ; les formes se répètent, donc elles s'anticipent. *(Deux formes ajoutées le 2026-08-23 — la n° 6 trouvée deux fois dans la journée, la n° 7 trois fois dans trois systèmes sans rapport. Relues et validées par Hector le jour même.)*

## Les sept formes

### 1. `exit 0` sur un contenu vide

Le processus réussit. Il ne produit rien d'utile, et personne ne le sait.

> Du **09/07 au 31/07**, le brief alternance de 07 h 45 est sorti en `status=0/SUCCESS` **tous les matins**. Il ne pouvait rien montrer de neuf : il sélectionne les offres `classement == "top"`, et `classement` n'est posé que par une étape de scoring **manuelle** qui s'était arrêtée le 09/07. Vingt-deux matins verts pour zéro information nouvelle.

**Signature** : un rapport dont le contenu ne varie plus, alors que l'entrée, elle, varie.

### 2. Le timer qui tire sur une étape absente

L'ordonnanceur fait son travail. L'étape qu'il déclenche n'existe pas, ou plus.

> Le diagnostic du 31/07 l'a établi : **aucune unité systemd de scoring n'a jamais existé**. Les quatre unités alternance sont `scrape`, `brief`, `bilan`, `perplexity-fetch`. Toutes les occurrences de `score` dans le code sont **en lecture**. Le cycle `nouvelle → analysee → …` était spécifié dans le schéma, jamais implémenté — la panne n'était pas une régression, c'était un **trou d'origine**.

**Signature** : une chaîne dont on n'a jamais vérifié qu'elle est *complète*, seulement que chaque maillon présent répond.

### 3. Le job pendu, qui n'échoue même pas

Pire que l'échec : l'absence de fin. Rien à signaler, puisque rien ne se termine.

> `launchd` lançait `claude` pour le rêve hebdomadaire ; macOS bloquait l'accès au vault par TCC. `claude` **démarrait quand même et restait pendu indéfiniment** — pas de code d'erreur, pas de notification, pas de trace. La parade a été un **préflight** : le wrapper vérifie qu'il peut lire le vault **avant** de lancer quoi que ce soit, et un chien de garde de 40 min derrière. Voir [[dream]].

**Signature** : une durée d'exécution qui n'a pas de plafond.

### 4. L'écriture à moitié faite entre deux magasins

Le job annonce un succès et n'écrit que la moitié de ce qu'il prétend.

> Le **14/07 à 04:03**, le gateway Hermes a journalisé `Session expiry: 1 sessions to finalize` puis `Session expiry done: 1 finalized`. Il a bien écrit `sessions.json`. Il **n'a jamais écrit `ended_at` dans `state.db`**, dont le `-wal` n'a plus bougé depuis la veille 20:16. **Sept heures quarante-sept d'écart entre deux magasins qui devraient dire la même chose**, et aucun signal. Voir [[Hermes finalise une session sans l'écrire en base]].

**Signature** : deux sources de vérité pour le même fait, et personne qui les compare.

### 5. La configuration acceptée mais jamais appliquée

Le système accepte la consigne, la stocke, l'affiche — et ne l'exécute pas.

> Une unité `systemctl --user` accepte `ProtectHome=read-only` et `IPAddressDeny=any`, et les expose dans ses propriétés. Sur Ubuntu 24.04 (`kernel.apparmor_restrict_unprivileged_userns=1`), **aucune des deux n'est appliquée** : le test d'écriture dans `/home` réussit, `curl` sort sur internet en HTTP 302. systemd le journalise lui-même — *« unit configures an IP firewall, but not running as root »* — mais le service tourne, et le tableau de bord de sécurité est vert. Voir [[01-Projects/dream-console/decisions-et-lecons|dream-console — decisions-et-lecons]].

**Signature** : une propriété de sécurité qu'on a **lue** au lieu de la **tester**.

### 6. L'alerte juste que personne n'écoute

Le système ne ment pas. Il détecte, il formule correctement, il émet à l'heure. **Et le message s'arrête à un saut que personne ne regarde.**

> **Deux cas le 2026-08-23, dans deux systèmes sans rapport.**
> — `format_recap()` de la veille IA construisait le récap Telegram **sans jamais afficher `run_stats["errors"]`**. Le compteur d'erreurs était correctement rempli depuis le 03/06 ; un run en échec envoyait un message **identique** à un run sain. L'information existait, elle n'avait aucune sortie.
> — `bot3-heartbeat` rapporte `heartbeat ROUGE — … disk_free: 5.3 G (mini 15 G) → /fail`, et le POST vers healthchecks.io répond **HTTP 200 « OK » en ~130 ms, chaque minute, depuis six jours**. Le calcul est juste, le seuil est bon, la livraison réussit. **C'est le dernier saut — healthchecks.io vers l'humain — qui est muet**, et le disque sature vers le 27/08. Voir [[_hermes-polymarket]].

**Signature** : un canal d'alerte qui n'a **jamais délivré une vraie alerte à un humain**. Pas un test qui n'a jamais échoué — un **chemin** qui n'a jamais servi.

**Ce qu'elle ajoute aux cinq autres.** Les cinq premières décrivent un système qui **ment ou se tait**. Celle-ci décrit un système qui **dit vrai, à l'heure, et dans le vide**. C'est la forme n° 5 déplacée d'un cran : là où « une propriété non testée n'existe pas », ici **un canal jamais emprunté n'existe pas**. On teste volontiers l'émetteur ; on ne teste presque jamais le trajet complet jusqu'à l'œil.

**Aggravant, et c'est ce qui l'a rendue invisible six jours** : `bot3-heartbeat` échoue aussi dans `systemctl` — mais **pour une autre raison que celle qu'il signale**. C'est un `oneshot` à `TimeoutStartSec=45` dont le run dure en réalité **43,6 s à 51,6 s** (chronométré le 23/08) : le délai était posé **à l'intérieur** de sa plage de fonctionnement, et systemd le tuait en `status=15/TERM`, `Failed with result 'timeout'`, deux fois sur trois. **1 484 échecs en trois jours, dont aucun ne dit « le disque se remplit ».** La forme n° 7 (le seuil périmé) fabrique ici le bruit qui enterre la forme n° 6 — et rejoint la parade déjà écrite plus haut : *un test qui crie à chaque usage nominal est un test qu'on désarme.* Deux formes superposées se protègent l'une l'autre.

**Parade — livrer une fausse alerte, une fois, et vérifier qu'elle arrive.** Le seul test qui vaut est de bout en bout : forcer l'émission d'une alerte réelle et confirmer qu'**un humain l'a reçue là où il regarde déjà**. Pas « le POST a répondu 200 » — *200 est la réponse du saut n−1, jamais celle du dernier*. Corollaire de conception : **une alerte doit atterrir où l'attention vit** (ici Telegram), pas là où c'était commode de l'envoyer. Et tout canal doit porter une date de dernière délivrance vérifiée : un canal sans cette date est un canal non testé.

*(La correction du 23/08 sur `format_recap()` — bloc « 🚨 Anomalies » — n'a été trouvée qu'en câblant une escalade vers `errors` : sans cette vérification, l'escalade elle-même serait née muette. Troisième instance évitée de justesse.)*

### 7. Le seuil calibré sur un régime qui a changé

Le garde-fou est correct, mesuré, documenté — **sur un système qui n'est plus celui d'aujourd'hui.** Le chiffre ne bouge pas ; c'est ce qu'il mesure qui a bougé sous lui.

> **Trois fois le 2026-08-23, dans trois systèmes sans rapport.**
> — `bot3-heartbeat` : `TimeoutStartSec=45` posé quand le run durait quelques secondes. Mesuré ce jour-là : **43,6 s et 51,6 s.** Le délai était **à l'intérieur** de la plage de fonctionnement, donc le service perdait la course deux fois sur trois — **1 484 faux échecs en trois jours**, dont aucun ne disait la vraie panne. Voir [[_hermes-polymarket]].
> — **La garde d'espace du backup `bot3`** : son modèle `pic = clair × (1 + 1/ratio)` était juste quand la base pesait 0,85 Gio. À 16,77 Gio il annonçait **8,64 Gio** là où la construction en a consommé **plus de 15,1** — et le disque est tombé à zéro le 23/08 à 16:33:37, emportant deux bots de trading pendant deux minutes.
> — `veillebot-weekly` : `TimeoutStartSec=900`, commenté *« un run peut prendre jusqu'à 15 min »* — estimation faite pour ~31 items à scorer. Le premier run après le correctif du dédoublonnage en a présenté **130**, et le run a été tué à **918 s**, une seconde après le début de la curation. Voir [[_veille-ia]].

**Signature** : un seuil dont **le commentaire cite une mesure datée**, et dont la grandeur mesurée a changé d'ordre depuis. Le code n'a pas bougé, le système si.

**Ce qu'elle ajoute aux six autres.** Les six premières décrivent une information qui n'arrive pas. Celle-ci décrit une information juste, arrivée, **et devenue fausse par le passage du temps** — sans que rien ne le signale, puisque rien n'a changé dans le code. C'est la seule forme qui **s'installe toute seule** : il suffit d'attendre. Et elle est asymétrique — quand le seuil dérive vers le trop strict, on récolte du bruit (le heartbeat) ; **quand il dérive vers le trop permissif, on récolte la panne dont il protégeait** (le backup). *Libérer de la place a rendu le système plus dangereux* : tant que la garde refusait, elle refusait proprement.

**Parade — dater le seuil, pas seulement le justifier.** Un seuil doit porter dans son commentaire **la mesure, sa date, et la grandeur dont il dépend** : « 1800 s, mesuré le 23/08 sur 130 items à scorer ; le poste dominant est le scoring, qui croît avec le nombre d'items sans verdict ». Ainsi le prochain lecteur sait **à quoi le comparer** au lieu de découvrir l'écart par la panne. Corollaire : **un seuil qui ne dit pas de quoi il dépend est un seuil qu'on ne peut pas re-mesurer** — et qu'on finira par monter au jugé, ce qui déplace la panne sans la voir.


## Les parades

Aucune n'est coûteuse. Toutes se posent une fois.

**Un heartbeat externe.** Le système ne peut pas attester de lui-même. Un ping vers un tiers (`healthchecks.io`, UptimeRobot) qui **alerte sur l'absence** de signal, pas sur sa présence : c'est le seul dispositif qui survit à la mort du processus censé prévenir. Déjà en place sur le Bot 3 (heartbeat minute) et sur Autonomous (3 monitors).

**Un préflight, pas un post-mortem.** Vérifier la condition d'exécution **avant** de lancer, et échouer bruyamment si elle manque. Un service qui répond `200` sur `/health` en servant un contenu cassé est exactement la forme n° 1 ; la parade est de **construire la réponse complète avant d'ouvrir le port**, et de mourir sinon.

**Comparer les magasins.** Dès qu'un même fait vit à deux endroits, quelque chose doit les confronter et **afficher l'écart comme une donnée**, pas le corriger en silence. C'est ce qui a rendu la forme n° 4 visible.

**Des assertions structurelles, pas volumétriques.** Un test qui vérifie « 2 rêves » crie à chaque dimanche 20 h et finit ignoré ; un test qui vérifie « chaque rêve couvre une période strictement positive » attrape la vraie erreur. **Un test qui crie à chaque usage nominal est un test qu'on désarme.**

**Rendre bruyant ce qui est muet.** Un `except` qui avale une erreur et rend une liste vide produit un module qui affiche « 0 » en ayant l'air correct. Journaliser la cause coûte une ligne.

**Tester la propriété, jamais la lire.** Pour une garantie de sécurité : tenter l'action interdite et vérifier qu'elle échoue. Un fichier témoin qu'on essaie d'écrire, un `curl` qui doit être bloqué, un `DELETE` qui doit être refusé par un trigger.

## Ce que la forme n° 5 ajoute aux quatre premières

Les quatre premières formes sont des **pannes**. La cinquième est une **illusion de garantie** : rien n'est cassé, tout fonctionne, et la protection sur laquelle on comptait n'a jamais existé. C'est la plus dangereuse, parce qu'elle ne se manifeste que le jour où on en a besoin.

D'où la formule qui vaut pour tout le reste : **une propriété non testée n'existe pas.**

## Liens

- [[contexte-hector]] — la règle d'origine : dead-man's switch externe, zéro panne *silencieuse*
- [[Hermes finalise une session sans l'écrire en base]] — forme n° 4, la plus récente
- [[dream]] — forme n° 3, et le préflight qui en est né
- [[_agent-alternance]] — formes n° 1 et n° 2
- [[_autonomous]] · [[01-Projects/autonomous/decisions-et-lecons|autonomous — decisions-et-lecons]] — double panne infra : image jamais transférée, env store silencieusement vide
- [[01-Projects/hermes-polymarket/decisions-et-lecons|hermes — decisions-et-lecons]] — « winrate n'est pas edge » : une *gate* qui mesure la mauvaise chose valide n'importe quoi
- [[01-Projects/dream-console/decisions-et-lecons|dream-console — decisions-et-lecons]] — forme n° 5
- [[vps-git-et-deploiement]] — la discipline de déploiement qui en découle
- [[Treize pannes silencieuses]] — la page sœur (chantier [[_jarvis-voice]], 11-18/08) : treize cas en huit jours, et **deux familles que celle-ci n'a pas** — le garde-fou qui détruit ce qu'il protège, la correction qui maquille la sortie pendant que l'état interne continue de mentir. *(Lien ajouté par Claude le 2026-08-15 sur demande d'Hector, renommé « Treize » le 18/08 sur son GO ; la page liée reste une proposition à valider.)*
