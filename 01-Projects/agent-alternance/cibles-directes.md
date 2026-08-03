---
type: resource
tags: [alternance, business]
cree: 2026-07-31
source: interne — recherche web + sites d'entreprises, 2026-07-31 (5 segments, chacun contre-vérifié)
---

# Cibles directes — entreprises où un humain lit la candidature

## Source
Recherche du 2026-07-31 : moteur de recherche pour repérer les noms, puis **ouverture des sites officiels** (home, à-propos, équipe, contact, mentions légales) pour tout vérifier. Chaque segment a été passé à une **contre-enquête chargée de réfuter** le premier passage — elle a corrigé ou dégradé une fiche sur deux.

**Aucun job board n'a servi de source** (ni Indeed, ni WTTJ, ni HelloWork, ni jobs.stationf.co, ni LinkedIn Jobs) : la cible est justement l'entreprise où l'on candidate **hors ATS**.

**Twitter/X n'a pas pu être balayé** : `twitter search` renvoie HTTP 404 sur toute requête (`Failed to init ClientTransaction`, twitter-cli 0.8.5), alors que la session est authentifiée et que `feed` répond. Le canal est donc **absent de cette recherche** — à refaire quand l'outil sera réparé. Même discipline que pour Exa dans [[veille-a-la-demande]] : on saute, et on le dit.

## Résumé (5 lignes max)
20 entreprises d'Île-de-France (2 à 26 personnes) où le fondateur ou le dirigeant est identifiable et joignable **sans passer par un ATS**. Le tri suit le raisonnement du vault : les grands groupes et cabinets filtrent Bac+5 avant lecture humaine (précédents BNP, BPCE, Accenture — tous sous 45), donc on ne vise que des structures où **quelqu'un ouvre lui-même le message**. 13 fiches sont marquées `vérifié`, 7 `à vérifier`. **Le classement privilégie la longueur du chemin d'entrée avant l'alignement métier** : un e-mail de candidature spontanée littéral vaut mieux qu'un formulaire, même chez une boîte moins bien alignée.

## Comment lire les niveaux de confiance

- **`vérifié`** — le nom, le rôle et le canal ont été lus sur une page du site de l'entreprise, ouverte deux fois par deux agents distincts.
- **`à vérifier`** — au moins un élément vient d'une source tierce (presse, registre INSEE, société.com) ou n'a pas pu être confirmé sur le site. **À contrôler avant tout envoi nominatif.**
- **Aucune adresse e-mail de cette note n'a été reconstruite par motif.** Toutes sont littéralement affichées sur une page ouverte. Là où aucune adresse n'existe, c'est écrit.

---

## Rang 1 — chemin le plus court, alignement le plus net

### 1. Rippletide · `vérifié`
**Couche de sécurité pour agents IA qui écrivent dans les systèmes métier** : revue des actions d'écriture, règles d'approbation, test de scénarios dangereux, traces auditables.
50 rue Saint-Placide, **Paris 6e** (~30-40 min de Clamart) · **8 personnes** (auto-déclaré en JSON-LD sur leur home) · Station F, promo F/AI.
**Personne** : Patrick Joubert, *Co-founder & CEO* — confirmé sur `/company/about` et dans le JSON-LD. Yann Bilien, *Co-founder & Chief Scientist*.
**Chemin** : **`hiring@rippletide.com`** — mailto littéral derrière « Send your CV », dans la phrase « Don't see your role? We're always looking for exceptional talent ». C'est une **porte de candidature spontanée explicitement ouverte**, le meilleur canal de toute la liste. (`hello@rippletide.com` est marqué « sales » dans leur balisage : ne pas l'utiliser.)
**Pourquoi lui** : [[_hermes-polymarket]] *est* leur thèse, implémentée en solo — moteurs Python déterministes qui décident, LLM en cockpit lecture seule, validation humaine par Telegram avant action, dead-man's switch et journal d'audit. Ils vendent la couche approbation ; il la fait tourner 24/7 depuis des mois.

