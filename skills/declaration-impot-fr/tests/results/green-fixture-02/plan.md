# Déclaration des revenus 2025 — Plan

**État** : terminé • Phase 6/6
**Profil** : Couple marié (Jean & Claire Dupont), imposition commune, 2 enfants à charge (Léa 4 ans en crèche, Paul 11 ans au collège), propriétaires RP avec emprunt en cours. Parts fiscales : 2 + 2 × 0,5 = 3 parts.
**Formulaires applicables** : 2042, 2042-RICI, 2047 (revenus fonciers étrangers SCPI)
**Généré** : 2026-04-23
**Mise à jour** : 2026-04-23

---

## Légende

- 🟢 terminé • 🟡 en cours • ⚪ à faire
- `[x]` / `[ ]` : case individuelle remplie / à remplir
- `[!]` : piège (voir `journal.md` décision D-NN)
- `TODO` : valeur encore inconnue

---

## [🟢] 2042 — Déclaration principale

### [🟢] Section "État civil & situation de famille"

- [x] Case B (couple marié, imposition commune) — source : profil phase 1 (A1).
- [x] Nombre d'enfants à charge : 2 (cases F/G) — Léa née 2021-06-15, Paul né en 2014 (11 ans au collège).
- [x] Pas de garde alternée, pas d'enfant majeur rattaché, pas d'invalide au foyer.

### [🟢] Section 1 — Traitements, salaires

- [x] 1AJ (Salaires déclarant — Jean) = `48 000` — source : bulletin de paie ACME SAS décembre 2025 (cumul net imposable).
- [x] 1BJ (Salaires conjoint — Claire) = `42 000` — source : bulletin de paie BETA SARL décembre 2025 (cumul net imposable).
- [x] 1AK / 1BK (frais réels) = non renseigné → abattement 10 % par défaut (pas de frais réels évoqués, P-02 écarté).

### [🟢] Section 2 — Revenus de capitaux mobiliers [!] P-04

- [x] 2DC (Dividendes éligibles abattement 40 %) = `1 200` — source : IFU Boursorama.
- [x] 2CK (Crédit d'impôt PFU 12,8 %) = `153,60` — source : IFU Boursorama.
- [x] 2BH (Revenus déjà soumis aux PS) = `1 200` (PS 17,2 % déjà retenus 206,40 €) — source : IFU Boursorama.
- [x] 2OP (Option barème progressif) = **NON cochée** — décision D-02 (PFU retenu, TMI 30 %).

### [🟢] Section 4 — Revenus fonciers [!] P-01

- [x] 4BK (Revenus fonciers étrangers avec crédit d'impôt égal impôt français) = `3 400` — source : IFU SCPI Corum XL (report 2047 section VI).
- [ ] 4BA / 4BB / 4BE : non applicable (pas de revenu foncier France, pas de déficit).

### [🟢] Section 8 — Divers / reports

- [x] 8TK (Crédit d'impôt pour revenus étrangers) = `3 400` — source : report 2047 section VI, Allemagne, méthode crédit d'impôt (calcul du CI = 3 400 × taux moyen IR du foyer, effectué automatiquement par impots.gouv.fr).
- [ ] 8UU (Compte à l'étranger) = **non coché** — aucun compte étranger identifié (Boursorama = banque FR, SCPI Corum XL = société française). P-06 écarté.
- [ ] 8HV / 8HW (CEHR lissage) = non applicable — revenus du foyer très inférieurs au seuil 500 000 € (couple). P-05 écarté.

## [🟢] 2042-RICI — Réductions et crédits d'impôt

### [🟢] Garde d'enfants < 6 ans [!] P-08

- [x] 7GA (Frais de garde hors domicile, enfant < 6 ans — Léa) = `2 400` — source : attestation crèche Les Petits Loups (4 200 € payés − 1 800 € CMG PAJE = 2 400 € net). Décision D-03.

### [🟢] Scolarité

- [x] 7EA (Enfant au collège — Paul) = `1` (nombre d'enfants) → réduction forfaitaire 61 €. Source : profil phase 1 (E2a).

### [🟢] Dons [!] P-10

- [x] 7UD (Dons aux associations d'aide aux personnes en difficulté) = `400` — source : reçu fiscal Secours Catholique n° 2025-00456, art. 200-1 ter CGI. Décision D-04.
- [ ] 7UF (Intérêt général) = 0.

## [🟢] 2047 — Revenus encaissés à l'étranger

### [🟢] Section VI — Revenus fonciers étrangers [!] P-01

- [x] Ligne : Pays = **Allemagne**, Revenu net = `3 400 €`, Méthode = **crédit d'impôt égal à l'impôt français** (convention France-Allemagne art. 20), colonne "Crédit d'impôt".
- [x] Report 2042 case **4BK** = 3 400 €.
- [x] Report 2042 case **8TK** = 3 400 € (base de calcul du CI ; impots.gouv.fr calcule ensuite le CI effectif = 3 400 × taux moyen IR du foyer).
- Source : IFU SCPI Corum XL + décision D-01.

## [🟢] 3916 — Comptes à l'étranger

- [ ] Non requis : aucun compte détenu à l'étranger dans le profil. La SCPI Corum XL est gérée par Corum AM (France) ; les immeubles sont à l'étranger mais cela ne constitue pas un compte étranger. Le compte Boursorama est français.

---

## Éléments écartés / non applicables

- **Intérêts emprunt RP 4 500 €** : non déductibles depuis 2011 (le prêt est destiné à la résidence principale ; pas d'emprunt "historique" 2007-2010 évoqué). Aucune case à remplir. Voir note dans `summary.md`.
- **Assurance emprunteur 620 €** : même traitement, non déductible (RP).
- **Capital remboursé** : jamais déductible.
- **Pas de travaux** (D4 = non) → pas de MaPrimeRénov', pas de CITE, P-11 écarté.
- **Pas de pension alimentaire** (E3 = non) → pas de 6DD/6GI/6GJ.
- **Pas de PER ni Madelin** (F5/F6 = non) → pas de 6NS/6NT.

## Pièges actifs

- [!] **P-01** — SCPI étrangère (Corum XL / Allemagne) → décision **D-01** dans `journal.md`.
- [!] **P-04** — Option PFU vs barème (case 2OP) → décision **D-02**.
- [!] **P-08** — Crédit garde d'enfants < 6 ans (montant net après CMG) → décision **D-03**.
- [!] **P-10** — Don : qualification 7UD vs 7UF → décision **D-04**.

## Pièges écartés

- P-02 (frais réels) : pas de frais significatifs évoqués → abattement 10 % par défaut.
- P-05 (CEHR) : RFR très inférieur à 500 000 €.
- P-06 (3916) : aucun compte étranger.
- P-07 (stock-options) : pas d'equity mentionné.
- P-11 (travaux RP) : pas de travaux.
- P-12 (changement familial) : aucun changement en 2025.

## Questions en attente

Aucune — toutes les ambiguïtés ont été levées lors des questions de phase 3.
