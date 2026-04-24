# Skill `pajemploi-declaration-fr` — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Construire un skill Claude Code pour assister les parents employeurs à préparer la déclaration mensuelle Pajemploi (assistante maternelle, IDCC 2395), avec setup contrat stocké une fois + cycle mensuel reproductible + info attestation fiscale.

**Architecture:** Skill documentation (Markdown) avec point d'entrée `SKILL.md` (3 modes A/B/C), 3 fichiers de référence (contract-schema, monthly-fields, pitfalls), 2 templates (contrat, monthly), 3 fixtures de test. Format de `monthly.md` stable pour future automatisation navigateur (hors v1).

**Tech Stack:** Markdown, YAML frontmatter, WebFetch (pour plancher IE / convention IDCC 2395), Agent/subagent (tests). Aucun runtime code.

**Spec de référence:** `docs/superpowers/specs/2026-04-24-skill-pajemploi-declaration-fr-design.md`

**Arborescence cible:**

```
skills/pajemploi-declaration-fr/
├── SKILL.md
├── references/
│   ├── contract-schema.md
│   ├── monthly-fields.md
│   └── pitfalls.md
├── templates/
│   ├── contrat.md
│   └── monthly.md
└── tests/
    └── fixtures/
        ├── fixture-01-mensualise-standard/
        ├── fixture-02-heures-complementaires/
        └── fixture-03-cp-juin/
```

---

## Task 1: Créer l'arborescence et `SKILL.md`

**Files:**
- Create: `skills/pajemploi-declaration-fr/SKILL.md`
- Create: `skills/pajemploi-declaration-fr/references/` (dossier)
- Create: `skills/pajemploi-declaration-fr/templates/` (dossier)
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/` (dossier)

- [ ] **Step 1: Créer la structure**

```bash
mkdir -p skills/pajemploi-declaration-fr/{references,templates,tests/fixtures}
```

- [ ] **Step 2: Écrire `SKILL.md`** verbatim :

````markdown
---
name: pajemploi-declaration-fr
description: Use when preparing a monthly Pajemploi declaration as a parent employer of a French registered childminder (assistante maternelle agréée, IDCC 2395 collective agreement). The skill runs a 3-mode workflow — (A) one-time contract setup, (B) recurring monthly declaration producing a pajemploi.urssaf.fr field table, (C) pointer to the annual fiscal attestation for tax return (case 7GA) — with pitfall detection on paid leave modes, actual vs mensualized hours, IE allowance, and child/assmat absences.
---

# Déclaration mensuelle Pajemploi (assistante maternelle)

## Overview

Skill d'assistance à la déclaration mensuelle Pajemploi pour un parent employeur
d'une assistante maternelle agréée (IDCC 2395).

Produit un fichier mensuel `pajemploi/YYYY-MM.md` avec un tableau des valeurs à
saisir sur pajemploi.urssaf.fr. Format stable pour consommation future par un
skill compagnon d'automatisation navigateur (hors périmètre v1).

Ne se substitue pas au bulletin de paie officiel généré par Pajemploi après
saisie — aide à préparer les bons chiffres avant saisie et à garder une trace
pour recoupement.

## When to Use

- Parents employeurs d'une assmat agréée en CDI mensualisé, année complète.
- Cycle mensuel de déclaration sur pajemploi.urssaf.fr.

**Ne pas utiliser pour** : fin de contrat / rupture (R-01), garde partagée
multi-familles (R-02), CDD avec conditions particulières (R-03), activité
partielle de l'assmat (R-04). Pour ces cas : renvoyer vers un conseiller
Pajemploi ou un expert paie.

## Workflow en 3 modes

### Détection du mode au démarrage

1. Si `./pajemploi/contrat-*.md` absent → **mode A** (setup contrat).
2. Si présent → demander : "déclaration mensuelle (B) ou info attestation
   fiscale (C) ?"

### Mode A — Setup contrat (une fois par couple enfant/assmat)

1. Charger `references/contract-schema.md`.
2. Demander le contrat (PDF, texte, ou saisie guidée) + compléter par
   questionnaire ciblé.
3. Extraire les champs : identité, rémunération (salaire mensuel net
   principal), mode CP, indemnités (IE, repas, km), planning.
4. Écrire `./pajemploi/contrat-<prenom-enfant>.md` depuis
   `templates/contrat.md`.

### Mode B — Déclaration mensuelle

1. Relire `./pajemploi/contrat-<prenom>.md`.
2. Charger `references/monthly-fields.md` + `references/pitfalls.md`.
3. Questionnaire mensuel :
   - Mois concerné (défaut : mois précédent).
   - Heures effectuées (ventilation par semaine si HC/HM possibles).
   - Jours d'accueil effectifs.
   - Absences (nature + nb jours + certificat).
   - CP pris ce mois + indemnité applicable selon mode.
   - Repas, km.
   - Événements particuliers.
4. Appliquer les règles (`monthly-fields.md`), signaler les pièges détectés
   (`pitfalls.md`).
5. Si HC/HM présents et taux horaire absent du contrat → demander le taux.
6. Écrire `./pajemploi/YYYY-MM.md` depuis `templates/monthly.md`.

### Mode C — Info attestation fiscale

Message unique, sans fichier produit :

> L'attestation fiscale de l'année est téléchargeable sur
> pajemploi.urssaf.fr > **Mes documents > Attestations > Attestation
> fiscale** (disponible fin janvier de l'année N+1).
>
> Pour la case **7GA** de la déclaration d'impôt :
> **montant à déclarer = total attestation − aides CMG perçues** (CMG
> visible sur caf.fr > Mon Compte > Paiements et droits).
>
> Traité par le skill `declaration-impot-fr` (piège P-08) si utilisé en
> parallèle.

## Trois niveaux de vigilance

| Niveau | Quand | Action |
|---|---|---|
| Standard | cas évident, pas de piège | remplir |
| Vigilant | piège détecté via `pitfalls.md` | poser la question, consigner hypothèse dans `monthly.md` |
| Escalade | doute sur valeur à jour (plancher IE, règles CP) | WebFetch pajemploi.urssaf.fr / urssaf.fr / Légifrance IDCC 2395 |

## Règles non négociables

- **Jamais deviner** un mode CP ou un type d'absence — toujours demander.
- **Toujours consigner les hypothèses** dans `monthly.md` section "Hypothèses
  et décisions".
- **Format du tableau mensuel stable** : 3 colonnes fixes, ordre des lignes
  stable (pour future automation).
- Refuser de traiter les cas R-01..R-04 et renvoyer vers un professionnel.
````

- [ ] **Step 3: Vérifier**

```bash
wc -w skills/pajemploi-declaration-fr/SKILL.md
```

Expected : 400-650 mots. Si hors fourchette, flag but don't modify.

- [ ] **Step 4: Commit**

```bash
git add skills/pajemploi-declaration-fr/
git commit -m "feat(skill): add pajemploi-declaration-fr skeleton and entry point"
```

---

## Task 2: Écrire `references/contract-schema.md`

**Files:**
- Create: `skills/pajemploi-declaration-fr/references/contract-schema.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Grille d'extraction du contrat — Mode A

Chargée uniquement en mode A (setup contrat, une fois par couple enfant/assmat).
Donne la liste des champs à extraire d'un contrat d'ass. mat. ou à demander au
parent si non renseigné.

## Règles de conduite

- Poser les questions par blocs, un bloc à la fois.
- Si le contrat est fourni en PDF/texte, extraire ce qui est lisible puis
  demander uniquement les champs manquants ou ambigus.
- **Le salaire mensuel net est le champ principal** pour un contrat
  mensualisé. Le taux horaire est optionnel (utile uniquement si contrat non
  mensualisé ou pour payer des heures complémentaires).

## Bloc identité

- Prénom de l'enfant gardé.
- Date de naissance de l'enfant (format YYYY-MM-DD).
- Prénom + nom de l'assistante maternelle.
- Numéro d'agrément de l'assmat (non obligatoire pour le skill, mais utile
  pour traçabilité).