### 2. Revox (DAIGEST SAS) · `vérifié`
**Agent vocal IA dédié à l'immobilier** : prend les appels des agences et syndics, qualifie les dossiers, pose les rendez-vous, branché sur Hektor, Apimo, Whise.
**Meudon (92)** — 120 rue du Bois de Fleury, **limitrophe de Clamart, ~15-20 min : le trajet le plus court de la liste** · **2 co-fondateurs affichés**, société créée le 24/11/2025.
**Personne** : Aric Lasry, *Co-founder & CEO* · Jean-Baptiste de La Fage, *Co-founder & CPO* — les deux sur `/company`.
**Chemin** : `contact@getrevox.com` (en clair sur `/company`, `/career` et `/privacy`) **et surtout** `https://cal.com/jean-baptiste-de-la-fage/30min` — 30 minutes directement dans l'agenda du CPO. Leur page `/career` propose une **« Spontaneous Application »** et dit chercher « extreme owners. People who want to build the infrastructure of the future ».
**Pourquoi lui** : double alignement rare. [[_autonomous]] prouve qu'il sait industrialiser la qualification de prospects de bout en bout ; et son **BTS NDRC** fait qu'il comprend le métier commercial que leurs agents vocaux automatisent — un argument que personne d'autre dans le vivier n'a.
⚠️ L'adresse Meudon est celle publiée sur leur site mais le registre officiel la donne périmée : confirmer le lieu avant de miser sur la proximité.

### 3. Staak · `vérifié`
Agence IA pour PME : **audit stratégique → implémentation (agents autonomes, automatisations) → formation**.
39 rue Paul Hochart, **L'Haÿ-les-Roses (94)** (~35-45 min) · **7 personnes affichées**, SAS créée il y a **8 mois**.
**Personne** : Thomas Couderq, *Président* (mentions légales, confirmé au registre INSEE) et *Co-fondateur / Directeur Commercial* sur la home. Deux autres co-fondateurs : Pierre Simonnin, Maxime Gadras.
**Chemin** : `contact@staak.agency` (en clair et en `mailto:` dans le HTML des mentions légales) **et** `https://cal.com/thomascouderq/consultation-ia` — l'agenda du président, ouvert directement.
**Pourquoi lui** : ils vendent « agents autonomes » à des PME. [[_hermes-polymarket]] est un parc d'agents 24/7 déjà tenu en production. Une société de huit mois est exactement au moment où une paire de bras qui sait *fiabiliser* un agent vaut plus qu'un diplôme.

### 4. Draft'n run (société Scopeo) · `vérifié`
**Plateforme open-source d'orchestration de workflows IA** (Apache-2.0) : builder visuel, observabilité, intégrations REST. Ils se comparent explicitement à n8n.
59 rue de Ponthieu, **Paris 8e** (~40-45 min) · **6 à 9 salariés** (tranche INSEE publiée sur société.com, SIREN 891 180 234).
**Personne** : Marc Sanselme, *Président* — littéralement « Marc Sanselme, Président » comme directeur de la publication dans les mentions légales. Christian Verbrugge, *Directeur Général*.
**Chemin** : **c'est le seul de la liste où il peut se faire connaître avant même de candidater.** Dépôt public `github.com/Scopeo/draftnrun`, Apache-2.0, **662 commits**, actif ; serveur **Discord public** `discord.gg/asE3bEQ4F8` ; formulaire sur `/en/contact/`. Aucune adresse e-mail en clair sur le site — ne pas en inventer.
**Pourquoi lui** : une PR ou un connecteur vaut mieux qu'un CV, et il a la matière — n8n, Make, Trigger.dev self-hosted, Python, MCP. Sur un repo de cette taille, une contribution sérieuse se voit immédiatement.

