---
type: resource
tags: [ia, dev, automation]
cree: 2026-08-02
source: local — ~/dream-viewer (git init, aucun remote au 2026-08-02)
---

# DREAM CONSOLE — décisions et leçons

## Décisions produit

- **Direction visuelle VERRIÈRE, arbitrée sur pièces (2026-08-01).** Quatre directions produites, jugées sur trois axes indépendants — véracité de l'encodage, faisabilité 60 fps, tenue dans le temps. Deux gagnantes à un point d'écart, sur des axes opposés : VERRIÈRE tient la sensation de cockpit, ÉPHÉMÉRIDE tient la 200ᵉ ouverture. Choix de VERRIÈRE **avec la loi typographique d'ÉPHÉMÉRIDE greffée**.
- **Règle cardinale : chaque effet encode une donnée nommable.** Écartement des équerres = âge · durée de l'odomètre = âge · période du pointillé = 1 jour · fréquence de pulsation = degré entrant · position du curseur = phase du cycle d'un timer. Un effet dont on ne peut pas nommer la donnée est retiré.
- **2026-08-04 — la règle « chaque effet encode une donnée nommable » est SUSPENDUE sur l'écran d'accueil, et seulement là.** Décision de conception d'Hector à la v3, pas un oubli ni une dérive : l'accueil se regarde avant de se lire, la beauté d'abord et les données derrière un clic. La règle reste **entière et non négociable dans les sept vues de module**, qui n'ont pas bougé. La frontière est nette et vérifiable : si un chiffre dense apparaît sur l'accueil, c'est une régression ; s'il apparaît un effet non nommable dans un module, c'en est une aussi.
- **Le rouge est plafonné à cinq étiquettes** dans toute l'application, réservées aux conséquences réelles qui tournent pendant l'indécision. La divergence des magasins Hermes n'en prend pas : c'est une mesure contradictoire, pas une décision non prise.
- **Le bandeau BOUCLES ne se replie jamais.** Demande explicite d'Hector rendue mécanique plutôt que graphique : on ne peut pas fermer cette fenêtre-là.
- **HERMES et ATLAS en onglets, pas en 7ᵉ et 8ᵉ modules.** Atlas est une skill d'Hermes, une seule sonde les alimente, et la réglure repliée reste lisible à six lignes.

## Décisions techniques

- **La loi typographique est un canal de donnée.** mono = ce que la machine a écrit littéralement · sans = ce qu'elle a mesuré · **sérif = ce qu'un humain a écrit**. Le vault ne contenant aucune note de la main d'Hector, le sérif n'apparaît que dans un cadre, et ce cadre est vide : *la proportion de sérif à l'écran EST la mesure du Miroir*, en permanence, sans qu'aucun graphique ait à l'énoncer.
- **Un axe unique de 30 jours partagé par les cinq modules du vault** — c'est ce qui fait une console et non cinq widgets. Le module HERMES a sa propre échelle (la minute) et **le dit à l'écran** : aucun `x` ne ment sur son échelle.
- **Layout du graphe figé au build**, simulation déterministe à graine fixe : la carte ne change pas d'un rechargement à l'autre, on l'apprend. Seules les pulsations sont animées.
- **Âge = date écrite dans `## Statut`, jamais le mtime du fichier.** Un fichier touché n'est pas un statut rafraîchi.
- **Compteurs vivants** : l'âge d'une boucle est recalculé depuis son origine à chaque rendu, jamais recopié du chiffre figé par le rêve.
- **Collecteur en stdlib Python seule** : rien n'a été installé sur le VPS. Routeur `GET` uniquement, écoute sur `127.0.0.1`, filtres par liste close, **aucune requête sur les tables FTS** — elles contiennent le texte intégral des conversations.
- **Escalade de garde par base** : `title` est un résumé de conversation dans `state.db` (interdit) et un nom public de marché dans `atlas.db` (anodin). Un garde global refusait le second à cause du premier.

## Leçons durcies

