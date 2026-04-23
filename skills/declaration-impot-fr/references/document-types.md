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