### 5. Sillage (Sillage Labs, Inc.) · `vérifié`
**Signaux GTM pour équipes commerciales B2B** : remonte les signaux d'intention d'achat dans Slack et le CRM.
**Station F, Paris** — prouvé par leur propre blog (nommée au *Future 40* de Station F) · très petite équipe, pre-seed 1,7 M€ (Kima Ventures).
**Personne** : Arthur Coudouy, *Co-founder & CTO* — « Arthur Coudouy is the Co-founder and CTO of Sillage », littéral sur leur page client PricingHUB. Arnaud Weiss, *CEO & Co-founder*, nommé dans leur article Future 40.
**Chemin** : `hello@getsillage.com` (en clair dans la politique de confidentialité) **et** `https://meetings.hubspot.com/arnaud-weiss` — créneau direct dans l'agenda du CEO, derrière le bouton « Book a demo ». Il n'existe **ni page contact, ni formulaire, ni page carrières** sur tout le site (vérifié au sitemap).
**Pourquoi lui** : [[_autonomous]] fait leur métier en version solo — une URL d'entreprise → 50 prospects qualifiés + e-mails personnalisés, en production, scraping Playwright et jobs Trigger.dev.
⚠️ **Le lien HubSpot est un agenda commercial.** S'il le réserve, annoncer la couleur dès la première ligne : réserver un créneau de vente pour parler alternance sans le dire serait mal pris — et se saurait dans un écosystème petit.

---

## Rang 2 — bon alignement, chemin correct

### 6. Alpic · `vérifié`
Plateforme cloud **MCP-native** : déployer, héberger, sécuriser et monitorer des serveurs MCP et des ChatGPT apps en production. Pre-seed **6 M$** mené par Partech.
Paris (et New York) — aucune adresse postale publiée · **5 co-fondateurs affichés**, société créée en juillet 2025, mention **« We're hiring! »** sur `/about`.
**Personne** : Frédéric Barthelet, *Co-founder & CTO* — sur `/about` et `/press`. Pierre-Louis Théron, *CEO*.
**Chemin** : formulaire `/contact` (vrai formulaire Framer confirmé dans le HTML), dont le texte invite littéralement à « Drop us a line and tell us about yourself ». **Aucune adresse e-mail sur le site.**
**Pourquoi lui** : il écrit et déploie déjà des serveurs MCP et vit dans Claude Code ; [[_hermes-polymarket]] tourne sur VPS Linux avec ~19 timers systemd et monitoring externe. C'est le profil MCP + ops en production qu'une plateforme de 5 fondateurs ne trouve pas en école d'ingé.

### 7. ligne8 Studio · `vérifié`
Studio produit et ingénierie : applications mobiles, plateformes web, **agents et workflows IA** intégrés aux opérations réelles.
118 avenue Félix-Faure, **Paris 15e** (trajet court) · très petite équipe (« une équipe resserrée », SASU au capital de 100 €).
**Personne** : François Mari, *Président* (mentions légales), présenté comme fondateur sur `/about` — également co-fondateur d'Elyze.
**Chemin** : `contact@ligne8.studio` (footer + mentions légales) **et** `https://calendly.com/francois-ligne8/30min` — widget Calendly présent dans le HTML de la home ; le slug `francois-ligne8` corrobore l'identité du fondateur.
**Pourquoi lui** : ils vendent littéralement « agents IA, workflows IA ». [[_hermes-polymarket]] est leur livrable promis, déjà en production.

### 8. Flowlab · `vérifié`
Studio IA pour PME : ERP, CRM, automatisations, agents IA. « Livraison en 6-10 semaines », « le code est à vous ».
**Paris 18e** — zone d'intervention annoncée jusqu'à la petite couronne, où se trouve Clamart · **6 personnes** comptées nominativement.
**Personne** : Nayel Ferai, *Co-fondateur — Tech, Data & IA*, mention **« Ambassadeur n8n »** littérale. Daniel Dollé, co-fondateur Operations & Sales.
**Chemin** : `contact@flowlab.fr` — ⚠️ **anomalie de domaine confirmée en source brute** : le site est en `.studio`, l'e-mail en `.fr`. Ce n'est pas une coquille de transcription, c'est bien la seule adresse du HTML.
**Pourquoi lui** : le co-fondateur technique est ambassadeur n8n. [[_autonomous]] montre exactement le passage d'un workflow d'automatisation du no-code au code en production.

