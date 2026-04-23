# Skill `declaration-impot-fr` — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Construire un skill Claude Code généraliste d'assistance à la déclaration d'impôt sur le revenu en France, avec questionnaire initial + itération sur les documents + plan/journal/summary persistés + catalogue de pièges + escalade WebFetch sur doute.

**Architecture:** Skill documentation-lourde (Markdown) avec point d'entrée `SKILL.md` (workflow en 6 phases) qui charge des fichiers de référence via progressive disclosure (`references/*.md`), copie trois templates (`templates/*.md`) dans le répertoire de travail utilisateur, et est validé par trois fixtures de test (`tests/fixtures/*/`) exécutées via subagents.

**Tech Stack:** Markdown, YAML frontmatter, WebFetch (pour notices officielles à l'exécution), Agent/subagent (pour tests). Aucun runtime code.

**Spec de référence:** `docs/superpowers/specs/2026-04-23-skill-declaration-impot-fr-design.md`

**Arborescence cible:**
```
skills/declaration-impot-fr/
├── SKILL.md
├── references/
│   ├── initial-questionnaire.md
│   ├── document-types.md
│   ├── forms-catalog.md
│   ├── pitfalls.md
│   └── online-mapping.md
├── templates/
│   ├── plan.md
│   ├── journal.md
│   └── summary.md
└── tests/
    └── fixtures/
        ├── fixture-01-simple/
        ├── fixture-02-mixte/
        └── fixture-03-complexe/
```

---

## Task 1: Créer l'arborescence du skill et `SKILL.md`

**Files:**
- Create: `skills/declaration-impot-fr/SKILL.md`
- Create: `skills/declaration-impot-fr/references/` (dossier)
- Create: `skills/declaration-impot-fr/templates/` (dossier)
- Create: `skills/declaration-impot-fr/tests/fixtures/` (dossier)

- [ ] **Step 1: Créer la structure de dossiers**

```bash
mkdir -p skills/declaration-impot-fr/{references,templates,tests/fixtures}
```

- [ ] **Step 2: Vérifier la structure**

```bash
ls -la skills/declaration-impot-fr/
```
Expected: dossiers `references/`, `templates/`, `tests/` visibles.

- [ ] **Step 3: Écrire `SKILL.md`**

Contenu exact :

````markdown
---
name: declaration-impot-fr
description: Use when preparing a French income tax return (déclaration d'impôt sur le revenu) from personal documents (IFU, relevés bancaires, échéanciers de crédit, attestations de garde, situation familiale). The skill runs a 6-phase interactive workflow — initial questionnaire, document inventory, follow-up questionnaire, planning, adaptive step-by-step filling, pre-flight check — producing three persistent artifacts (plan, journal of decisions, summary of boxes to fill) with pitfall detection and escalation to official notices (impots.gouv.fr) on doubt.
---

# Déclaration d'impôt sur le revenu (France)

## Overview

Skill d'assistance à la préparation d'une déclaration d'impôt sur le revenu française.
Ne télédéclare pas ; produit trois artefacts (`plan.md`, `journal.md`, `summary.md`) que
l'utilisateur utilise ensuite pour saisir sur impots.gouv.fr.

**Principe directeur :** poser des questions plutôt que deviner, particulièrement sur
les arbitrages (régime micro vs réel, PFU vs barème, frais réels vs abattement,
traitement conventionnel d'un revenu étranger).

## When to Use

- Préparation de la déclaration annuelle de revenus en France (particulier).
- Situations courantes à modérément complexes : salaires, RCM, plus-values, revenus
  fonciers France/étranger, SCPI, comptes à l'étranger, crédits/réductions d'impôt.

**Ne pas utiliser pour :** cessions d'entreprise, plus-values pro complexes, régime
d'impatriation (art. 155B), fonctionnaires internationaux, doubles résidences,
successions/donations — renvoyer vers expert-comptable ou centre des finances publiques.

## Workflow en 6 phases

```
Phase 1 — Questionnaire initial      (charger references/initial-questionnaire.md)
Phase 2 — Inventaire documentaire    (charger references/document-types.md)
Phase 3 — Questionnaire complémentaire
Phase 4 — Planification              (charger references/forms-catalog.md + pitfalls.md)
                                     (copier templates/*.md dans le répertoire de travail)
Phase 5 — Exécution adaptative       (charger references/online-mapping.md)
Phase 6 — Pré-flight check
```

### Phase 1 — Questionnaire initial

Avant toute ouverture de documents. Charger `references/initial-questionnaire.md` et
parcourir les 7 blocs (situation foyer, changements, revenus attendus, logement/emprunts,
enfants/famille, autres réductions, méta). Une question à la fois.

Sortie : profil fiscal préliminaire résumé dans `journal.md` (phase 4).

### Phase 2 — Inventaire documentaire

Demander à l'utilisateur de fournir les documents (PDF, images, texte, captures).
Charger `references/document-types.md` pour savoir quoi chercher dans chaque type.
Comparer au profil phase 1 et signaler les manques probables.

### Phase 3 — Questionnaire complémentaire

Questions ciblées déclenchées par ce que les docs ont révélé. Une question à la fois.
Mode adaptatif : case par case quand doute.

### Phase 4 — Planification

1. Créer le répertoire de travail `./declaration-impot-<année>/` (par défaut à la
   racine du projet courant).
2. Copier les trois templates (`plan.md`, `journal.md`, `summary.md`) depuis
   `templates/` vers le répertoire de travail.
3. Charger `references/forms-catalog.md` et `references/pitfalls.md`.
4. Remplir `plan.md` (checklist ordonnée) et l'entête de `journal.md` (profil +
   documents + décisions déjà prises).
5. **Gate utilisateur :** présenter le plan et attendre "ok on y va" avant phase 5.

### Phase 5 — Exécution adaptative

Charger `references/online-mapping.md`. Parcourir `plan.md` dans l'ordre :

1. Annoncer la section.
2. Donner le bloc de cases (numéro + libellé + valeur).
3. Attendre "ok c'est rempli" de l'utilisateur avant de passer à la suivante.
4. Sur case à doute : case par case, WebFetch de la notice officielle (escalade),
   poser la question, mettre à jour `plan.md` + `journal.md` + `summary.md`.

### Phase 6 — Pré-flight check

Relecture globale. Règles de cohérence automatiques :

- Aucun `TODO` résiduel dans `plan.md`.
- Une seule option entre abattement 10 % et frais réels par déclarant.
- Reports corrects (8TK = somme des CI étrangers).
- CEHR si RFR prévisionnel > 250 000 € (célibataire) / 500 000 € (couple).
- 3916 par compte étranger identifié.
- Sommes redondantes cohérentes.

En cas d'incohérence : message explicite + décision utilisateur consignée.

## Trois niveaux de vigilance

| Niveau | Quand | Action |
|---|---|---|
| Standard | case évidente, doc clair, pas de piège | remplir + consigner source |
| Vigilant | piège détecté via `pitfalls.md` | poser question + options + conséquences, consigner décision |
| Escalade | doute persistant, cas non couvert | WebFetch notice officielle + reformuler ; si doute demeure, refus de conclure |

## Règles non négociables

- Toujours demander avant de deviner sur un arbitrage fiscal.
- Toujours consigner les décisions dans `journal.md` avec ID (`D-01`, `D-02`…).
- Toujours afficher le disclaimer IA en tête de `summary.md`.
- Refuser de conclure sur les cas hors périmètre (voir section "When to Use") et
  renvoyer vers un professionnel.
````

- [ ] **Step 4: Vérifier la longueur de `SKILL.md`**

```bash
wc -w skills/declaration-impot-fr/SKILL.md
```
Expected: entre 400 et 600 mots. Si > 700, réviser pour déplacer du contenu dans les `references/`.

- [ ] **Step 5: Commit**

```bash
git add skills/declaration-impot-fr/
git commit -m "feat(skill): add declaration-impot-fr skeleton and entry point SKILL.md"
```

---

## Task 2: Écrire `references/initial-questionnaire.md`

**Files:**
- Create: `skills/declaration-impot-fr/references/initial-questionnaire.md`

- [ ] **Step 1: Écrire le fichier**

Structure à produire (reprendre les blocs A-G de la spec section 4 phase 1) :

```markdown
# Questionnaire initial — Phase 1

Avant toute ouverture de documents. Une question à la fois. Le but est d'établir
un profil fiscal préliminaire qui guide l'inventaire des docs attendus (phase 2) et
pré-sélectionne les formulaires/cases applicables (phase 4).

**Règles de conduite :**
- Une question à la fois, jamais de liste à 10 puces.
- Préférer les questions fermées (oui/non, multi-choix) quand possible.
- Si la réponse a des implications, les expliquer brièvement ("ok donc on va
  ajouter le formulaire 2047").
- Consigner les réponses dans `journal.md` (section "Profil fiscal préliminaire").

## Bloc A — Situation du foyer

- A1. État civil actuel : célibataire / marié / pacsé / divorcé / veuf ?
- A2. Régime matrimonial (si marié) : communauté légale / séparation de biens /
  participation aux acquêts / communauté universelle ?
- A3. Enfants à charge au 1er janvier de l'année : nombre + âge de chacun.
- A4. Garde alternée pour l'un des enfants ? (si oui → demi-parts différentes)
- A5. Rattachement d'un enfant majeur (≤ 25 ans étudiant ou ≤ 21 ans) ? Oui/Non.
- A6. Personne invalide à charge (titulaire carte mobilité inclusion mention
  "invalidité" ou ex-carte invalidité ≥ 80 %) ?

## Bloc B — Changements d'année N

(Année N = année des revenus déclarés ; par défaut 2025 si campagne 2026.)

- B1. Mariage ou PACS en N ? (si oui → imposition commune l'année entière
  depuis 2011, mais choix possible pour l'année du mariage uniquement dans
  quelques cas résiduels — à vérifier)
- B2. Divorce ou rupture de PACS en N ?
- B3. Naissance / adoption en N ?
- B4. Décès d'un membre du foyer en N ?
- B5. Déménagement en N ? (si oui → adresse nouvelle, cas impôts locaux)
- B6. Expatriation ou retour en France en N ? (⚠ si impatriation avec régime
  art. 155B → REFUS, renvoyer vers expert-comptable)
- B7. Départ d'un enfant du foyer (fin rattachement) ?

## Bloc C — Revenus et patrimoine attendus

Cocher tout ce qui s'applique :

- C1. Salaires (déclarant et/ou conjoint) ? Nombre d'employeurs dans l'année ?
- C2. Chômage / allocations Pôle Emploi / indemnités maladie ?
- C3. Pensions retraite / rentes ?
- C4. Revenus BNC (professions libérales, droits d'auteur, consulting) ?
- C5. Revenus BIC (commerçant, artisan, location meublée) ?
- C6. Revenus BA (agricole) ?
- C7. Revenus de capitaux mobiliers (dividendes, intérêts livrets fiscalisés,
  obligations, PEL > 12 ans) ?
- C8. Plus-values sur titres (vente actions, OPCVM, ETF) ?
- C9. Plus-values sur crypto-actifs ?
- C10. Revenus fonciers en France (location nue) ?
- C11. Revenus fonciers à l'étranger (location nue hors France, SCPI étrangère) ?
- C12. SCPI françaises (dividendes, plus-values) ?
- C13. Stock-options / RSU / AGA / BSPCE ? (attention : fiscalité complexe,
  escalade si gain de levée)
- C14. Comptes ou contrats à l'étranger (bancaires, courtage type IBKR / Trade
  Republic / Revolut, assurance-vie étrangère) ? (⚠ 3916 obligatoire)
- C15. Cession d'entreprise / plus-value professionnelle en N ? (⚠ REFUS si oui,
  renvoyer vers expert-comptable)

## Bloc D — Logement et emprunts

- D1. Propriétaire ou locataire de la résidence principale ?
- D2. Emprunt immobilier RP en cours ? (les intérêts RP ne sont plus déductibles
  depuis 2011 sauf cas historiques, mais les travaux financés à crédit peuvent
  ouvrir à réduction)
- D3. Autres emprunts avec potentiel fiscal :
  - D3a. Emprunt pour investissement locatif (nu ou meublé) ? → intérêts
    déductibles des revenus fonciers ou BIC.
  - D3b. Emprunt pour souscription de parts de SCPI ?
  - D3c. Emprunt pour souscription au capital de PME/ETI (Madelin) ?
  - D3d. Emprunt pour rachat de parts sociales de société soumise à IS ?
  - D3e. Emprunt pour acquisition/travaux sur monument historique ?
- D4. Travaux éligibles en N :
  - D4a. MaPrimeRénov' perçue ou demandée ? (non cumulable avec CITE sur la
    même dépense)
  - D4b. CEE (Certificats d'Économies d'Énergie) ?
  - D4c. Travaux dispositif Denormandie ?
  - D4d. Travaux monument historique ?

## Bloc E — Enfants et famille (réductions/crédits)

- E1. Frais de garde pour enfants < 6 ans : crèche, assistante maternelle
  agréée, garde à domicile ? → cases 7GA/7GB/7GC/7GG (crédit 50 %, plafond
  à vérifier selon année).
- E2. Enfants scolarisés :
  - E2a. Collège (7EA) — 61 € par enfant.
  - E2b. Lycée (7EC) — 153 € par enfant.
  - E2c. Enseignement supérieur (7EF) — 183 € par enfant.
- E3. Pension alimentaire versée à :
  - E3a. Enfant majeur hors foyer (plafond déductible).
  - E3b. Ex-conjoint (divorce/séparation, selon jugement).

## Bloc F — Autres réductions/crédits d'impôt courants

- F1. Emploi à domicile (ménage, jardinage, soutien scolaire, garde enfant > 6 ans
  à domicile) ? → crédit 50 %, plafond et majorations selon situation.
- F2. Dons aux associations en N :
  - F2a. Aide aux personnes en difficulté (Restos du Cœur, Croix-Rouge…) :
    réduction 75 % jusqu'à plafond, puis 66 %.
  - F2b. Intérêt général : réduction 66 %.
- F3. Dispositifs locatifs en cours :
  - F3a. Pinel / Pinel+ (engagement 6/9/12 ans) ?
  - F3b. Denormandie ?
  - F3c. Loc'Avantages (ex-Cosse) ?
  - F3d. Malraux ?
- F4. Cotisations syndicales ? → crédit 66 %.
- F5. Prévoyance Madelin (TNS) ?
- F6. PER (Plan Épargne Retraite) — versements déductibles ?
- F7. Souscription FCPI / FIP / SOFICA en N ?

## Bloc G — Méta

- G1. Année fiscale : revenus 2025 (campagne 2026) par défaut. Confirmer.
- G2. Déclaration conjointe ou séparée ? (couple marié/pacsé : conjointe par
  défaut ; première année de mariage/PACS pris en compte en 2011+ = commune).

## En sortie de phase 1

Écrire dans `journal.md` (section "Profil fiscal préliminaire") un résumé
structuré des réponses, en listant :

- Composition du foyer et parts fiscales prévisionnelles.
- Événements de l'année ayant un impact fiscal.
- Formulaires pressentis (liste déduite des réponses C/D/E/F).
- Pièges à surveiller identifiés (liens vers IDs de `pitfalls.md` : P-XX).
- **Cas de refus détectés** (B6 impatriation, C15 cession d'entreprise, etc.) —
  si oui, arrêter le skill ici et renvoyer vers professionnel.
```

- [ ] **Step 2: Vérifier que tous les blocs A-G sont présents**

```bash
grep "^## Bloc" skills/declaration-impot-fr/references/initial-questionnaire.md
```
Expected: 7 lignes (Bloc A, B, C, D, E, F, G).

- [ ] **Step 3: Commit**

```bash
git add skills/declaration-impot-fr/references/initial-questionnaire.md
git commit -m "feat(skill): add initial questionnaire reference (phase 1)"
```

---

## Task 3: Écrire `references/document-types.md`

**Files:**
- Create: `skills/declaration-impot-fr/references/document-types.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Types de documents — Phase 2

Guide des documents d'entrée qu'un déclarant peut fournir, ce qu'on y trouve, et
quelles cases ça alimente dans la déclaration.

**Mode d'ingestion : hybride.** L'utilisateur fournit ce qu'il peut (PDFs attachés,
captures, transcription texte). Le skill extrait ce qu'il peut lire et demande le
reste en saisie manuelle.

**En cas de doc illisible (PDF scanné basse résolution, captures floues) :** ne
PAS halluciner de chiffres. Demander à l'utilisateur de saisir les montants clés.

## IFU — Imprimé Fiscal Unique

Document annuel fourni par chaque établissement financier (banque, courtier,
SCPI, assureur vie). Disponible fin février / début mars de l'année N+1.

**Sections clés** (numéros de la grille officielle, reprises sur chaque IFU) :

- **Case 2DC** : revenus des valeurs mobilières (dividendes d'actions françaises
  et de certains pays) éligibles à l'abattement de 40 %.
- **Case 2CH** : revenus assimilés / produits de placements à revenu fixe autres
  que livrets fiscalisés.
- **Case 2TT** : intérêts sur livrets fiscalisés (livret ordinaire, comptes à terme).
- **Case 2DH** : jetons de présence et autres rémunérations.
- **Case 2BH** : revenus déjà soumis aux prélèvements sociaux (à reporter sur 2042).
- **Plus-values** : gains/pertes réalisés sur cessions → 3VG (gain net) ou 3VH (perte).
- **Prélèvements à la source** (crédit d'impôt) : case 2CK (PFU) ou 2CG (barème).

**Pour un IFU PEA :** normalement pas de revenus imposables à l'IR avant retrait,
mais un IFU peut mentionner des PS ou un retrait. Vérifier les cases 2CG/2BH.

**Pour un IFU SCPI :**
- SCPI française investissant en France → revenus fonciers (2044 : lignes
  "revenus de parts de sociétés").
- SCPI française investissant à l'étranger → revenus fonciers ÉTRANGERS →
  2047 + 2044. Piège P-01 à vérifier.

**Pour un IFU assurance-vie :** rachats imposables (prélèvement forfaitaire
libératoire ou IR barème selon date et durée contrat).

## Relevés bancaires

- Utiles pour : identifier des flux non expliqués (virements d'un
  broker étranger, intérêts de livret étranger, revenus locatifs perçus…).
- Montants à récupérer manuellement — pas de case directe.
- Chercher : virements SCPI récurrents, dividendes d'un broker non-IFU (ex:
  IBKR), rentes, salaires multiples.

## Échéancier de crédit immobilier

Document de la banque listant les mensualités payées en N, avec répartition
capital / intérêts / assurance.

**Cases concernées (usage classique) :**
- **Intérêts de prêt locatif** (immeuble donné en location nue) → déductibles
  des revenus fonciers sur 2044 (ligne 250).
- **Intérêts de prêt locatif meublé** → déductibles des recettes BIC sur 2031.
- **Intérêts RP** : en général non déductibles (sauf cas historique
  art. 199 sexdecies ou emprunts contractés entre 2007 et 2010 pour RP neuve).
- **Assurance emprunteur** : suit le régime des intérêts pour la RP ou le
  locatif.

## Attestations de garde d'enfants

- Crèche, micro-crèche, RAM → attestation annuelle avec montant payé, aide CAF
  perçue (PAJE CMG), et montant net restant à charge.
- Assistante maternelle agréée → attestation employeur + bulletins Pajemploi.
- Garde à domicile → bulletins Cesu + attestation fiscale Cesu.

**Case :**
- **7GA** (crèche, assistante maternelle, garde à domicile hors Cesu).
- **7GB** (si domicile).
- **7GC** (RAM).
- **7GG** (en cas de garde alternée).
- Montant à déclarer = net restant à charge (après déduction PAJE CMG et
  aides CAF). Piège P-08.

## Attestations de scolarité

- Certificat de scolarité du collège / lycée / enseignement supérieur.
- Pas de montant — juste cocher la case 7EA / 7EC / 7EF par enfant.

## Attestations fiscales diverses

- **CESU (emploi à domicile)** : attestation annuelle → case 7DB.
  Montant = dépenses réelles − aides perçues (APA, PCH).
- **Dons associations** : reçu fiscal CERFA nº 11580 → case 7UD (aide aux
  personnes) ou 7UF (intérêt général).
- **Madelin / PER** : attestation du contrat → case 6NS/6NT (PER déductible)
  ou 7GZ/7RS (Madelin).
- **Pinel** : attestation d'engagement + déclaration 2044-EB → cases 7QA à 7QR
  selon millésime et durée.

## Documents immobilier locatif

Pour les revenus fonciers (2044) ou BIC (2031) :

- **Décompte annuel de gestion locative** (si gérance) — recettes brutes,
  charges déductibles, honoraires.
- **Factures travaux** en N — déductibles sous conditions (travaux d'entretien,
  de réparation, non la construction/agrandissement).
- **Primes d'assurance loyers impayés, PNO** — déductibles.
- **Taxe foncière** — déductible (hors TEOM réputée à la charge du locataire).

## Attestations fonds de solidarité / autres

- Indemnités journalières, allocations CAF (RSA, AAH…) : usuellement non
  imposables mais certaines le sont — vérifier au cas par cas.

## Sur les documents manquants

Après réception de ce que l'utilisateur a, **comparer au profil phase 1** :

- Profil dit "salarié" mais aucun bulletin/IFU employeur → demander.
- Profil dit "PEA" mais pas d'IFU broker → demander.
- Profil dit "compte IBKR" mais pas d'extraction → demander (IBKR fournit un
  consolidated statement mais pas d'IFU français, il faut saisir manuellement
  et déclarer 3916).
- Profil dit "SCPI" mais pas d'IFU SCPI → demander (envoyé par la société de
  gestion).

Consigner dans `journal.md` la liste des documents fournis, extraits, et
manquants.
```

- [ ] **Step 2: Vérifier la structure**

```bash
grep "^## " skills/declaration-impot-fr/references/document-types.md
```
Expected: au moins 8 sections (IFU, Relevés, Échéancier, Garde, Scolarité, CESU/dons, Immo locatif, Manquants).

- [ ] **Step 3: Commit**

```bash
git add skills/declaration-impot-fr/references/document-types.md
git commit -m "feat(skill): add document-types reference (phase 2)"
```

---

## Task 4: Écrire `references/forms-catalog.md`

**Files:**
- Create: `skills/declaration-impot-fr/references/forms-catalog.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Catalogue des formulaires CERFA — Phase 4

Référence des formulaires CERFA courants pour la déclaration d'impôt sur le revenu
et leurs cases clés. Utilisé en phase 4 pour sélectionner les formulaires
applicables à partir du profil (phase 1) et des documents (phase 2).

**Règle d'or :** pour les valeurs précises (plafonds, seuils, taux) et les cases
non listées ici, **fetcher la notice officielle** sur impots.gouv.fr. Ce catalogue
donne le squelette, pas le détail à jour.

## 2042 — Déclaration principale

Formulaire de base, obligatoire. Toutes les personnes déclarent au moins une 2042.

**Sections et cases clés :**

- **1 — Traitements, salaires, pensions**
  - 1AJ / 1BJ : salaires imposables (déclarant / conjoint).
  - 1AP / 1BP : pensions, retraites.
  - 1AK / 1BK : frais réels (option vs abattement 10 %).
  - 1AA / 1BA : allocations chômage.
- **2 — Revenus de capitaux mobiliers**
  - 2DC : dividendes éligibles abattement 40 %.
  - 2TT : intérêts livrets fiscalisés.
  - 2CH : produits à revenu fixe.
  - 2BH : revenus déjà soumis aux PS.
  - 2CK / 2CG : prélèvements forfaitaires (crédits d'impôt).
  - **2OP : option imposition au barème progressif (PFU sinon par défaut).**
- **3 — Plus-values**
  - 3VG : plus-value nette globale de l'année.
  - 3VH : moins-value globale (report 10 ans).
  - 3WB : plus-value avec abattement pour durée de détention (régime ancien).
- **4 — Revenus fonciers**
  - 4BA : revenus fonciers nets (report du 2044).
  - 4BB : déficit foncier imputable sur revenu global (plafond 10 700 €).
  - 4BK : revenus fonciers étrangers avec crédit d'impôt égal impôt français.
  - 4BL : revenus fonciers étrangers avec exonération + taux effectif.
- **6 — Charges déductibles**
  - 6DD : pensions alimentaires à enfants majeurs.
  - 6GI / 6GJ : pensions alimentaires à ex-conjoint.
  - 6NS / 6NT : versements PER.
- **8 — Divers / reports**
  - 8TK : crédit d'impôt pour revenus étrangers (report de 2047).
  - 8TI : revenus étrangers exonérés retenus pour taux effectif (report 2047).
  - 8UU : comptes à l'étranger (case à cocher si 3916 joint).
  - 8HV / 8HW : contribution exceptionnelle sur hauts revenus (CEHR).

**Notice officielle :** https://www.impots.gouv.fr/formulaire/2042/declaration-des-revenus

## 2042-C — Déclaration complémentaire

Pour : revenus exceptionnels, plus-values complexes, revenus spécifiques.

- 1TP / 1UP : salaires imposables au quotient.
- 3SG : plus-values mobilières déclarées avec abattement renforcé.
- 3VE : gains de levée d'options sur actions.
- 3VI : gains RSU.

## 2042-C-PRO — Revenus des professions non salariées

Pour : BIC, BNC, BA en micro ou réel.

- 5HQ : recettes BIC micro (location meublée saisonnière non classée).
- 5ND / 5OD : recettes BIC micro (location meublée longue durée).
- 5HP / 5IP : recettes micro-BNC.
- 5QC / 5RC : BNC réel déclaration contrôlée.

## 2042-RICI — Réductions et crédits d'impôt

Détail des réductions/crédits.

- 7DB : emploi à domicile (crédit 50 %).
- 7GA / 7GB / 7GC / 7GG : garde d'enfants < 6 ans.
- 7EA / 7EC / 7EF : scolarité enfants (collège / lycée / sup).
- 7UD / 7UF : dons (aide aux personnes 75 % / intérêt général 66 %).
- 7AC / 7AE : cotisations syndicales.
- 7UH : investissements FIP / FCPI / SOFICA.
- 7QA-7QR : Pinel / Pinel+ par millésime.

## 2044 — Revenus fonciers (régime réel)

Pour : location nue en France (hors micro-foncier).

Sections principales : recettes (lignes 210-215), charges déductibles (lignes
220-265, dont intérêts 250), résultat (lignes 400+), report sur 2042 ligne 4BA.

Obligatoire dès que recettes > 15 000 € OU option au réel OU déficit.

## 2044-SPE — Revenus fonciers spéciaux

Pour : monuments historiques, Périssol, Borloo, Pinel, Scellier… Régime dérogatoire.

## 2047 — Revenus encaissés à l'étranger

Obligatoire dès qu'un revenu provient de l'étranger (même avec crédit d'impôt).

- Section VI : revenus fonciers étrangers (SCPI étrangère incluse).
- Section VII : plus-values étrangères.
- Section VIII : revenus de capitaux mobiliers étrangers.
- **Pour chaque ligne : préciser le pays + la convention applicable** (crédit
  d'impôt égal / exonération avec taux effectif / pas de convention).

Report sur 2042 cases 4BK / 4BL (fonciers) + 8TK / 8TI (crédit / exonération).

**Notice 2047 :** https://www.impots.gouv.fr/formulaire/2047/

## 2074 — Plus-values sur titres (régime général)

Pour : cessions d'actions, parts, OPCVM hors PEA.

Détail des lignes de cession, calcul de la plus-value ou moins-value, report sur
2042 case 3VG ou 3VH.

## 2074-DIR / 2074-NR / 2074-I

- 2074-DIR : dirigeants partant à la retraite (abattement fixe).
- 2074-NR : non-résidents (hors périmètre, REFUS).
- 2074-I : impatriés (hors périmètre, REFUS).

## 2086 — Plus-values sur crypto-actifs

Pour : cessions d'actifs numériques en régime occasionnel (régime général PFU 30 %
sur plus-value nette annuelle).

Détail par cession : date, prix, valeur initiale portefeuille, valeur actuelle.
Report sur 2042 case 3AN (plus-value) ou 3BN (moins-value).

## 3916 / 3916-bis — Déclaration de comptes à l'étranger

Obligatoire pour tout compte bancaire, compte de paiement, compte de cryptoactifs,
ou contrat de capitalisation ouvert, détenu, utilisé ou clos à l'étranger en N.

**Un formulaire 3916 par compte.** Pour les cryptos (ex : Binance, Kraken, portefeuille
auto-hébergé lié à un service étranger) : 3916-bis.

**Cases 2042 associée :** 8UU (cocher).

Sanction en cas d'omission : amende 1500 €/compte (10 000 € si pays non coopératif).

## Tableau de sélection rapide (par type de revenu)

| Type de revenu / situation | Formulaire(s) |
|---|---|
| Salaires uniquement | 2042 |
| Dividendes, intérêts français | 2042 |
| Plus-values titres (hors PEA) | 2042 + 2074 |
| PEA | 2042 (pas de 2074 sauf retrait anticipé) |
| Crypto-actifs | 2042 + 2086 |
| Location nue France (< 15 000 €) | 2042 (micro-foncier) |
| Location nue France (> 15 000 € ou déficit) | 2042 + 2044 |
| Location meublée (LMNP micro) | 2042 + 2042-C-PRO |
| Location meublée (LMNP réel) | 2042 + 2042-C-PRO + 2031 (cerfa pro) |
| SCPI française revenus France | 2042 + 2044 |
| SCPI étrangère | 2042 + 2044 + 2047 |
| Compte à l'étranger | 2042 (8UU) + 3916 par compte |
| Stock-options levée | 2042 + 2042-C |
| RSU / AGA | 2042 + 2042-C |
| Emploi à domicile | 2042 + 2042-RICI |
| Garde enfants < 6 ans | 2042 + 2042-RICI |
| Dons | 2042 + 2042-RICI |
| Pinel en cours | 2042 + 2042-RICI + 2044-EB (la première année) |

Utiliser ce tableau en phase 4 pour composer la liste des formulaires à remplir.
```

- [ ] **Step 2: Vérifier la structure**

```bash
grep "^## " skills/declaration-impot-fr/references/forms-catalog.md
```
Expected: au moins 10 sections (formulaires + tableau de sélection).

- [ ] **Step 3: Commit**

```bash
git add skills/declaration-impot-fr/references/forms-catalog.md
git commit -m "feat(skill): add forms catalog reference (CERFA overview)"
```

---

## Task 5: Écrire `references/pitfalls.md`

**Files:**
- Create: `skills/declaration-impot-fr/references/pitfalls.md`

- [ ] **Step 1: Écrire le fichier**

Structure : 12 pièges P-01 à P-12, chaque entrée ayant Détection / Risque / Question
user / Branches / Source.

```markdown
# Catalogue des pièges fiscaux — Phase 4 & 5

Chaque piège est une **situation ambiguë ou à fort risque d'erreur** qui doit
déclencher un mode vigilant (question utilisateur systématique) et éventuellement
une escalade (WebFetch notice officielle).

Chaque entrée :
- **Détection** : comment le skill sait qu'il est dans ce cas (docs ou profil).
- **Risque** : conséquence en cas d'erreur.
- **Question user** : formulation type pour lever l'ambiguïté.
- **Branches** : les options possibles et leur traitement.
- **Source** : notice officielle ou BOFiP à référencer.

Chaque décision prise sur un piège doit être consignée dans `journal.md` avec
son ID (`D-NN`) et une référence au piège (`P-NN`).

---

## P-01 — SCPI étrangère : traitement conventionnel

**Détection** : `document-types.md` identifie un IFU SCPI dont les immeubles
sont majoritairement à l'étranger (nom type Corum XL, Eurovalys, Pierval Santé,
LF Europimmo, …).

**Risque** : mauvais traitement conventionnel → double imposition OU imposition
moins-disante → redressement.

**Question user** :
> "Dans quel(s) pays cette SCPI investit-elle majoritairement ?
> (Allemagne / Italie / Espagne / Portugal / Pays-Bas / Irlande / autre)"

**Branches** :
- **Convention avec crédit d'impôt égal à l'impôt français** (Allemagne, Italie,
  Espagne, Portugal, la plupart des conventions récentes) →
  - Revenus nets fonciers sur **2042 case 4BK** (et détail 2044 si réel).
  - Report sur **2047 section VI**, colonne "crédit d'impôt".
  - Report du crédit d'impôt sur **2042 case 8TK**.
- **Convention avec exonération avec taux effectif** (certaines conventions
  anciennes, UK pré-Brexit, Pays-Bas sur certains revenus) →
  - Revenus nets fonciers sur **2042 case 4BL**.
  - Report sur **2047 section VI**, colonne "exonéré".
  - Report sur **2042 case 8TI**.
- **Pas de convention** ou pays non couvert → imposition normale en France.

**Source** : notice 2047 section VI + liste BOI-INT-CVB-<pays> sur BOFiP.
Escalade recommandée (WebFetch notice 2047) sur ce piège.

---

## P-02 — Frais réels vs abattement forfaitaire 10 %

**Détection** : salaires déclarant ou conjoint > 15 000 € ET profil phase 1
mentionne frais professionnels significatifs (km domicile-travail > 40 km aller,
double résidence, formation, déjeuner sur lieu de travail, télétravail).

**Risque** : choisir l'option moins avantageuse → manque à gagner (ou au contraire
choisir frais réels sans justificatifs → redressement).

**Question user** :
> "Tu veux déclarer tes frais professionnels aux frais réels ? (nécessite
> justificatifs : relevés km, tickets resto, factures formation, etc.) Ou garder
> l'abattement forfaitaire de 10 % ?"

**Branches** :
- **Abattement 10 %** (par défaut) : rien à faire, abattement auto, plafond
  14 426 € (2024 — vérifier valeur 2025).
- **Frais réels** :
  - Cases **1AK / 1BK** sur 2042.
  - Montant total saisi ; détail à produire en cas de contrôle.
  - Indemnités kilométriques : barème officiel DGFIP.
  - **Attention** : si frais réels choisi → toutes les indemnités et remboursements
    de frais perçus de l'employeur deviennent imposables (à ajouter aux salaires
    1AJ/1BJ).

**Source** : BOI-RSA-BASE-30 + barème kilométrique annuel.

---

## P-03 — LMNP micro-BIC vs réel simplifié

**Détection** : profil phase 1 mentionne location meublée (C5 = oui) OU doc
d'activité locative meublée (bail meublé, livre-journal LMNP).

**Risque** : choisir le régime moins avantageux → impôt supérieur de milliers d'euros
sur la durée.

**Seuils (à valider pour l'année N)** :
- Micro-BIC applicable si recettes annuelles ≤ **77 700 €** (location meublée
  classique) — abattement 50 %.
- Meublé de tourisme **non classé** : seuil abaissé à **15 000 €** et abattement
  **30 %** depuis loi de finances 2024.
- Meublé de tourisme **classé** : seuil **188 700 €** et abattement 71 %.

**Question user** :
> "Quel est le montant total des recettes meublées de l'année ? Et tu as des
> charges/amortissements significatifs (amortissement du bien, travaux, intérêts
> d'emprunt, frais de notaire, gestion locative, assurance) ?"

**Arbitrage** :
- Si abattement micro > charges réelles + amortissements → **micro**.
- Sinon → **réel simplifié** (plus avantageux le plus souvent car amortissement
  du bien déductible sur 20-30 ans).

**Branches** :
- **Micro-BIC** : cases 5ND/5OD (longue durée) ou 5HQ (saisonnier non classé) sur
  2042-C-PRO.
- **Réel simplifié** : remplir 2031 + 2033 (cerfa pro), reporter résultat sur
  2042-C-PRO cases 5NC/5OC.

**Source** : notice 2042-C-PRO + notice 2031.

---

## P-04 — Option PFU 30 % vs barème progressif (case 2OP)

**Détection** : RCM > 0 (dividendes, intérêts fiscalisés, produits fixes).

**Risque** : choisir PFU alors que barème serait plus avantageux (ou l'inverse).

**Comparatif rapide** :
- **PFU (prélèvement forfaitaire unique)** : 30 % total (12,8 % IR + 17,2 % PS).
  Par défaut, case 2OP **non cochée**.
- **Barème progressif** : TMI sur RCM après abattement 40 % sur dividendes
  éligibles + PS 17,2 %. Avantageux si TMI ≤ 11 %. Case 2OP **cochée**.

**Question user** :
> "Tu veux cocher l'option pour le barème progressif (case 2OP) ? Je peux
> estimer si c'est plus avantageux selon ta TMI et tes revenus de capitaux."

**Branches** :
- TMI ≤ 11 % et RCM avec beaucoup de dividendes éligibles abattement 40 % →
  barème probable.
- TMI ≥ 30 % → PFU en général.
- **Attention** : l'option barème s'applique à **tous** les RCM du foyer (pas case
  par case).

**Source** : BOI-RPPM-RCM-20.

---

## P-05 — Contribution exceptionnelle sur hauts revenus (CEHR)

**Détection** : Revenu Fiscal de Référence prévisionnel ou calculé dépassant
les seuils.

**Seuils** :
- Célibataire / divorcé / veuf : > 250 000 € RFR → CEHR.
- Couple marié / pacsé : > 500 000 € RFR → CEHR.

**Taux** : 3 % (entre seuil et double seuil) puis 4 % (au-delà). Mécanisme de
lissage en cas de revenu exceptionnel (quotient).

**Risque** : omission → rappel + intérêts de retard (pas de pénalité intentionnelle
normalement).

**Action** :
- Compléter cases **8HV (déclarant) / 8HW (conjoint)** si revenu exceptionnel
  pouvant bénéficier du lissage.
- Sinon le calcul est automatique sur impots.gouv.fr ; rien à cocher.

**Source** : art. 223 sexies CGI, BOI-IR-CEHR.

---

## P-06 — Compte à l'étranger non déclaré (3916)

**Détection** : profil phase 1 (C14 = oui) ou présence d'un IFU étranger / relevé
broker étranger / appli type Trade Republic / Revolut.

**Risque** : amende **1500 €/compte** par année non déclarée (10 000 € si pays
non coopératif). Interruption prescription sur revenus du compte → risque rappel
10 ans en arrière.

**Action obligatoire** :
- Un formulaire **3916** (ou 3916-bis pour crypto) **par compte**.
- **Case 8UU** cochée sur 2042.
- Informations à fournir : désignation, n° compte, date d'ouverture, organisme
  tenant le compte, adresse organisme, caractéristiques.

**Question user** :
> "Liste-moi tous tes comptes à l'étranger utilisés ou ouverts en N : banque,
> courtage (IBKR, Trade Republic, eToro…), néo-banque (Revolut, Wise), assurance-vie
> étrangère, plateforme crypto hébergée à l'étranger (Binance, Kraken, Bitstamp…).
> J'ai besoin du nom, du n° de compte, du pays, et de la date d'ouverture."

**Point d'attention** : Revolut a une entité française depuis 2021, donc les
comptes ouverts après cette date peuvent être français (à vérifier sur le IBAN —
commence par FR = France, LT = Lituanie = étranger).

**Source** : art. 1649 A CGI, notice 3916.

---

## P-07 — Stock-options / RSU / AGA — qualification fiscale

**Détection** : profil phase 1 (C13) OU mention dans contrat de travail / IFU
employeur / doc type "equity statement".

**Risque** : fiscalité complexe, variable selon :
- Type d'instrument (SO, RSU, AGA, BSPCE).
- Date d'attribution (les régimes ont changé en 2012, 2015, 2018).
- Statut PME / non-PME à l'attribution.
- Date et modalité de cession.

**Action** : **ne pas conclure seul**. Demander à l'utilisateur :

**Question user** :
> "Pour chaque plan (SO/RSU/AGA/BSPCE) que tu as :
> 1. Type d'instrument ?
> 2. Date d'attribution ?
> 3. Date et prix de levée / acquisition ?
> 4. As-tu cédé les actions ? Si oui, date et prix de cession ?
> 5. Montant qui apparaît dans ton IFU employeur ou ton bulletin de salaire ?"

**Branches** : traitement très variable — escalade systématique, WebFetch notice
2042-C + BOI-RSA-ES. Si cas peu clair → **refus et renvoi vers expert-comptable**.

**Source** : notice 2042-C + BOI-RSA-ES.

---

## P-08 — Crédit d'impôt garde d'enfants < 6 ans

**Détection** : profil phase 1 (E1) OU attestation de garde dans documents.

**Risque** :
- Oublier une aide CAF déjà perçue → crédit d'impôt trop élevé → redressement.
- Déclarer pour un enfant de > 6 ans au 1er janvier → refus.

**Règle** :
- Enfant **< 6 ans au 1er janvier N**.
- Crédit **50 %** des dépenses.
- **Plafond** : 3 500 € par enfant (2024, à vérifier pour N), **7 000 €** en cas
  de handicap.
- Montant déclaré = dépenses réelles **− aides perçues (PAJE CMG, aides CAF)**.
- Cases : **7GA** (hors domicile : crèche, AssMat, RAM) / **7GB** (à domicile) /
  **7GC** (RAM spécifique) / **7GG** (garde alternée).

**Question user** :
> "Pour chaque enfant < 6 ans : quel était le mode de garde (crèche / AssMat /
> domicile) ? Quel est le montant **net restant à charge** (après déduction du CMG
> versé par la CAF) ? C'est le montant à déclarer."

**Source** : art. 200 quater B CGI, notice 2042-RICI.

---

## P-09 — Emploi à domicile : plafond majoré

**Détection** : profil phase 1 (F1) OU attestation Cesu dans documents.

**Règle** :
- Crédit d'impôt 50 % sur dépenses éligibles.
- Plafond **12 000 €** (2024) + 1 500 € par enfant ou ascendant > 65 ans (plafond
  max 18 000 €).
- Plafond **20 000 €** si déclarant ou membre foyer invalide / titulaire CMI.
- **Dépenses = paiements réels − aides (APA, PCH, aide CAF).**
- Case : **7DB** sur 2042-RICI.

**Piège** : ne pas confondre avec garde d'enfants (7GA). Pour garde enfant
*hors domicile* (crèche) → 7GA. Pour garde enfant *à domicile* : c'est 7DB
(emploi à domicile) si l'enfant a > 6 ans, ou 7GB si < 6 ans.

**Question user** :
> "Les services à domicile concernent-ils : ménage, jardinage, soutien scolaire,
> garde d'enfant > 6 ans à domicile, aide à une personne âgée/handicapée au foyer ?"

**Source** : art. 199 sexdecies CGI, notice 2042-RICI.

---

## P-10 — Dons : deux plafonds différents

**Détection** : profil phase 1 (F2) OU reçu fiscal dans documents.

**Règle** :
- **Dons aux associations d'aide aux personnes en difficulté** (Restos du Cœur,
  Secours Catholique, Banque Alimentaire, Croix-Rouge pour certaines actions) :
  **réduction 75 %** jusqu'à 1 000 € (plafond 2025 à vérifier), puis 66 %
  au-delà. Case **7UD**.
- **Dons aux autres associations d'intérêt général** (culture, environnement,
  recherche, association sportive d'intérêt général) : **réduction 66 %**,
  plafond **20 % du revenu imposable**. Case **7UF**.

**Risque** : déclarer un don en 7UD alors qu'il relève de 7UF → refus admin ou
redressement.

**Question user** :
> "Pour chaque don, quel est le nom de l'association ? L'association délivre-t-elle
> un reçu fiscal précisant 'aide aux personnes en difficulté' (art. 200.1 ter) ou
> 'intérêt général' (art. 200.1) ?"

**Source** : art. 200 CGI, notice 2042-RICI.

---

## P-11 — Travaux RP : non-cumul MaPrimeRénov' / CITE

**Détection** : profil phase 1 (D4a et/ou D4b).

**Règle** :
- **MaPrimeRénov'** : versée directement par l'ANAH au ménage, non imposable,
  **à déduire** du montant des travaux éligibles au CITE pour éviter le double
  avantage.
- **CITE** : supprimé pour les ménages à revenus intermédiaires/élevés depuis
  2020, remplacé par MaPrimeRénov'. Il reste des reliquats (reports antérieurs)
  mais pas de nouveaux CITE.
- **CEE** (Certificats d'Économies d'Énergie) : versés par l'énergéticien, non
  imposables, à déduire aussi.

**Action** :
- Pas de case CITE pour travaux récents (2024-2025).
- Éventuelles primes à ne pas déclarer comme revenus.
- Si travaux sur investissement locatif → déductibles des revenus fonciers
  (2044), montant **net des aides**.

**Source** : art. 200 quater CGI, BOI-IR-RICI-280.

---

## P-12 — Changement de situation familiale en année N

**Détection** : profil phase 1 bloc B (B1, B2, B3, B4, B6, B7).

**Règles clés** :
- **Mariage / PACS en N** : depuis 2011, imposition **commune** pour toute l'année
  N. Plus de déclaration séparée possible (sauf rare option).
- **Divorce / rupture PACS en N** : **deux déclarations séparées** pour l'année
  entière — à la date de rupture, chaque ex-conjoint refait sa propre déclaration
  couvrant l'année entière avec sa quote-part de revenus.
- **Décès d'un conjoint en N** : deux déclarations — une pour le couple du 1/1
  à la date de décès, une pour le survivant du jour du décès au 31/12.
- **Naissance d'un enfant en N** : demi-part entière au titre de l'année N
  (pas de prorata).

**Risque** : déclaration mal scindée → double imposition ou omission.

**Question user** (en cas de changement) :
> "Tu as mentionné un [mariage/divorce/décès] en N à la date du JJ/MM. Veux-tu
> que je t'explique comment cela scinde ta déclaration ? (Important : depuis 2011,
> le mariage = commune pour l'année entière, et le divorce = deux séparées pour
> l'année entière)."

**Source** : art. 6 CGI, BOI-IR-CHAMP-20.

---

## Cas déclenchant un REFUS systématique

Ne pas traiter dans le skill. Renvoyer explicitement vers un expert-comptable ou
le centre des finances publiques :

- **R-01** : cession d'entreprise individuelle ou parts de société (plus-value
  professionnelle) en N.
- **R-02** : régime d'impatriation (art. 155B) — primes d'impatriation, exonération
  partielle.
- **R-03** : fonctionnaire international ou salarié d'organisation internationale
  avec statut fiscal spécifique.
- **R-04** : non-résident fiscal (résidence principale hors France une partie de
  l'année, doute sur article 4B CGI).
- **R-05** : double résidence fiscale ou conflit de convention fiscale.
- **R-06** : montages complexes (holdings patrimoniales, pactes Dutreil,
  déclaration 2035 pour profession libérale en SELARL, etc.).
- **R-07** : succession ouverte en N avec impact sur les revenus du foyer
  (à déclarer en partie séparée par les héritiers).

Pour chacun de ces cas, message type :
> "Ta situation inclut [cas]. Ce type de déclaration nécessite une expertise
> spécifique que ce skill ne couvre pas. Je te recommande de consulter un
> expert-comptable ou de prendre rendez-vous avec ton centre des finances publiques
> avant de déposer ta déclaration."
```

- [ ] **Step 2: Vérifier tous les pièges**

```bash
grep -E "^## (P-|R-)" skills/declaration-impot-fr/references/pitfalls.md
```
Expected: 19 lignes (P-01 à P-12 + R-01 à R-07).

- [ ] **Step 3: Commit**

```bash
git add skills/declaration-impot-fr/references/pitfalls.md
git commit -m "feat(skill): add pitfalls catalog (P-01 to P-12 + refusal cases R-01 to R-07)"
```

---

## Task 6: Écrire `references/online-mapping.md`

**Files:**
- Create: `skills/declaration-impot-fr/references/online-mapping.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Mapping CERFA ↔ écrans impots.gouv.fr — Phase 5

Correspondance entre les cases des formulaires CERFA papier et les écrans de la
télédéclaration sur impots.gouv.fr. Utilisé en phase 5 pour guider l'utilisateur
dans sa saisie en ligne.

**Principe** : sur impots.gouv.fr, la déclaration se fait en 3 étapes (la vue
cases papier est aussi disponible via "Afficher la vue cases" mais moins ergonomique) :

1. **Étape "État civil et situation de famille"** → correspond aux en-têtes 2042.
2. **Étape "Revenus et charges"** → écrans par catégorie (salaires, RCM, fonciers,
   étranger, réductions).
3. **Étape "Récap et signer"**.

## Étape 1 — État civil et situation de famille

Écrans successifs :

- **Situation personnelle** :
  - Case à cocher A-F (marié/pacsé/célibataire/divorcé/veuf).
  - Date de mariage/PACS/divorce si changement en N.
- **Personnes à charge** :
  - Nombre d'enfants (cases F, G, H, I, J de la 2042).
  - Garde alternée (case H).
  - Enfant majeur rattaché (case N).
- **Adresse au 1er janvier et au 31 décembre**.

## Étape 2 — Revenus et charges

Navigation par catégorie, dans cet ordre généralement :

### Écran "Traitements, salaires, pensions, rentes"
- Cases **1AJ / 1BJ** : salaires nets imposables (pré-remplis par DSN employeur).
- Cases **1AK / 1BK** : option frais réels (saisie manuelle → formulaire de détail).
- Cases **1AP / 1BP** : pensions.
- Cases **1AA / 1BA** : allocations chômage.
- **Cocher si option quotient** (1 MAJOR = revenu exceptionnel).

### Écran "Revenus de capitaux mobiliers"
- Cases **2DC, 2TT, 2CH, 2BH** (pré-remplis par IFU).
- Cases **2CK, 2CG** : prélèvements (crédit d'impôt) pré-remplis.
- **Case 2OP** : option barème progressif (à cocher ou non).
- **Si revenus étrangers** (ex : dividendes broker US) : renvoyer vers la 2047.

### Écran "Plus-values et gains divers"
- **Case 3VG** : plus-value nette (report de 2074).
- **Case 3VH** : moins-value nette.
- **Case 3AN** : plus-value crypto (report 2086).
- **Case 3WB** : plus-value avec abattement durée détention.

### Écran "Revenus fonciers"
- Si micro-foncier (recettes ≤ 15 000 €, location nue) : saisie directe sur 2042
  ligne **4BE**.
- Si régime réel : annexe **2044** (lien dédié en haut de l'écran).
- **4BK** : revenus fonciers étrangers avec CI (report de 2047).
- **4BL** : revenus fonciers étrangers exonérés (report de 2047).
- **4BB** : déficit imputable sur revenu global.

### Écran "Revenus étrangers"
- Accès : "Revenus encaissés à l'étranger" → **formulaire 2047**.
- Pour chaque revenu : préciser pays, type (foncier, RCM, PV, salaire),
  convention applicable.
- Reports automatiques vers 2042 cases 4BK/4BL, 8TI/8TK, etc.

### Écran "Charges déductibles"
- **6DD** : pensions alimentaires.
- **6NS / 6NT** : versements PER.

### Écran "Réductions et crédits d'impôt" (2042-RICI)
- **7DB** : emploi à domicile.
- **7GA/7GB/7GC/7GG** : garde enfants < 6 ans.
- **7EA/7EC/7EF** : scolarité collège/lycée/sup.
- **7UD / 7UF** : dons.
- **7AC / 7AE** : cotisations syndicales.
- **7QA à 7QR** : dispositifs Pinel.

### Écran "Divers" / "Éléments divers"
- **8UU** : compte à l'étranger (à cocher).
- **8HV / 8HW** : CEHR option lissage.
- **8TK / 8TI** : reports étrangers.

### Écran "Comptes à l'étranger" (annexe 3916)
- Un bloc 3916 par compte (ajout via "+ Ajouter un compte").

## Étape 3 — Récap et signer

- Vérifier le **total imposable** et l'**impôt calculé**.
- Ne pas signer avant d'avoir rapproché chaque case du `summary.md`.
- En cas d'écart : retour étape 2 via "Modifier".

## Correspondance "vue cases" vs "écrans guidés"

Sur impots.gouv.fr, bouton "Afficher les cases" en haut à droite : permet une vue
formulaire papier, utile pour suivre le `summary.md` case par case.

## Règles en phase 5

1. Lire `summary.md` dans l'ordre.
2. Pour chaque ligne → indiquer à l'utilisateur l'écran cible + case + valeur.
3. Attendre "ok c'est rempli".
4. Passer à la suivante.
5. Si la case n'existe pas à l'écran indiqué (mise à jour du site) → basculer en
   "vue cases" et chercher par code.
```

- [ ] **Step 2: Vérifier la structure**

```bash
grep "^## " skills/declaration-impot-fr/references/online-mapping.md
```
Expected: au moins 4 sections principales (Étape 1, Étape 2, Étape 3, vue cases).

- [ ] **Step 3: Commit**

```bash
git add skills/declaration-impot-fr/references/online-mapping.md
git commit -m "feat(skill): add online mapping reference (CERFA boxes to impots.gouv.fr screens)"
```

---

## Task 7: Écrire les trois templates

**Files:**
- Create: `skills/declaration-impot-fr/templates/plan.md`
- Create: `skills/declaration-impot-fr/templates/journal.md`
- Create: `skills/declaration-impot-fr/templates/summary.md`

- [ ] **Step 1: Écrire `templates/plan.md`**

```markdown
# Déclaration des revenus <ANNÉE> — Plan

**État** : en cours • Phase <N>/6
**Profil** : <composition foyer + situations clés>
**Formulaires applicables** : <liste : 2042, 2042-C, 2042-RICI, 2044, 2047, 3916…>
**Généré** : <YYYY-MM-DD>
**Mise à jour** : <YYYY-MM-DD HH:MM>

---

## Légende

- 🟢 terminé • 🟡 en cours • ⚪ à faire
- `[x]` / `[ ]` : case individuelle remplie / à remplir
- `[!]` : piège (voir `journal.md` décision D-NN)
- `TODO` : valeur encore inconnue

---

## [⚪] <FORMULAIRE 1 : ex. 2042 — Déclaration principale>

### [⚪] Section <N> — <Libellé>

- [ ] <code case> (<libellé>) = `<valeur|TODO>` — source : <doc + page ou saisie user>
- [ ] …

### [⚪] Section <N+1> — <Libellé> [!] <piège éventuel>

- [ ] …

## [⚪] <FORMULAIRE 2 : ex. 2044 — Revenus fonciers>
…

## [⚪] <FORMULAIRE 3 : ex. 2047 — Revenus étrangers>
…

---

## Pièges actifs

- [!] P-NN — <nom du piège> → décision D-NN dans `journal.md`.

## Questions en attente

- <question ouverte adressée à l'utilisateur>
```

- [ ] **Step 2: Écrire `templates/journal.md`**

```markdown
# Déclaration <ANNÉE> — Journal

**Déclarant(s)** : <nom(s)>
**Foyer fiscal** : <composition>
**Campagne** : <année N+1>
**Démarré** : <YYYY-MM-DD>

---

## Profil fiscal préliminaire (phase 1)

### Situation du foyer
- <résumé bloc A>

### Changements en N
- <résumé bloc B>

### Revenus et patrimoine attendus
- <résumé bloc C>

### Logement et emprunts
- <résumé bloc D>

### Enfants et famille
- <résumé bloc E>

### Autres réductions/crédits
- <résumé bloc F>

### Méta
- <résumé bloc G>

---

## Documents (phase 2)

### Fournis
- `<fichier>` — reçu <YYYY-MM-DD> — <type> (IFU / relevé / échéancier / attestation…)

### Extraits / saisis manuellement
- <résumé par doc : cases clés extraites ou saisies>

### Manquants (à demander à l'utilisateur)
- <document attendu mais non fourni>

---

## Décisions

### D-01 — <titre court>
**Date** : <YYYY-MM-DD>
**Piège rattaché** : P-NN (ou aucun)
**Contexte** : <explication situation>
**Options envisagées** : <option A / option B>
**Choix** : <option retenue>
**Justification** : <pourquoi>
**Source** : <notice CERFA / BOI / WebFetch impots.gouv.fr du YYYY-MM-DD>
**Impact sur cases** : <liste cases + formulaires impactés>

### D-02 — …

---

## Pré-flight check (phase 6)

À remplir lors du check final. Pour chaque règle : OK / écart détecté.

- [ ] Aucun `TODO` résiduel dans `plan.md`.
- [ ] Abattement 10 % OU frais réels choisi une seule fois par déclarant.
- [ ] Reports étrangers corrects (8TK = somme CI 2044 + 2047).
- [ ] CEHR vérifiée si RFR > seuil.
- [ ] 3916 présent pour chaque compte étranger identifié.
- [ ] Sommes redondantes cohérentes (1AJ ≈ IFU employeur).
- [ ] Cases à cocher critiques non oubliées (2OP, 8UU, 7UF, 7UD…).
```

- [ ] **Step 3: Écrire `templates/summary.md`**

```markdown
# Déclaration <ANNÉE> — Récap cases à remplir

> ⚠ Ce récapitulatif a été préparé avec assistance IA à partir des documents et
> réponses fournis. Les calculs sont indicatifs. Vérifie chaque valeur avant
> soumission. En cas de doute sur un cas complexe, consulte un expert-comptable
> ou le centre des finances publiques.

**Foyer** : <nom(s)>
**Campagne** : <année N+1>
**Généré** : <YYYY-MM-DD HH:MM>

## Ordre de saisie sur impots.gouv.fr

<liste numérotée des écrans dans l'ordre>

## Tableau des cases

| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
|---|---|---|---|---|---|---|
| <2042> | <écran> | <1AJ> | <Salaires déclarant> | <42000> | <IFU employeur A> | <D-XX si décision, sinon vide> |

## À vérifier avant validation

- <checklist de points de vigilance spécifiques au profil>
```

- [ ] **Step 4: Vérifier les trois fichiers**

```bash
ls -la skills/declaration-impot-fr/templates/
```
Expected: plan.md, journal.md, summary.md.

- [ ] **Step 5: Commit**

```bash
git add skills/declaration-impot-fr/templates/
git commit -m "feat(skill): add templates (plan.md, journal.md, summary.md)"
```

---

## Task 8: Créer `fixture-01-simple`

**Files:**
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/README.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/documents/IFU-banque-populaire.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/documents/bulletin-paie-dec.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/expected-profile.json`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/expected-plan.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/expected-summary.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/expected-journal-decisions.txt`

- [ ] **Step 1: Créer la structure**

```bash
mkdir -p skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/documents
```

- [ ] **Step 2: Écrire `README.md`**

```markdown
# Fixture 01 — Salarié simple

## Profil synthétique
- Célibataire, pas d'enfant.
- Salarié unique employeur en CDI toute l'année 2025.
- Compte Livret A (non imposable) + PEA Boursorama (aucun retrait).
- Pas d'emprunt, pas de travaux, pas de dons, pas de garde d'enfants.
- Résidence principale : locataire Paris.

## Couverture visée
- Formulaire unique : 2042.
- Pas de piège déclenché. Pas de cas de refus.

## Attendus principaux
- Salaires déclarés en 1AJ.
- Option PFU par défaut (2OP non cochée).
- Pas de formulaire annexe.
```

- [ ] **Step 3: Écrire `documents/IFU-banque-populaire.txt`**

```text
IMPRIMÉ FISCAL UNIQUE 2025
Boursorama Banque — PEA n° 12345678

Titulaire : Martin Jean
NIR : 1 85 03 75 056 234 17
Adresse : 10 rue de la Paix, 75002 Paris

Résumé des opérations imposables :
- Aucune cession au cours de l'année
- Aucun retrait du PEA
- Revenus en instance (non imposables) : 120,45 €

Cases à reporter sur la déclaration 2042 : AUCUNE
```

- [ ] **Step 4: Écrire `documents/bulletin-paie-dec.txt`**

```text
BULLETIN DE PAIE — DÉCEMBRE 2025
Employeur : ACME SAS
Salarié : Martin Jean
Période : 01/12/2025 au 31/12/2025

Salaire brut mensuel : 3 800,00 €
Cotisations salariales : 950,00 €
Net imposable mensuel : 2 920,00 €

CUMUL ANNUEL 2025
Brut imposable cumul : 45 600,00 €
Net imposable cumul (1AJ) : 35 040,00 €
Prélèvement à la source cumul : 2 108,00 €
```

- [ ] **Step 5: Écrire `expected-profile.json`**

```json
{
  "foyer": {
    "etat_civil": "celibataire",
    "enfants": 0,
    "parts_fiscales": 1.0
  },
  "changements_N": [],
  "revenus_attendus": ["salaires"],
  "logement_emprunts": {
    "rp": "locataire",
    "emprunts": []
  },
  "enfants_famille": {},
  "autres_reductions": {},
  "meta": {
    "annee_N": 2025,
    "campagne": 2026,
    "declaration": "separee"
  },
  "formulaires_pressentis": ["2042"],
  "pieges_a_surveiller": ["P-04"],
  "cas_de_refus": []
}
```

- [ ] **Step 6: Écrire `expected-plan.md`**

```markdown
# Déclaration des revenus 2025 — Plan

**État** : terminé • Phase 6/6
**Profil** : célibataire, pas d'enfant, salarié, PEA sans retrait
**Formulaires applicables** : 2042
**Généré** : 2026-XX-XX

---

## [🟢] 2042 — Déclaration principale

### [🟢] Section 1 — Traitements et salaires

- [x] 1AJ (salaires) = `35040` — source : bulletin paie décembre (cumul net imposable)

### [🟢] Section 2 — Revenus de capitaux mobiliers [!] P-04 PFU vs barème

- [x] 2OP (option barème) = `☐ non cochée` — décision D-01 (PFU par défaut, pas de RCM donc sans effet)

---

## Pièges actifs
- [!] P-04 — PFU vs barème → décision D-01

## Questions en attente
- aucune
```

- [ ] **Step 7: Écrire `expected-summary.md`**

```markdown
# Déclaration 2025 — Récap cases à remplir

> ⚠ Ce récapitulatif a été préparé avec assistance IA…

**Foyer** : Martin Jean • **Campagne** : 2026

## Ordre de saisie sur impots.gouv.fr
1. 2042 (étapes 1-3)

## Tableau des cases

| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
|---|---|---|---|---|---|---|
| 2042 | Salaires | 1AJ | Salaires déclarant | 35040 | bulletin paie déc. cumul | |
| 2042 | RCM | 2OP | Option barème progressif | ☐ | D-01 | PFU par défaut |

## À vérifier avant validation
- Salaires 1AJ = 35040 cohérent avec DSN employeur (pré-remplissage).
- 2OP non cochée = pas d'impact (aucun RCM).
```

- [ ] **Step 8: Écrire `expected-journal-decisions.txt`**

```text
# IDs de décisions attendues dans journal.md

D-01 : PFU vs barème → PFU retenu (2OP non cochée)
# Justification attendue : pas de RCM imposables dans l'année, l'option n'a pas d'impact.

# Pièges attendus signalés : P-04
# Cas de refus attendus : aucun
```

- [ ] **Step 9: Commit**

```bash
git add skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/
git commit -m "test(skill): add fixture-01-simple (salarié célibataire, PEA sans retrait)"
```

---

## Task 9: Créer `fixture-02-mixte`

**Files:**
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/README.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/IFU-boursorama.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/IFU-scpi-corum-xl.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/bulletin-paie-dec-A.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/bulletin-paie-dec-B.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/echeancier-credit-2025.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/attestation-creche.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/recu-don-secours-catho.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/expected-profile.json`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/expected-plan.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/expected-summary.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/expected-journal-decisions.txt`

- [ ] **Step 1: Créer la structure**

```bash
mkdir -p skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents
```

- [ ] **Step 2: Écrire `README.md`**

```markdown
# Fixture 02 — Couple mixte (cas représentatif)

## Profil synthétique
- Couple marié, 2 enfants (4 ans en crèche, 11 ans au collège).
- Déclarant A : salarié CDI, 48 000 € net imposable.
- Déclarant B : salarié CDI, 42 000 € net imposable.
- CTO chez Boursorama (France) : dividendes 1 200 € + PS + CI 12,8 %.
- SCPI Corum XL (pays : Allemagne) : revenu net 3 400 €.
- Emprunt RP en cours : intérêts 4 500 € en 2025 (non déductibles car RP).
- Crèche pour l'enfant de 4 ans : 4 200 € payés, aide CMG perçue 1 800 €
  → net restant à charge : 2 400 €.
- Don Secours Catholique 400 € (aide aux personnes).
- Locataire résidence principale secondaire : non applicable (propriétaires RP).

## Couverture visée
- Formulaires : 2042, 2042-RICI, 2044, 2047, 3916 (aucun compte étranger
  donc en fait pas de 3916 — à vérifier avec l'utilisateur).
- Pièges attendus : P-01 (SCPI étrangère), P-04 (PFU vs barème), P-08 (garde
  enfant), P-10 (don 7UD vs 7UF).
- Pas de cas de refus.

## Attendus principaux
- Salaires 1AJ = 48000 / 1BJ = 42000.
- RCM dividendes 2DC = 1200.
- Revenus fonciers étrangers : 4BK + 2047 section VI + 8TK.
- Crédit garde 7GA = 2400.
- Dons 7UD = 400.
- Question posée sur PFU vs barème.
- Question posée sur pays SCPI → Allemagne → CI (branche A de P-01).
```

- [ ] **Step 3: Écrire les documents (format texte synthétique)**

`documents/IFU-boursorama.txt` :

```text
IMPRIMÉ FISCAL UNIQUE 2025
Boursorama Banque — Compte Titres Ordinaire n° 87654321

Titulaire : Dupont Claire et Jean (compte joint)

Revenus imposables :
- Dividendes actions françaises (case 2DC) : 1 200,00 €
- Crédit d'impôt prélèvement forfaitaire 12,8 % (case 2CK) : 153,60 €
- Prélèvements sociaux 17,2 % déjà retenus (case 2BH) : 206,40 €
- Plus-values réalisées : 0,00 €
```

`documents/IFU-scpi-corum-xl.txt` :

```text
IMPRIMÉ FISCAL UNIQUE 2025
SCPI Corum XL — Société de gestion : Corum AM
Parts n° 4567

Associé : Dupont Claire et Jean (souscription commune)

La SCPI Corum XL investit dans des immeubles situés dans l'Union européenne
(majoritairement Allemagne).

Revenus fonciers nets 2025 :
- Revenus bruts : 4 500,00 €
- Charges et frais : -1 100,00 €
- Revenus nets imposables : 3 400,00 €

Convention fiscale applicable : France-Allemagne (art. 20) — crédit d'impôt
égal à l'impôt français.

Cases CERFA :
- 2042 case 4BK : 3 400 €
- 2047 section VI : 3 400 € / pays = Allemagne / méthode = crédit d'impôt
- 2042 case 8TK : calcul automatique (CI = 3 400 × taux moyen IR du foyer)
```

`documents/bulletin-paie-dec-A.txt` :

```text
BULLETIN DE PAIE — DÉCEMBRE 2025
Employeur : ACME SAS
Salarié : Dupont Jean

CUMUL ANNUEL 2025
Net imposable cumul (1AJ) : 48 000,00 €
```

`documents/bulletin-paie-dec-B.txt` :

```text
BULLETIN DE PAIE — DÉCEMBRE 2025
Employeur : BETA SARL
Salarié : Dupont Claire

CUMUL ANNUEL 2025
Net imposable cumul (1BJ) : 42 000,00 €
```

`documents/echeancier-credit-2025.txt` :

```text
ÉCHÉANCIER DE PRÊT IMMOBILIER 2025
Banque : Crédit Agricole
Emprunteurs : Dupont Jean et Claire
Objet du prêt : acquisition résidence principale

Cumul 2025 :
- Capital remboursé : 9 800,00 €
- Intérêts payés : 4 500,00 €
- Assurance emprunteur : 620,00 €
```

`documents/attestation-creche.txt` :

```text
ATTESTATION ANNUELLE CRÈCHE — 2025
Crèche Les Petits Loups, Paris 15
Enfant : Dupont Léa (née 2021-06-15)

Cumul payé par la famille : 4 200,00 €
Aide CMG PAJE perçue par la famille : 1 800,00 €
Net restant à charge : 2 400,00 €

Case CERFA à utiliser : 7GA (crèche hors domicile)
```

`documents/recu-don-secours-catho.txt` :

```text
REÇU FISCAL POUR DON
N° 2025-00456
Association : Secours Catholique — Caritas France
Reconnue d'utilité publique — art. 200-1 ter du CGI
(Aide aux personnes en difficulté)

Donateur : Dupont Jean
Montant du don : 400,00 €
Date : 2025-11-23

Réduction d'impôt applicable : 75 % jusqu'à 1 000 € (case 7UD)
```

- [ ] **Step 4: Écrire `expected-profile.json`**

```json
{
  "foyer": {
    "etat_civil": "marie",
    "enfants": [
      {"prenom": "Léa", "age": 4, "garde_alternee": false},
      {"prenom": "Paul", "age": 11, "garde_alternee": false}
    ],
    "parts_fiscales": 3.0
  },
  "changements_N": [],
  "revenus_attendus": ["salaires", "dividendes_francais", "revenus_fonciers_etranger"],
  "logement_emprunts": {
    "rp": "proprietaire",
    "emprunts": ["rp_en_cours"]
  },
  "enfants_famille": {
    "garde_enfants_moins_6": true,
    "scolarite": ["college"]
  },
  "autres_reductions": {
    "dons": true
  },
  "meta": {
    "annee_N": 2025,
    "campagne": 2026,
    "declaration": "commune"
  },
  "formulaires_pressentis": ["2042", "2042-RICI", "2044", "2047"],
  "pieges_a_surveiller": ["P-01", "P-04", "P-08", "P-10"],
  "cas_de_refus": []
}
```

- [ ] **Step 5: Écrire `expected-plan.md`**

```markdown
# Déclaration des revenus 2025 — Plan

**État** : terminé • Phase 6/6
**Profil** : couple marié, 2 enfants (4 et 11 ans), salaires + CTO FR + SCPI Allemagne + emprunt RP + garde crèche + don
**Formulaires applicables** : 2042, 2042-RICI, 2044, 2047
**Généré** : 2026-XX-XX

---

## [🟢] 2042 — Déclaration principale

### [🟢] Section 1 — Traitements et salaires
- [x] 1AJ = `48000` — source : bulletin paie A
- [x] 1BJ = `42000` — source : bulletin paie B

### [🟢] Section 2 — RCM [!] P-04
- [x] 2DC = `1200` — IFU Boursorama
- [x] 2BH = `206` — IFU Boursorama (PS retenus)
- [x] 2CK = `154` — IFU Boursorama (crédit PFU)
- [x] 2OP = `☐` — décision D-01 (PFU retenu, TMI 30 %)

### [🟢] Section 4 — Revenus fonciers [!] P-01
- [x] 4BK (fonciers étrangers avec CI) = `3400` — IFU Corum XL → report via 2047

### [🟢] Section 8 — Divers
- [x] 8TK = `<calculé>` — CI égal impôt français sur revenus 4BK

## [🟢] 2042-RICI — Réductions et crédits

### [🟢] Garde d'enfants < 6 ans [!] P-08
- [x] 7GA = `2400` — attestation crèche (4200 − 1800 CMG)

### [🟢] Scolarité
- [x] 7EA (collège — Paul, 11 ans) = `☑ 1` — profil

### [🟢] Dons [!] P-10
- [x] 7UD = `400` — reçu Secours Catholique (aide aux personnes, réduction 75 %)

## [🟢] 2044 — Revenus fonciers

### [🟢] SCPI étrangère (via 2047)
- [x] Report depuis IFU Corum XL = `3400` — convention France-Allemagne, méthode CI

## [🟢] 2047 — Revenus encaissés à l'étranger

### [🟢] Section VI — Revenus immobiliers
- [x] Pays = `Allemagne`, montant = `3400`, méthode = `crédit d'impôt égal à l'impôt français` — décision D-02 (P-01)

---

## Pièges actifs
- [!] P-01 — SCPI étrangère → D-02
- [!] P-04 — PFU vs barème → D-01
- [!] P-08 — crédit garde enfant → D-03 (plafond OK, aide CMG déduite)
- [!] P-10 — don aide aux personnes vs intérêt général → D-04 (7UD retenu)
```

- [ ] **Step 6: Écrire `expected-summary.md`**

```markdown
# Déclaration 2025 — Récap cases à remplir

> ⚠ Ce récapitulatif a été préparé avec assistance IA…

**Foyer** : Dupont Jean & Claire • **Campagne** : 2026

## Ordre de saisie sur impots.gouv.fr
1. 2042 (état civil, salaires, RCM) → 2. 2047 (étranger) → 3. 2044 (fonciers) → 4. 2042-RICI → 5. Retour 2042 (reports 8TK, 4BK)

## Tableau des cases

| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
|---|---|---|---|---|---|---|
| 2042 | Salaires | 1AJ | Salaires déclarant | 48000 | bulletin paie A | |
| 2042 | Salaires | 1BJ | Salaires conjoint | 42000 | bulletin paie B | |
| 2042 | RCM | 2DC | Dividendes abattement 40% | 1200 | IFU Boursorama | |
| 2042 | RCM | 2BH | PS déjà retenus | 206 | IFU Boursorama | |
| 2042 | RCM | 2CK | Crédit PFU | 154 | IFU Boursorama | |
| 2042 | RCM | 2OP | Option barème progressif | ☐ | D-01 | PFU retenu |
| 2047 | Étranger | VI | Revenus fonciers étrangers | 3400 | IFU Corum XL | Allemagne, CI |
| 2042 | Fonciers | 4BK | Revenus fonciers étrangers CI | 3400 | report 2047 | D-02 |
| 2042 | Divers | 8TK | Crédit impôt revenus étrangers | calculé | 2047 section VI | |
| 2042-RICI | Réductions | 7GA | Garde enfant < 6 ans | 2400 | attestation crèche | net 4200−1800 CMG |
| 2042-RICI | Réductions | 7EA | Collège (Paul 11 ans) | 1 | profil | 1 enfant |
| 2042-RICI | Réductions | 7UD | Don aide aux personnes | 400 | reçu Secours Catholique | D-04, réduction 75% |

## À vérifier avant validation
- Cohérence 1AJ+1BJ avec DSN employeur pré-rempli.
- Case 4BK bien alimentée par la 2047 (pas de double saisie).
- 8TK = calculé auto par impots.gouv.fr, vérifier cohérence.
- Pas de 3916 (aucun compte étranger identifié dans cette fixture).
- Intérêts RP 4500 € NON déductibles (emprunt acquisition RP) — bien ne pas
  les saisir.
```

- [ ] **Step 7: Écrire `expected-journal-decisions.txt`**

```text
# IDs de décisions attendues dans journal.md

D-01 : PFU vs barème → PFU retenu (2OP non cochée)
# Justification : TMI 30 %, abattement 40 % sur 1200 = base 720, barème donne ≈ 216 IR vs PFU 154 déjà payé. PS dans les deux cas. PFU plus simple, écart ~62 €.

D-02 : SCPI Corum XL (Allemagne) → méthode crédit d'impôt égal impôt français
# Source : convention France-Allemagne art. 20 + notice 2047 section VI.
# Cases : 2047 section VI + 2042 4BK + 8TK.

D-03 : crédit garde enfant Léa (4 ans, crèche) → 7GA = 2400
# Plafond 3500 €/enfant (2024, à vérifier N), net après CMG = 2400 < plafond.

D-04 : don Secours Catholique → 7UD (aide aux personnes, 75 %)
# Source : reçu fiscal mentionne art. 200-1 ter CGI.

# Pièges attendus signalés : P-01, P-04, P-08, P-10
# Cas de refus attendus : aucun
```

- [ ] **Step 8: Commit**

```bash
git add skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/
git commit -m "test(skill): add fixture-02-mixte (couple + SCPI étrangère + crèche + don)"
```

---

## Task 10: Créer `fixture-03-complexe`

**Files:**
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/README.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/documents/bulletin-paie-dec-retour.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/documents/IFU-ibkr.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/documents/releve-lmnp-2025.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/documents/crypto-consolide.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/documents/rsu-plan-employer.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/expected-profile.json`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/expected-plan.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/expected-summary.md`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/expected-journal-decisions.txt`
- Create: `skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/expected-refusals.txt`

- [ ] **Step 1: Créer la structure**

```bash
mkdir -p skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/documents
```

- [ ] **Step 2: Écrire `README.md`**

```markdown
# Fixture 03 — Célibataire expatrié retour 2025 (cas complexe)

## Profil synthétique
- Célibataire, retour en France le 2025-06-15 (Berlin → Paris).
- Embauche en France le 2025-07-01 sous régime d'impatriation art. 155B.
- Location meublée longue durée à Paris depuis 2023 (LMNP) — recettes 14 200 €.
- Compte IBKR (Interactive Brokers, Irlande) ouvert 2020-03-15 — toujours ouvert.
- Portefeuille crypto sur Binance (exchange étranger) — quelques cessions en 2025.
- Plan RSU chez employeur français : acquisitions en 2025, pas de cession encore.

## Couverture visée
- Formulaires théoriques : 2042, 2042-C (RSU), 2042-C-PRO (LMNP), 2044 (si réel),
  2074 (titres hors PEA), 2086 (crypto), 3916 (IBKR) + 3916-bis (Binance), 2047
  (dividendes IBKR étranger éventuels).
- Pièges : P-03 (LMNP micro vs réel), P-06 (3916 IBKR + Binance), P-07 (RSU).

## Cas de refus attendu
- **R-02** : régime d'impatriation art. 155B → REFUS et renvoi vers
  expert-comptable. Le skill doit détecter en phase 1 (question B6) et
  s'arrêter ou limiter la portée.

## Attendus
- Le skill pose la question R-02 en phase 1, reçoit "oui impatriation 155B",
  et **refuse de conclure sur la partie impatriation** (primes, exonérations).
- Il peut éventuellement continuer sur les parties **non liées** à
  l'impatriation (LMNP, 3916, RSU hors primes d'impatriation) avec un
  avertissement explicite.
- Alternative acceptable : refus global + renvoi expert.
```

- [ ] **Step 3: Écrire les documents**

`documents/bulletin-paie-dec-retour.txt` :

```text
BULLETIN DE PAIE — DÉCEMBRE 2025
Employeur : GAMMA France SAS
Salarié : Durand Alice
Date d'entrée : 2025-07-01
Régime d'impatriation : art. 155B du CGI (exonération prime d'impatriation
30 % de la rémunération totale)

