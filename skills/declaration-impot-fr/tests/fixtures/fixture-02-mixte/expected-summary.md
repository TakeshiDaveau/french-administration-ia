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