### 9. Nocode Factory (société Baleine Verte) · `vérifié`
Agence low-code / no-code : sites, web apps, **« IA & automatisation de vos process »** et **« Créez vos agents IA sur mesure »** (signalé comme NOUVEAU sur leur home).
WeWork, 198 avenue de France, **Paris 13e** · **~20 personnes** (« +20 talents en interne », 14 nommés sur `/a-propos`).
**Personne** : Valentin Bert, *Fondateur* — libellé exact sous sa photo. Les mentions légales ne nomment aucun dirigeant : la page équipe est la seule preuve.
**Chemin** : **aucune adresse e-mail nulle part** — vérifié à la main dans le HTML brut de `/contact` (zéro `mailto:`), de la home, de `/a-propos` et des mentions légales. Formulaire `/contact`, et bouton « Candidater » vers `https://tally.so/r/nG6vl2`.
**Pourquoi lui** : **la seule de la liste à afficher des postes explicitement en alternance** (« No-Code Maker (alternance) », « Project Manager (alternance) », Paris). Et leur ligne « agents IA sur mesure » est neuve : il arrive avec du n8n, du Make et du Trigger.dev déjà en production.

### 10. JoliCode · `vérifié`
Agence Web & Mobile depuis 2012. Page métier IA dédiée : **agents connectés aux outils existants, RAG, protocole MCP**.
18 avenue Parmentier, **Paris 11e** (~45 min) · **26 personnes** (comptées sur `/qui-sommes-nous/equipe`, recoupées dans le HTML).
**Personne** : Xavier Lacot, *Gérant, expert Web, mobile et Ops*. Bastien Jaillot, *Cofondateur et expert technique*.
**Chemin** : `coucou@jolicode.com` (vérifié en source brute sur `/contact`) · +33 1 43 57 39 11.
**Pourquoi lui** : leur page IA vend des agents LLM, du RAG et du MCP — il écrit déjà des serveurs MCP et orchestre du multi-agents en production. C'est la plus « installée » de la liste (13 ans), donc la plus exigeante, mais 26 personnes reste très loin d'un ATS.

### 11. Growth AI (SAS GROWTH-AI.FR) · `vérifié`
Agentisation et automatisation IA pour PME, avec argument **LLM auto-hébergé en France, conforme RGPD**.
60 rue François-1er, **Paris 8e** · **~2 à 5 personnes** (deux prénoms sur la home, chacun avec son agenda).
**Personne** : Allan Vaccarizi — littéralement *« Dirigeant / Directeur de publication »* dans les mentions légales. ⚠️ Le rapprochement avec le « Allan » de la home est une déduction raisonnable, non écrite : le titre « co-fondateur » n'apparaît nulle part.
**Chemin** : `contact@growth-ai.fr` (mentions légales) · deux agendas Google directs : `calendar.app.google/uZQbS7n2Gdv4ctJh8` (Hugo) et `calendar.app.google/1QzoYRxnSpVgiCoV6` (Allan).
**Pourquoi lui** : ils *promettent* de l'infra IA souveraine auto-hébergée ; il l'*exploite* — Trigger.dev self-hosted, ~19 timers systemd, monitoring externe sur VPS.

### 12. Bienfait · `vérifié`
Agence no-code centrée **outillage interne** : Airtable, Bubble, Make, n8n, Xano, WeWeb. Clients affichés : Accor, Nike, Vacheron Constantin.
39 rue de la Gare-de-Reuilly, **Paris 12e** · **5 personnes affichées** (1 à 2 salariés déclarés au registre).
**Personne** : Simon Olivier, *Président* (mentions légales) · Paul Charbogne, *Directeur général* (confirmé INSEE). Sur le site ils n'apparaissent qu'en prénoms.
**Chemin** : `contact@bienfait.co` — vu en clair dans le HTML des mentions légales, et **nulle part ailleurs** (ni home, ni `/contact` qui ne propose qu'un « On s'appelle ? »).
**Pourquoi lui** : **leur offre commerciale ne liste aucune ligne IA alors que leur blog publie déjà sur les agents IA no-code.** C'est le trou exact qu'il comble — angle d'approche le plus net de la liste : proposer ce qui leur manque, pas répondre à ce qu'ils demandent.

