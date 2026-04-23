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