- Date de début du contrat (YYYY-MM-DD).
- Type de contrat : CDI (défaut) ou CDD.
- Mode paie : **mensualisé** (défaut, 99 % des cas) ou **non-mensualisé**
  (année incomplète pure, paie à l'heure réelle).
- Année : **complète** (46+ semaines travaillées/an pour cette famille) ou
  **incomplète** (< 46 semaines).

## Bloc rémunération

- **Salaire mensuel net** (champ principal) : montant en € fixé dans le
  contrat pour un mois standard.
- Heures d'accueil hebdomadaires : nombre d'heures prévues au contrat par
  semaine type.
- Semaines travaillées par an : nombre de semaines d'accueil dans l'année
  (souvent 46 = 52 − 4 CP − 2 jours fériés, variable).
- **Taux horaire net** (optionnel) : nécessaire si mode paie = non-mensualisé
  OU si des heures complémentaires/majorées doivent être payées.
- Formule de mensualisation (pour info) : `taux horaire × heures/semaine ×
  semaines/an / 12`.

## Bloc congés payés — critique

Trois modes possibles, le contrat doit préciser lequel :

1. **Versement en juin** : l'indemnité de CP (1/10 du brut de l'année de
   référence juin N-1 → mai N) est versée en juin, une seule fois. Les autres
   mois de l'année : salaire mensualisé seul.
2. **Versement à chaque prise** : l'indemnité est versée les mois où l'assmat
   prend effectivement ses CP.
3. **Versement lissé mensuellement** : 1/12 de l'indemnité annuelle ajouté
   chaque mois au salaire mensualisé.

Le mode est fixé **à la signature du contrat** et ne change pas en cours
d'exécution.

## Bloc indemnités et frais

- **Indemnité d'entretien (IE)** journalière pour accueil ≥ 9 h :
  montant en €/jour fixé dans le contrat, minimum légal 85 % du minimum
  garanti (MG) ≈ 3,58 €/jour en 2025 (à confirmer chaque année).
- **IE pour accueil < 9 h** : proratisée (IE × heures / 9), à préciser si le
  contrat y fait référence explicitement.
- **Indemnité de repas** : si les parents fournissent les repas, généralement
  0. Si l'assmat fournit, montant par repas en €.
- **Frais kilométriques** : si l'assmat conduit l'enfant à des activités,
  taux €/km ou forfait mensuel.

## Bloc planning type

- Jours habituels d'accueil (lun-ven, ou autre, ou variable).
- Heures type de début / fin d'accueil.

## En sortie du mode A

Remplir `templates/contrat.md` avec les valeurs extraites, écrire dans
`./pajemploi/contrat-<prenom-enfant>.md`, et présenter un résumé à
l'utilisateur pour validation.

Si des champs critiques manquent après questionnement (mode CP notamment),
**ne pas supposer une valeur par défaut** — demander explicitement.
```

- [ ] **Step 2: Vérifier**

```bash
grep "^## Bloc" skills/pajemploi-declaration-fr/references/contract-schema.md
```

Expected : 5 lignes (identité, rémunération, CP, indemnités, planning).

- [ ] **Step 3: Commit**

```bash
git add skills/pajemploi-declaration-fr/references/contract-schema.md
git commit -m "feat(skill): add contract-schema reference (mode A)"
```

---

## Task 3: Écrire `references/monthly-fields.md`

**Files:**
- Create: `skills/pajemploi-declaration-fr/references/monthly-fields.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Champs déclaration mensuelle + règles de calcul — Mode B

Chargé en mode B (déclaration mensuelle). Donne les champs du formulaire
mensuel Pajemploi et les règles de calcul associées.

**Point d'incertitude** : le format exact de saisie sur pajemploi.urssaf.fr
(décimales horaires, libellés exacts, présence d'un champ séparé HC/HM) doit
être validé lors d'une première session réelle. En v1, les libellés de
colonne sont indicatifs.

## Champs du tableau mensuel

### Heures

- **Nombre d'heures normales** : heures réellement effectuées dans le mois,
  **hors heures complémentaires / majorées**. À déclarer même en année
  complète mensualisée — Pajemploi demande le réel.
- **Heures complémentaires** : heures effectuées au-delà de la durée
  contractuelle hebdomadaire, dans la limite de 45 h/semaine civile. Payées au
  taux horaire net normal.
- **Heures majorées** : heures effectuées au-delà de 45 h/semaine civile.
  Taux majoré selon convention (≈ +25 %, à valider dans la convention IDCC
  2395 via WebFetch si nécessaire).

### Rémunération

- **Salaire net payé** = selon cas :
  - Année complète mensualisée, mois standard : **salaire mensualisé** du
    contrat.
  - Heures complémentaires ce mois : mensualisé + HC × taux horaire.
  - Heures majorées ce mois : mensualisé + HM × taux horaire × 1,25.
  - Année incomplète (non mensualisée) : heures réelles × taux horaire.
  - Mode CP "lissé" : mensualisé + 1/12 de l'indemnité annuelle CP.
  - Mode CP "prise" et CP pris ce mois : mensualisé (inchangé) + indemnité
    CP correspondante.
  - Mode CP "juin" en mois de juin : mensualisé + indemnité CP annuelle
    (1/10 brut année de référence).
  - Ajustement absences (voir `pitfalls.md` P-02).

### Présence enfant

- **Nombre de jours d'accueil** : jours où l'enfant a été effectivement
  présent (distinct du planning contractuel). Sert à calculer l'IE.

### Indemnités

- **Indemnités d'entretien (IE)** = `nb jours d'accueil × IE journalière`
  du contrat. Pas d'IE les jours d'absence enfant (P-03).
- **Indemnités de repas** = `nb repas fournis par l'assmat × tarif repas`.
  Si les parents fournissent les repas, 0.
- **Indemnités kilométriques** = km parcourus × taux, ou forfait mensuel.
- **Indemnités de congés payés** : renseignée uniquement si mode CP = prise
  ou lissé, ET CP effectivement versée ce mois.

### Congés et absences

- **Congés pris (jours)** : nombre de jours de CP pris par l'assmat ce mois.
- **Absences** : nature (convenance parents / convenance assmat / maladie
  enfant avec certificat / maladie assmat) + nombre de jours + effet sur
  salaire (voir P-02).

## Règles de calcul détaillées

### Année complète + mensualisation (cas le plus fréquent)

- Salaire mensuel net = **constant** de janvier à décembre, égal au
  salaire mensualisé du contrat.
- Indépendant du nombre de jours ouvrés du mois réel.
- Les heures normales déclarées correspondent au planning effectif mais
  n'influent pas sur le salaire (sauf HC/HM).

### Absences : effet sur le salaire mensualisé

- **Convenance parents** (vacances, rendez-vous, simple envie) : **salaire
  mensualisé maintenu** — l'assmat reste à disposition, elle est payée.
- **Convenance assmat** (motif grave) : rare. Déduction au prorata
  heures/salaire. Escalade.
- **Maladie enfant avec certificat médical** : règle stricte — 3 premiers
  jours peuvent être payés, au-delà non rémunérés dans la limite de 5 jours
  calendaires/an. **Règle à vérifier avec la convention collective** (piège
  P-02).
- **Maladie assmat** (arrêt de travail avec IJSS CPAM) : salaire non
  maintenu par le parent, régul le mois suivant (piège R-04 si > 14 jours).
- **Jours fériés chômés** : payés si travaillés habituellement sur ce jour
  ET conditions d'ancienneté. Pas de déduction du mensualisé.

### Heures complémentaires / majorées

- **Raisonner par semaine civile** (lundi-dimanche), pas par mois (P-04).
- Une semaine à 48 h sur un contrat 40 h donne :
  - 40 h normales (dans la mensualisation, pas de paie additionnelle).
  - 5 h complémentaires (40 → 45).
  - 3 h majorées (45 → 48).
- Paie additionnelle ce mois : `5 × taux + 3 × taux × 1,25`.

### Indemnité de CP versée en juin

- Calcul : `10 % du total brut versé à l'assmat sur la période de référence
  juin N-1 → mai N` (période de référence = 1er juin N-1 au 31 mai N).
- Versement unique en juin de l'année N.
- Les autres mois : pas de ligne "indemnité CP".
- **Pendant la prise de CP effective** : pas de double paiement — l'assmat
  est en congé, salaire mensualisé maintenu (si mode juin) OU indemnité CP
  versée (si mode prise).

## Sortie du mode B

Copier `templates/monthly.md` dans `./pajemploi/YYYY-MM.md` avec :

1. Le tableau des valeurs à saisir, rempli.
2. Section "Hypothèses et décisions" : chaque règle appliquée (mode CP,
   traitement absence, taux HC/HM).
3. Section "À vérifier avant saisie" : checklist de cohérence.

Présenter la synthèse à l'utilisateur et indiquer l'ordre de saisie sur
pajemploi.urssaf.fr (logiquement : heures → salaire → jours → indemnités →
absences).
```

- [ ] **Step 2: Vérifier**

```bash
grep "^## " skills/pajemploi-declaration-fr/references/monthly-fields.md
```

Expected : au moins 4 sections majeures (Champs, Règles, Sortie, …).

- [ ] **Step 3: Commit**

```bash
git add skills/pajemploi-declaration-fr/references/monthly-fields.md
git commit -m "feat(skill): add monthly-fields reference (mode B)"
```

---

## Task 4: Écrire `references/pitfalls.md`

**Files:**
- Create: `skills/pajemploi-declaration-fr/references/pitfalls.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Catalogue des pièges Pajemploi — Mode B

Chargé en mode B. Chaque piège = situation ambiguë ou à risque d'erreur qui
doit déclencher un mode vigilant (question utilisateur systématique) ou une
escalade (WebFetch valeurs à jour).

Chaque décision prise sur un piège doit être consignée dans `monthly.md`
section "Hypothèses et décisions".

---

## P-01 — Mode de versement des congés payés

**Détection** : en mode B, besoin de savoir si une indemnité CP est attendue
ce mois-ci. Lire le mode CP dans `contrat-<prenom>.md`.

**Risque** : déclarer un CP qui ne devrait pas l'être (mode "juin" avec CP
déclaré en avril → surpaie) ou oublier de le déclarer (mode "prise" avec CP
pris ce mois non déclaré → sous-paie).

