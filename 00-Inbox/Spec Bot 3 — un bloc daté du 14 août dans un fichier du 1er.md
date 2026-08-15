---
type: permanent
tags: [idee, dev]
cree: 2026-08-03
---

# Spec Bot 3 — un bloc daté du 14 août dans un fichier du 1er

## Développement

`~/bot3_etape5_spec_2026-08-01.md` contient une section « VOLUMÉTRIE ET BACKUP » qui décrit `bot3.db` à **10,71 Gio**, deux archives datées des **13 et 14 août**, une ligne `report_daily` au **2026-08-14** et un backup « refusé ce matin » — alors que le fichier réel pesait **1,64 Go le 3 août** et que les seules archives présentes datent des 30 juillet et 2 août.

**Deux lectures possibles, non tranchées** : soit c'est un exemple travaillé écrit au futur pour illustrer la sortie du rapport quotidien, soit un maillon de la chaîne se date en avance — auquel cas les gardes qui s'appuient sur ces dates (rétention d'archives, projection du mur de disque, appariement par `.sha256`) raisonneraient sur un calendrier faux.

## Origine

Relevé le 2026-08-03 en lecture seule pendant l'arbitrage stockage du VPS ([[_dream-console]]). Les **chiffres de croissance** du même bloc (0,735 Gio/j, `trades` 52 % du fichier), eux, sont **corroborés par une mesure indépendante** — c'est la datation seule qui est en cause.

## Devenue vérifiable — ajout du 2026-08-15

Les dates du bloc sont désormais **passées** : l'anomalie, indécidable le 03/08, se tranche depuis le 15/08 en lecture seule sur le VPS — **comparer les archives et la ligne `report_daily` réellement produites les 13-14/08 au bloc de la spec**. Si les artefacts réels correspondent, le bloc était un exemple écrit au futur ; s'ils manquent ou diffèrent, un maillon se date en avance et les gardes cités plus haut raisonnent sur un calendrier faux. À trancher dans une session VPS (décision d'Hector du 15/08) ; la note reste en Inbox d'ici là. *(Ajout Claude.)*

## Liens

- [[_hermes-polymarket]] — Bot 3 y est le collecteur BTC-5m
- [[01-Projects/dream-console/decisions-et-lecons|dream-console — decisions-et-lecons]] — « une propriété de sécurité non testée n'existe pas »
- Même famille que la graine de wiki **« La panne silencieuse — quand le vert ne veut rien dire »** : ici ce n'est pas un vert qui ment, c'est une date.
