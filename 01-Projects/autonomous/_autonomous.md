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
