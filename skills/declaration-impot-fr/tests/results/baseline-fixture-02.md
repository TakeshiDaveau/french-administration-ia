# Déclaration de revenus 2025 (campagne 2026) — Foyer Dupont

Document préparatoire établi à partir des pièces fournies. Il ne remplace pas
les conseils d'un professionnel. Les cases indiquées correspondent aux
formulaires CERFA « papier » habituels ; en ligne, elles sont reprises avec le
même numéro dans le parcours déclaratif impots.gouv.fr.

## 1. Formulaires

D'après les documents fournis, les formulaires CERFA à utiliser sont :

- **Formulaire 2042** (n° 10330) — déclaration principale des revenus. Sert à
  reporter les salaires, les revenus de capitaux mobiliers (dividendes), les
  revenus fonciers nets, les crédits/réductions d'impôt et les informations
  du foyer (mariage, 2 enfants à charge).
- **Formulaire 2042-RICI** (n° 15637) — annexe dédiée aux réductions et
  crédits d'impôt : frais de garde d'enfant de moins de 6 ans (crèche) et
  dons aux œuvres.
- **Formulaire 2044** (n° 10334) — déclaration des revenus fonciers. À
  utiliser pour les revenus nets de parts de SCPI (revenus fonciers).
  Remarque : pour une SCPI purement étrangère dont les revenus relèvent de
  la convention fiscale, ils sont en pratique reportés directement via le
  2047 et la case 4BK du 2042, sans nécessairement remplir un 2044 complet.
  À confirmer avec l'utilisateur (cf. points d'attention).
- **Formulaire 2047** (n° 11226) — déclaration des revenus encaissés à
  l'étranger. Section VI « revenus fonciers » pour la SCPI Corum XL
  (immeubles en Allemagne), avec méthode du crédit d'impôt égal à l'impôt
  français (convention France-Allemagne).
- **Formulaire 3916 / 3916-bis** — NON NÉCESSAIRE en l'état : aucun compte
  bancaire, contrat d'assurance-vie ou compte d'actifs numériques détenu à
  l'étranger n'apparaît dans les documents. Le CTO Boursorama est un compte
  français et la SCPI Corum XL est souscrite via une société de gestion
  française (pas de compte-titres étranger). À confirmer avec l'utilisateur.

