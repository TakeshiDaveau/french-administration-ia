# Skill `pajemploi-declaration-fr` — Design Spec

**Date** : 2026-04-24
**Auteur** : Takeshi Daveau (brainstorming avec Claude)
**Statut** : brouillon à valider avant passage à la phase plan

## 1. Objectif et portée

Skill Claude Code d'assistance à la préparation de la **déclaration mensuelle
Pajemploi** d'un parent employeur d'une assistante maternelle (convention
collective IDCC 2395).

Produit, pour chaque mois, un fichier stable prêt à être saisi manuellement
sur pajemploi.urssaf.fr. Le format reste machine-lisible pour une éventuelle
automatisation navigateur ultérieure (hors périmètre v1).

**Utilisateurs cibles** : parents employeurs d'une assistante maternelle
agréée en contrat CDI mensualisé, année complète, cas standard.

**Dans le périmètre (v1)** :
- Setup initial du contrat (mode A, une fois).
- Déclaration mensuelle récurrente (mode B, ~12 fois/an).
- Pointeur "attestation fiscale" (mode C, info seule — téléchargement sur
  pajemploi.urssaf.fr > Mes documents > Attestation fiscale).

**Hors périmètre (v1)** :
- Fin de contrat, rupture, solde de tout compte, régul CP (R-01).
- Garde partagée multi-familles (R-02).
- CDD avec conditions particulières (R-03).
- Activité partielle de l'assmat (R-04).
- Automatisation de la saisie sur pajemploi.urssaf.fr (évolution prévue
  via skill compagnon séparé).

## 2. Principes directeurs

- **Cohérence avec `declaration-impot-fr`** : même pattern skill + references +
  templates + fixtures, mêmes conventions (pièges `P-NN`, cas refus `R-NN`).
