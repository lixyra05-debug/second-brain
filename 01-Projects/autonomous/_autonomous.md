---
type: project
statut: actif
tags: [ia, business, automation]
cree: 2026-07-12
---

# Autonomous

## Objectif
SaaS outbound B2B — une URL d'entreprise → 50 prospects qualifiés + emails personnalisés (autonomous.coldsend.app).

## Statut
Au 2026-07-12 : **premier run Trigger.dev SUCCESS en prod confirmé**, après une double panne infra (image jamais transférée sur le VPS + env store silencieusement vide). Monitoring UptimeRobot actif (3 monitors). Prochaine étape : recharge Explorium → test E2E sur stripe.com → DM 5 warm leads. Clarification SIRET/SEPA en attente pour encaisser.

## Next actions
- [ ] 🔴 **La pile Trigger.dev tourne parfaitement et n'exécute plus rien du métier depuis trois mois — 14,1 Go de volumes pour ça.** Mesuré le 2026-08-23 en base, pas au statut. **49 118 runs depuis le 07/05, ~456 par jour, et sur les 7 derniers jours : 3 024 runs, tous `COMPLETED_SUCCESSFULLY`, zéro échec.** Le problème n'est pas la santé de la pile — c'est ce qu'elle exécute :

    | tâche | runs 7 j | total | dernier run |
    |---|---|---|---|
    | `dispatch-scheduled-sends` | 2 016 (une / 5 min) | 32 726 | **maintenant** |
    | `poll-batch` | 1 008 (une / 10 min) | 16 341 | **maintenant** |
    | `send-email` | 0 | 12 | 2026-05-13 |
    | `find-prospects` | 0 | 11 | **2026-05-21** |
    | `analyze-icp` | 0 | 10 | **2026-05-21** |
    | `orchestrate-campaign` | 0 | 8 | **2026-05-21** |
    | `generate-emails` | 0 | 8 | **2026-05-21** |

  **Les deux seules tâches vivantes sont l'ordonnanceur qui se réveille pour ne rien trouver.** Les quatre tâches qui *sont* le produit — `analyze-icp`, `find-prospects`, `generate-emails`, `orchestrate-campaign` — n'ont pas tourné depuis le **21/05**. Le `## Statut` du 12/07 (« premier run SUCCESS en prod ») reste vrai et n'a jamais eu de suite.
  - **Ce que ça coûte, mesuré** : **14,10 Go de volumes** (ClickHouse **8,8 Go** de télémétrie, Postgres **4,7 Go**) + **6,6 Go d'images**. Dans Postgres, `TaskEvent` pèse **1,4 Go pour 921 198 lignes** et `TaskRunExecutionSnapshot` 278 Mo — **c'est la trace de 49 000 exécutions qui n'ont rien produit.** S'y ajoutent **5 799 conteneurs `runner-*` arrêtés** jamais nettoyés, le plus vieux datant de 13 jours.
  - **Et ce n'est PAS récupérable par du ménage** : `docker system df` donne **0 B réclamable sur les volumes** (ils sont attachés à des conteneurs vivants). Un `docker system prune` ne rendrait que ~255 Mo (231,6 Mo d'images + 23,7 Mo de conteneurs). **Les 14,1 Go ne se libèrent que par une décision sur la pile elle-même**, pas par un nettoyage.
  - **Le lien avec l'urgence du serveur** : le disque sature vers le **27/08** ([[_hermes-polymarket]]), et **Docker est la seule réserve assez grosse pour débloquer `bot3_trim.py`**, dont la garde d'espace exige 23,3 Go libres. Libérer les caches ne suffit pas ; libérer Docker, oui.
  - **Rapport seul, aucune action prise** — consigne d'Hector du 23/08. La question posée était « est-ce que je paie 14 Go pour une pile morte ? » : **la pile est vivante, c'est le produit au-dessus qui est à l'arrêt.** *(À noter : `trigger-electric-1`, arrêté le 03/08, n'a rien à voir — il ne portait que l'API Realtime, voir [[01-Projects/autonomous/fiche-technique|fiche-technique]].)*
- [ ] Recharge Explorium
- [ ] Test E2E sur stripe.com
- [ ] DM 5 warm leads
- [ ] Clarification SIRET/SEPA (pour encaisser)

## Décisions
- **2026-08-03 — Clarification SIRET/SEPA : REPORTÉE AU 2026-09-15.** Décision d'Hector, datée et assumée — **pas un oubli**. Motif : la rentrée Ynov (septembre 2026) est prioritaire, et l'encaissement n'est pas bloquant avant cette date. La boucle est **fermée jusqu'au 15/09** ; elle doit ressortir à partir de là, et elle reste la seule du vault qui conditionne un encaissement réel.
- 2026-07-12 — Projet entré au vault suite à [[inventaire-projets]] : il n'avait ni pivot ni mention dans [[contexte-hector]] (corrigé le même jour).

## Ressources liées
- [[01-Projects/autonomous/fiche-technique|fiche-technique]] — stack, architecture, features, état de prod, pointeurs
- [[01-Projects/autonomous/decisions-et-lecons|decisions-et-lecons]] — décisions produit/tech et leçons durcies

## Journal du projet
### 2026-07-12
- Pivot créé + fiche technique + décisions/leçons via [[documenter-un-projet]] (repo `~/autonomous`, lecture seule, aucun code copié). Log : `AI/logs/2026-07-12-session.md`.
