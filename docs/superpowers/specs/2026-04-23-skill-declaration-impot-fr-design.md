# Skill `declaration-impot-fr` — Design Spec

**Date** : 2026-04-23
**Auteur** : Takeshi Daveau (brainstorming avec Claude)
**Statut** : brouillon à valider avant passage à la phase plan

## 1. Objectif et portée

Créer un skill Claude Code généraliste pour assister un particulier dans la préparation
de sa déclaration d'impôt sur le revenu en France.

Le skill ne télédéclare pas à la place de l'utilisateur. Il produit trois artefacts
exploitables (plan, journal, récap) qui permettent à l'utilisateur de saisir ensuite
les valeurs sur impots.gouv.fr avec un maximum de confiance. Un futur skill pourra
prendre le navigateur en main pour consommer le `summary.md` ; cet évolution n'est pas
dans le périmètre actuel, mais le format de `summary.md` est pensé pour rester stable.

**Utilisateurs cibles** : particuliers avec situations fiscales courantes à modérément
complexes (salaires, revenus de capitaux mobiliers, plus-values, revenus fonciers France
et étranger, SCPI, comptes à l'étranger, crédits/réductions d'impôt usuels).

**Hors périmètre** (cas où le skill refuse et renvoie vers un professionnel) :
cessions d'entreprise, plus-values pro complexes, régime d'impatriation (art. 155B),
fonctionnaires internationaux, doubles résidences fiscales, montages nécessitant
une expertise juridique, successions/donations (mention seulement).

## 2. Principes directeurs

- **Généraliste mais honnête sur ses limites** : catalogue large des cas courants,
  refus explicite sur les cas complexes.
- **Interactif plutôt qu'automatique** : le skill pose des questions plutôt que
  de deviner, particulièrement sur les arbitrages fiscaux (régime micro vs réel,
  PFU vs barème, frais réels vs abattement, traitement conventionnel d'un revenu
  étranger).
- **Traçabilité des décisions** : chaque choix fiscal est consigné dans un journal
  avec sa justification et sa source (notice officielle, réponse utilisateur).
- **Persistance** : la déclaration se fait rarement d'une traite, les artefacts sont
  écrits sur disque et reprennables entre sessions.
- **Progressive disclosure** : les fichiers de référence ne sont chargés que quand
  ils sont pertinents, pour économiser le contexte.

## 3. Architecture du skill

### 3.1 Arborescence

```
skills/declaration-impot-fr/
├── SKILL.md                            # point d'entrée, workflow en 6 phases
├── references/
│   ├── initial-questionnaire.md        # grille des Q initiales (situation, changements, revenus, emprunts, garde enfants…)
│   ├── forms-catalog.md                # CERFA : 2042 / 2042-C / 2042-C-PRO / 2042-RICI / 2044 / 2044-SPE / 2047 / 2074 / 2086 / 3916 / 3916-bis
│   ├── document-types.md               # guide des docs d'entrée (IFU, relevés, échéancier crédit, acte, attestations garde/école…)
│   ├── pitfalls.md                     # pièges par catégorie (SCPI étrangère, LMNP, frais réels, CEHR, PFU vs barème, 3916, RSU/SO, garde enfants, dons, travaux RP, mariage en année N)
│   └── online-mapping.md               # correspondance cases CERFA ↔ écrans impots.gouv.fr
├── templates/
│   ├── plan.md                         # template checklist progressive
│   ├── journal.md                      # template journal de décisions
│   └── summary.md                      # template tableau final consolidé
└── tests/
    └── fixtures/
        ├── fixture-01-simple/
        ├── fixture-02-mixte/
        └── fixture-03-complexe/
```

### 3.2 Règles de chargement (progressive disclosure)

