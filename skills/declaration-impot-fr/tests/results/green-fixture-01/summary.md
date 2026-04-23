# Déclaration 2025 — Récap cases à remplir

> ⚠ Ce récapitulatif a été préparé avec assistance IA à partir des documents et
> réponses fournis. Les calculs sont indicatifs. Vérifie chaque valeur avant
> soumission. En cas de doute sur un cas complexe, consulte un expert-comptable
> ou le centre des finances publiques.

**Foyer** : Martin Jean (célibataire, 1 part)
**Campagne** : 2026 (revenus 2025)
**Généré** : 2026-04-23

## Ordre de saisie sur impots.gouv.fr

1. **État civil et situation de famille** : confirmer « célibataire », aucune personne à charge, adresse 10 rue de la Paix 75002 Paris (1er janvier = 31 décembre).
2. **Revenus et charges → Traitements, salaires, pensions, rentes** : vérifier / saisir **1AJ = 35 040 €**.
3. **Revenus et charges → Revenus de capitaux mobiliers** : laisser toutes les cases à 0, **ne pas cocher 2OP** (PFU par défaut, décision D-01).
4. **Revenus et charges → Plus-values et gains divers** : rien à saisir (PEA sans retrait).
5. **Revenus et charges → Revenus fonciers** : rien à saisir.
6. **Revenus et charges → Revenus étrangers** : ne pas ouvrir la 2047 (aucun revenu étranger).
7. **Charges déductibles** : rien à saisir.
8. **Réductions et crédits d'impôt (2042-RICI)** : rien à saisir.
9. **Éléments divers (section 8)** : **8UU non cochée** (aucun compte étranger). Pas de CEHR (8HV/8HW) à lisser.
10. **Récap et signer** : vérifier l'impôt estimé contre le PAS déjà prélevé (2 108 €).

## Tableau des cases

| Formulaire | Écran en ligne | Case | Libellé | Valeur | Source | Note |
|---|---|---|---|---|---|---|
| 2042 | État civil | — | Situation : célibataire (case C) | cochée | Profil phase 1 (A1) | 1 part |
| 2042 | État civil | — | Personnes à charge | 0 | Profil phase 1 (A3) | — |
| 2042 | Traitements, salaires | **1AJ** | Salaires nets imposables déclarant | **35 040 €** | Bulletin ACME SAS décembre 2025 (cumul annuel) | Pré-rempli via DSN, à vérifier |
| 2042 | Traitements, salaires | 1AK | Frais réels déclarant | — | — | Abattement 10 % par défaut (P-02 écarté) |
| 2042 | RCM | 2DC / 2TT / 2CH / 2BH / 2CK / 2CG | Dividendes, intérêts, etc. | 0 | IFU Boursorama : « AUCUNE » | PEA sans retrait, Livret A non imposable |
| 2042 | RCM | **2OP** | Option barème progressif | **NON cochée** | Décision utilisateur | **D-01** (P-04) — PFU par défaut |
| 2042 | Divers (section 8) | 8UU | Compte à l'étranger | **NON cochée** | Profil phase 1 (C14 = non) | Pas de 3916 associé |
| 2042 | Divers (section 8) | 8HV / 8HW | CEHR lissage | — | RFR ~31 500 € << 250 000 € | P-05 écarté |

**Aucune autre case à remplir.** Pas de 2042-C, pas de 2042-C-PRO, pas de 2042-RICI, pas de 2044, pas de 2047, pas de 2074, pas de 2086, pas de 3916.

## À vérifier avant validation

- [ ] **1AJ pré-rempli** = 35 040 € (DSN ACME SAS). Si écart avec le bulletin cumul, corriger à la main.
- [ ] **Prélèvement à la source** affiché en récap ≈ 2 108 € (info cumul bulletin décembre).
- [ ] **2OP** bien **non cochée** (sinon barème progressif appliqué à tous les RCM du foyer).
- [ ] **8UU** bien **non cochée** (aucun compte étranger à déclarer).
- [ ] **PEA** : aucun événement fiscal. En cas de retrait ultérieur, rouvrir le traitement RCM/plus-value (> 5 ans = PS seuls, < 5 ans = PFU + PS).
- [ ] **Livret A** : exonéré par nature, ne jamais déclarer.
- [ ] **Adresse** sur la déclaration = 10 rue de la Paix, 75002 Paris — cohérente avec l'IFU Boursorama.
- [ ] **Pas de 2042-RICI, pas de 3916** : ne rien ouvrir par inadvertance.

---

## Pièges signalés

- **P-04** — PFU vs barème progressif (case 2OP) → **décision D-01 consignée** dans `journal.md`. Choix : PFU par défaut, 2OP non cochée.

## Pièges écartés (pour mémoire)

P-01 (SCPI étrangère), P-02 (frais réels), P-03 (LMNP), P-05 (CEHR), P-06 (3916), P-07 (stock-options), P-08 (garde enfants), P-09 (emploi à domicile), P-10 (dons), P-11 (travaux RP), P-12 (changement familial).

## Cas de refus

Aucun. Profil standard, entièrement couvert par le skill.