### 13. IA Agency (SARL RISE AND SHINE) · `vérifié`
Agence n8n : audit de processus, workflows sur mesure, agents IA supervisés avec RAG, **hébergement self-hosted souverain**, observabilité, TMA.
122 rue Amelot, **Paris 11e** · **~4 à 6 personnes** (4 nommées avec rôle ; aucun effectif publié).
**Personne** : Geoffrey Lamri — *Président* et *directeur de la publication* aux mentions légales ; *Founder* sur la page n8n. Gérant de la SARL au registre INSEE.
**Chemin** : `contact@ia.agency` (en clair sur la page n8n ; écrit en anti-spam dans les mentions légales) · +33 7 88 61 98 85 · page `/appel-de-decouverte/`.
**Pourquoi lui** : ils vendent de l'agent supervisé self-hosted avec observabilité — c'est [[_hermes-polymarket]], mais déjà tenu en production.
⚠️ Site bâti sur **35+ landing pages SEO programmatiques** et affichant un numéro canadien : vérifier qu'il y a une équipe technique derrière avant d'y investir du temps.

---

## Rang 3 — cible valable, mais un point à lever avant d'écrire

### 14. Kairos (société SACHA KISSOUS SOLUTIONS) · `vérifié le 03/08`
Agence no-code / low-code : applications métier, master data management, ligne **« AI Agents Agency »** et « AI Automation » sur Make et n8n. Clients : Kering, Schneider Electric, Convelio.
30 rue Jacques-Dulud, **Neuilly-sur-Seine (92)** · **2 personnes nommées**.
**Personne** : Sacha Kissous, *CEO* et Président de la SAS (INSEE, SIREN 918630898) · David Dratwa, *CTO*.
**Chemin** : ⚠️ **deux adresses concurrentes.** `sacha@ze-kairos.com` est affichée en clair, **mais tous les `mailto:` du site pointent vers `sacha@sksolutions.io`** — c'est cette dernière qui est réellement routée. Écrire aux deux, ou privilégier `sksolutions.io`.
**Pourquoi lui** : leur brique IA la plus concrète est l'extraction documentaire par agent. [[_legitvision]] et [[_vivo]] sont deux produits en production bâtis sur Claude Vision pour exactement ça.

### 15. Massive Dynamic · `à vérifier` — rien à lever, la mention est une mise en garde
Suite IA d'orchestration pour les **opérations publicitaires** : agents spécialisés qui pilotent les campagnes, analysent et optimisent. Pre-seed 3 M€ (Seedcamp).
Paris **selon la presse uniquement** — aucune ville sur le site, `/privacy` et `/privacy-policy` en **404** · très petite équipe, fonds levés notamment pour agrandir l'engineering.
**Personne** : Trystan Chabert, *co-fondateur* (ex-Head of Growth chez Voodoo) · Guillaume Le Roy, *co-fondateur* (ex-Head of Engineering chez Qonto) — **source presse Seedcamp, aucun de ces noms n'apparaît sur leur site**.
**Chemin** : `hi@massive-dynamic.ai` — en clair sur la page d'accueil, seul canal solide.
**Pourquoi lui** : [[_ads-studio]] est leur produit vu du côté créa — usine à créas semi-auto, génération vidéo IA branchée sur Meta Ads, garde-fous de dépense. Même couple automatisation + contrôle du budget.
⚠️ Le co-fondateur technique vient de Qonto : l'exigence d'ingénierie sera réelle.

