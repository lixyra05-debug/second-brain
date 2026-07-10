---
type: resource
tags: [automation, dev]
cree: 2026-07-10
source: interne — pilote LegitVision du 2026-07-10
---

# Runbook — Documenter un projet construit hors du vault

Importer dans le vault la **connaissance** d'un projet dont le code vit ailleurs (repo local ou VPS) : fiche technique + décisions/leçons, pointeurs plutôt que copies (critère evergreen). Validé sur LegitVision, appliqué à Vivo (2026-07-10).

## 1. Localiser le repo
- Chercher : racine du home, `~/Documents`, `~/Downloads`, `~/dev`, `~/projects` — `find ~ -maxdepth 2 -iname "*<nom>*" -type d`.
- Plusieurs candidats → les départager soi-même (`git log -3`, champ `name` du package.json, structure) ; ne demander à Hector que si l'ambiguïté est réelle. Attention aux satellites (`legitvision` vs `legitvision-ads`).
- Repos distants (VPS) : ssh en **lecture seule**, session dédiée.

## 2. Lecture SEULE — rien modifier, rien commiter dans le repo
- Commandes autorisées : `ls`, `find`, `cat`/Read, `head`, `grep`, `git log/remote/status`.
- **Ne jamais lire les secrets** (`.env*`, credentials) : inutiles au vault.
- Si `git status` montre des changements locaux préexistants : les signaler à Hector, ne pas y toucher.

## 3. Quoi lire
1. `CLAUDE.md` du repo — souvent LA doc (le README est fréquemment resté boilerplate).
2. `README`, PRD, rapports d'audit/QA s'ils existent.
3. Manifeste : `package.json` (+ `app.json`/`eas.json` pour Expo, `pyproject.toml` pour Python…) ; le lockfile dit le gestionnaire de paquets.
4. Structure : `find . -maxdepth 2 -type d` hors `node_modules`/`.next`/`.git`/`ios`/`android`.
5. Schéma DB : `supabase/migrations/` (les noms des fichiers racontent l'histoire) + Edge Functions.
6. `git log --oneline` **complet** (les messages parlants nourrissent décisions-et-leçons) + `git remote -v`.
7. Config de déploiement : `vercel.json`, `.vercel/project.json`, `eas.json` → URL de prod, identifiants projet.

## 4. Produire 2 notes dans `01-Projects/<projet>/`
- **`fiche-technique.md`** — sections : Stack · Architecture (5-10 lignes) · Features principales · État de prod · Pointeurs (URL prod, chemin du repo local, GitHub, docs internes du repo) · Liens.
- **`decisions-et-lecons.md`** — Décisions produit · Décisions techniques · Leçons durcies : 1 à 3 lignes chacune, sourcées par le hash de commit quand il parle.
- Frontmatter : `type: resource` · `source:` = URL GitHub · tags du vocabulaire fermé · `cree:`.
- **Zéro code copié** : connaissance + pointeurs ; la doc vivante reste dans le repo, la fiche pointe dessus. Croiser le CLAUDE.md du repo avec le git log — il peut être en retard sur les derniers commits.
- ⚠️ `fiche-technique` / `decisions-et-lecons` se répètent d'un projet à l'autre : toujours des **liens qualifiés par le chemin** — `[[01-Projects/<projet>/fiche-technique|fiche-technique]]`.

## 5. Mettre à jour la note pivot `_<projet>.md`
- « Ressources liées » : les 2 notes (liens qualifiés).
- « Journal du projet » : entrée datée (import + référence du log de session). Ajouts seuls (R2).

## 6. Clore (R4)
- Log de session : repo lu (et candidats écartés), notes créées, mention explicite « aucune modification du repo ».
- Commit vault : `projets: fiche <projet> (import)`.

## Liens
- [[01-Projects/legitvision/fiche-technique|fiche-technique (LegitVision)]] · [[01-Projects/vivo/fiche-technique|fiche-technique (Vivo)]]
- [[_second-brain]]