CUMUL du 01/07 au 31/12 :
Rémunération brute : 60 000 €
Dont prime d'impatriation 30 % (exonérée) : 18 000 €
Salaire net imposable (1AJ) : 32 000 €
```

`documents/IFU-ibkr.txt` :

```text
INTERACTIVE BROKERS IRELAND LIMITED
Consolidated Tax Statement 2025

Account : U1234567
Holder : Durand Alice
Address : 20 avenue Kléber, 75116 Paris

Interest received : 450,00 €
Dividends (US securities) : 820,00 € (withholding tax 15 % already applied)
Capital gains realized : 1 250,00 €

NOTE : IBKR does NOT issue a French IFU. Report must be done manually.
Account was opened on 2020-03-15 and remains active. Reporting obligation
under art. 1649 A CGI (3916).
```

`documents/releve-lmnp-2025.txt` :

```text
COMPTE DE GÉRANCE LOCATIVE 2025
Logement meublé : 25 rue de Turenne, 75003 Paris
Propriétaire : Durand Alice (LMNP)
Activité déclarée : location meublée non professionnelle

Recettes annuelles : 14 200,00 €
Charges réelles (entretien, petite réparation) : 1 800,00 €
Honoraires gestion : 1 420,00 €
Taxe foncière : 1 200,00 €
Assurance PNO : 280,00 €
Amortissement potentiel (si réel) : ~5 200 €/an