- **Une propriété de sécurité non testée n'existe pas.** Une unité `systemctl --user` accepte `ProtectHome=read-only` et `IPAddressDeny=any`, les stocke dans ses propriétés — et **ne les applique pas** sur cet hôte (Ubuntu 24.04, `kernel.apparmor_restrict_unprivileged_userns=1`). Le fichier témoin a été créé, `curl` est sorti en HTTP 302, et systemd le journalise lui-même : *« unit configures an IP firewall, but not running as root »*. Le service aurait tourné, répondu, et n'aurait été cloisonné par rien. **Corrigé en unité SYSTÈME avec `User=veillebot`, sur preuve.**
- **La lecture seule se garantit par l'OS, pas par une convention** — mais elle se vérifie par un test d'écriture, pas par une lecture de fichier de configuration.
- **Une exception au cloisonnement se mesure.** SQLite exige un handle en écriture sur le `-shm` d'une base WAL, même en `mode=ro` : invisible sur `state.db` (le gateway tient le `-shm` ouvert), bloquant sur `atlas.db`. Une seule ligne concédée (`ReadWritePaths` sur un dossier), puis **mesurée** : md5 des bases identiques après 4 cycles de lecture, mtime inchangé, aucun descripteur laissé ouvert.
- **Un `except` muet qui rend une liste vide est un module qui affiche « 0 » en ayant l'air correct.** C'est ce qui a caché un `CANTOPEN` pendant deux itérations. Rendu bruyant.
- **Un service qui répond `200` sur `/health` en servant un `/snapshot` cassé est une panne silencieuse.** Le collecteur construit donc un instantané **complet** avant d'ouvrir le port, et meurt bruyamment sinon.
- **Un filtre trop large est un filtre qu'on finit par désarmer.** Le garde anti-fuite a bloqué `reasoning_tokens` — une métadonnée légitime — parce que le motif `reasoning` n'avait pas de limite de mot.
- **Ne jamais parser un tableau aligné à droite.** Le parsing de `systemctl list-timers` rendait « 0 timer » sur 20 sans lever la moindre erreur. Remplacé par `--output=json`.
- **Mesurer la taille de MISE EN PAGE, pas la taille visuelle.** Le canvas du graphe se dimensionnait via `getBoundingClientRect()` pendant une animation FLIP, qui renvoie la taille *transformée* ; `ResizeObserver` ne se redéclenche jamais ensuite puisqu'un transform ne change pas la boîte. Le graphe peignait hors champ, sans erreur. `clientWidth`/`clientHeight` corrigent.
- **Une assertion doit porter sur un invariant, pas sur un volume.** `dreams.length === 2` aurait transformé chaque dimanche 20 h — quand le rêve automatique tire — en fausse alerte.
- **Le dossier ne prouve pas l'auteur.** Une capture de `00-Inbox/` peut avoir été écrite par l'agent : le module MIROIR a compté une note de l'agent comme mémoire humaine avant correction. La console ne revendique plus que ce qu'elle sait ; seul le rêve tranche qui a tenu la plume.
- **Une couche brillante posée sur une sphère aura toujours deux silhouettes.** La sphère de l'accueil a été reprise trois fois, chaque fois jugée sur capture : une ceinture équatoriale distincte, vue par la tranche, accumule toute sa profondeur sur quelques pixels à gauche et à droite — en mélange additif, deux nœuds éblouissants, et l'élargir ne fait que grossir les nœuds. Corrigé en gradant la **densité et la couleur d'un seul nuage** : l'emphase est dans la matière, pas par-dessus, et l'accumulation se répartit tout autour du limbe.
- **Une irrationalité multipliée par un rationnel redevient presque rationnelle.** L'angle d'or multiplié par 2,4 donne un pas de 0,9167 tour — presque 11/12 : la bande se refermait tous les douze points et les amassait en douze paquets. L'angle d'or **seul**, jamais mis à l'échelle.
- **Un canvas dimensionné par son attribut fait grossir son parent.** `setSize(w, h)` sans `updateStyle = false` donne au canvas une taille intrinsèque ; le parent s'élargit pour l'accueillir, le `ResizeObserver` le remesure, et la largeur s'emballe jusqu'à 2^24 px — sans la moindre erreur. Le CSS dimensionne, `setSize` ne touche que le tampon de dessin.
- **Un fond translucide laisse passer sa propre bordure.** Le cadre à coins coupés se fait de deux calques `clip-path` superposés ; le calque du dessous est un rectangle PLEIN de la couleur du filet. À 0,32 d'opacité, le cadre virait au bloc turquoise.
- **Un test qui dépend de la vitesse d'un import est un test qui criera un jour sur du code correct.** Le contrôle de mouvement réduit échouait par intermittence : le premier échantillon tombait parfois sur un canvas non encore peint. Il attend désormais que la sphère soit dessinée avant d'échantillonner.
- **Un tir réel trouve ce qu'aucun test synthétique ne trouve.** Le premier rêve automatique (02/08, 20 h) a révélé trois défauts d'un coup, dont une heure extraite d'une citation entre parenthèses qui faussait une période de 3 h 30.

## Liens

- [[_dream-console]]
- [[01-Projects/dream-console/fiche-technique|fiche-technique]]
- [[contexte-hector]] — « dead-man's switch externe : zéro panne *silencieuse*, pas zéro panne »
- [[01-Projects/hermes-polymarket/decisions-et-lecons|hermes — decisions-et-lecons]] — « winrate n'est pas edge », appliquée telle quelle dans le panneau ATLAS