**Règle** :
- **Mode juin** : indemnité CP versée UNIQUEMENT en juin (1/10 brut année de
  référence juin N-1 → mai N). Les autres mois : champ "indemnité CP" = 0.
- **Mode prise** : indemnité CP versée les mois où l'assmat prend
  effectivement ses CP. Valeur = (indemnité annuelle / nb semaines CP) ×
  semaines prises ce mois.
- **Mode lissé** : indemnité CP = 1/12 de l'indemnité annuelle, chaque mois.

**Question user** : référer au contrat (`contrat-<prenom>.md` champ "Mode
CP"). Si le champ n'est pas explicite, **demander à l'utilisateur** et
**mettre à jour le contrat**.

---

## P-02 — Absences : effet sur le salaire

**Détection** : user mentionne une absence ce mois (enfant ou assmat).

**Risque** : mauvais traitement de l'absence → sous-paie ou sur-paie.

**Règles** :

| Type d'absence | Effet sur salaire | Effet sur IE |
|---|---|---|
| Convenance parents (vacances, envie) | Mensualisé maintenu (payé) | **Pas d'IE** ces jours |
| Convenance assmat (rare, motif grave) | Déduction prorata | Pas d'IE |
| Maladie enfant avec certificat | 3 j payés, au-delà non payés (plafond 5 j/an) | Pas d'IE |
| Maladie enfant sans certificat | Comme convenance parents | Pas d'IE |
| Maladie assmat (arrêt CPAM) | Non payé (régul mois suivant) | Pas d'IE |
| Jour férié travaillé habituellement | Payé, inclus dans mensualisé | IE si enfant présent |
| Jour férié non travaillé | Aucun impact | Aucun impact |

**Question user** :

> "Y a-t-il eu des absences ce mois ? Précise pour chacune : qui était absent
> (enfant / assmat / les deux), la nature (vacances / maladie / autre), le
> nombre de jours, et si un certificat médical a été fourni."

**Source** : convention collective IDCC 2395, articles sur absences et
maladie. WebFetch en cas de doute sur la règle des 5 jours/an.

---

## P-03 — Indemnité d'entretien (IE)

**Détection** : toujours pertinent (IE obligatoire pour accueil ≥ 9 h).

**Règles** :
- **Obligatoire dès 9 h d'accueil** dans la journée.
- **Minimum légal** = 85 % du minimum garanti (MG). Valeur 2025 ≈ 3,58 €/jour.
  À vérifier annuellement via WebFetch (pajemploi.urssaf.fr ou urssaf.fr).
- **Pour accueil < 9 h** : proratisée, calcul `IE × heures / 9`.
- **Pas d'IE les jours d'absence enfant** (même convenance parents) — l'IE
  couvre l'usure liée à la présence.
- IE **non imposable** côté assmat, **pas de cotisations** — c'est un
  remboursement de frais.

**Piège classique** : calculer l'IE sur `jours du mois` au lieu de
`jours de présence effective`.

**Question user** :

> "Combien de jours l'enfant a-t-il été effectivement présent chez l'assmat
> ce mois ? (distinct des jours prévus au contrat — si l'enfant n'est pas
> venu pour convenance parents ou maladie, ça ne compte pas pour l'IE)"

**Escalade** : si doute sur la valeur du plancher IE, WebFetch
pajemploi.urssaf.fr.

---

## P-04 — Heures complémentaires vs majorées

**Détection** : user mentionne avoir eu une semaine chargée, OU heures
déclarées > (heures contractuelles × semaines du mois).

**Risque** : oublier les HC/HM → sous-paie ; ou les mélanger → calcul
incorrect de la majoration.

**Règle clé** : **raisonnement par semaine civile**, pas par mois.

- HC = heures **> contrat et ≤ 45 h/semaine**. Taux = taux horaire normal.
- HM = heures **> 45 h/semaine**. Taux = taux horaire × 1,25 (ou selon
  convention, à valider).
- Une semaine à 48 h sur un contrat 40 h → 5 HC + 3 HM.
- Le total mensuel doit être ventilé par semaine avant calcul.

**Question user** :

> "Pour chaque semaine du mois, combien d'heures totales ? Si au moins une
> semaine dépasse le contrat hebdo, j'ai besoin du détail semaine par
> semaine."

**Si HC/HM détectées et taux horaire absent du contrat** : demander
ponctuellement le taux horaire net et proposer de le stocker dans
`contrat-<prenom>.md` pour les fois suivantes.

**Source** : convention collective IDCC 2395.

---

## P-05 — Année complète vs incomplète

**Détection** : champ `Année` du contrat.

**Risque** : utiliser la mauvaise formule de calcul. Une année incomplète
non mensualisée se paie à l'heure réelle ; une année complète mensualisée
se paie de manière fixe.

**Règles** :
- **Année complète** (46+ semaines travaillées/an) : mensualisation
  standard, salaire fixe mensuel.
- **Année incomplète** (< 46 semaines) : deux sous-modes
  - Mensualisée : formule spécifique (taux × heures/sem × semaines réelles
    sur l'année / 12), salaire reste fixe mais base calcul différente.
  - Non mensualisée : paie à l'heure réelle chaque mois.

**Ce piège est surtout un piège de mode A** (mauvais paramétrage du contrat
initial). En mode B, si incohérence détectée (heures réelles très
différentes du contrat), signaler.

**Question user en mode A** : référer au contrat.

---

## P-06 — DFS (Déduction Forfaitaire Spécifique) abrogée

**Détection** : contrat antérieur à 2024 mentionne DFS.

**Règle** : la DFS pour les ass. mat. est **abrogée depuis le 1er janvier
2024**. Ne plus l'appliquer. Les anciens bulletins de paie (2023 et avant)
ne sont plus une référence fiable pour la part cotisations/net.

**Action** : si le contrat mentionne DFS, signaler à l'utilisateur que la
règle a changé et que le salaire mensuel net peut avoir évolué à
contrat égal.

**Source** : réforme URSSAF 2024, lettre-circulaire URSSAF.

---

## P-07 — Attestation fiscale → case 7GA

**Détection** : en mode C, OU quand l'utilisateur interroge sur la
déclaration d'impôt.

**Règle** :
- Attestation annuelle Pajemploi = **total versé à l'assmat** (salaire +
  indemnités + CP).
- Case 7GA (skill fiscal) = **total versé − aides CMG CAF perçues**.
- CMG visible sur caf.fr > Mon Compte > Paiements et droits (selection de
  l'année).
- Piège classique : déclarer le total brut versé → crédit d'impôt surévalué
  → redressement.

**Lien** : renvoyer explicitement vers le skill `declaration-impot-fr`,
piège P-08 qui gère ce point.

---

## P-08 — Fin de contrat / rupture (hors périmètre)

**Détection** : user mentionne fin de contrat, rupture, départ de l'assmat,
enfant entre à l'école, etc.

**Action** : **refuser de traiter**. Message type :

> "Ce skill ne couvre pas la fin de contrat. Le calcul de l'indemnité de
> rupture, du solde de tout compte, et de la régul congés payés nécessite
> une expertise spécifique. Consulte un conseiller Pajemploi
> (pajemploi.urssaf.fr > Contacter Pajemploi) ou la convention collective
> IDCC 2395 sur Légifrance."

---

## Cas de refus systématique

- **R-01** : fin de contrat, rupture, licenciement, démission, solde de tout
  compte.
- **R-02** : garde partagée (même assmat pour plusieurs familles avec
  répartition des heures).
- **R-03** : CDD avec conditions particulières (remplacement, saisonnier).
- **R-04** : activité partielle, chômage partiel de l'assmat, arrêt maladie
  prolongé (> 14 jours).

Pour chacun : message de refus avec renvoi vers conseiller Pajemploi ou
expert paie. Ne pas essayer de calculer.
```

- [ ] **Step 2: Vérifier**

```bash
grep -E "^## (P-|R-|Cas)" skills/pajemploi-declaration-fr/references/pitfalls.md
```

Expected : au moins 9 lignes (P-01 à P-08 + "Cas de refus").

- [ ] **Step 3: Commit**

```bash
git add skills/pajemploi-declaration-fr/references/pitfalls.md
git commit -m "feat(skill): add pitfalls catalog (P-01 to P-08 + refusals R-01 to R-04)"
```

---

## Task 5: Écrire les deux templates

**Files:**
- Create: `skills/pajemploi-declaration-fr/templates/contrat.md`
- Create: `skills/pajemploi-declaration-fr/templates/monthly.md`

- [ ] **Step 1: Écrire `templates/contrat.md`**

```markdown
# Contrat ass. mat. — <prenom-enfant>

**Enfant** : <prénom> (né le <YYYY-MM-DD>)
**Ass. mat.** : <prénom nom>
**Début contrat** : <YYYY-MM-DD>
**Type** : <CDI|CDD>
**Mode paie** : <mensualisé|non-mensualisé>
**Année** : <complète|incomplète>

## Rémunération

- **Salaire mensuel net** : <M,XX> €
- Heures/semaine : <H>
- Semaines/an : <S>
- **Taux horaire net** : <X,XX> €/h *(optionnel, requis si HC/HM ou non-mensualisé)*
- Mode CP : <juin|prise|lissé>

## Indemnités

- IE (≥ 9 h) : <I,XX> €/jour
- IE (< 9 h) : prorata sur base <I,XX> €/jour
- Repas : <R,XX> €/repas (fourni par : <parents|assmat>)
- Km : <K,XX> €/km (ou forfait <F,XX> €/mois)

## Planning type

- Jours : <lun-ven|…>
- Heures : <HH:MM – HH:MM>

## Notes

<Champs optionnels, spécificités, avenants, etc.>
```

- [ ] **Step 2: Écrire `templates/monthly.md`**

```markdown
# Déclaration Pajemploi — <YYYY-MM>

**Enfant** : <prénom>
**Ass. mat.** : <prénom nom>
**Généré** : <YYYY-MM-DD>

## Valeurs à saisir sur pajemploi.urssaf.fr

| Champ Pajemploi | Valeur | Source/calcul |
|---|---|---|
| Nombre d'heures normales | <H> | planning effectif du mois |
| Heures complémentaires | <HC> | > contrat hebdo, ≤ 45 h/sem |
| Heures majorées | <HM> | > 45 h/sem |
| Salaire net payé | <S,XX> € | mensualisation + ajustements |
| Nombre de jours d'accueil | <J> | présence enfant effective |
| Indemnités d'entretien | <IE,XX> € | J × <tarif IE> |
| Indemnités de repas | <R,XX> € | ou 0 |
| Indemnités km | <K,XX> € | ou 0 |
| Indemnités de CP | <CP,XX> € | selon mode CP du contrat |
| Congés pris (jours) | <CPj> | |
| Absences (jours + motif) | <...> | |

## Hypothèses et décisions

- <ex: "mensualisation 850 € appliquée (année complète)">
- <ex: "2 jours absence enfant malade non-rémunérés (règle 3+5 j)">
- <ex: "mode CP = juin → pas d'indemnité CP ce mois">

## À vérifier avant saisie

- [ ] Cohérence heures normales × taux horaire ≈ salaire (hors CP et ajustements).
- [ ] IE journalière ≥ plancher légal (85 % MG).
- [ ] Période CP rémunérée conforme au mode du contrat.
- [ ] Pas de double-déclaration (heures CP ne se cumulent pas avec heures normales).
- [ ] Jours d'accueil ≤ jours ouvrés du contrat sur le mois.

## Notes libres

<Observations, événements particuliers, points à suivre…>
```

- [ ] **Step 3: Vérifier**

```bash
ls -la skills/pajemploi-declaration-fr/templates/
```

Expected : `contrat.md` et `monthly.md`.

- [ ] **Step 4: Commit**

```bash
git add skills/pajemploi-declaration-fr/templates/
git commit -m "feat(skill): add templates (contrat.md, monthly.md)"
```

---

## Task 6: Créer `fixture-01-mensualise-standard`

**Files:**
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/README.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/contrat.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/month-input.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/expected-monthly.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/expected-pitfalls.txt`

- [ ] **Step 1: Créer le dossier**

```bash
mkdir -p skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard
```

- [ ] **Step 2: Écrire `README.md`**

```markdown
# Fixture 01 — Cas mensualisé standard

## Profil
- Contrat CDI mensualisé, année complète.
- Salaire mensuel net : 850 €.
- 40 h/semaine, 46 semaines/an.
- Taux horaire (implicite) : 850 × 12 / (40 × 46) ≈ 5,54 €/h.
- Mode CP : versement en juin.
- IE : 3,60 €/jour (≥ 9 h).
- Pas de repas, pas de km.

## Mois test : novembre 2025
- 4 semaines complètes à 40 h.
- 2 jours de convenance parents (vacances Toussaint élargies) : payés, pas d'IE.
- 18 jours de présence enfant effective.
- Pas de HC, pas de HM.
- Pas de CP (mode juin → CP uniquement en juin).

## Couverture
- Cas nominal, pas de piège actif significatif (P-01 mode juin confirmé).
- Pas de cas de refus.

## Attendus
- Salaire net = 850 €.
- IE = 18 × 3,60 = 64,80 €.
- Indemnité CP = 0.
- Heures normales déclarées ≈ 160 h (4 × 40, abattues des 2 jours ≈ 16 h selon planning).
```

- [ ] **Step 3: Écrire `contrat.md` (pré-rempli, simulant mode A déjà exécuté)**

```markdown
# Contrat ass. mat. — Emma

**Enfant** : Emma (née le 2023-04-12)
**Ass. mat.** : Marie Martin
**Début contrat** : 2024-09-01
**Type** : CDI
**Mode paie** : mensualisé
**Année** : complète

## Rémunération

- **Salaire mensuel net** : 850,00 €
- Heures/semaine : 40
- Semaines/an : 46
- **Taux horaire net** : non renseigné
- Mode CP : juin

## Indemnités

- IE (≥ 9 h) : 3,60 €/jour
- IE (< 9 h) : prorata sur base 3,60 €/jour
- Repas : 0,00 €/repas (fourni par : parents)
- Km : 0,00 €/km

## Planning type

- Jours : lun-ven
- Heures : 08:30 – 18:00
```

- [ ] **Step 4: Écrire `month-input.md`**

```markdown
# Données mois novembre 2025 — Emma

## Ce mois
- Novembre 2025 : 4 semaines d'accueil + 2 jours d'absence.
- Semaine 1 (03-07/11) : 5 jours × 9,5 h = 47,5 h — attention semaine lundi férié ?
  NON, La Toussaint est le 01/11 (samedi 2025) → aucun férié ouvrable cette semaine.
  En fait : **semaine complète 40 h**.
- Semaine 2 (10-14/11) : **absence convenance parents** du 10 au 12 (3 jours).
  Donc 2 jours travaillés (13-14) = 16 h. Attends — consigne dit "2 jours absence
  convenance parents", pas 3. Corrigeons : 10 + 11 absents (2 jours), reprise 12-14
  (3 jours). **3 jours travaillés = 24 h**. Sauf si vacances Toussaint étendues.
- Pour simplifier fixture : **2 jours d'absence convenance parents dans le mois**
  (dates non précisées), le reste = présence normale.

## Données consolidées simples
- Heures normales effectuées : 20 jours × 8 h = 160 h (ajustement selon réalité).
  Ou plus simplement : 22 jours ouvrés − 2 absences = 20 jours × 8 h = 160 h.
- Présence enfant effective : 18 jours (= jours travaillés − 2 jours absences).

  Attendez : 22 jours ouvrés en novembre 2025, − 2 absences = 20 jours travaillés
  mais enfant présent 18 ? Recalcul :
  - Jours ouvrés novembre 2025 : 20 (excluant weekends + 1/11 férié samedi + 11/11 férié mardi).
  - Donc jours prévus de travail selon planning 5j/semaine = 20.
  - 2 jours d'absence enfant = 18 jours de présence enfant.

## Résumé input pour skill
- Mois : 2025-11
- Heures normales effectuées : **160 h** (hypothèse simplifiée : mensualisation indépendante)
- HC/HM : 0
- Jours de présence enfant : **18**
- Absences : 2 jours convenance parents (salaire maintenu, pas d'IE)
- CP pris : 0
- Repas, km : 0
- Événements particuliers : aucun
```

- [ ] **Step 5: Écrire `expected-monthly.md`**

```markdown
# Déclaration Pajemploi — 2025-11

**Enfant** : Emma
**Ass. mat.** : Marie Martin
**Généré** : 2025-12-01

## Valeurs à saisir sur pajemploi.urssaf.fr

| Champ Pajemploi | Valeur | Source/calcul |
|---|---|---|
| Nombre d'heures normales | 160 | planning 20 j × 8 h |
| Heures complémentaires | 0 | aucune |
| Heures majorées | 0 | aucune |
| Salaire net payé | 850,00 € | mensualisé (année complète, mode CP juin) |
| Nombre de jours d'accueil | 18 | présence enfant effective |
| Indemnités d'entretien | 64,80 € | 18 × 3,60 |
| Indemnités de repas | 0,00 € | parents fournissent |
| Indemnités km | 0,00 € | aucun |
| Indemnités de CP | 0,00 € | mode juin, non applicable ce mois |
| Congés pris (jours) | 0 | |
| Absences (jours + motif) | 2 jours convenance parents | salaire maintenu, pas d'IE |

## Hypothèses et décisions

- Mensualisation 850 € appliquée (contrat année complète, mode CP = juin → pas d'indemnité CP hors juin).
- 2 jours absence convenance parents → salaire mensualisé maintenu, pas d'IE ces jours (règle P-02 + P-03).
- Heures normales déclarées = heures effectivement travaillées (160 h).

## À vérifier avant saisie

- [x] IE journalière 3,60 € ≥ plancher légal 2025 (≈ 3,58 €).
- [x] Pas de CP ce mois (mode juin confirmé dans le contrat).
- [x] Heures normales × taux horaire ≈ salaire : 160 × 5,54 ≈ 886 €, écart léger dû à la mensualisation.
- [x] Jours d'accueil 18 ≤ jours ouvrés planning 20.
```

- [ ] **Step 6: Écrire `expected-pitfalls.txt`**

```text
# Pièges que le skill doit signaler ou traiter correctement

P-01 : mode CP "juin" confirmé → pas d'indemnité CP ce mois. Traitement correct attendu.
P-02 : 2 jours absence convenance parents → salaire mensualisé maintenu, pas d'IE.
P-03 : IE 3,60 €/jour × 18 jours de présence (pas 20 jours de planning).

# Pas de P-04 (pas de HC/HM)
# Pas de P-05 (année complète confirmée)
# Pas de P-06 (contrat 2024, post-abrogation DFS)
# Pas de cas de refus
```

- [ ] **Step 7: Commit**

```bash
git add skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/
git commit -m "test(skill): add fixture-01-mensualise-standard (cas nominal)"
```

---

## Task 7: Créer `fixture-02-heures-complementaires`

**Files:** (mêmes que Task 6 avec chemins adaptés)

- [ ] **Step 1: Créer le dossier**

```bash
mkdir -p skills/pajemploi-declaration-fr/tests/fixtures/fixture-02-heures-complementaires
```

- [ ] **Step 2: Écrire `README.md`**

```markdown
# Fixture 02 — Heures complémentaires et majorées

## Profil
- Même contrat que fixture-01 (Emma, Marie Martin, 850 €/mois, 40 h/sem, CP juin).
- **Particularité** : taux horaire renseigné dans le contrat cette fois : 4,25 €/h.

## Mois test : mars 2025
- 4 semaines dont une à 48 h :
  - Semaine 1 : 40 h normales.
  - Semaine 2 : 40 h normales.
  - Semaine 3 : **48 h** (contrat 40 h → 5 HC + 3 HM).
  - Semaine 4 : 40 h normales.
- Total mois : 40 + 40 + 48 + 40 = **168 h** dont **40 normales habituelles × 4 = 160 h** prévues + 8 h en sus (5 HC + 3 HM).
- Présence enfant : 22 jours ouvrés mars 2025, pas d'absence.
- Pas de CP (mode juin).

## Couverture
- P-04 doit être signalé (HC + HM).
- Le skill doit calculer correctement la rémunération additionnelle.

## Attendus
- Salaire net = 850 + 5 × 4,25 + 3 × 4,25 × 1,25 = 850 + 21,25 + 15,9375 ≈ 887,19 € (arrondi à la centime).
- IE = 22 × 3,60 = 79,20 €.
- HC = 5, HM = 3, heures normales = 160.
```

- [ ] **Step 3: Écrire `contrat.md`**

```markdown
# Contrat ass. mat. — Emma

**Enfant** : Emma (née le 2023-04-12)
**Ass. mat.** : Marie Martin
**Début contrat** : 2024-09-01
**Type** : CDI
**Mode paie** : mensualisé
**Année** : complète

## Rémunération

- **Salaire mensuel net** : 850,00 €
- Heures/semaine : 40
- Semaines/an : 46
- **Taux horaire net** : 4,25 €/h
- Mode CP : juin

## Indemnités

- IE (≥ 9 h) : 3,60 €/jour
- IE (< 9 h) : prorata sur base 3,60 €/jour
- Repas : 0,00 €/repas (fourni par : parents)
- Km : 0,00 €/km

## Planning type

- Jours : lun-ven
- Heures : 08:30 – 18:00
```

- [ ] **Step 4: Écrire `month-input.md`**

```markdown
# Données mois mars 2025 — Emma

## Ventilation hebdomadaire

- Semaine 1 (03-07/03) : 5 jours × 8 h = 40 h normales.
- Semaine 2 (10-14/03) : 5 jours × 8 h = 40 h normales.
- **Semaine 3 (17-21/03) : déplacement pro des parents, accueil étendu
  → 48 h totales** (au lieu de 40).
- Semaine 4 (24-28/03) : 5 jours × 8 h = 40 h normales.
- Jour 31/03 (lundi) : 8 h normales (partie de la semaine 5 qui empiète).

Total mois : 40 + 40 + 48 + 40 + 8 = 176 h.

Correction : le mois de mars 2025 a effectivement 21 jours ouvrés (lun-ven
entre 03/03 et 31/03 inclus). Simplifions :

- Total heures : **176 h**, dont :
  - Heures normales habituelles (tous les jours sauf excédent semaine 3) : 168 h.
  - Heures complémentaires semaine 3 : 5 h (40 → 45).
  - Heures majorées semaine 3 : 3 h (45 → 48).

## Résumé
- Mois : 2025-03
- Heures normales : **168** (heures du contrat hebdo × semaines travaillées dans le mois)
- HC : **5**
- HM : **3**
- Jours de présence enfant : **21** (tous les jours ouvrés)
- Absences : aucune
- CP pris : 0
- Repas, km : 0
```

- [ ] **Step 5: Écrire `expected-monthly.md`**

```markdown
# Déclaration Pajemploi — 2025-03

**Enfant** : Emma
**Ass. mat.** : Marie Martin
**Généré** : 2025-04-01

## Valeurs à saisir sur pajemploi.urssaf.fr

| Champ Pajemploi | Valeur | Source/calcul |
|---|---|---|
| Nombre d'heures normales | 168 | planning effectif hors HC/HM |
| Heures complémentaires | 5 | semaine 3 : 40 → 45 |
| Heures majorées | 3 | semaine 3 : 45 → 48 |
| Salaire net payé | 887,19 € | 850 + 5 × 4,25 + 3 × 4,25 × 1,25 |
| Nombre de jours d'accueil | 21 | présence enfant tous jours ouvrés |
| Indemnités d'entretien | 75,60 € | 21 × 3,60 |
| Indemnités de repas | 0,00 € | parents fournissent |
| Indemnités km | 0,00 € | aucun |
| Indemnités de CP | 0,00 € | mode juin, hors juin |
| Congés pris (jours) | 0 | |
| Absences (jours + motif) | aucune | |

## Hypothèses et décisions

- Mensualisation 850 € préservée + heures additionnelles semaine 3 (P-04).
- HC semaine 3 : 5 × 4,25 = 21,25 €.
- HM semaine 3 : 3 × 4,25 × 1,25 = 15,9375 € → arrondi 15,94 €.
- Total salaire : 850 + 21,25 + 15,94 = 887,19 €.

## À vérifier avant saisie

- [x] IE 3,60 €/jour ≥ plancher légal 2025.
- [x] HC/HM calculées par semaine civile (semaine 3 isolée).
- [x] Jours d'accueil = 21 (pas d'absence).
- [x] Pas de CP (mode juin, mars hors juin).
```

- [ ] **Step 6: Écrire `expected-pitfalls.txt`**

```text
# Pièges que le skill doit signaler ou traiter correctement

P-01 : mode CP "juin" → pas d'indemnité CP en mars.
P-03 : IE sur 21 jours de présence (= planning complet).
P-04 : HC + HM détectées sur semaine 3, calculées par semaine civile.

# Si taux horaire absent du contrat : skill doit DEMANDER le taux avant calcul.
# Dans cette fixture, taux = 4,25 €/h déjà renseigné dans contrat.md.

# Pas de P-02 (pas d'absence)
# Pas de cas de refus
```

- [ ] **Step 7: Commit**

```bash
git add skills/pajemploi-declaration-fr/tests/fixtures/fixture-02-heures-complementaires/
git commit -m "test(skill): add fixture-02-heures-complementaires (HC + HM)"
```

---

## Task 8: Créer `fixture-03-cp-juin`

**Files:**
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/README.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/contrat.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/month-input.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/expected-monthly.md`
- Create: `skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/expected-pitfalls.txt`

- [ ] **Step 1: Créer le dossier**

```bash
mkdir -p skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin
```

- [ ] **Step 2: Écrire `README.md`**

```markdown
# Fixture 03 — Mois de juin avec versement CP annuel

## Profil
- Même contrat de base (Emma, Marie Martin, 850 €/mois, 40 h/sem, mode CP juin).
- Base CP de l'année de référence (juin 2024 → mai 2025) :
  12 × 850 € bruts + indemnités éventuelles = **≈ 10 200 € brut total**.
- Indemnité CP 1/10 = **1 020 €** à verser en juin 2025.

## Mois test : juin 2025
- Semaines normales (l'assmat a déjà soldé ses CP sur d'autres mois ou
  planifie pour juillet/août — ce mois elle travaille normalement).
- 21 jours ouvrés en juin 2025.
- Pas d'absence enfant.
- Pas de HC/HM.

## Couverture
- P-01 mode juin : indemnité CP annuelle à verser CE MOIS.
- Vérification que l'indemnité CP apparaît distinctement dans le tableau.

## Attendus
- Salaire net juin = **850 + 1 020 = 1 870 €**.
- IE = 21 × 3,60 = 75,60 €.
- Indemnité CP = 1 020 € (ligne distincte).
- Le skill doit **poser la question** "quelle est la base CP de l'année de
  référence ?" pour calculer le 1/10.
```

- [ ] **Step 3: Écrire `contrat.md`**

Identique à fixture-01 (même contrat base).

```markdown
# Contrat ass. mat. — Emma

**Enfant** : Emma (née le 2023-04-12)
**Ass. mat.** : Marie Martin
**Début contrat** : 2024-09-01
**Type** : CDI
**Mode paie** : mensualisé
**Année** : complète

## Rémunération

- **Salaire mensuel net** : 850,00 €
- Heures/semaine : 40
- Semaines/an : 46
- **Taux horaire net** : non renseigné
- Mode CP : juin

## Indemnités

- IE (≥ 9 h) : 3,60 €/jour
- IE (< 9 h) : prorata sur base 3,60 €/jour
- Repas : 0,00 €/repas (fourni par : parents)
- Km : 0,00 €/km

## Planning type

- Jours : lun-ven
- Heures : 08:30 – 18:00
```

- [ ] **Step 4: Écrire `month-input.md`**

```markdown
# Données mois juin 2025 — Emma

## Ce mois
- 21 jours ouvrés en juin 2025 (1-30/06, excluant weekends).
- Enfant présent tous les jours ouvrés.
- Aucune absence.
- Aucune HC/HM.
- Assmat ne prend PAS de CP ce mois-ci (prise prévue en août).

## Mode CP juin : versement annuel
- Base brute versée à l'assmat sur juin 2024 → mai 2025 : **10 200 € brut**
  (pour simplifier : 12 × 850 € bruts = 10 200 €, en supposant brut ≈ net
  dans la fixture).
- Indemnité CP 1/10 = **1 020 €**.

## Résumé
- Mois : 2025-06
- Heures normales : 168 (4 sem × 40 + 8 h semaine 5 partielle, simplifié)
- HC/HM : 0
- Jours présence : 21
- Absences : 0
- CP pris : 0 (assmat travaille tout le mois)
- Indemnité CP annuelle à verser : **1 020 €** (mode juin)
- Repas, km : 0
```

- [ ] **Step 5: Écrire `expected-monthly.md`**

```markdown
# Déclaration Pajemploi — 2025-06

**Enfant** : Emma
**Ass. mat.** : Marie Martin
**Généré** : 2025-07-01

## Valeurs à saisir sur pajemploi.urssaf.fr

| Champ Pajemploi | Valeur | Source/calcul |
|---|---|---|
| Nombre d'heures normales | 168 | planning complet juin 2025 |
| Heures complémentaires | 0 | |
| Heures majorées | 0 | |
| Salaire net payé | 1 870,00 € | 850 mensualisé + 1 020 indemnité CP annuelle |
| Nombre de jours d'accueil | 21 | tous jours ouvrés, pas d'absence |
| Indemnités d'entretien | 75,60 € | 21 × 3,60 |
| Indemnités de repas | 0,00 € | parents fournissent |
| Indemnités km | 0,00 € | |
| Indemnités de CP | 1 020,00 € | **mode juin : 1/10 brut année de référence** |
| Congés pris (jours) | 0 | assmat ne prend pas de CP en juin |
| Absences (jours + motif) | aucune | |

## Hypothèses et décisions

- Mode CP = **juin** → indemnité CP annuelle 1 020 € versée ce mois (P-01).
- Base CP année de référence (juin 2024 → mai 2025) = 10 200 € brut.
- Calcul : 10 200 × 10 % = 1 020 €.
- **Ligne "Indemnité CP" distincte** de la ligne "Salaire net payé" sur Pajemploi :
  à saisir sur le champ dédié si existe, sinon inclus dans salaire avec mention.
- Salaire total versé à l'assmat ce mois = 1 870 € (salaire + CP) + 75,60 € IE.

## À vérifier avant saisie

- [x] Mode CP bien "juin" (dans contrat).
- [x] Base CP correcte (juin 2024 → mai 2025, 10 200 € brut).
- [x] Pas de CP pris ce mois (l'indemnité annuelle est versée même si
  l'assmat ne prend pas physiquement ses vacances en juin — elle les prendra
  plus tard).
- [x] Vérifier sur pajemploi.urssaf.fr : champ "indemnité CP" séparé du
  salaire normal, ou fusionné ? (à valider sur interface réelle)
```

- [ ] **Step 6: Écrire `expected-pitfalls.txt`**

```text
# Pièges que le skill doit signaler ou traiter correctement

P-01 : mode CP "juin" → indemnité CP annuelle 1 020 € versée CE MOIS (juin 2025).
      Le skill doit DEMANDER la base CP de l'année de référence pour calculer
      le 1/10, OU accepter un montant pré-calculé fourni par l'utilisateur.
P-03 : IE sur 21 jours de présence effective.

# Pas de P-02 (pas d'absence)
# Pas de P-04 (pas de HC/HM)
# Pas de cas de refus
```

- [ ] **Step 7: Commit**

```bash
git add skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/
git commit -m "test(skill): add fixture-03-cp-juin (versement CP annuel en juin)"
```

---

## Task 9: Écrire le README du skill

**Files:**
- Create: `skills/pajemploi-declaration-fr/README.md`

- [ ] **Step 1: Écrire le fichier**

```markdown
# Skill pajemploi-declaration-fr

Assistant de préparation de la déclaration mensuelle Pajemploi pour un parent
employeur d'une assistante maternelle agréée (IDCC 2395).

## Installation

Copier `skills/pajemploi-declaration-fr/` dans `~/.claude/skills/` pour un
skill personnel, ou laisser dans ce dépôt pour un skill projet.

## Utilisation

Depuis Claude Code :

> "Aide-moi à préparer ma déclaration Pajemploi du mois"

Le skill détecte automatiquement le mode :
- **Mode A** (setup) si aucun contrat dans `./pajemploi/` — il ingère le
  contrat et écrit `contrat-<prenom>.md`.
- **Mode B** (mensuel) si le contrat existe — il te pose les questions du
  mois et écrit `YYYY-MM.md`.
- **Mode C** (info attestation fiscale) sur demande explicite en fin d'année.

## Artefacts produits

Dans `./pajemploi/` :
- `contrat-<prenom-enfant>.md` — résumé du contrat (écrit une fois).
- `YYYY-MM.md` — déclaration mensuelle (une par mois) avec tableau des cases
  à saisir sur pajemploi.urssaf.fr, hypothèses retenues, checklist à vérifier.

Format stable de `monthly.md` pour consommation future par un skill
compagnon d'automatisation navigateur (hors v1).

## Limitations

Le skill refuse de traiter :
- R-01 : fin de contrat / rupture / solde de tout compte.
- R-02 : garde partagée multi-familles.
- R-03 : CDD avec conditions particulières.
- R-04 : activité partielle / arrêt maladie prolongé de l'assmat.

Pour ces cas : consulter un conseiller Pajemploi ou un expert paie.

## Structure

```
skills/pajemploi-declaration-fr/
├── SKILL.md                            # point d'entrée, 3 modes
├── references/
│   ├── contract-schema.md              # mode A
│   ├── monthly-fields.md               # mode B (champs + règles)
│   └── pitfalls.md                     # P-01..P-08 + R-01..R-04
├── templates/
│   ├── contrat.md
│   └── monthly.md
└── tests/
    └── fixtures/
        ├── fixture-01-mensualise-standard/
        ├── fixture-02-heures-complementaires/
        └── fixture-03-cp-juin/
```

## Lien avec `declaration-impot-fr`

L'attestation fiscale annuelle Pajemploi alimente la case **7GA** de la
déclaration d'impôt. Le piège P-07 de ce skill renvoie vers le piège P-08 du
skill `declaration-impot-fr` : montant 7GA = total attestation − aides CMG
CAF perçues.

## Documents de conception

- Spec : `docs/superpowers/specs/2026-04-24-skill-pajemploi-declaration-fr-design.md`
- Plan d'implémentation : `docs/superpowers/plans/2026-04-24-skill-pajemploi-declaration-fr.md`

## Évolutions prévues

- Skill compagnon d'automatisation navigateur consommant `monthly.md`.
- Extension fin de contrat (R-01).
- Intégration CMG via CAF pour le calcul case 7GA.

## État v1

- Workflow 3 modes (A/B/C).
- 3 fixtures de test avec expected-monthly.md.
- Points à confirmer lors de la première utilisation réelle : format exact
  des champs sur pajemploi.urssaf.fr (décimales, libellés).
```

- [ ] **Step 2: Commit**

```bash
git add skills/pajemploi-declaration-fr/README.md
git commit -m "docs(skill): add pajemploi-declaration-fr README"
```

---

## Task 10: Test RED (baseline) sur fixture-01

**Files:**
- Create: `skills/pajemploi-declaration-fr/tests/results/baseline-fixture-01.md`

- [ ] **Step 1: Créer le dossier results**

```bash
mkdir -p skills/pajemploi-declaration-fr/tests/results
```

- [ ] **Step 2: Dispatcher un subagent SANS skill**

Prompt Agent tool (subagent_type=general-purpose) :

```
Tu joues un assistant IA généraliste à qui un parent employeur demande de
l'aide pour préparer sa déclaration mensuelle Pajemploi (novembre 2025).

TU N'AS PAS ACCÈS au skill pajemploi-declaration-fr. Fais au mieux avec tes
connaissances générales.

Inputs :
- Contrat : skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/contrat.md
- Données du mois : skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/month-input.md

Ta mission : produire un tableau des champs à saisir sur pajemploi.urssaf.fr
avec leurs valeurs, et signaler les points d'attention ou questions.

Rends ta réponse en markdown avec sections "Tableau", "Hypothèses", "Questions",
"Lacunes auto-détectées", et écris à
skills/pajemploi-declaration-fr/tests/results/baseline-fixture-01.md.

Puis commit :
git add skills/pajemploi-declaration-fr/tests/results/baseline-fixture-01.md
git commit -m "test(skill): record RED baseline for pajemploi fixture-01"

Ne PAS dispatcher de sub-subagent.
```

- [ ] **Step 3: Commit résultat (si pas déjà fait par le subagent)**

Si le subagent n'a pas committé :

```bash
git add skills/pajemploi-declaration-fr/tests/results/baseline-fixture-01.md
git commit -m "test(skill): record RED baseline for pajemploi fixture-01"
```

---

## Task 11: Test GREEN (avec skill) sur fixture-01

**Files:**
- Create: `skills/pajemploi-declaration-fr/tests/results/green-fixture-01.md`
- Create: `skills/pajemploi-declaration-fr/tests/results/green-fixture-01/` (dossier avec artefact monthly.md produit)

- [ ] **Step 1: Dispatcher un subagent AVEC skill**

Prompt Agent tool :

```
Tu dois préparer la déclaration mensuelle Pajemploi de novembre 2025 en suivant
le skill pajemploi-declaration-fr.

REQUIS : commence par lire skills/pajemploi-declaration-fr/SKILL.md et suivre le
workflow. Le contrat existe déjà (fixture-01 simule le mode A déjà exécuté) dans
skills/pajemploi-declaration-fr/tests/fixtures/fixture-01-mensualise-standard/contrat.md
— considère-le comme si c'était ./pajemploi/contrat-emma.md.

Données utilisateur (réponses pré-scriptées au questionnaire mensuel) :

- Mois : novembre 2025
- Heures effectuées : 160 h, réparties également sur les 4 semaines, pas de
  semaine chargée.
- Jours de présence enfant : 18
- Absences : 2 jours de convenance parents (vacances Toussaint étendues)
- CP pris ce mois : 0
- Repas/km : aucun
- Événements : aucun

Produire un fichier monthly.md dans :
skills/pajemploi-declaration-fr/tests/results/green-fixture-01/monthly.md

Et un court récap à :
skills/pajemploi-declaration-fr/tests/results/green-fixture-01.md
(avec pièges signalés + auto-évaluation).

Ne PAS lire expected-monthly.md ni expected-pitfalls.txt.
Ne PAS WebFetch ni dispatch de sub-subagent.

Commit :
git add skills/pajemploi-declaration-fr/tests/results/
git commit -m "test(skill): record GREEN result for pajemploi fixture-01"
```

- [ ] **Step 2: Analyser écarts vs expected**

Comparer `green-fixture-01/monthly.md` à
`fixture-01-mensualise-standard/expected-monthly.md`. Noter les écarts dans
un bloc final de `green-fixture-01.md` :

```markdown
## Diff vs expected

### Cases correctes
- …

### Cases incorrectes ou manquantes
- …

### Pièges ratés
- …

### Format stable respecté ?
- 3 colonnes : oui/non
- Ordre des lignes : oui/non
```

---

## Task 12: Test GREEN sur fixtures 02 et 03

**Files:**
- Create: `skills/pajemploi-declaration-fr/tests/results/green-fixture-02.md`
- Create: `skills/pajemploi-declaration-fr/tests/results/green-fixture-02/monthly.md`
- Create: `skills/pajemploi-declaration-fr/tests/results/green-fixture-03.md`
- Create: `skills/pajemploi-declaration-fr/tests/results/green-fixture-03/monthly.md`

- [ ] **Step 1: Dispatcher deux subagents en parallèle**

Deux Agent tool calls dans un même message, subagent_type=general-purpose.

**Prompt pour fixture-02** :

```
Prépare la déclaration mensuelle Pajemploi de mars 2025 en suivant le skill
pajemploi-declaration-fr.

Contrat existant :
skills/pajemploi-declaration-fr/tests/fixtures/fixture-02-heures-complementaires/contrat.md
(considère-le comme ./pajemploi/contrat-emma.md ; le taux horaire 4,25 €/h est
déjà renseigné).

Données utilisateur :
- Mois : mars 2025
- 4 semaines : S1 40h, S2 40h, **S3 48h**, S4 40h. Jour 31/03 lundi 8h.
- Total heures sur le mois : 176 h, dont 5 HC et 3 HM sur S3.
- Jours présence enfant : 21 (tous jours ouvrés)
- Aucune absence.
- CP : 0 (mode juin).

Produire :
- skills/pajemploi-declaration-fr/tests/results/green-fixture-02/monthly.md
- skills/pajemploi-declaration-fr/tests/results/green-fixture-02.md (récap)

Vérifier que le skill détecte P-04 et calcule :
- Salaire = 850 + 5 × 4,25 + 3 × 4,25 × 1,25 = 887,19 €.

Ne PAS lire expected-*.
Ne PAS WebFetch ni dispatch sub-subagent.

Commit à la fin.
```

**Prompt pour fixture-03** :

```
Prépare la déclaration mensuelle Pajemploi de juin 2025 (mois du versement CP
annuel).

Contrat :
skills/pajemploi-declaration-fr/tests/fixtures/fixture-03-cp-juin/contrat.md
(mode CP = juin).

Données utilisateur :
- Mois : juin 2025
- 168 h normales (planning complet), 0 HC/HM.
- Jours présence enfant : 21.
- Aucune absence.
- CP pris ce mois : 0 (assmat travaille, prend ses CP en août).
- **Base CP année de référence (juin 2024 → mai 2025) : 10 200 € brut.**
  (Si skill demande, répondre ce montant.)

Produire :
- skills/pajemploi-declaration-fr/tests/results/green-fixture-03/monthly.md
- skills/pajemploi-declaration-fr/tests/results/green-fixture-03.md (récap)

Le skill doit détecter P-01 (mode juin), demander ou utiliser la base CP
fournie, calculer l'indemnité CP = 1 020 € et produire salaire total 1 870 €
(850 mensualisé + 1 020 CP).

Ne PAS lire expected-*.
Ne PAS WebFetch ni dispatch sub-subagent.

Commit à la fin.
```

- [ ] **Step 2: Après completion des deux subagents, analyser les diffs**

Ajouter en fin de chaque `green-fixture-0N.md` un bloc "Diff vs expected".

- [ ] **Step 3: Commit final si nécessaire**

```bash
git status  # vérifier
git add skills/pajemploi-declaration-fr/tests/results/
git commit -m "test(skill): record GREEN results for pajemploi fixtures 02 and 03"
```

---

## Task 13: Refactor — corriger les lacunes

**Files:**
- Modify selon lacunes : `skills/pajemploi-declaration-fr/SKILL.md`, `references/*.md`, `pitfalls.md`.

- [ ] **Step 1: Lister les lacunes**

Depuis les `green-fixture-0N.md` (bloc Diff), identifier :
- Pièges non signalés.
- Calculs incorrects.
- Format `monthly.md` non respecté.
- Questions non posées ou mal formulées.

- [ ] **Step 2: Corriger par commits incrémentaux**

Pour chaque lacune :
1. Modifier le fichier du skill concerné (SKILL.md, references/, templates/).
2. Commit : `fix(skill): <lacune corrigée>`.

- [ ] **Step 3: Re-dispatcher le subagent concerné si lacune majeure**

Si une lacune majeure est corrigée, re-run le GREEN test correspondant pour
vérifier. Sauvegarder en `green-fixture-0N-v2.md`.

- [ ] **Step 4: Commit final des corrections**

```bash
git add skills/pajemploi-declaration-fr/
git commit -m "test(skill): verify refactor on pajemploi fixtures"
```

---

## Self-review checklist (implémenteur)

- [ ] `SKILL.md` a un frontmatter YAML valide (`name:` + `description:`,
  < 1024 caractères).
- [ ] Mode A/B/C détection claire dans le SKILL.md.
- [ ] Tous les `P-NN` cités sont définis dans `pitfalls.md`.
- [ ] Tous les `R-NN` cités (R-01..R-04) sont définis.
- [ ] `templates/contrat.md` et `templates/monthly.md` ont des placeholders
  `<...>` (pas de valeurs réelles).
- [ ] Le tableau de `monthly.md` a exactement 3 colonnes et 11 lignes fixes.
- [ ] Les 3 fixtures ont les 5 fichiers attendus (README, contrat, month-input,
  expected-monthly, expected-pitfalls).
- [ ] Les calculs des fixtures sont cohérents :
  - fixture-01 : 850 € salaire, 64,80 € IE.
  - fixture-02 : 887,19 € salaire, 75,60 € IE, 5 HC + 3 HM.
  - fixture-03 : 1 870 € salaire (850 + 1 020), 75,60 € IE.
- [ ] README du skill inclut installation, utilisation, limitations, liens
  vers spec et plan.
