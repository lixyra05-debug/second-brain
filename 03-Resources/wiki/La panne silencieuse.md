---
type: wiki
tags: [agents, automation, ia]
cree: 2026-08-03
---

# La panne silencieuse — quand le vert ne veut rien dire

> **(proposition Claude — à valider)** · Première page de `03-Resources/wiki/`, ouverture de la Phase 4. Écrite le 2026-08-03 sur GO d'Hector, à partir de la graine des rêves des 31/07 et 02/08. Hector relit avant validation.

Une panne bruyante se répare. Une panne silencieuse s'installe.

Le principe est déjà dans le vault, hérité de l'incident Curator : *« dead-man's switch **externe** — objectif : zéro panne **silencieuse**, pas zéro panne »* ([[contexte-hector]]). Ce qui manquait, c'est le **catalogue des formes** que prend le mensonge. Cinq systèmes indépendants ont produit la même panne en deux mois ; les formes se répètent, donc elles s'anticipent.

## Les cinq formes

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

---

## Les parades

Aucune n'est coûteuse. Toutes se posent une fois.

**Un heartbeat externe.** Le système ne peut pas attester de lui-même. Un ping vers un tiers (`healthchecks.io`, UptimeRobot) qui **alerte sur l'absence** de signal, pas sur sa présence : c'est le seul dispositif qui survit à la mort du processus censé prévenir. Déjà en place sur le Bot 3 (heartbeat minute) et sur Autonomous (3 monitors).

**Un préflight, pas un post-mortem.** Vérifier la condition d'exécution **avant** de lancer, et échouer bruyamment si elle manque. Un service qui répond `200` sur `/health` en servant un contenu cassé est exactement la forme n° 1 ; la parade est de **construire la réponse complète avant d'ouvrir le port**, et de mourir sinon.

**Comparer les magasins.** Dès qu'un même fait vit à deux endroits, quelque chose doit les confronter et **afficher l'écart comme une donnée**, pas le corriger en silence. C'est ce qui a rendu la forme n° 4 visible.

**Des assertions structurelles, pas volumétriques.** Un test qui vérifie « 2 rêves » crie à chaque dimanche 20 h et finit ignoré ; un test qui vérifie « chaque rêve couvre une période strictement positive » attrape la vraie erreur. **Un test qui crie à chaque usage nominal est un test qu'on désarme.**

**Rendre bruyant ce qui est muet.** Un `except` qui avale une erreur et rend une liste vide produit un module qui affiche « 0 » en ayant l'air correct. Journaliser la cause coûte une ligne.

**Tester la propriété, jamais la lire.** Pour une garantie de sécurité : tenter l'action interdite et vérifier qu'elle échoue. Un fichier témoin qu'on essaie d'écrire, un `curl` qui doit être bloqué, un `DELETE` qui doit être refusé par un trigger.

## Ce que la forme n° 5 ajoute aux quatre autres

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