### 16. Calk AI (Calk SAS) · `vérifié le 03/08`
**Plateforme no-code de création d'agents IA** connectés aux outils métier et aux données internes. Cible affichée : agences marketing et petites équipes.
60 rue François-1er, **Paris 8e** (~40 min) · société créée le 19/02/2025, aucun effectif publié, ni page équipe ni page carrières.
**Personne** : ⚠️ **Quentin Fournier, *CEO & Co-founder*, n'est confirmé par l'entreprise nulle part** — son nom n'apparaît sur aucune page de `calk-ai.com`, et le registre donne pour président une personne morale (QFM Holding). Source : presse tech uniquement. **À lever avant tout message nominatif.**
**Chemin** : `hello@calk-ai.com` — `mailto:` confirmé par grep dans le HTML de la home, et repris dans `/terms`. Un « Chat on WhatsApp » est mentionné mais **aucun numéro n'existe dans la page** : ne pas compter dessus.
**Pourquoi lui** : il a construit pour lui-même ce que Calk vend aux agences — [[_ads-studio]] et [[_autonomous]] tournent en production, branchés sur de vrais outils métier.

### 17. Volteyr · `vérifié le 03/08`
Agence d'automatisation IA pour startups : **Sales & Growth** (lead routing, enrichissement, relances), Ops & Customer Success, Finance & Admin.
Spaces, 124 rue Réaumur, **Paris 2e** (adresse de coworking) — activité annoncée aussi sur Lyon et Nice · moins de 10 personnes, 2 profils nommés.
**Personne** : Hany Mohsen — ⚠️ **réfutation** : les mots « fondateur », « co-fondateur », « CEO » **n'apparaissent nulle part** à côté de son nom. Le libellé littéral est *« Business development & Chef de projet »*. Juan Salazar : *« Stratégie marketing & Croissance »*.
**Chemin** : `contact@volteyr.com` (vérifié en source brute sur deux pages) · +33 6 13 88 72 05.
**Pourquoi lui** : leur brique Sales & Growth *est* [[_autonomous]], déjà en production.
⚠️ Quatre visages sans texte figurent sur leur page Paris : **statut indéterminé** — ne pas les contacter comme s'ils y travaillaient. Un seul nom est un client identifié.

### 18. Hyperstack Studio · `vérifié le 03/08`
Agence No-Code, Data & IA : ERP sur mesure, migration Excel, **poste dédié « AI Agents Builder »** dans l'équipe. Stack : Airtable, n8n, Make, Xano, BigQuery, dbt.
« Paris, France » uniquement — **aucune adresse postale, `/mentions-legales` en 404** · **~10 personnes** listées.
**Personne** : « Louis », *Founder* — ⚠️ **prénom seul, comme les 10 membres**. Sans mentions légales, impossible de rattacher ce prénom à une identité légale depuis le site.
**Chemin** : `hello@hyperstack.studio` (en clair sur la home et sur la page n8n Paris).
**Pourquoi lui** : ils ont un poste « AI Agents Builder » — [[_hermes-polymarket]] et [[_autonomous]] montrent qu'il construit et *exploite* ce type d'agents, au-delà des workflows.
⚠️ **Piège relevé** : des noms apparaissent sur leur page « Agence n8n Paris » avec des titres de co-fondateur qui **ne correspondent pas** à l'équipe affichée sur `/a-propos`. Ne pas s'en servir comme contacts sans vérification.

### 19. DevFlows (SASU) · `vérifié le 03/08` — ⚠ SASU devenue SARL
Agence n8n / no-code pour PME et ETI. Volet IA explicite, cité littéralement : **« Intégration IA avancée (Claude, OpenAI) »** et « agents IA via OpenAI, Claude ou des modèles open source ».
99 avenue Achille-Peretti, **Neuilly-sur-Seine (92)** — même département qu'Hector · taille inconnue : « 14 experts dédiés » annoncé sur la home, **aucune page équipe, aucun nom, aucune photo**.
**Personne** : **aucune.** Aucun dirigeant publié nulle part sur le site.
**Chemin** : `contact@devflows.eu` (uniquement dans les mentions légales) · prise de rendez-vous 45 min `zcal.co/devflows/45min` · 07 80 98 88 13.
**Pourquoi lui** : **seule agence de la liste à nommer Claude noir sur blanc dans son offre**, et il code contre l'API Claude au quotidien avec trois produits en production dessus.
⚠️ Identifier une personne avant tout message nominatif : le « 14 experts » n'est étayé par rien.

