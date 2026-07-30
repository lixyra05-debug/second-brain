---
type: resource
tags: [ia, agents, automation]
cree: 2026-07-30
source: https://github.com/andrewyng/openworker
---

# OpenWorker — agent open-source model-independent

## Source
- Code source : https://github.com/andrewyng/openworker
- Site : https://openworker.com/
- Annonce (Andrew Ng, 2026-07-23) : https://x.com/i/status/2080333504446108104

## Résumé (5 lignes max)
Agent open-source qui tourne en local (macOS, Windows à venir) et produit un **livrable fini** plutôt que du chat : document, message Slack, entrée d'agenda, à partir de tes fichiers et outils du quotidien.
**Indépendant du modèle** : BYO-API-key, fonctionne avec des modèles fermés (GPT, Claude, Gemini), open-weight (Kimi, GLM, DeepSeek…) ou en local via Ollama.
Garde-fou intégré : l'agent **demande confirmation avant toute action conséquente**.
Local-first : les données ne quittent la machine que via le provider LLM et les intégrations explicitement choisies.

## Points clés
**Patterns d'archi réutilisables (au-delà de la promesse produit) :**

- **Abstraction du modèle derrière une interface BYO-key.** Le moteur d'agent est découplé du provider : un même agent tourne sur cloud fermé, open-weight, ou Ollama local. Pattern d'indépendance au modèle à lever tel quel.
- **Human-in-the-loop comme garde-fou par défaut.** Confirmation explicite avant les actions à effet de bord (envoi, écriture, modification). Modèle de sécurité pour un agent qui agit vraiment sur des outils.
- **Local-first / minimisation de la surface de données.** Rien ne sort de la machine sauf via le provider LLM et les connecteurs choisis un par un — frontière de confiance explicite.
- **Orientation livrable, pas conversation.** L'agent vise un artefact fini et agit sur les outils (fichiers, Slack, agenda) au lieu de rendre du texte à copier-coller.
- **Base open-source** inspectable et forkable : on peut lire l'orchestration réelle plutôt que déduire d'une démo.

**Pourquoi pour moi :** deux briques directement transposables à [[_autonomous]] — la couche d'abstraction provider (BYO-key, cloud/local interchangeables) et le gate de confirmation human-in-the-loop avant action conséquente. À étudier comme référence d'archi, indépendamment de l'usage du produit.

## Liens
- [[_autonomous]]
