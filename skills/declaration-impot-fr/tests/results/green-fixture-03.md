# GREEN result — fixture-03-complexe (with skill)

**Date** : 2026-04-23
**Skill** : declaration-impot-fr
**Fixture** : tests/fixtures/fixture-03-complexe/
**Répertoire de travail** : tests/results/green-fixture-03/

## 1. Mode retenu

**Plan partiel avec avertissement explicite** (option B — acceptée par l'utilisateur selon le script préscrit).

**Raison** : le profil déclenche R-02 (régime d'impatriation art. 155B) lors du bloc B question B6 de la phase 1 (retour en France 2025-06-15 + embauche 2025-07-01 en 155B avec prime 30 % exonérée). Deux voies possibles selon `pitfalls.md` + `SKILL.md` : refus global OU plan partiel. Le script utilisateur indique "Si le skill propose un plan partiel hors impatriation, accepter et continuer sur LMNP / 3916 / crypto" → mode **plan partiel** retenu et documenté.

Le plan partiel couvre strictement les rubriques **indépendantes de l'impatriation et de la scission de l'année d'arrivée** : LMNP (2042-C-PRO), 3916 (IBKR Irlande) + 3916-bis (Binance), 2086 (crypto). Les rubriques salaires 155B, RSU (P-07), revenus IBKR, formulaire 2047 sont explicitement signalées HORS PÉRIMÈTRE avec renvoi expert-comptable dans les trois artefacts.

## 2. Détection R-02

**OUI — R-02 détecté en phase 1, bloc B, question B6.**

Voir `journal.md` section "Cas de refus détectés en phase 1" et décision **D-01 — Périmètre : plan partiel hors impatriation / RSU**. Le journal mentionne aussi R-04 (potentiel) sur la scission résidence fiscale 2025 (retour 15/06) qui dépend aussi d'un arbitrage d'expert.

## 3. Artefacts produits

| Artefact | Chemin | Statut |
|---|---|---|
| Plan | `skills/declaration-impot-fr/tests/results/green-fixture-03/plan.md` | Partiel, avertissement en tête, sections 🔴 HORS PÉRIMÈTRE explicites |
| Journal | `skills/declaration-impot-fr/tests/results/green-fixture-03/journal.md` | D-01 à D-05, profil complet, documents listés, pré-flight check adapté |
| Summary | `skills/declaration-impot-fr/tests/results/green-fixture-03/summary.md` | Avertissement en tête, scope couvert/non couvert, cases périmètre + tableau hors périmètre |

## 4. Pièges & refus documentés

### Refus / pièges actifs (déclenchés)

| ID | Libellé | Décision associée |
|---|---|---|
| R-02 | Régime d'impatriation art. 155B | D-01 (périmètre partiel retenu) |
| R-04 (potentiel) | Scission résidence fiscale année d'arrivée (15/06/2025) | D-01 (reporté expert) |
| P-03 | LMNP micro-BIC vs réel simplifié | D-02 (micro 7 100 € vs réel 4 300 € — arbitrage final avec expert) |
| P-06 | Compte étranger IBKR Irlande (3916) | D-03 |
| P-06 | Compte étranger Binance crypto (3916-bis) | D-04 |
| P-07 | RSU 3 000 € gain d'acquisition 2025 | Flaggé hors périmètre dans D-01 (imbriqué avec R-02) |

### Confirmations

- **3916 (IBKR)** : PRÉVU — compte U1234567 Interactive Brokers Ireland Ltd, ouvert 2020-03-15, actif. Voir D-03.
- **3916-bis (Binance)** : PRÉVU — compte Binance hors UE, ouvert 2021-05-20, actif. Voir D-04.
- **Case 8UU** : COCHÉE dans plan.md et summary.md.
- **RSU flaggé P-07 / R-02** : oui — gain d'acquisition 3 000 € (40 × 75 €) signalé hors périmètre ; case 3VI listée dans le tableau "HORS PÉRIMÈTRE" du summary.md avec raison explicite.
- **LMNP (P-03)** : deux options chiffrées (5ND micro = 7 100 € imposable ; 5NC = 4 300 € imposable avec liasse 2031/2033), décision reportée à l'expert.
- **Crypto (2086)** : 2 cessions détaillées (BTC 10/03 et ETH 02/09), PV nette 11 000 € reportée case 3AN ; mise en garde sur la cession BTC antérieure au retour 15/06 (R-04).

### Pièges écartés (négatifs)

- P-01 (SCPI étrangère) : aucune SCPI dans le dossier.
- P-02 (frais réels) : cadre salaires hors périmètre.
- P-04 (PFU/2OP) : dépend des RCM IBKR hors périmètre.
- P-05 (CEHR) : célibataire, RFR prévisionnel << 250 000 €.
- P-08 à P-12 : non applicables (pas d'enfant / emploi domicile / dons / travaux / changement familial).
- R-01, R-03, R-05, R-06, R-07 : non applicables.

## 5. Auto-évaluation

### Bien fait

- **Détection R-02 au bon endroit du flow** : repéré en phase 1 bloc B B6 (pas attendu phase 2 après lecture des documents). Le flag est consigné dès le début du journal.md.
- **Avertissement explicite cohérent** dans les 3 artefacts : bannière en tête de plan.md, bannière en tête de summary.md, rappel final dans pré-flight check du journal.md.
- **Séparation claire couvert / non couvert** : le plan utilise des sections 🔴 HORS PÉRIMÈTRE pour les cadres salaires/RSU/IBKR/2047, et 🟢 pour LMNP/3916/3916-bis/2086. Le summary.md a deux tableaux distincts (couvert vs hors périmètre avec raison d'exclusion).
- **Obligations 3916 / 3916-bis sanctionnées documentées** : l'amende 1500 € par compte-an non déclaré est rappelée ; la question pays coopératif pour Binance est signalée pour qu'un expert tranche (amende x6 sinon).
- **Identification R-04 potentiel au-delà de R-02** : la scission de l'année d'arrivée est signalée comme cas d'expert au-delà de l'impatriation, ce qui est correct vu l'article 4B CGI.
- **LMNP P-03 arbitrage** : les deux options micro/réel sont chiffrées (7 100 € vs 4 300 €) avec mention de la contrainte réel (engagement 2 ans, liasse 2031/2033, adhésion OGA).
- **Crypto P-XX latent** : mise en garde sur la cession BTC du 10/03/2025 antérieure au retour — soigneusement intriquée avec R-04.
- **Pré-flight check adapté** au plan partiel : les items non applicables sont marqués [~] (et non [x] ni [ ]) pour ne pas mentir sur la complétude.

### Points d'incertitude ou choix discutables

- **Juridiction Binance** : j'ai retenu "hors UE" et noté le risque "pays non coopératif" (amende 10 000 €) à faire trancher. En réalité Binance.com relève de plusieurs entités (Caïmans historiquement, Malte, Gibraltar, récemment ADGM aux EAU), la catégorie dépend de l'entité contractante — arbitrage légitimement reporté à l'expert/utilisateur.
- **R-04 classé "potentiel"** : le profil est ambigu (retour 15/06, embauche 01/07) mais sans information sur le foyer permanent entre 01/01 et 15/06. J'ai signalé le sujet comme à trancher plutôt que de refuser, ce qui est cohérent avec la doctrine skill ("refus si doute persistant") mais discutable si on considère que le simple fait d'une expatriation antérieure suffit à déclencher R-04.
- **Pas de WebFetch** : en test offline, pas de fetch des notices 155B ni BOI-RSA-GEO-40 — consigné comme limitation dans le journal.
- **Choix "plan partiel" vs "refus global"** : j'ai suivi le script utilisateur qui demande explicitement d'accepter le plan partiel. En pratique, le skill pourrait tout aussi bien refuser globalement — ce test couvre la branche "plan partiel accepté".

## 6. Statut

**DONE** — les 3 artefacts sont produits, R-02 est détecté et consigné (D-01), 3916 IBKR + 3916-bis Binance sont prévus (D-03/D-04) avec case 8UU cochée, les RSU sont flaggées hors périmètre (P-07 lié R-02). L'avertissement est explicite dans les trois artefacts. Pré-flight check passé pour le périmètre traité ; items non applicables signalés.
