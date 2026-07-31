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

1. `AI/dreams/` — **tous les rêves précédents**, pas seulement le dernier. Le plus récent **borne le « depuis »** du Digest (aucun rêve antérieur → la période court du premier log au jour même). Les autres servent à la section « Boucles non fermées » : il faut savoir ce qui a déjà été proposé pour savoir ce qui n'a pas été tranché.
2. `AI/logs/*` — tous les logs de la période, **en entier**. C'est la source de vérité de ce qui s'est passé (un même fichier peut contenir plusieurs sessions du même jour : les lire toutes).
3. `00-Inbox/*` — chaque capture **en entier** : c'est le tampon, il est court, il se lit à fond.
4. `05-Journal/*` — les dailies de la période.
5. `01-Projects/*/_*.md` — **toutes** les notes pivot, en entier : frontmatter (`statut`), date de l'instantané dans `## Statut`, `## Next actions`, `## Décisions`, `## Journal du projet`.
6. `03-Resources/**` et `02-Areas/**` — **titres et frontmatter seulement** (`type`, `tags`, `cree`, `source`). On cherche des rapprochements, pas à relire la matière.
7. `AI/index/`, `AI/runbooks/`, `AI/contexte-hector.md` — pour croiser ce que le vault **croit** savoir avec ce que les logs **racontent**.

## 2. Écrire la note de rêve

Un seul fichier : `AI/dreams/YYYY-MM-DD-dream.md` (date du jour). Frontmatter `type: log` · `tags: []` · `cree: <date du jour>`. **Six sections**, dans cet ordre, et rien d'autre.

⚠️ **Si un rêve du jour existe déjà, ne jamais l'écraser** (R1) : y **ajouter** un bloc `---` puis `## Rêve N (même jour)` contenant les six sections, exactement comme les logs empilent leurs sessions. Un rêve déjà arbitré est une trace, pas un brouillon à remplacer.

### Digest
Ce qui s'est passé depuis le dernier rêve, **10 lignes maximum**, une ligne par jour actif (`JJ/MM — ce qui a bougé`). Les jours sans trace comptent aussi : signaler explicitement les fenêtres de silence.

### Boucles non fermées
**Le rêve rend d'abord des comptes sur lui-même.** Reprendre les propositions de **tous les rêves précédents** et dire, pour chacune, ce qu'elle est devenue :

- **Appliquée** — le vault le montre. Vérifier dans les fichiers, pas seulement dans un log qui l'annonce.
- **Écartée** — Hector a tranché non, et un log ou une note le dit. Une décision explicite ferme la boucle, même négative.
- **Sans réponse** — aucune trace ni dans un sens ni dans l'autre. **C'est la seule catégorie qui compte vraiment** : la proposition dort.

Format : une ligne par proposition — `[rêve du JJ/MM] la proposition — <état>, N jours`. **N = jours écoulés depuis le rêve qui l'a émise**, pas depuis la dernière mention. Grouper par état, et mettre « sans réponse » en premier, du plus ancien au plus récent.

Si tout a été arbitré, l'écrire en une ligne — c'est un bon résultat, pas une section vide. S'il n'existe aucun rêve antérieur, écrire « premier rêve, rien à reprendre ».

*(À ne pas confondre avec « Boucles ouvertes » dans les Consolidations : celles-là sont les décisions non tranchées **du vault** ; celles-ci sont le passif **du rêve lui-même**.)*

### Statuts périmés
Tout pivot dont l'instantané `## Statut` est daté de **plus de 7 jours**, **ou** que les logs contredisent (quel que soit son âge).

**Exception — les pivots `pause`.** Un projet en pause est censé ne pas bouger : sa date qui vieillit n'est pas une dérive, c'est le fonctionnement normal. **Ne jamais le signaler sur le seul critère de la date.** Il n'entre dans cette section que si **les logs le contredisent** — activité constatée, commit, décision qui le réveille. *(Leçon du rêve du 2026-07-31 : `remedes-naturels` y figurait à 19 jours alors que son statut `pause` était juste et acté ; faux positif, ne pas répéter.)*

Pour chaque pivot retenu : le pivot, la date de l'instantané, son âge en jours, et **ce qui le contredit** — en citant le log et sa date. Un pivot frais est mentionné comme tel en une ligne (c'est une information, pas du remplissage).

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
3. Restituer à Hector **en chat** : les boucles sans réponse, les statuts périmés et les consolidations, en une ligne chacun, puis **STOP**. Il arbitre section par section — le rêve n'applique jamais rien de lui-même.
4. **Ne pas commiter.** Le rêve laisse la note et le log non suivis : c'est volontaire. Hector relit, arbitre, et c'est la session d'application qui commite. Un `git status` non vide est le rappel qu'un rêve attend une lecture.

## En exécution automatique (hebdomadaire)

Lancé par launchd le dimanche à 20 h via `claude -p "/dream"` — voir [[dream]] dans `AI/runbooks/`. Dans ce contexte il n'y a **pas de chat** : le point 3 ci-dessus n'a personne à qui parler, et c'est la notification macOS qui prévient qu'un rêve attend. Tout le reste est identique — mêmes lectures, mêmes six sections, même interdiction d'appliquer quoi que ce soit. **Un rêve automatique n'a pas plus de droits qu'un rêve manuel.**
