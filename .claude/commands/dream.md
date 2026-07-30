---
description: Consolidation de mémoire du vault — lit tout, ne modifie rien, produit une proposition qu'Hector arbitre
---

# /dream — consolidation de mémoire

Le rêve **lit tout le vault et n'y touche pas**. Il produit une note de proposition qu'Hector arbitre section par section. C'est un miroir, pas une main.

## Périmètre — inviolable

- **Lecture seule.** Aucune note existante n'est modifiée, déplacée, renommée ni archivée pendant un rêve. Les seuls fichiers écrits sont la note de rêve et le log de session (R4).
- **Le vault uniquement** — `~/Documents/second-brain`. **Jamais** `~/.claude/projects/`, **jamais** le VPS (aucun `ssh`), **jamais** un autre dossier du Mac, **même en lecture**. Un rêve ne sort pas du vault.
- **Zéro application automatique** (R3). Le rêve s'arrête à la proposition. L'application se fait ensuite, sur GO d'Hector, section par section — dans une session normale, pas ici.
- **Zéro réécriture des mots d'Hector** (R2) : le rêve signale une dérive et pointe la source fraîche ; il ne rédige pas le nouveau Statut à sa place.

## 1. Lire

Dans cet ordre :

1. `AI/dreams/` — le dernier rêve, s'il existe : **sa date borne le « depuis »** du Digest. Aucun rêve antérieur → la période court du premier log au jour même.
2. `AI/logs/*` — tous les logs de la période, **en entier**. C'est la source de vérité de ce qui s'est passé (un même fichier peut contenir plusieurs sessions du même jour : les lire toutes).
3. `00-Inbox/*` — chaque capture **en entier** : c'est le tampon, il est court, il se lit à fond.
4. `05-Journal/*` — les dailies de la période.
5. `01-Projects/*/_*.md` — **toutes** les notes pivot, en entier : frontmatter (`statut`), date de l'instantané dans `## Statut`, `## Next actions`, `## Décisions`, `## Journal du projet`.
6. `03-Resources/**` et `02-Areas/**` — **titres et frontmatter seulement** (`type`, `tags`, `cree`, `source`). On cherche des rapprochements, pas à relire la matière.
7. `AI/index/`, `AI/runbooks/`, `AI/contexte-hector.md` — pour croiser ce que le vault **croit** savoir avec ce que les logs **racontent**.

## 2. Écrire la note de rêve

Un seul fichier : `AI/dreams/YYYY-MM-DD-dream.md` (date du jour). Frontmatter `type: log` · `tags: []` · `cree: <date du jour>`. Cinq sections, dans cet ordre, et rien d'autre.

### Digest
Ce qui s'est passé depuis le dernier rêve, **10 lignes maximum**, une ligne par jour actif (`JJ/MM — ce qui a bougé`). Les jours sans trace comptent aussi : signaler explicitement les fenêtres de silence.

### Statuts périmés
Tout pivot dont l'instantané `## Statut` est daté de **plus de 7 jours**, **ou** que les logs contredisent (quel que soit son âge). Pour chacun : le pivot, la date de l'instantané, son âge en jours, et **ce qui le contredit** — en citant le log et sa date. Un pivot `pause` daté n'est périmé que par la date, pas par le sens : le dire. Un pivot frais est mentionné comme tel en une ligne (c'est une information, pas du remplissage).

### Consolidations proposées
Trois familles, **une ligne de justification chacune** :
- **Inbox** — chaque capture : promouvoir vers `03-Resources/` (evergreen), classer, ou archiver vers `04-Archive/` (R1 : jamais supprimer).
- **Doublons et incohérences** — paires de notes redondantes à fusionner, vocabulaire divergent entre deux notes, champ qu'une note dément elle-même. **Si aucun doublon franc n'existe, l'écrire** plutôt que d'en inventer un.
- **Boucles ouvertes** — décision annoncée jamais tranchée, next action gelée, item de backlog gaté depuis longtemps : depuis quand, et sur quoi il bloque.

### Graines de wiki
**2 à 3** pages `03-Resources/wiki/` qui émergent de ce qui existe déjà. Pour chacune : le titre proposé, les **notes sources** (liens `[[…]]`), et une phrase sur ce qui les relie. Critère : le sujet doit revenir dans **au moins trois notes indépendantes** — sinon ce n'est pas une graine, c'est une idée. **Ne créer aucune page** : la graine reste une proposition.

### Miroir
**Deux lignes factuelles** sur l'équilibre mémoire machine / mémoire humaine de la période. Des nombres, pas des adjectifs : n notes, n logs, n commits, n dailies, n captures, et qui les a écrites. Aucun jugement, aucun conseil.

## 3. Clore

1. **Rien d'autre n'a été écrit.** Vérifier : `git status` ne doit montrer que la note de rêve et le log de session.
2. Log de session `AI/logs/YYYY-MM-DD-session.md` (R4) : notes lues, rêve créé, décisions en attente de GO.
3. Restituer à Hector **en chat** : les statuts périmés et les consolidations, en une ligne chacun, puis **STOP**. Il arbitre section par section — le rêve n'applique jamais rien de lui-même.
