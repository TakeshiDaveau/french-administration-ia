# Déclaration 2025 — Récap cases à remplir

> ⚠ Ce récapitulatif a été préparé avec assistance IA à partir des documents et
> réponses fournis. Les calculs sont indicatifs. Vérifie chaque valeur avant
> soumission. En cas de doute sur un cas complexe, consulte un expert-comptable
> ou le centre des finances publiques.

**Foyer** : Jean & Claire Dupont (couple marié, 2 enfants Léa & Paul)
**Campagne** : 2026 (revenus 2025)
**Généré** : 2026-04-23

## Ordre de saisie sur impots.gouv.fr

1. **État civil & situation de famille** — vérifier couple marié + 2 enfants (dont Léa < 6 ans). Pas de changement 2025.
2. **Traitements, salaires, pensions** — vérifier 1AJ (48 000 €) et 1BJ (42 000 €) pré-remplis par la DSN ; pas d'option frais réels.
3. **Revenus de capitaux mobiliers** — vérifier 2DC / 2CK / 2BH pré-remplis par Boursorama ; **NE PAS cocher 2OP** (PFU retenu, voir D-02).
4. **Revenus encaissés à l'étranger (2047)** — ajouter 1 ligne section VI : pays Allemagne, 3 400 €, méthode "crédit d'impôt égal à l'impôt français". Report automatique vers 4BK et 8TK.
5. **Revenus fonciers** — vérifier que **4BK = 3 400 €** après report 2047. Pas de 2044 (aucun foncier France).
6. **Réductions et crédits (2042-RICI)** — saisir 7GA (2 400 €), 7EA (1 enfant), 7UD (400 €).
7. **Divers / comptes étrangers** — **NE PAS cocher 8UU** (aucun compte à l'étranger). Vérifier 8TK = 3 400 €. Pas de 8HV/8HW (CEHR non applicable).
8. **Récap & signer** — rapprocher chaque case du présent tableau avant signature.

## Tableau des cases

| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
|---|---|---|---|---|---|---|
| 2042 | État civil | Case B | Couple marié, imposition commune | cochée | profil phase 1 | |
| 2042 | Personnes à charge | F/G | 2 enfants à charge (Léa 4 ans, Paul 11 ans) | 2 | profil phase 1 | |
| 2042 | Traitements & salaires | 1AJ | Salaires déclarant (Jean) | 48 000 € | bulletin ACME SAS déc. 2025 | Pré-rempli DSN — à vérifier |
| 2042 | Traitements & salaires | 1BJ | Salaires conjoint (Claire) | 42 000 € | bulletin BETA SARL déc. 2025 | Pré-rempli DSN — à vérifier |
| 2042 | RCM | 2DC | Dividendes éligibles abattement 40 % | 1 200 € | IFU Boursorama | Pré-rempli IFU |
| 2042 | RCM | 2CK | Prélèvement forfaitaire 12,8 % (crédit d'impôt) | 153,60 € | IFU Boursorama | Pré-rempli IFU |
| 2042 | RCM | 2BH | Revenus déjà soumis aux PS | 1 200 € | IFU Boursorama | PS 17,2 % = 206,40 € déjà retenus |
| 2042 | RCM | 2OP | Option barème progressif | **non cochée** | — | **D-02** — PFU retenu |
| 2047 | Revenus étrangers | Section VI | SCPI Corum XL — Allemagne | 3 400 € | IFU SCPI Corum XL | **D-01** — méthode : crédit d'impôt égal impôt français |
| 2042 | Revenus fonciers | 4BK | Revenus fonciers étrangers avec CI | 3 400 € | report 2047 | **D-01** (P-01) |
| 2042 | Divers / reports | 8TK | Crédit d'impôt pour revenus étrangers | 3 400 € | report 2047 | **D-01** — base CI ; impots.gouv calcule CI effectif = 3 400 × taux moyen |
| 2042 | Divers / reports | 8UU | Compte à l'étranger | **non cochée** | — | Aucun compte étranger au profil (P-06 écarté) |
| 2042-RICI | Réductions & crédits | 7GA | Frais de garde hors domicile (Léa < 6 ans) | 2 400 € | attestation crèche Les Petits Loups | **D-03** (P-08) — net = 4 200 − 1 800 CMG |
| 2042-RICI | Réductions & crédits | 7EA | Enfant au collège (Paul) | 1 | profil phase 1 | Réduction forfaitaire 61 € |
| 2042-RICI | Réductions & crédits | 7UD | Don aux associations d'aide aux personnes en difficulté | 400 € | reçu Secours Catholique n° 2025-00456 | **D-04** (P-10) — réduction 75 % |
| 2042-RICI | Réductions & crédits | 7UF | Dons d'intérêt général | 0 | — | Aucun don d'intérêt général |

## Cases explicitement écartées

- **4BA / 4BB / 4BE / 2044** : aucun revenu foncier en France.
- **1AK / 1BK (frais réels)** : non retenus → abattement 10 % par défaut (P-02 écarté).
- **6DD / 6GI / 6GJ (pensions alimentaires)** : non applicable.
- **6NS / 6NT (PER)** : non applicable.
- **7DB (emploi à domicile)** : non applicable.
- **8HV / 8HW (CEHR lissage)** : non applicable (RFR ≈ 94 600 € << 500 000 €).
- **3916** : aucun compte étranger.
- **Intérêts RP 4 500 € + assurance 620 €** : non déductibles (RP, post-2011).

## À vérifier avant validation

- [ ] **1AJ / 1BJ pré-remplis** : vérifier que les montants DSN pré-remplis (normalement 48 000 / 42 000) correspondent bien aux bulletins décembre. Corriger au besoin.
- [ ] **IFU Boursorama** : vérifier que 2DC / 2CK / 2BH sont bien pré-remplis. S'ils manquent ou diffèrent, les saisir manuellement.
- [ ] **2OP** : confirmer que la case n'est **pas** cochée (décision D-02).
- [ ] **2047 Section VI** : bien sélectionner "crédit d'impôt égal à l'impôt français" (et non "exonération avec taux effectif") pour l'Allemagne.
- [ ] **4BK** : vérifier le report automatique depuis 2047 ; doit être 3 400 €.
- [ ] **8TK** : vérifier le report automatique ; doit être 3 400 €. Le crédit d'impôt effectif sera calculé par le simulateur ( = 3 400 × taux moyen IR du foyer).
- [ ] **7GA** : saisir **2 400 €** (montant net), et non 4 200 € (piège P-08 classique).
- [ ] **7UD** : cocher le bon régime (aide aux personnes, 75 %) et non 7UF (intérêt général, 66 %).
- [ ] **7EA** : indiquer 1 enfant au collège (Paul), réduction forfaitaire 61 €.
- [ ] **8UU** : rester **décochée** (aucun compte étranger, pas de 3916 nécessaire).
- [ ] **Étape récap** : vérifier RFR, impôt calculé, taux moyen. Comparer à l'avis de l'an dernier pour détecter toute anomalie.

## Décisions prises (renvoi journal.md)

- **D-01** (P-01) — SCPI Corum XL : convention France-Allemagne art. 20, méthode crédit d'impôt égal à l'impôt français. Cases 4BK + 8TK + 2047 section VI.
- **D-02** (P-04) — PFU retenu (2OP non cochée) ; TMI 30 %, préférence utilisateur.
- **D-03** (P-08) — 7GA = 2 400 € (net après CMG PAJE de 1 800 €).
- **D-04** (P-10) — Don 400 € en 7UD (art. 200-1 ter CGI, aide aux personnes en difficulté).
