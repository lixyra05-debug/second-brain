---
type: resource
tags: [ia, agents, dev]
cree: 2026-07-30
source: https://modelcontextprotocol.io/specification/2026-07-28
---

# MCP 2026-07-28 — cœur stateless

## Source
- Spec officielle : https://modelcontextprotocol.io/specification/2026-07-28
- Release notes protocole : https://blog.modelcontextprotocol.io/posts/2026-07-28/
- Annonce Anthropic : https://claude.com/blog/bringing-mcp-2026-07-28-to-claude

## Résumé (5 lignes max)
La plus grosse révision de MCP depuis le lancement : le protocole passe d'un modèle bidirectionnel **à état** à un modèle **requête/réponse stateless**.
Conséquence : un serveur MCP devient un simple workload HTTP, scalable horizontalement (serverless/edge, load balancer sans affinité de session), au prix de la suppression des sessions et des flux bidirectionnels.
L'auth s'aligne sur les déploiements OAuth 2.0 / OIDC de production (connexion directe aux IdP entreprise type Entra/Okta).
Le transport HTTP+SSE hérité et la Dynamic Client Registration sont dépréciés, avec un offramp d'environ 1 an.
Non rétro-compatible pour tout ce qui dépendait des identifiants de session.

## Points clés
**Ce qui change pour qui construit un serveur MCP aujourd'hui :**

- **Archi — fin de l'état de session.** Suppression du handshake `initialize`/`initialized` et du header `Mcp-Session-Id`. Chaque requête est auto-portée : version du protocole, identité client et capacités voyagent dans `_meta`. La négociation de capacités obligatoire devient un `server/discover` optionnel.
- **Archi — plus de flux bidirectionnels.** Les requêtes initiées par le serveur (sampling, elicitation, roots) via streams tenus ouverts disparaissent. Remplacées par les **Multi Round-Trip Requests (MRTR)** : le serveur renvoie `resultType: "input_required"` avec les entrées nécessaires, le client relance avec `inputResponses`.
- **Déploiement.** Serveur = workload HTTP first-class, déployable en serverless/edge, derrière un load balancer round-robin **sans stockage de session partagé**. L'état applicatif doit passer par des **handles explicites** renvoyés par les tools (repassés en arguments par le modèle), jamais par la couche transport.
- **Headers requis** pour le streamable HTTP : `Mcp-Method` et `Mcp-Name`.
- **Auth durcie.** Validation de l'issuer avant échange du code d'autorisation (RFC 9207) ; paramètre `application_type` requis à l'enregistrement (autorise les redirects localhost pour desktop/CLI) ; credentials liés à l'issuer, pas de réutilisation cross-serveur d'autorisation. **DCR (Dynamic Client Registration) formellement déprécié** au profit des **Client ID Metadata Documents (CIMD)** — fenêtre de compat ≥ 12 mois.
- **Fonctions à état = extensions optionnelles versionnées**, qui héritent du cœur stateless : **Tasks** (`io.modelcontextprotocol/tasks`, poll `tasks/get` + `tasks/update`, notifs via `subscriptions/listen`), **MCP Apps** (UI dans la conversation), **Enterprise Managed Auth**.
- **Migration.** Ruptures : dépendances aux session-IDs et aux flux bidirectionnels à refactorer. Roots/Sampling/Logging dépréciés (support ≥ 12 mois, à ne pas adopter en neuf). SDK Tier 1 (TypeScript, Python, Go, C#) déjà à jour avec guides ; Rust en beta. Checklist : retirer `Mcp-Session-Id` → embarquer les métadonnées client dans chaque `_meta` → remplacer serveur→client par MRTR → ajouter `Mcp-Method`/`Mcp-Name` → passer l'auth en RFC 9207 + CIMD.

**Effet direct sur mes projets :** tout serveur MCP maison exposé côté [[_hermes-polymarket]] devra perdre l'état de session, adopter MRTR et migrer l'auth OAuth avant la fin de l'offramp HTTP+SSE (~1 an). À planifier tant que l'ancien transport tourne encore.

## Liens
- [[_second-brain]]
- [[_hermes-polymarket]]
