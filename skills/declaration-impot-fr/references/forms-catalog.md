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