Régime micro-BIC applicable : recettes < 77 700 € (seuil 2024).
Abattement micro : 50 % = 7 100 € → revenu imposable 7 100 €.
Alternative réel : 14 200 − 1800 − 1420 − 1200 − 280 − 5200 = 4 300 €.
```

`documents/crypto-consolide.txt` :

```text
RELEVÉ CONSOLIDÉ CRYPTO 2025 (via outil agrégateur)
Plateforme principale : Binance (exchange, hors UE)
Titulaire : Durand Alice

Cessions 2025 :
- 0,5 BTC vendu 2025-03-10 → 22 000 € (valeur d'achat 2021 : 14 000 €)
- 10 ETH vendu 2025-09-02 → 18 000 € (valeur d'achat 2022 : 15 000 €)

Valeur portefeuille total au 2025-12-31 : 45 000 €
Plus-value nette 2025 : 11 000 €

Obligation déclarative : 3916-bis pour le compte Binance (ouvert 2021-05-20,
encore actif).
```

`documents/rsu-plan-employer.txt` :

```text
PLAN RSU GAMMA France SAS
Bénéficiaire : Durand Alice
Date d'attribution initiale : 2023-01-15
Vesting en 2025 : 40 unités à 75 € = 3 000 € (inclus dans bulletin de paie,
soumis à CSG/CRDS sur salaire).
Aucune cession en 2025.

Régime fiscal (loi Macron 2015 modifiée 2017) : gain d'acquisition imposable
au barème sur moitié, autre moitié au taux réduit selon montant seuil — règle
complexe à vérifier.

Pour les cessions futures : 2042-C + 2074.
```

- [ ] **Step 4: Écrire `expected-profile.json`**

```json
{
  "foyer": {
    "etat_civil": "celibataire",
    "enfants": 0,
    "parts_fiscales": 1.0
  },
  "changements_N": ["retour_france", "embauche_france", "impatriation_art_155B"],
  "revenus_attendus": ["salaires", "dividendes_etranger", "plus_values_titres", "plus_values_crypto", "lmnp"],
  "logement_emprunts": {
    "rp": "locataire_ou_proprietaire",
    "emprunts": []
  },
  "enfants_famille": {},
  "autres_reductions": {},
  "meta": {
    "annee_N": 2025,
    "campagne": 2026,
    "declaration": "separee"
  },
  "formulaires_pressentis": ["2042", "2042-C", "2042-C-PRO", "2086", "3916", "3916-bis"],
  "pieges_a_surveiller": ["P-03", "P-06", "P-07"],
  "cas_de_refus": ["R-02"]
}
```

- [ ] **Step 5: Écrire `expected-plan.md`**

```markdown
# Déclaration des revenus 2025 — Plan

**État** : partiel — volet impatriation exclu (R-02)
**Profil** : célibataire, retour France 06/2025, impatriation 155B, LMNP, IBKR, Binance, RSU non cédés
**Formulaires** : partiel — 2042-C-PRO (LMNP), 2086 (crypto), 3916 + 3916-bis
**Note** : la partie "impatriation art. 155B" nécessite un expert-comptable.
Ce plan ne couvre QUE les éléments indépendants de ce régime, avec avertissement.

## [⚪] 2042-C-PRO — LMNP [!] P-03
### [ ] Arbitrage micro vs réel → décision D-01 (voir journal)
- Micro (5ND) = `7100` OU réel (5NC avec liasse 2031) = `4300`

## [⚪] 2086 — Plus-values crypto
- [ ] 3AN = `11000` (PV nette)

## [⚪] 3916 — IBKR
- compte U1234567, Irlande, ouvert 2020-03-15

## [⚪] 3916-bis — Binance
- compte crypto, ouvert 2021-05-20

## [⚪] 2042 — 8UU
- [x] 8UU = `☑` (deux comptes étrangers à déclarer)

## Cas exclus de ce plan
- Salaire GAMMA France (32 000 € net imposable) : saisir 1AJ, MAIS la prime
  d'impatriation 30 % (exonérée) relève de R-02 → expert-comptable pour vérifier
  le calcul et les cases spécifiques (1AD / 1DH / 155B).
- RSU GAMMA France 2023 vestées en 2025 : complexité P-07, à examiner par
  expert-comptable en même temps que le volet impatriation.

---

## Pièges actifs
- [!] P-03 LMNP micro vs réel → D-01
- [!] P-06 comptes étrangers IBKR + Binance → 3916 + 3916-bis obligatoires
- [!] P-07 RSU vestées 2025 → renvoyé à expert-comptable (cohérent avec R-02)

## Refus explicites
- [!] R-02 impatriation art. 155B → **expert-comptable requis** pour ce volet.
```

- [ ] **Step 6: Écrire `expected-summary.md`**

```markdown
# Déclaration 2025 — Récap PARTIEL (cas complexe)

> ⚠ AVERTISSEMENT : cette déclaration est partielle. Le régime d'impatriation
> art. 155B et le traitement des RSU vestées en 2025 nécessitent un
> expert-comptable. Les éléments ci-dessous couvrent UNIQUEMENT LMNP, crypto et
> déclaration de comptes étrangers. Compléter avec un professionnel avant
> soumission.

**Foyer** : Durand Alice • **Campagne** : 2026

## Ordre de saisie (partiel)
1. 2042 (8UU à cocher) → 2. 3916 × 2 → 3. 2042-C-PRO (LMNP) → 4. 2086 (crypto).

## Tableau des cases (hors impatriation / RSU)

| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
|---|---|---|---|---|---|---|
| 2042 | Divers | 8UU | Comptes étrangers | ☑ | IBKR + Binance | P-06 |
| 3916 | Comptes étrangers | — | IBKR U1234567 | IE / 2020-03-15 / actif | IFU IBKR | un 3916 |
| 3916-bis | Comptes étrangers | — | Binance | hors UE / 2021-05-20 / actif | relevé crypto | un 3916-bis |
| 2042-C-PRO | Pro | 5ND | Recettes LMNP micro | 14200 | relevé gérance | D-01 : micro retenu/réel ? |
| 2086 | Crypto | — | PV nette crypto | 11000 | relevé consolidé | PFU 30 % |
| 2042 | Crypto | 3AN | Report PV crypto | 11000 | 2086 | |

## À compléter avec un expert-comptable
- Prime d'impatriation 155B (exonération 30 %).
- Salaire imposable après impatriation (1AJ et cases 1AD/1DH).
- RSU vestées 2025 (fiscalité plan 2023).
- Éventuelles plus-values titres IBKR (1250 €) → 2042 3VG + 2074 + 2047 (dividendes US).
```

- [ ] **Step 7: Écrire `expected-journal-decisions.txt`**

```text
# IDs de décisions attendues dans journal.md

D-01 : LMNP micro vs réel → À DÉCIDER par utilisateur
# Micro 5ND = 7100, Réel 5NC = 4300. Question posée, attend réponse.

# Pièges attendus signalés : P-03, P-06, P-07
# Cas de refus attendus : R-02 (impatriation art. 155B)
# Avertissement explicite doit apparaître en tête de plan et summary.
```

- [ ] **Step 8: Écrire `expected-refusals.txt`**

```text
# Refus attendus dans cette fixture

R-02 : régime d'impatriation art. 155B
# Détecté en phase 1 (question B6 ou bulletin de paie en phase 2).
# Action attendue du skill : soit refus global, soit plan partiel avec
#   avertissement explicite en tête et en synthèse.
# Message type attendu : "Ta situation inclut un régime d'impatriation
#   art. 155B. Ce type de déclaration nécessite une expertise spécifique que
#   ce skill ne couvre pas. Je te recommande de consulter un expert-comptable."

# Cas implicitement exclus par R-02 :
# - calcul de l'exonération prime d'impatriation
# - RSU vestées en 2025 sous régime impat (P-07 complexifié par R-02)
```

- [ ] **Step 9: Commit**

```bash
git add skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/
git commit -m "test(skill): add fixture-03-complexe (impat 155B refusé + LMNP + crypto + IBKR)"
```

---

## Task 11: Test RED — baseline sans skill sur fixture-02

**Files:**
- Create: `skills/declaration-impot-fr/tests/results/baseline-fixture-02.md` (créé par dispatch Agent)

- [ ] **Step 1: Dispatcher un subagent SANS le skill**

Prompt à utiliser (copier tel quel dans l'Agent tool avec subagent_type `general-purpose`) :

```
Tu vas jouer le rôle d'un assistant IA généraliste à qui un utilisateur demande de l'aide pour préparer sa déclaration d'impôt sur le revenu française (revenus 2025, campagne 2026).

TU N'AS PAS ACCÈS à un skill dédié. Tu fais au mieux avec tes connaissances.

Documents fournis (lire attentivement) :
<coller ici le contenu des 7 fichiers documents/ de fixture-02-mixte>

Profil connu :
- Couple marié, 2 enfants (Léa 4 ans en crèche, Paul 11 ans au collège).
- Propriétaires RP avec emprunt en cours.

Ta mission :
1. Identifier les formulaires CERFA nécessaires.
2. Lister les cases à remplir avec leurs valeurs.
3. Identifier les points d'attention/pièges fiscaux.
4. Indiquer les questions que tu poserais à l'utilisateur pour lever les ambiguïtés.

Rends ta réponse sous forme d'un document markdown avec sections : "Formulaires", "Cases", "Pièges", "Questions".
```

- [ ] **Step 2: Enregistrer le résultat**

```bash
mkdir -p skills/declaration-impot-fr/tests/results/
# Coller le résultat du subagent dans baseline-fixture-02.md
```

- [ ] **Step 3: Analyser les lacunes**

Comparer au `expected-*.md` de fixture-02. Remplir un tableau dans
`baseline-fixture-02.md` à la fin :

```markdown
## Analyse lacunes baseline

| Attendu | Détecté par baseline ? | Commentaire |
|---|---|---|
| P-01 SCPI étrangère (Allemagne → CI) | Oui/Non | |
| P-04 PFU vs barème (question posée ?) | Oui/Non | |
| P-08 crédit garde (net après CMG) | Oui/Non | |
| P-10 don 7UD vs 7UF | Oui/Non | |
| 3916 non requis (aucun compte étranger) | correct ? | |
| Intérêts RP non déductibles | correctement ignorés ? | |
```

- [ ] **Step 4: Commit résultat RED**

```bash
git add skills/declaration-impot-fr/tests/results/baseline-fixture-02.md
git commit -m "test(skill): record RED baseline for fixture-02-mixte (no skill)"
```

---

## Task 12: Test GREEN — avec skill sur fixture-02

**Files:**
- Create: `skills/declaration-impot-fr/tests/results/green-fixture-02.md`

- [ ] **Step 1: Dispatcher un subagent AVEC le skill**

Prompt :

```
Tu dois préparer la déclaration d'impôt 2025 d'un utilisateur.

REQUIS : invoquer le skill `declaration-impot-fr` dès le début.
Suivre les 6 phases dans l'ordre. Utiliser les templates et produire les trois
artefacts (plan.md, journal.md, summary.md) dans un répertoire de travail
`./tmp-declaration-2025/`.

Interactions :
- Les documents sont dans skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/documents/
- L'utilisateur (simulé) répondra aux questions ; pour ce test, répondre comme suit :
  - Questionnaire initial : couple marié, 2 enfants (4 et 11 ans), salariés tous les deux,
    propriétaires RP avec emprunt, pas d'autres emprunts, pas de travaux, crèche pour
    la petite, collège pour le grand, don Secours Catholique.
  - Convention SCPI Corum XL : "Allemagne principalement".
  - Option PFU vs barème : "laisse le PFU, TMI 30 %".
  - Don Secours Catholique : "c'est aide aux personnes".

À la fin, donner le contenu des trois artefacts.
```

- [ ] **Step 2: Enregistrer le résultat**

Sauver dans `green-fixture-02.md` le contenu des 3 artefacts produits.

- [ ] **Step 3: Comparer aux `expected-*.md`**

Ajouter en fin de `green-fixture-02.md` :

```markdown
## Diff vs expected

### Cases manquantes ou incorrectes
- …

### Pièges ratés
- …

### Questions non posées
- …

### Cases en trop
- …
```

- [ ] **Step 4: Commit résultat GREEN**

```bash
git add skills/declaration-impot-fr/tests/results/green-fixture-02.md
git commit -m "test(skill): record GREEN result for fixture-02-mixte (with skill)"
```

---

## Task 13: Refactor — corriger les lacunes identifiées

**Files:**
- Modify: `skills/declaration-impot-fr/SKILL.md` (si workflow manque de clarté)
- Modify: `skills/declaration-impot-fr/references/*.md` (ajouts précisions)
- Modify: `skills/declaration-impot-fr/references/pitfalls.md` (si piège raté)

- [ ] **Step 1: Lister les lacunes GREEN à corriger**

Relire `green-fixture-02.md` et noter en commentaire de commit :
- lacunes structurelles (workflow non suivi, phase sautée) → SKILL.md
- piège raté → pitfalls.md (ajouter entrée ou affiner détection)
- doc type mal compris → document-types.md
- forme artefact incorrecte → templates/

- [ ] **Step 2: Faire les corrections par petites étapes**

Pour chaque lacune :
1. Modifier le fichier concerné.
2. Commit incrémental : `fix(skill): <lacune corrigée>`.

- [ ] **Step 3: Re-dispatcher un subagent avec skill sur fixture-02**

Répéter la commande de Task 12 Step 1. Sauvegarder en
`green-fixture-02-v2.md`.

- [ ] **Step 4: Vérifier que les lacunes sont levées**

Comparer à nouveau aux `expected-*.md`.

- [ ] **Step 5: Commit résultat post-refactor**

```bash
git add skills/declaration-impot-fr/tests/results/green-fixture-02-v2.md
git commit -m "test(skill): verify refactor — GREEN v2 on fixture-02-mixte"
```

---

## Task 14: Tester sur fixture-01 et fixture-03

**Files:**
- Create: `skills/declaration-impot-fr/tests/results/green-fixture-01.md`
- Create: `skills/declaration-impot-fr/tests/results/green-fixture-03.md`

- [ ] **Step 1: Dispatcher un subagent avec skill sur fixture-01**

Prompt analogue à Task 12, adapté pour la fixture simple (réponses utilisateur :
célibataire, aucun enfant, salarié unique, PEA sans retrait, pas d'emprunt,
pas de réduction, option PFU acceptée).

- [ ] **Step 2: Vérifier vs expected (fixture-01)**

Le skill doit :
- Identifier uniquement 2042.
- Ne pas générer 2044 / 2047 / 3916.
- Poser la question PFU vs barème (P-04) même si pas de RCM.
- Compléter les 3 artefacts cohérents avec la simplicité du cas.

- [ ] **Step 3: Dispatcher un subagent avec skill sur fixture-03**

Prompt analogue, avec réponses utilisateur :
- Célibataire, retour France juin 2025, contrat GAMMA France depuis juillet
  avec régime d'impatriation 155B (question B6 → OUI).
- LMNP à Paris depuis 2023, recettes 14 200 €, amortissements envisagés.
- Compte IBKR Irlande ouvert 2020-03-15 toujours actif.
- Compte Binance ouvert 2021-05-20 toujours actif.
- RSU vestées 2025 (40 unités × 75 €).

- [ ] **Step 4: Vérifier vs expected (fixture-03)**

Le skill doit :
- **Détecter R-02 dès la phase 1** (question B6 réponse "oui impatriation 155B").
- Soit **refuser globalement** avec message "expert-comptable requis",
- Soit produire un **plan partiel** avec avertissement explicite excluant les
  volets impatriation et RSU.
- Produire 3916 pour IBKR et 3916-bis pour Binance dans le plan partiel.
- Ne PAS essayer de calculer l'exonération prime d'impatriation.

- [ ] **Step 5: Commit résultats**

```bash
git add skills/declaration-impot-fr/tests/results/green-fixture-01.md \
        skills/declaration-impot-fr/tests/results/green-fixture-03.md
git commit -m "test(skill): verify fixture-01 (simple) and fixture-03 (refusal R-02)"
```

---

## Task 15: Refactor final et push

**Files:**
- Modify: tous fichiers skill selon lacunes restantes.
- Create: `skills/declaration-impot-fr/README.md`

- [ ] **Step 1: Lister les lacunes restantes**

Relire les 3 résultats GREEN et identifier :
- des précisions à ajouter dans un fichier de référence,
- des entrées pièges à enrichir,
- des règles non suivies qui méritent un renforcement dans SKILL.md.

- [ ] **Step 2: Corriger chaque lacune par commit incrémental**

```bash
git commit -m "fix(skill): <lacune>"
```

- [ ] **Step 3: Écrire un `README.md` au niveau du skill**

```markdown
# Skill declaration-impot-fr

Assistant de préparation de déclaration d'impôt sur le revenu (France) pour particuliers.

## Installation

Copier le dossier `skills/declaration-impot-fr/` dans ton répertoire skills Claude
Code (`~/.claude/skills/` pour un skill personnel, ou `.claude/skills/` pour un
skill projet).

## Utilisation

Depuis Claude Code, lancer :

> "Aide-moi à préparer ma déclaration d'impôt 2025"

ou explicitement :

> "Invoque le skill declaration-impot-fr"

Le skill te guidera en 6 phases : questionnaire initial → inventaire des docs →
questionnaire complémentaire → plan → remplissage interactif → pré-flight check.

## Limitations

Le skill refuse de traiter :
- cessions d'entreprise / plus-values pro complexes,
- régime d'impatriation art. 155B,
- fonctionnaires internationaux,
- doubles résidences / conflits conventionnels,
- montages complexes (holdings patrimoniales, pactes Dutreil…).

Pour ces cas, consulter un expert-comptable.

## Contribuer / itérer

- Fixtures de test dans `tests/fixtures/`.
- Pièges à ajouter dans `references/pitfalls.md` (convention `P-NN`).
- Cas de refus à ajouter dans `references/pitfalls.md` section "Cas
  déclenchant un REFUS" (convention `R-NN`).

Mise à jour annuelle recommandée (seuils, plafonds, nouveaux dispositifs).
```

- [ ] **Step 4: Commit final**

```bash
git add skills/declaration-impot-fr/README.md
git commit -m "docs(skill): add declaration-impot-fr README"
```

- [ ] **Step 5: Vérifier l'arborescence finale**

```bash
find skills/declaration-impot-fr -type f | sort
```

Expected :
```
skills/declaration-impot-fr/README.md
skills/declaration-impot-fr/SKILL.md
skills/declaration-impot-fr/references/document-types.md
skills/declaration-impot-fr/references/forms-catalog.md
skills/declaration-impot-fr/references/initial-questionnaire.md
skills/declaration-impot-fr/references/online-mapping.md
skills/declaration-impot-fr/references/pitfalls.md
skills/declaration-impot-fr/templates/journal.md
skills/declaration-impot-fr/templates/plan.md
skills/declaration-impot-fr/templates/summary.md
skills/declaration-impot-fr/tests/fixtures/fixture-01-simple/*
skills/declaration-impot-fr/tests/fixtures/fixture-02-mixte/*
skills/declaration-impot-fr/tests/fixtures/fixture-03-complexe/*
skills/declaration-impot-fr/tests/results/*
```

- [ ] **Step 6: Log final**

```bash
git log --oneline | head -30
```

Expected: ~20 commits, un par tâche/étape significative.

---

## Self-review checklist (à faire par l'implémenteur après écriture)

- [ ] **Placeholder scan** : rechercher `TODO`, `TBD`, `implement later` dans les
  fichiers du skill (hors exemples et templates où TODO est un marqueur fonctionnel).
- [ ] **Cohérence frontmatter** : le frontmatter YAML de `SKILL.md` respecte le
  format `name:` + `description:` et fait < 1024 caractères.
- [ ] **Références croisées** : tous les `P-NN` cités dans SKILL.md / templates /
  fixtures existent dans `pitfalls.md` ; tous les `R-NN` aussi.
- [ ] **Cases CERFA** : toutes les cases citées (1AJ, 2DC, 4BK, 7GA, 8TK, 8UU…)
  sont mentionnées dans `forms-catalog.md` ou `online-mapping.md`.
- [ ] **Templates** : plan.md / journal.md / summary.md ont des placeholders
  `<...>` clairs et ne contiennent pas de valeurs réelles.
- [ ] **Fixtures expected** : les trois `expected-*.md` sont auto-cohérents
  (les cases citées existent, les valeurs sont plausibles).
- [ ] **Word count SKILL.md** : `wc -w SKILL.md` < 800 mots idéalement.