Formulaires **non nécessaires** a priori :
- 2042 C / 2042 C PRO : aucun revenu non salarié, ni plus-value mobilière.
- 2074 : pas de plus-value de cession de titres (IFU Boursorama : PV = 0).
- Crédit d'impôt travaux / intérêts RP : les intérêts d'emprunt de la
  résidence principale ne sont **pas déductibles** depuis 2011 (le crédit
  d'impôt TEPA est éteint pour les prêts postérieurs à 2010). L'échéancier
  de crédit n'a donc **pas** à être reporté dans la déclaration d'impôt sur
  le revenu (il reste utile pour la taxe foncière / assurance, mais pas IR).

## 2. Cases à remplir

| Formulaire | Case | Libellé | Valeur | Source doc |
|---|---|---|---|---|
| 2042 | Situation | Mariés (M) | coché | Énoncé utilisateur |
| 2042 | F | Nombre d'enfants à charge | 2 (Léa 4 ans, Paul 11 ans) | Énoncé utilisateur |
| 2042 | 1AJ | Traitements et salaires — Déclarant 1 (Jean) | 48 000 € | bulletin-paie-dec-A.txt |
| 2042 | 1BJ | Traitements et salaires — Déclarant 2 (Claire) | 42 000 € | bulletin-paie-dec-B.txt |
| 2042 | 2DC | Revenus des actions et parts — dividendes éligibles abattement 40 % | 1 200 € | IFU-boursorama.txt |
| 2042 | 2CK | Crédit d'impôt prélèvement forfaitaire 12,8 % déjà versé | 153,60 € | IFU-boursorama.txt |
| 2042 | 2BH | Revenus déjà soumis aux prélèvements sociaux (avec CSG déductible) | 206,40 € | IFU-boursorama.txt |
| 2042 | 4BK | Revenus fonciers imposables (micro ou régime réel) — SCPI étrangère avec CI | 3 400 € | IFU-scpi-corum-xl.txt |
| 2042 | 8TK | Revenus étrangers ouvrant droit à un crédit d'impôt égal à l'impôt français | 3 400 € | IFU-scpi-corum-xl.txt |
| 2042-RICI | 7GA | Frais de garde d'enfants de moins de 6 ans hors du domicile (crèche) — net à charge après CMG | 2 400 € | attestation-creche.txt |
| 2042-RICI | 7UD | Dons à des organismes d'aide aux personnes en difficulté (réduction 75 %, plafond 1 000 €) | 400 € | recu-don-secours-catho.txt |
| 2047 | Section VI | Revenus fonciers de source étrangère — pays Allemagne, méthode crédit d'impôt, montant | 3 400 € | IFU-scpi-corum-xl.txt |
| 2047 | — | Identification du pays : Allemagne | — | IFU-scpi-corum-xl.txt |

Enfants à charge (détail informatif sur 2042) :
- Léa Dupont, née 15/06/2021 → case F (enfants mineurs à charge).
- Paul Dupont, 11 ans, au collège → case F également. Il n'existe pas de
  crédit d'impôt pour scolarité au collège dès l'année de déclaration 2025
  (se référer à 7EA / 7EC / 7EF si le barème est reconduit — à vérifier ;
  historiquement 61 € pour collège).
  - Piste éventuelle : 2042-RICI case **7EC** (enfant au collège) = 1
    (forfait 61 € si reconduit dans la campagne 2026).

## 3. Pièges / points d'attention

1. **Intérêts d'emprunt résidence principale non déductibles.** Le cumul
   d'intérêts 2025 (4 500 €) figurant sur l'échéancier ne doit PAS être
   reporté. Le crédit d'impôt TEPA (cases 7VY/7VZ) ne concerne que les prêts
   contractés entre 2007 et 2010. À ne pas reporter par erreur.

2. **Dividendes — PFU (12,8 %) vs barème progressif.** Par défaut, les
   dividendes (2DC) sont imposés au PFU de 12,8 %. Il est possible d'opter
   pour le barème (case **2OP**) afin de bénéficier de l'abattement de 40 %
   et de la CSG déductible. L'opt-in est **global** pour tous les revenus
   mobiliers du foyer ; il faut simuler les deux hypothèses avant de cocher
   2OP. À 1 200 € de dividendes seuls, le PFU est souvent plus avantageux,
   mais cela dépend du TMI du foyer.

3. **SCPI étrangère (Corum XL) — méthode du crédit d'impôt.** Les revenus
   fonciers de source allemande relèvent de la convention France-Allemagne.
   Ils sont imposés en France mais ouvrent droit à un crédit d'impôt égal à
   l'impôt français (méthode de l'exonération avec taux effectif / crédit
   d'impôt selon le cas). Ils doivent être reportés :
   - en **4BK** (revenus fonciers taxables) ET
   - en **8TK** (pour neutraliser l'impôt via le crédit d'impôt),
   - avec la section VI du 2047.
   Piège fréquent : oublier 8TK → double imposition. Autre piège : confondre
   avec une SCPI française (qui serait en 4BA sans crédit d'impôt).

4. **Garde d'enfant — montant NET après CMG.** La case 7GA prend le montant
   restant à charge après déduction de l'aide CMG PAJE. Ici : 4 200 - 1 800
   = **2 400 €** et non 4 200 €. L'attestation de la crèche le confirme
   explicitement. Plafond crédit d'impôt : 3 500 € par enfant de moins de
   6 ans (soit 50 % × plafond 3 500 = 1 750 € de crédit maximum par enfant).

5. **Don Secours Catholique — case 7UD et non 7UF.** Le Secours Catholique
   est éligible au taux majoré de 75 % au titre de l'aide aux personnes en
   difficulté (dispositif « Coluche »), plafonné à 1 000 € en 2025. À ne pas
   mettre en 7UF (dons aux œuvres d'intérêt général, taux 66 %). Les 400 €
   ouvrent donc droit à 300 € de réduction d'impôt.

6. **CTO Boursorama — crédit d'impôt 2CK et PS déjà retenus 2BH.** Bien
   reporter 2CK (153,60 €) pour éviter la double imposition sur les
   dividendes, et 2BH (206,40 €) pour la CSG déductible (si option barème).

7. **Formulaire 3916.** Le CTO Boursorama étant ouvert en France, pas de
   3916 à déposer. Vérifier avec l'utilisateur qu'il n'existe pas d'autre
   compte étranger (Revolut, Trade Republic, N26, PEA étranger, crypto sur
   exchange hors UE, etc.) — oubli fréquent et sanctionné (1 500 € par
   compte).

8. **Quotient familial.** 2 enfants à charge = 1 part supplémentaire (0,5 +
   0,5). Le couple marié avec 2 enfants = 3 parts. Plafond du quotient
   familial à surveiller (1 791 € par demi-part en 2025, sous réserve de
   revalorisation 2026). Vu les revenus (90 000 € cumulés), le plafond
   pourrait jouer — à contrôler dans la simulation.

9. **Assurance emprunteur (620 €).** Non déductible sur la RP.

10. **Case 7EC collège.** À ne pas oublier pour Paul (11 ans, collège) si le
    crédit d'impôt scolarité est reconduit en 2026.

## 4. Questions à l'utilisateur

1. **Comptes et actifs détenus à l'étranger.** Avez-vous (l'un ou l'autre)
   un compte bancaire, un compte-titres, un portefeuille d'actifs numériques
   (crypto) ou un contrat d'assurance-vie détenu hors de France ? Même s'il
   est inactif. (Détermine l'obligation 3916 / 3916-bis.)
2. **Option barème progressif (2OP) pour les revenus mobiliers.** Souhaitez-
   vous que je simule les deux options (PFU 12,8 % vs barème avec abattement
   40 %) pour choisir la plus avantageuse compte tenu de votre TMI ?
3. **Pays de la SCPI.** L'IFU Corum mentionne « majoritairement Allemagne ».
   Avez-vous un détail par pays (Allemagne, Pays-Bas, Italie, etc.) fourni
   par Corum ? Cela conditionne la section VI du 2047 (une ligne par pays)
   et la méthode applicable (crédit d'impôt vs taux effectif selon la
   convention).
4. **Régime foncier (micro ou réel).** Avez-vous d'autres revenus fonciers
   en France ? Si oui, le choix du régime micro-foncier (< 15 000 € bruts,
   abattement 30 %) ou réel s'applique. Pour une SCPI étrangère seule avec
   méthode du crédit d'impôt, la question est secondaire, mais elle peut
   importer si vous avez d'autres biens loués.
5. **Enfant au collège (Paul).** Paul était-il scolarisé au collège au
   31/12/2025 ? (Case 7EC si le crédit d'impôt scolarité est reconduit.)
6. **Garde de Léa — prestataire unique ?** Y a-t-il eu des frais de garde
   hors crèche (assistante maternelle, garde partagée à domicile) ? La
   case 7GA concerne la garde hors domicile ; la garde au domicile relève
   d'une autre case (employeur direct = 7DB).
7. **Autres réductions / crédits d'impôt.** Avez-vous des versements PER,
   des cotisations syndicales, des frais de téléassistance, des travaux de
   rénovation énergétique (MaPrimeRénov'), des emplois à domicile (ménage,
   soutien scolaire) ? Ce sont les oublis les plus fréquents.
8. **Indemnités, primes, avantages en nature.** Les 1AJ/1BJ annoncés sur les
   bulletins de décembre tiennent-ils compte de toutes les primes (13e mois,
   participation, intéressement versés en dehors du PEE) ?
9. **IFI.** Patrimoine immobilier net taxable > 1,3 M€ ? (Valeur RP après
   abattement de 30 %, SCPI, etc.) Si oui → formulaire 2042-IFI.
10. **Prélèvement à la source.** Le taux actuel est-il individualisé ou
    commun au foyer ? Souhaitez-vous l'adapter après déclaration ?
11. **Dons complémentaires.** D'autres dons en 2025 (associations cultuelles,
    politiques, parti, ONG type Croix-Rouge, Restos du Cœur…) ?
12. **Reports éventuels.** Avez-vous des reports d'années antérieures :
    déficit foncier, moins-values mobilières, excédent de dons au-delà du
    plafond ?

## 5. Lacunes auto-détectées

Voici ce qui, d'après mon ressenti, pourrait manquer pour que la déclaration
soit entièrement fiable :

1. **Numéros de cases exacts campagne 2026.** Je reporte les cases
   « historiques » (7GA, 7UD, 7EC, 2DC, 2CK, 2BH, 2OP, 4BK, 8TK). Il faudra
   vérifier qu'elles n'ont pas été renumérotées ou fusionnées dans la notice
   2042 de 2026. Les plafonds (1 000 € pour 7UD, 3 500 € pour 7GA, 1 791 €
   par demi-part) doivent aussi être confirmés dans la notice 2026.
2. **Traitement exact des SCPI étrangères.** Selon la convention applicable,
   certaines SCPI relèvent de la « méthode du taux effectif » (revenus
   exonérés en France mais pris en compte pour déterminer le taux) et
   d'autres de la « méthode du crédit d'impôt égal à l'impôt français ».
   L'IFU Corum indique « crédit d'impôt égal à l'impôt français » (donc
   8TK). Mais pour des SCPI multi-pays (Pays-Bas, Italie, Espagne, etc.),
   certains pays relèvent de 8TK et d'autres de 4EA/8TI. Je n'ai pas de
   détail pays par pays.
3. **Report des revenus fonciers : 2044 ou pas 2044 ?** La pratique varie :
   certains praticiens recommandent de remplir un 2044 même pour une SCPI
   étrangère (section « immeubles ruraux » ou régime réel), d'autres non.
   Je n'ai pas la référence BOFiP sous les yeux pour trancher.
4. **Revenus complémentaires potentiellement non fournis.** Je n'ai que les
   bulletins de décembre. Il manque potentiellement :
   - récapitulatif annuel des indemnités journalières (maladie),
   - attestation Pôle emploi / France Travail (si chômage partiel),
   - allocations PAJE autres que CMG,
   - revenus de participation/intéressement (si versés hors PEE).
5. **Autres placements.** Pas d'information sur PEA, PEE, assurance-vie,
   livret A (non imposable mais utile pour contexte), PER individuel.
6. **Pension alimentaire, prestations compensatoires.** Aucune info — à
   écarter si le couple est effectivement marié et non séparé.
7. **Charges non mentionnées.** Frais réels professionnels (si > 10 %
   forfaitaire), télétravail, cotisations syndicales, frais de formation.
8. **Contrôle de cohérence préremplissage.** Les montants préremplis par
   l'administration (DGFiP) via la DSN (1AJ, 1BJ) et les IFU doivent être
   comparés à ceux que je propose ici. Un écart peut signaler une prime
   non comptée ou un double préremplissage.
9. **Domiciliation fiscale et adresse.** Je n'ai pas l'adresse exacte du
   foyer ni le numéro fiscal de référence — à reprendre du dernier avis
   d'imposition.
10. **Régime matrimonial.** Communauté / séparation de biens / participation
    aux acquêts : sans impact sur l'IR du foyer commun, mais à connaître
    pour la cohérence patrimoniale (notamment SCPI et CTO).
11. **Impact taxe foncière / taxe d'habitation.** Hors IR mais l'échéancier
    de crédit RP suggère qu'il faut vérifier que la taxe foncière est bien
    payée et que la RP est bien déclarée comme telle pour éviter la taxe
    sur logements vacants ou taxe d'habitation résidence secondaire.
12. **Crédit d'impôt employé à domicile.** Si un intervenant (ménage,
    baby-sitter ponctuel) a été payé via CESU, c'est une case 7DB oubliée
    fréquente.
13. **Simulation finale.** Je ne peux pas produire le montant d'impôt dû
    sans simulateur officiel — il faudra le simuler sur impots.gouv.fr en
    testant l'option 2OP.

---

*Document généré le 23/04/2026 à partir des pièces fournies. À valider
avant dépôt sur impots.gouv.fr (ouverture de la campagne déclarative 2026
généralement début avril ; dates limites de dépôt échelonnées par
département, fin mai / début juin pour la déclaration en ligne).*