- **Poser plutôt que deviner** sur les points ambigus (mode CP, type
  d'absence, heures complémentaires, etc.).
- **Traçabilité mensuelle** : chaque déclaration laisse un fichier durable
  recoupable avec le bulletin de paie Pajemploi généré a posteriori.
- **Format stable pour automatisation future** : tableau des valeurs à saisir
  en structure fixe (colonnes, ordre des champs, formatage monétaire).
- **Maintenance annuelle légère** : plancher IE, abrogation DFS, taux CP…
  via WebFetch ponctuel des sites officiels.

## 3. Architecture

### 3.1 Arborescence du skill

```
skills/pajemploi-declaration-fr/
├── SKILL.md                            # point d'entrée, détecte mode A/B/C
├── references/
│   ├── contract-schema.md              # grille des champs du contrat (mode A)
│   ├── monthly-fields.md               # champs Pajemploi + règles calcul (mode B)
│   └── pitfalls.md                     # P-01..P-08 + refus R-01..R-04
├── templates/
│   ├── contrat.md                      # écrit une fois dans répertoire utilisateur
│   └── monthly.md                      # écrit chaque mois
└── tests/
    └── fixtures/
        ├── fixture-01-mensualise-standard/
        ├── fixture-02-heures-complementaires/
        └── fixture-03-cp-juin/
```

### 3.2 Règles de chargement (progressive disclosure)

- `SKILL.md` chargé à l'invocation.
- `contract-schema.md` chargé **en mode A uniquement**.
- `monthly-fields.md` + `pitfalls.md` chargés **en mode B**.
- Templates copiés dans le répertoire de travail à la création du fichier
  correspondant.

### 3.3 Répertoire de travail utilisateur

`./pajemploi/` à la racine du projet courant, contenant :

```
pajemploi/
├── contrat-<prenom-enfant>.md          # un par couple enfant/assmat
├── 2025-11.md                          # déclaration mensuelle
├── 2025-12.md
└── 2026-01.md
```

### 3.4 Détection du mode au démarrage

1. Si `./pajemploi/contrat-*.md` absent → **mode A** (setup contrat).
2. Si présent → demander explicitement : **déclaration mensuelle (B)** ou
   **info attestation fiscale (C)** ?

## 4. Workflow en 3 modes

### Mode A — Setup contrat (une fois par couple enfant/assmat)

1. Charger `references/contract-schema.md`.
2. Demander à l'utilisateur le contrat (PDF, texte, ou saisie guidée).
3. Extraire / demander les champs structurés :
   - **Bloc identité** : prénom enfant, date naissance, nom assmat, date
     début contrat, type (CDI par défaut), année (complète/incomplète).
   - **Bloc rémunération** : **salaire mensuel net** (champ principal),
     heures/semaine, semaines/an, mode paie (mensualisé par défaut).
     Taux horaire **optionnel**, utile seulement si non-mensualisé ou
     si heures complémentaires à payer.
   - **Bloc CP** : mode de versement (juin / prise / lissé) — critique.
   - **Bloc indemnités** : IE (≥ 9 h + prorata < 9 h), repas, km.
   - **Bloc planning** : jours et heures type.
4. Copier `templates/contrat.md` en `./pajemploi/contrat-<prenom>.md` et le
   remplir avec les valeurs extraites.

### Mode B — Déclaration mensuelle (répété chaque mois)

1. Relire `./pajemploi/contrat-<prenom>.md`.
2. Charger `references/monthly-fields.md` + `references/pitfalls.md`.
3. Questionnaire mensuel :
   - Mois concerné (par défaut : mois précédent).
   - Heures réellement effectuées sur le mois + ventilation par semaine
     si heures complémentaires/majorées suspectées (P-04).
   - Jours de présence effective de l'enfant (distinct des jours de contrat).
   - Absences : nature, nombre de jours, certificat (P-02).
   - Jours de CP pris par l'assmat ce mois-ci + indemnité applicable
     selon mode (P-01).
   - Repas fournis, km parcourus.
   - Événements particuliers (maladie assmat, heures exceptionnelles, régul).
4. Appliquer les règles de calcul (`monthly-fields.md`) et signaler les
   pièges détectés (`pitfalls.md`).
5. Si heures complémentaires/majorées et taux horaire absent du contrat :
   demander le taux horaire ponctuellement.
6. Copier `templates/monthly.md` en `./pajemploi/YYYY-MM.md` et le remplir.
7. Présenter la synthèse à l'utilisateur et l'inviter à saisir sur
   pajemploi.urssaf.fr en suivant l'ordre des champs du tableau.

### Mode C — Info attestation fiscale (ponctuel, fin d'année)

Message guide unique, sans fichier produit :

> L'attestation fiscale <ANNÉE> est téléchargeable sur
> pajemploi.urssaf.fr > **Mes documents > Attestations > Attestation
> fiscale** (disponible à partir de fin janvier <ANNÉE+1>).
>
> Pour la déclaration d'impôt case **7GA** :
> **montant à déclarer = total de l'attestation − aides CMG perçues**
> (CMG visible sur caf.fr > Mon Compte > Paiements et droits).
>
> Ce point est traité automatiquement par le skill `declaration-impot-fr`
> (piège P-08) si tu l'utilises en parallèle.

## 5. Format des artefacts persistés

### 5.1 `contrat-<prenom>.md` (écrit une fois)

Structure fixe :

```markdown
# Contrat ass. mat. — <prenom-enfant>

**Enfant** : <prénom> (né le <YYYY-MM-DD>)
**Ass. mat.** : <prénom nom>
**Début contrat** : <YYYY-MM-DD>
**Type** : <CDI|CDD>
**Mode paie** : <mensualisé|non-mensualisé>
**Année** : <complète|incomplète>

## Rémunération
- Salaire mensuel net : <M,XX> €
- Heures/semaine : <H>
- Semaines/an : <S>
- Taux horaire net : <X,XX> €/h (optionnel)
- Mode CP : <juin|prise|lissé>

## Indemnités
- IE (≥ 9 h) : <I,XX> €/jour
- IE (< 9 h) : prorata sur base <I,XX> €/jour
- Repas : <R,XX> €/repas (fourni par : parents|assmat)
- Km : <K,XX> €/km

## Planning type
- Jours : <lun-ven|…>
- Heures : <HH:MM – HH:MM>
```

### 5.2 `YYYY-MM.md` (écrit chaque mois)

Structure fixe à 3 colonnes pour le tableau principal :

```markdown
# Déclaration Pajemploi — <YYYY-MM>

**Enfant** : <prénom>
**Ass. mat.** : <prénom nom>
**Généré** : <YYYY-MM-DD>

## Valeurs à saisir sur pajemploi.urssaf.fr

| Champ Pajemploi | Valeur | Source/calcul |
|---|---|---|
| Nombre d'heures normales | <H> | planning mois |
| Heures complémentaires | <HC> | au-delà contrat, ≤ 45 h/sem |
| Heures majorées | <HM> | > 45 h/sem |
| Salaire net payé | <S,XX> € | mensualisation + ajustements |
| Nombre de jours d'accueil | <J> | pour IE |
| Indemnités d'entretien | <IE,XX> € | J × <tarif> |
| Indemnités de repas | <R,XX> € | ou 0 |
| Indemnités km | <K,XX> € | ou 0 |
| Indemnités de CP | <CP,XX> € | selon mode CP du contrat |
| Congés pris (jours) | <CPj> | |
| Absences (jours + motif) | <...> | |

## Hypothèses et décisions
- <ex: "mensualisation 850 € appliquée car année complète">
- <ex: "2 jours absence enfant malade non-rémunérés (certificat > 3 jours)">

## À vérifier avant saisie
- [ ] Cohérence heures normales × taux ≈ salaire (hors CP).
- [ ] IE journalière ≥ plancher légal (85 % MG).
- [ ] Période CP rémunérée conforme au mode du contrat.
- [ ] Pas de double-déclaration (heures CP ne se cumulent pas avec normales).
```

**Invariants du format** (pour consommation future par skill browser) :
- Tableau à 3 colonnes fixes dans cet ordre.
- Ordre des lignes stable (mêmes 11 champs).
- Formatage monétaire : `X,XX €` (virgule décimale).
- Heures : entier ou `H:MM` selon décision à lever en phase réelle.

## 6. Pièges pré-codés (`pitfalls.md`)

**P-01** — Mode de versement des CP (juin / prise / lissé), décisif pour
savoir si une indemnité CP est attendue ce mois-ci.

**P-02** — Absences : effet variable sur salaire selon nature (convenance
parents = payé, convenance assmat = rare, maladie enfant avec certificat =
règle stricte, maladie assmat = non payée).

**P-03** — Indemnité d'entretien : obligatoire dès 9 h d'accueil, plancher
légal 85 % du MG, proratisée pour accueil < 9 h, pas d'IE les jours
d'absence enfant.

**P-04** — Heures complémentaires (≤ 45 h/sem) vs majorées (> 45 h/sem) :
raisonnement par semaine civile, pas par mois.

**P-05** — Année complète vs incomplète : base de calcul CP différente.

**P-06** — DFS abrogée depuis 2024, ne plus appliquer.

**P-07** — Attestation fiscale → case 7GA : dépenses nettes = total versé
− aides CMG. Renvoi vers `declaration-impot-fr` P-08.

**P-08** — Fin de contrat hors périmètre → renvoi conseiller Pajemploi.

## 7. Cas de refus (`pitfalls.md`)

- **R-01** : fin de contrat / rupture / solde de tout compte.
- **R-02** : garde partagée multi-familles.
- **R-03** : CDD avec conditions particulières.
- **R-04** : activité partielle / chômage partiel de l'assmat.

Pour chacun : message "ce skill ne couvre pas X, consulter conseiller
Pajemploi / convention IDCC 2395 / expert paie".

## 8. Testing

Type : **reference + technique**. Tests via subagents sur fixtures.

### 8.1 Fixtures

**`fixture-01-mensualise-standard`** — cas nominal
- Contrat 850 €/mois, 40 h/sem, 46 sem/an, CP versé en juin, IE 3,60 €/jour.
- Mois test : novembre 2025, 2 jours convenance parents (payés), 18 jours
  de présence.
- Expected : salaire 850 €, IE 64,80 €, pas d'indemnité CP ce mois.

**`fixture-02-heures-complementaires`** — HC + taux horaire demandé
- Même contrat.
- Mois test : mars 2025, une semaine à 48 h (5 HC + 3 HM), taux 4,25 €/h.
- Expected : 850 + 5 × 4,25 + 3 × 4,25 × 1,25 = 886,19 €. Piège P-04
  signalé. Skill doit demander le taux horaire s'il est absent du contrat.

**`fixture-03-cp-juin`** — mois de juin avec versement CP
- Contrat 850 €/mois, mode CP juin, base année de référence 10 200 € brut
  → indemnité 1/10 = 1 020 €.
- Expected : salaire juin = 850 + 1 020 = 1 870 €, avec case "indemnité
  CP" distincte.

Chaque fixture contient :
- `contrat.md` (pré-rempli, simulant mode A déjà effectué).
- `month-input.md` (données mois fournies par "utilisateur simulé").
- `expected-monthly.md`.
- `expected-pitfalls.txt` (ex : P-04 attendu sur fixture-02).

### 8.2 Protocole RED / GREEN / refactor

Identique au skill fiscal : subagent baseline sans skill, puis avec skill,
analyse des écarts, enrichissement `pitfalls.md` / `monthly-fields.md`,
re-test.

### 8.3 Invariants automatiques sur `monthly.md`

- Tableau "Valeurs à saisir" = 3 colonnes exactement.
- Ordre des 11 lignes du tableau respecté.
- Formatage monétaire `X,XX €`.
- Aucun `TODO` résiduel en sortie de skill (sauf champ explicitement non
  applicable → valeur `0` ou `N/A`).

## 9. Source de connaissance

Stratégie **hybride** identique au skill fiscal :
- Squelette stable embarqué (schéma contrat, champs mensuels, pièges
  catalogués, règles CP).
- WebFetch ponctuel en cas de doute sur valeurs à jour (plancher IE, taux
  majorations, règles CP URSSAF) : pajemploi.urssaf.fr, urssaf.fr, convention
  collective IDCC 2395 sur Légifrance.

## 10. Évolutions prévues (hors périmètre v1)

- **Skill compagnon d'automatisation navigateur** consommant `monthly.md`
  pour remplir automatiquement pajemploi.urssaf.fr. Format contractualisé
  à cet effet.
- **Extension fin de contrat / rupture** (traitement R-01).
- **Extension garde partagée** (traitement R-02).
- **Intégration avec CAF pour récupérer CMG** automatiquement (téléchargement
  PDF ou API si disponible) pour le calcul case 7GA.

## 11. Risques et limites connues

- **Accès à l'interface Pajemploi au moment de la conception** : non
  disponible. Quelques invariants de format (décimales horaires, arrondis,
  libellés exacts) restent à confirmer. Impact limité : le format produit
  est lisible et structuré, l'utilisateur adapte la saisie.
- **Obsolescence réglementaire** : plancher IE annuel, règles CP, abrogation
  de dispositifs (ex: DFS en 2024). WebFetch + maintenance annuelle
  nécessaires.
- **Contrat non standard** : si le contrat réel diffère du schéma
  (avenants fréquents, garde partagée, cumul assmat/crèche) → le skill
  peut donner une sortie approximative. Les pièges P-05, R-02, R-03
  sont prévus pour flag explicite.
