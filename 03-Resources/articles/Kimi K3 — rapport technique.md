---
type: resource
tags: [ia, dev]
cree: 2026-07-30
source: https://github.com/MoonshotAI/Kimi-K3/blob/master/k3_tech_report.pdf
---

# Kimi K3 — rapport technique

## Source
- **Rapport technique** (l'artefact evergreen) : https://github.com/MoonshotAI/Kimi-K3/blob/master/k3_tech_report.pdf
- Poids : https://huggingface.co/moonshotai/Kimi-K3
- Blog d'annonce : https://kimi.com/blog/kimi-k3
- Capturée le 2026-07-30 pendant le premier run de veille Agent Reach ([[veille-a-la-demande]]), promue en fiche le 2026-07-31 (arbitrage du rêve du 31/07).

## Résumé (5 lignes max)
Kimi K3 (Moonshot) : MoE 2,8 T, contexte 1 M tokens, archi « 2,5× d'intelligence par unité de calcul », + kernels d'attention et infra pour environnements d'agents à l'échelle. L'**annonce** n'est pas evergreen ; le **rapport technique** peut l'être. À lire si un jour agents locaux.

## Points clés
- **MoE 2,8 T, contexte 1 M tokens** — le contexte long est ce qui compte ici : c'est lui qui décide si un agent peut porter un vault entier ou un repo entier en mémoire de travail.
- **« 2,5× d'intelligence par unité de calcul »** — claim constructeur, à vérifier dans le rapport avant d'en faire quoi que ce soit.
- **Kernels d'attention + infra pour environnements d'agents à l'échelle** — c'est la partie réutilisable : comment on sert un modèle d'agent en continu, pas le score sur un benchmark.
- **Lien direct avec le cockpit Hermes**, qui tourne aujourd'hui sur Kimi-K2.5 via OpenRouter ([[contexte-hector]]) : K3 est la génération suivante de la famille déjà en production chez Hector.
- **Filtre appliqué au run du 30/07** : l'annonce et la vague de réactions ont été écartées ; seul le rapport technique a passé le critère evergreen.

## Liens
- [[_hermes-polymarket]] — le cockpit Hermes tourne sur Kimi-K2.5 (OpenRouter)
- [[contexte-hector]] — stack IA & agents
- [[MCP 2026-07-28 — cœur stateless]] — même veille, même question : ce qu'il faut à un agent pour tenir à l'échelle
- [[veille-a-la-demande]] — le runbook qui a produit cette capture