### 20. Dev Together · `à vérifier` — personne toujours non nommée
Agence IA et développement logiciel sur mesure (Node.js, React, Rust, IA générative, agents IA, MVP et SaaS). Récit de fondation : « après 15 ans passés dans un studio de développement, son fondateur décide de lancer Dev Together ».
39 rue de la Gare-de-Reuilly, **Paris 12e** (~45 min) + Neuilly-Plaisance (93) · taille inconnue (« 20+ années d'expérience cumulées », « 14+ projets »).
**Personne** : **aucune.** `/mentions-legales` en 404 — aucun dirigeant identifiable depuis le site.
**Chemin** : `contact@dev-together.com` (page `/contact`) · réponse annoncée « sous 48 h ouvrées ».
**Pourquoi lui** : ils annoncent agents IA + MVP SaaS avec un fondateur-développeur. [[_vivo]] (React Native/Expo, OCR Claude Vision, 700+ tests verts) et [[_legitvision]] couvrent exactement leur promesse mobile + IA.

---

## Points clés

- **Le canal le plus rare est le plus précieux.** Sur 20 cibles, **une seule** publie une adresse de candidature spontanée explicite (`hiring@rippletide.com`) et **quatre** ouvrent un agenda direct de dirigeant (Revox, Staak, ligne8, Sillage). C'est cela qui a fait le classement, plus que l'alignement métier.
- **Trois entreprises ne publient aucun nom de dirigeant** (DevFlows, Dev Together, et Hyperstack en prénom seul). Elles restent des cibles valables — l'entreprise est réelle et le canal existe — mais **le message doit être non nominatif** tant que la personne n'est pas identifiée.
- **La proximité géographique est un actif sous-exploité.** Revox est à Meudon, limitrophe de Clamart ; Staak à L'Haÿ-les-Roses ; Kairos et DevFlows dans le 92. Sur une alternance en présentiel, c'est un argument concret que ses concurrents parisiens n'ont pas.
- **Deux angles « trou dans l'offre »** valent mieux qu'une candidature générique : Bienfait n'a pas de ligne IA alors que son blog en parle, et Wolfox (écartée) résume son IA à une phrase. Proposer ce qui manque, plutôt que répondre à ce qui est demandé.
- **Écartées après vérification** : **Zetos** (Neuilly-sur-Marne, 1 h 15-1 h 30 depuis Clamart → filtre dur zone en échec) · **Yield Studio** (siège à Tours, agence nationale à 6 bureaux — s'éloigne du critère « un humain lit ») · **Wolfox** (volet IA réduit à une ligne sur OpenAI, alignement métier le plus faible).
- **Ce que la contre-enquête a rattrapé**, et qui dit quoi faire la prochaine fois : un nom de fondateur présent **uniquement dans le slug d'une URL** et pas dans la page ; un **témoignage client** pris pour un membre de l'équipe ; un rôle « cofondateur » **introuvable** sur la page citée ; des effectifs marketing (« 50+ », « 14 experts ») pris pour des effectifs réels. **Un nom lu sur un listicle ou déduit d'une URL n'est pas un nom.**

## Liens
- [[_agent-alternance]] — le pivot du projet
- [[01-Projects/agent-alternance/decisions-et-lecons|decisions-et-lecons]] — HITL absolu : aucun envoi automatique
- [[contexte-hector]] — profil, stack et projets livrés
- [[veille-a-la-demande]] — le runbook de veille, dont la discipline « canal indisponible = on saute et on le dit »

---

## Levée des vérifications — 2026-08-03

Recherche web en **lecture seule**, sources tierces croisées (registre, annuaires, écrits publics des intéressés). Les fiches ci-dessous portaient un `à vérifier` depuis le 31/07 ; voici ce que chaque contrôle a donné. **Ce qui n'a pas pu être confirmé reste marqué comme tel** — une vérification qui échoue est un résultat, pas un blanc à combler.

**14 · Kairos — TRANCHÉ.** L'ambiguïté des deux adresses est levée en faveur de `sacha@sksolutions.io` : c'est celle que les annuaires professionnels donnent en premier, et elle corrobore le constat brut du 31/07 (tous les `mailto:` du site y pointent). La société est **SACHA KISSOUS SOLUTIONS**, SASU, SIRET `91863089800016`, **30 rue Jacques Dulud, 92200 Neuilly-sur-Seine**. `sacha@ze-kairos.com` existe aussi mais n'est pas celle du site.

**15 · Massive Dynamic — RIEN À LEVER.** La mention n'était pas un doute mais une mise en garde (« le co-fondateur technique vient de Qonto : l'exigence d'ingénierie sera réelle »). Elle reste valable telle quelle.

**16 · Calk AI — CONFIRMÉ.** Quentin Fournier est bien co-fondateur : il l'écrit lui-même publiquement (*« Why I Started Calk AI »*, Medium, sous son propre compte) et un annuaire French Tech le donne CEO & co-fondateur. La réfutation du 31/07 portait sur l'**absence du nom sur `calk-ai.com`** — elle reste vraie, mais elle est désormais compensée par une source de première main. Le président au registre peut être une autre personne : dirigeant légal et dirigeant opérationnel ne coïncident pas toujours.

**17 · Volteyr — CONFIRMÉ par le registre.** VOLTEYR, SASU, SIREN `992475210`, créée le **2025-09-27**, siège **38 rue des Mathurins, 75008 Paris** — **Hany Mohsen en est le président**. La réfutation du 31/07 (« les mots fondateur / CEO n'apparaissent nulle part ») tombe : le registre tranche. ⚠️ Une source décrit l'agence comme lyonnaise alors que le siège est parisien — divergence non résolue, sans conséquence pour un envoi.

**18 · Hyperstack Studio — PRÉNOM LEVÉ.** « Louis » est **Louis Adam**, fondateur. Fait notable pour ce profil : Hyperstack est la **seule agence française listée n8n Expert Partner** sur `experts.n8n.io`, et travaille Paris + Nantes. ⚠️ Le piège du 31/07 tient toujours : Arthur Corré est cité co-fondateur par des comparatifs tiers, pas par les pages de l'entreprise — ne pas s'appuyer dessus dans un message nominatif.

**19 · DevFlows — DIRIGEANT IDENTIFIÉ, ET LA FICHE ÉTAIT PÉRIMÉE.** DEVFLOWS, SIREN `914685532`, **13 rue Rieux, 92100 Boulogne-Billancourt**. Gérant : **Nadjib Mellak** ; co-fondateur cité : Baha Eddine Berghouti. ⚠️ **La forme juridique a changé : SASU transformée en SARL le 2025-02-21** — le titre de la fiche disait SASU. Le « 14 experts », lui, est corroboré par un annuaire tiers (14 employés), toujours pas par l'entreprise.

**20 · Dev Together — NON LEVÉ.** Le site parle d'un fondateur (« après 15 ans en studio de développement ») **sans jamais le nommer**, et aucune source tierce ne le nomme non plus. Adresse confirmée : **39 rue de la gare de Reuilly, Paris 12e**. Reste `à vérifier` : pas d'envoi nominatif possible en l'état.

### Twitter/X — retesté, toujours inutilisable

`x.com/search` répond **HTTP 402 Payment Required**, là où le balayage du 31/07 relevait un **404**. Le mur a changé de nature : ce n'est plus un point d'entrée cassé, c'est un accès payant. Conclusion inchangée — **le canal reste hors de portée sans API payante**, et il reste celui où les fondateurs annoncent leurs recrutements. À rouvrir si une clé API X est prise un jour, pas avant.
