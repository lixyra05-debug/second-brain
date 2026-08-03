---
type: resource
tags: [trading, agents, automation]
cree: 2026-08-03
source: VPS openclaw (ssh, lecture seule 2026-08-03) — relevé systemd + atlas.db
---

# Atlas — procédure de mise en pause (préparée, NON exécutée)

> **Rien n'a été exécuté.** Relevé en lecture seule le 2026-08-03 à 18:47 UTC. Hector arbitre ; les commandes ci-dessous sont écrites pour être copiées telles quelles, et chacune a son inverse.

## Les 4 unités concernées

| unité | déclenche | cadence | état au 03/08 |
|---|---|---|---|
| `copy-tracker.timer` | `run_tracker.sh -v` | **toutes les 15 min** | `enabled` · actif · dernier tir 18:45 |
| `atlas.timer` | `run_atlas.sh -v` | **toutes les 15 min**, 5 min après le tracker | `enabled` · actif · dernier tir 18:35 |
| `atlas-report.timer` | `run_atlas.sh --report` | **quotidien 18:00 UTC** | `enabled` · actif · dernier tir 18:00 |
| `atlas-validation-review.timer` | `run_atlas_review.sh -v` | **one-shot, déjà consommé** | `enabled` · `elapsed` · tiré **une fois** le 2026-07-17 18:30, **plus aucun tir prévu** |

**Le one-shot n'a plus d'effet.** `atlas-validation-review` a produit le verdict A/B le 17/07 et son `NEXT` est vide depuis. L'arrêter ne change rien au fonctionnement ; le désactiver le retire seulement de `list-timers`, où il sert aujourd'hui de rappel visible que l'arbitrage attend. **Le laisser tel quel a une valeur.**

## Trois granularités, parce que les deux boucles sont séparables

Le tracker **observe** (lecture seule des wallets de la shortlist), Atlas **copie en paper**. Arrêter l'un n'oblige pas à arrêter l'autre.

**A — Tout arrêter.** Plus d'observation, plus de copie, plus de rapport.
```bash
systemctl --user disable --now atlas.timer copy-tracker.timer atlas-report.timer
```

**B — Arrêter la copie, garder l'observation.** Atlas cesse d'ouvrir et de suivre des positions ; `tracking.db` continue d'accumuler les événements des wallets — la matière d'une future décision reste collectée.
```bash
systemctl --user disable --now atlas.timer atlas-report.timer
```

**C — Suspendre sans désarmer.** `stop` seul : les timers repartent au prochain redémarrage du gestionnaire ou du serveur. Utile pour une pause courte et surveillée, **dangereux pour une pause longue** — c'est le genre de pause qui se lève toute seule sans que personne le remarque.
```bash
systemctl --user stop atlas.timer copy-tracker.timer atlas-report.timer
```

**Réactivation, dans tous les cas :**
```bash
systemctl --user enable --now atlas.timer copy-tracker.timer atlas-report.timer
systemctl --user list-timers 'atlas*' 'copy-tracker*' --all   # contrôle
```

Si un cycle est en vol au moment de l'arrêt, ajouter : `systemctl --user stop atlas.service copy-tracker.service`.

## Ce qui est conservé — c'est-à-dire tout

Un `stop` ou un `disable` ne touche **aucune donnée**. Les trois bases restent intactes sur le disque :

| base | taille | contenu |
|---|---|---|
| `atlas.db` | 1,18 Mo | 248 positions, 1 076 décisions, l'état des traders suivis |
| `tracking.db` | 19,98 Mo | 25 k événements de wallets, les alertes |
| `candidates.db` | 0,07 Mo | la shortlist et ses candidats |

**État d'Atlas au moment du relevé** : **238 positions clôturées**, PnL cumulé **−15,18 USDC**, et **10 positions ouvertes pour 25,00 USDC engagés** — toutes en `paper`, la plus ancienne depuis le **2026-07-11** (23 jours).

**Aucune clé privée n'existe sur le serveur** — vérifié : Atlas est en observation pure, conformément à son `SKILL.md` v0.1.0. Une pause ne comporte donc **aucun risque financier**, ni dans un sens ni dans l'autre.

## Ce qu'une pause ne fait pas — et c'est le point à trancher

- **Elle ne clôture pas les 10 positions ouvertes.** Elles resteront ouvertes dans `atlas.db`, indéfiniment : c'est le `position monitor` d'Atlas qui les solde à la résolution des marchés, et il ne tournera plus. Le PnL affiché **cessera de bouger**, sans que ces 10 lignes soient jamais soldées. Il faudra le savoir en relisant les chiffres plus tard.
- **Elle n'arrête pas le Bot 1** (`polymarket_trader`), ni le Bot 3, ni le cockpit. Seules les 4 unités listées ci-dessus sont concernées.
- **Elle ne répond pas au verdict du 17/07.** Mettre en pause n'est pas *stop* : c'est *suspendre le keep par défaut*. La question keep / adjust / stop reste entière — mais elle cesse d'être tranchée par l'inaction, ce qui est déjà un changement.

## Le chiffre qui devrait peser dans l'arbitrage

Depuis le verdict du 17/07, **le non-choix a produit 140 clôtures de plus** — soit une fois et demie l'échantillon sur lequel le verdict a été rendu (92) — pour un PnL de cette période de **−0,55 USDC**, contre −17,14 sur les 92 premières. **Le verdict de juillet n'est plus la meilleure information disponible sur Atlas.**

Ce n'est pas un argument pour continuer : 140 clôtures sur 17 jours ne prouvent pas un edge, et « winrate n'est pas edge » est une leçon déjà inscrite ici. C'est un argument pour **rejuger sur les données d'aujourd'hui plutôt que sur celles d'il y a trois semaines**.

## Liens

- [[_hermes-polymarket]] — l'arbitrage Atlas y est en `## Next actions` depuis le 17/07
- [[01-Projects/hermes-polymarket/decisions-et-lecons|decisions-et-lecons]] — « winrate n'est pas edge »
- [[01-Projects/hermes-polymarket/fiche-technique|fiche-technique]] — cadences et architecture des 3 bots
- [[_dream-console]] — le panneau ATLAS affiche ces chiffres en direct