- `SKILL.md` : chargé à l'invocation.
- `initial-questionnaire.md` : phase 1.
- `document-types.md` : phase 2.
- `forms-catalog.md` + `pitfalls.md` : transition phase 3 → 4 (quand le profil fiscal
  est suffisamment stable pour savoir quels formulaires s'appliquent).
- `online-mapping.md` : phase 5 (quand l'utilisateur passe à la saisie effective).
- Templates : copiés dans le répertoire de travail en phase 4, pas chargés en lecture
  par le modèle.

### 3.3 Répertoire de travail utilisateur

```
declaration-impot-<année>/
├── plan.md
├── journal.md
├── summary.md
└── documents/
    ├── IFU-*.pdf
    ├── releve-*.pdf
    └── …
```

Par défaut à la racine du projet courant (pas dans `skills/`). Emplacement
modifiable par l'utilisateur en début de session.

## 4. Workflow en 6 phases

### Phase 1 — Questionnaire initial

Avant toute ouverture de documents. Objectif : dresser un profil fiscal préliminaire
qui guide la suite.

Blocs de questions :

- **A. Situation du foyer** : état civil, régime matrimonial, enfants à charge
  (âge, garde alternée), rattachement majeur, personnes invalides.
- **B. Changements d'année N** : mariage/pacs/divorce, naissance/adoption/décès,
  déménagement, expatriation/retour, départ/arrivée d'un enfant du foyer.
- **C. Revenus et patrimoine attendus** : salaires, chômage/retraite, BNC/BIC/BA,
  RCM, plus-values titres/crypto, revenus fonciers France/étranger, SCPI France/étranger,
  comptes à l'étranger, stock-options/RSU/AGA.
- **D. Logement et emprunts** : propriétaire/locataire RP, emprunt RP en cours,
  autres emprunts (locatif, SCPI à crédit, souscription PME/ETI, monument historique),
  travaux éligibles (MaPrimeRénov', CEE, Denormandie).
- **E. Enfants et famille (réductions/crédits)** : garde d'enfants < 6 ans (crèche,
  assistante maternelle, garde à domicile) → cases 7GA/7GB/7GC/7GG ; scolarité
  (collège 7EA, lycée 7EC, supérieur 7EF) ; pension alimentaire versée.
- **F. Autres réductions/crédits d'impôt courants** : emploi à domicile, dons
  (associations d'aide aux personnes vs intérêt général), dispositifs locatifs
  (Pinel/Denormandie/Loc'Avantages/Malraux), cotisations syndicales, Madelin/PER,
  FCPI/FIP/SOFICA.
- **G. Méta** : année fiscale (revenus 2025 / campagne 2026 par défaut), déclaration
  conjointe ou séparée.

Sortie : profil fiscal préliminaire (en mémoire de conversation et résumé dans
`journal.md`).

### Phase 2 — Inventaire documentaire

- L'utilisateur liste ou dépose les documents disponibles (mode D : hybride).
- Le skill lit ce qui est lisible (PDF, texte, images si OCR possible).
- Pour ce qui n'est pas lisible, il demande saisie manuelle des chiffres clés.
- Il compare au profil phase 1 et signale les documents attendus mais absents
  (ex : PEA mentionné mais pas d'IFU broker).
- Sortie : section "Documents" dans `journal.md` (fournis, extraits, manquants).

### Phase 3 — Questionnaire complémentaire

Questions déclenchées par ce que les docs ont révélé, notamment sur les arbitrages
fiscaux que seul l'utilisateur peut trancher :

- Régime micro vs réel pour LMNP.
- Convention fiscale du pays d'une SCPI étrangère.
- Nature exacte d'un flux non qualifié sur un relevé.
- Bénéficiaire réel d'un crédit d'impôt garde d'enfants.
- Etc.

Une question à la fois, mode adaptatif (case par case quand doute).

### Phase 4 — Planification

Écriture des trois artefacts dans le répertoire de travail :

- `plan.md` : checklist ordonnée (formulaire → section → case) avec valeur présumée
  ou `TODO`, source, et marqueur `[!]` sur les pièges détectés.
- `journal.md` : profil fiscal + documents + décisions déjà prises avec justification
  et lien vers notice officielle.
- `summary.md` : initialement vide (entête seulement), rempli au fil de la phase 5.

Gate utilisateur : le skill présente le plan et attend "ok on y va" avant phase 5.

### Phase 5 — Exécution adaptative

Parcours de `plan.md` dans l'ordre. Pour chaque section logique :

1. Annonce de la section ("Section 1 — Traitements et salaires : 4 cases").
2. Bloc de cases à remplir (numéro + libellé + valeur).
3. Attente de "ok c'est rempli" de l'utilisateur.
4. Sur case à doute : bascule en mode case par case, WebFetch de la notice officielle
   si besoin (escalade niveau C), question reformulée, mise à jour de `plan.md` et
   `journal.md`.

Si l'utilisateur signale un écart d'interface ("je ne vois pas cette case chez moi")
→ le skill consulte `online-mapping.md` ou pose des questions pour trouver
l'équivalent sur impots.gouv.fr.

Chaque case finalisée est reportée dans `summary.md`.

### Phase 6 — Pré-flight check

Relecture globale avant validation utilisateur. Règles de cohérence automatiques :

- Aucun `TODO` résiduel dans `plan.md` (sinon signaler).
- Une et une seule option retenue entre abattement 10 % et frais réels par déclarant.
- Reports corrects (ex : 8TK = somme des CI étrangers identifiés sur 2044/2047/2074).
- CEHR déclenchée si RFR prévisionnel > 250 000 € (célibataire) / 500 000 € (couple).
- 3916 présent pour chaque compte étranger identifié en phase 1 ou 2.
- Sommes redondantes cohérentes (1AJ ≈ total IFU employeur, 2DC + 2DH = total IFU broker).
- Cases à cocher critiques non oubliées (2OP, 8UU, 7UF…).

En cas d'incohérence : message explicite + décision utilisateur consignée.

## 5. Format des artefacts persistés

### 5.1 `plan.md`

Checklist Markdown avec statut par section et par case :

```
# Déclaration des revenus 2025 — Plan

État : en cours • Phase 5/6 (exécution)
Profil : marié, 2 enfants, salaires + SCPI étrangère + CTO
Formulaires : 2042, 2042-RICI, 2044, 2047, 3916

## [🟢] 2042 — Déclaration principale
### [🟢] Section 1 — Traitements et salaires
- [x] 1AJ (déclarant) = 42000 — source : IFU employeur A
- [x] 1BJ (conjoint) = 38500 — source : IFU employeur B

### [🟡] Section 2 — RCM [!] piège PFU vs barème
- [x] 2DC = 1200 — IFU Boursorama
- [ ] 2BH = TODO — à vérifier IFU broker étranger
```

Légende : 🟢 terminé • 🟡 en cours • ⚪ à faire • `[x]` / `[ ]` case • `[!]` piège.

### 5.2 `journal.md`

Journal de décisions avec ID stable (`D-01`, `D-02`…), date, contexte, choix, source,
impact sur les cases. Permet la reprise de session, la justification en cas de
contrôle, et la réutilisation l'année suivante.

### 5.3 `summary.md`

Tableau final consolidé, format stable pour consommation future par un skill
d'automatisation navigateur :

```
| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
```

Contraintes :

- 7 colonnes fixes dans cet ordre.
- Valeurs numériques en entiers € (pas de centimes sauf exception notée dans "Note").
- Cases à cocher normalisées : `☐` / `☑`.
- Codes de cases = codes officiels (`1AJ`, `8TK`, `4BK`…).
- Aucun `TODO` résiduel à l'issue de la phase 6.
- Disclaimer IA en tête.

## 6. Safety et gestion des erreurs

### 6.1 Trois niveaux de vigilance

- **Standard** : case évidente, doc clair, aucun piège connu → remplir + consigner source.
- **Vigilant** : piège détecté via `pitfalls.md` → poser la question utilisateur
  avec les options et conséquences, consigner la décision.
- **Escalade** : doute persistant, cas non couvert, divergence entre docs → WebFetch
  de la notice officielle + reformulation + nouvelle question ; si doute demeure,
  refus de conclure.

### 6.2 `pitfalls.md` — pièges pré-codés

Catalogue initial (non figé) :

- P-01 SCPI étrangère (crédit d'impôt vs exonération avec taux effectif).
- P-02 Frais réels vs abattement 10 %.
- P-03 LMNP micro-BIC vs réel simplifié.
- P-04 Option PFU vs barème (case 2OP).
- P-05 Contribution exceptionnelle sur hauts revenus (CEHR).
- P-06 Compte étranger non déclaré (3916).
- P-07 Stock-options / RSU / AGA — qualification fiscale.
- P-08 Crédit d'impôt garde d'enfants (plafond, < 6 ans).
- P-09 Emploi à domicile (plafond majoré selon situation).
- P-10 Dons (plafonds différents aide aux personnes / intérêt général).
- P-11 Travaux RP (non cumul MaPrimeRénov' / CITE sur même dépense).
- P-12 Changement de situation familiale en cours d'année.

Chaque entrée = condition de détection + risque + question type + branches + source
officielle.

### 6.3 Cas de refus

Le skill refuse de conclure et renvoie vers un expert-comptable ou le centre des
finances publiques sur :

- Cessions d'entreprise, plus-values professionnelles complexes.
- Régime d'impatriation (art. 155B).
- Fonctionnaire international, expatrié/non-résident.
- Conflits de conventions fiscales, doubles résidences.
- Tout montage nécessitant une expertise juridique.

Ces cas sont détectés dès la phase 1 par des questions explicites.

### 6.4 Disclaimer

En tête de `summary.md` :

> Ce récapitulatif a été préparé avec assistance IA à partir des documents et réponses
> fournis. Les calculs sont indicatifs. Vérifie chaque valeur avant soumission. En cas
> de doute sur un cas complexe, consulte un expert-comptable ou le centre des finances
> publiques.

## 7. Source de connaissance fiscale

Stratégie **hybride** :

- Squelette stable embarqué dans le skill (liste des formulaires, catégories de
  revenus, pièges connus, questions de diagnostic, mapping cases ↔ écrans en ligne).
- WebFetch ponctuel sur impots.gouv.fr pour les notices officielles (notice 2041,
  2044, 2047, 2074, 2086, brochure pratique) uniquement en cas de doute sur une
  case précise (escalade niveau C).

Conséquence : maintenance annuelle légère à faire chaque début d'année fiscale
(barèmes, seuils, nouveaux cas notables), mais le skill ne tombe pas en panne si
le site officiel est momentanément lent ou restructuré.

## 8. Testing

Type du skill : **reference + technique**. Test = un subagent-sujet produit-il un
résultat correct sur un scénario réaliste ?

### 8.1 Fixtures

Trois scénarios synthétiques versionnés :

- `fixture-01-simple` : salarié célibataire + livret A + PEA — 2042 pur.
- `fixture-02-mixte` : couple marié, 2 enfants en crèche, salaires + CTO + SCPI
  Corum (Allemagne) + emprunt immo RP + dons — 2042 + 2042-RICI + 2044 + 2047 + 3916 ;
  pièges P-01, P-04, P-08, P-10.
- `fixture-03-complexe` : célibataire expatrié retour 2025, LMNP + compte IBKR +
  crypto + stock-options — 2042 + 2042-C + 2044 + 2086 + 3916 + 2074 ; pièges
  P-03, P-06, P-07 ; doit **refuser** sur impatriation art. 155B.

Chaque fixture contient : `documents/`, `expected-profile.json`, `expected-plan.md`,
`expected-summary.md`, `expected-journal-decisions.txt`, optionnellement
`expected-refusals.txt`.

### 8.2 Protocole

Pour chaque fixture :

1. **RED** : subagent sans skill, documenter baseline (formulaires manqués, pièges
   ratés, questions non posées).
2. **GREEN** : subagent avec skill, vérifier la couverture complète (6 phases,
   3 artefacts conformes, pièges attendus signalés, refus attendu présent).
3. **REFACTOR** : enrichir `pitfalls.md` / `initial-questionnaire.md` sur les
   manques, re-tester.

### 8.3 Tests de robustesse

- Doc manquant : détection en phase 2, pas de hallucination.
- Doc illisible : demande de saisie manuelle.
- Réponse utilisateur ambiguë : reformulation jusqu'à clarté.
- Changement de situation mi-année : branches correctes.

### 8.4 Tests d'invariants sur `summary.md`

- 7 colonnes par ligne.
- Valeurs numériques entières.
- Codes cases conformes regex `^[0-9][A-Z]{1,2}$` ou `^[0-9]{1,2}$`.
- Aucun `TODO` résiduel en phase 6 terminée.

### 8.5 Méta-test

Un subagent "critique" relit le `summary.md` produit par un autre subagent et cherche
des erreurs ou omissions. Les trouvailles alimentent `pitfalls.md`.

## 9. Évolutions prévues (hors périmètre actuel)

- **Skill navigateur** qui consomme `summary.md` et pilote impots.gouv.fr pour la
  saisie. Le format de `summary.md` est contractualisé pour rester stable.
- **Mise à jour annuelle** des seuils, barèmes, et nouveaux cas dans
  `pitfalls.md` / `forms-catalog.md`.
- **Enrichissement progressif** du catalogue de pièges à partir des méta-tests et
  des retours d'usage.

## 10. Risques et limites connues

- **Dépendance à la qualité des docs fournis** : un IFU flou, une transcription
  incomplète → le skill ne pourra pas être plus juste que son entrée. Compensation
  par la demande de saisie manuelle et le pré-flight check.
- **Obsolescence fiscale** : malgré le squelette stable + WebFetch, des changements
  de règles (nouveau dispositif, seuil modifié) peuvent être ratés si le skill n'est
  pas maintenu. Le disclaimer IA atténue la responsabilité.
- **Cas exotiques** : montages non couverts → refus explicite, pas de fausse
  assurance.
- **Contexte linguistique** : skill en français, cible fiscale France. Pas adapté
  à d'autres juridictions.
