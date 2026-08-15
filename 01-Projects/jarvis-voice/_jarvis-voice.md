---
type: project
statut: actif
tags: [ia, agents, dev]
cree: 2026-08-15
---

# Jarvis Voice

## Objectif

Une conversation vocale en français **sous la seconde**, sur le MacBook Air M3 16 Go, **sans OpenAI** : STT `parakeet-tdt-0.6b-v3` → LLM Groq `llama-3.1-8b-instant` → TTS Pocket `french_24l` (voix `estelle`), sur le socle `huggingface/speech-to-speech`. La boucle est branchée sur le second brain **en lecture seule** : contexte distillé au lancement (profil `court`) + outil unique `chercher_vault`.

Découpage : **phase 1** — la latence (chantier des 11-13/08/2026, verdict : atteint, 954 ms) ; **phase 2** — l'intégration second brain : lot 1 contexte permanent, lot 2 recherche dans le vault, lot 3 Hermes.

## Statut

**Au 2026-08-15 — phase 2, lot 2 livré mais non validé à la voix.**
- **Phase 1 atteinte** : 954 ms médiane fin de parole → premier son (p95 2048 ms ; 60 tours, zéro erreur fournisseur), trajectoire 2396 → 1309 → 954 ms. Dix minutes de conversation réelle, latence non perceptible (verdict humain du 13/08). Le réseau vers Groq porte l'essentiel (445 ms sur 954) : plus rien à récupérer dans le code, le levier restant est géographique.
- **Lot 2** : outil `chercher_vault` (FTS5 en mémoire, 758 extraits, 0,31 ms) livré le 13/08 ; l'appel d'outil corrigé **à la racine** le 15/08 après deux pannes du correctif lui-même (n° 11 et 12 du recensement). Au banc : 13/14 appels corrects, 0/14 syntaxe prononcée, **0/14 syntaxe dans l'historique**, 6/6 rappels réels après un raté (contrefactuel sur historique contaminé : 0/6). **Le test vocal reste à faire** — les deux itérations précédentes sont tombées précisément là, jamais au banc.
- **La contrainte dominante n'est plus la latence, c'est le débit Groq** : 6 000 tokens/minute et **500 000 tokens/jour** (~90 réponses outillées) — mesurer et parler puisent au même seau. Un tour outillé coûte 2 474 tokens, soit 2,4 tours/minute ; 81 % de ce coût est de la redite (prompt système et schéma payés deux fois).
- **Arbitrage non tranché** : repasser au profil `nu` — le contexte permanent et l'outil font double emploi, 452 tokens récupérables par tour outillé — au prix de tours outillés supplémentaires (« qui suis-je » à deux allers-retours).
- Reste ouvert, assumé : la matrice A/B/E de la transcription live à re-mesurer (conflit de port corrigé) ; deux défauts audibles non bloquants (appels d'outil intempestifs 2/6 — resserrer la description n'a rien changé —, markdown dans la parole). La malformation d'appel du modèle (20-43 %) ne change pas ; sa conséquence est désormais un réessai, puis une phrase vraie, dite **et** mémorisée.

## Next actions

- [ ] **Valider le lot 2 à la voix** — le banc dit 13/14 et un historique propre ; seule une vraie conversation, avec un historique qui a vécu, a le droit de conclure.
- [ ] **Lot 3 — Hermes.**

## Décisions

- **2026-08-12 — Groq retenu pour l'étage LLM.** Chaque palier a changé le poste dominant : Kyutai local 2 396 ms → LLM local MLX 1 309 ms → Groq 954 ms (TTFT isolé 140 ms). 77 % du coût de l'étage est l'aller-retour réseau ; le code n'a rien à rendre.
- **2026-08-12 — Smart Turn coupé** (`--no_smart_turn`) : à grâce égale, 2 314 → 1 125 ms — **1 189 ms gagnés pour un taux de faux départs identique** (2/24). Réserve maintenue : corpus synthétique, à généraliser sur parole humaine hésitante.
- **2026-08-12 — Grâce spéculative maintenue à 800 ms** (`--speculative_reopen_ms 800`) : une assurance à prime faible — 73 ms sur parole liée — qui divise les faux départs par trois sur parole à pauses (8 % contre 25-42 % aux réglages plus courts).
- **2026-08-12, re-motivée le 13/08 — le 8B contre le 70B.** Le motif d'origine (« 429 dès la deuxième requête ») est périmé : le 70B a en réalité **le double du budget** (12 000 TPM contre 6 000). Le choix tient pour une autre raison, mesurée : sur 10 questions outillées, le 70B n'appelle l'outil que 6/10 (8B : 7-8/10) et répond donc sans chercher deux fois plus souvent. **Entre rationner et mentir, on rationne.**
- **2026-08-13 — Contexte court par défaut** (profil `court`, 618 tokens ; bascule `JARVIS_VAULT_PROFIL=complet`). La latence n'aurait pas tranché (+41 ms, non significatif) ; le débit tranche : 9,7 tours/minute contre 3,6 en profil complet — 3,6 tours/minute n'est pas une conversation. Vérifié en réel : le contexte complet rend les faux départs 4,4× plus chers (16 653 tokens en 90 s sur du son ambiant). Validé à la voix le 13/08.
- **2026-08-13 — Frontière entre dépôts** : `jarvis-voice` ne porte **aucune matière du second brain** — l'identifiant d'une note est autorisé, son texte jamais. Appliqué rétroactivement, contrôlé à chaque commit (`bench/scan_avant_commit.sh`).

## Contributions amont

Deux défauts rencontrés pendant le chantier, remontés à `huggingface/speech-to-speech` le 13/08/2026 — chiffres re-mesurés avant publication, textes conservés dans `results/issue-*.md` du dépôt source :

| issue | sujet | preuve chiffrée |
|---|---|---|
| [#482](https://github.com/huggingface/speech-to-speech/issues/482) | `PocketTTSHandler` charge toujours les poids anglais : `language_code` est lu puis jeté, et la voix se résout contre le modèle chargé — `--pocket_tts_voice estelle` reste donc de la phonétique anglaise | WER de relecture **87,8 % → 13,6 %** une fois `french_24l` réellement chargé |
| [#483](https://github.com/huggingface/speech-to-speech/issues/483) | le texte d'aide de `--audio_enhancement` promet une annulation d'écho que DeepFilterNet — monocanal, sans signal de référence, entraîné à *préserver* la parole — ne peut pas rendre | tours en écho **10/58 → 0/43** avec le vrai correctif, `--local_audio_block_mic_during_playback` |

## Ressources liées

- Dépôt source : `~/jarvis-voice` (GitHub privé `lixyra05-debug/jarvis-voice`) — synthèse `RESULTAT.md`, mesures et rapports dans `results/`. Hors vault ; la boucle le lit en lecture seule à trois barrières (porte unique en `"r"`, audit AST, `sandbox-exec`).
- [[Douze pannes silencieuses]] — les douze pannes du chantier, deuxième page wiki *(proposition Claude, à valider)*
- [[La panne silencieuse]] — la page sœur : les cinq formes cataloguées avant ce chantier
- [[_second-brain]] — le vault que la boucle lit (contexte distillé + `chercher_vault`)

## Journal du projet

### 2026-08-15
- Note pivot créée depuis `~/jarvis-voice/RESULTAT.md` (dépôt source en **lecture seule** — rien n'y a été écrit ni modifié). Chantier couvert : 11-15/08/2026. Log : `AI/logs/2026-08-15-session.md`.
