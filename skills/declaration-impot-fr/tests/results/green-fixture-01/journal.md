# Déclaration 2025 — Journal

**Déclarant(s)** : Martin Jean
**Foyer fiscal** : 1 personne (célibataire, sans charge)
**Campagne** : 2026
**Démarré** : 2026-04-23

---

## Profil fiscal préliminaire (phase 1)

### Situation du foyer (bloc A)
- A1 : célibataire.
- A2 : sans objet (non marié).
- A3 : pas d'enfant à charge.
- A4 : sans objet.
- A5 : pas d'enfant majeur rattaché.
- A6 : pas de personne invalide à charge.
- → **Parts fiscales : 1**.

### Changements en N (bloc B)
- B1-B7 : aucun changement en 2025 (pas de mariage/PACS, pas de divorce, pas de naissance, pas de décès, pas de déménagement, pas d'expatriation/retour, pas de départ d'enfant).

### Revenus et patrimoine attendus (bloc C)
- C1 : salaires, **1 seul employeur** sur l'année (ACME SAS).
- C2-C6 : non.
- C7 : **non** (PEA sans retrait ; Livret A non imposable).
- C8 : non (aucune cession hors PEA).
- C9 : non (pas de crypto).
- C10-C12 : non (pas de foncier, pas de SCPI).
- C13 : non (pas de stock-options / RSU / AGA / BSPCE).
- C14 : **non** (PEA Boursorama = France, Livret A = France).
- C15 : non (pas de cession d'entreprise) → **pas de cas de refus R-01**.

### Logement et emprunts (bloc D)
- D1 : **locataire** résidence principale (Paris 75002).
- D2 : pas d'emprunt RP.
- D3a-D3e : aucun emprunt à potentiel fiscal.
- D4a-D4d : aucun travaux.

### Enfants et famille (bloc E)
- E1-E3 : sans objet (pas d'enfant).

### Autres réductions/crédits (bloc F)
- F1-F7 : non (pas d'emploi à domicile, pas de dons, pas de dispositif locatif, pas de syndicat, pas de Madelin, pas de PER, pas de FCPI/FIP/SOFICA).

### Méta (bloc G)
- G1 : revenus **2025**, campagne **2026** — confirmé.
- G2 : déclaration **séparée** (célibataire, unique déclarant).

### Formulaires pressentis
- **2042 uniquement**.
- Pas de 2042-C, pas de 2042-C-PRO, pas de 2042-RICI, pas de 2044, pas de 2047, pas de 2074, pas de 2086, pas de 3916.

### Pièges à surveiller
- **P-04** (PFU vs barème, case 2OP) — signalé à titre préventif. Détection standard = RCM > 0 ; ici RCM = 0 mais la décision "laisser 2OP non cochée / PFU par défaut" est explicitement consignée (D-01).
- Tous les autres pièges (P-01, P-02, P-03, P-05, P-06, P-07, P-08, P-09, P-10, P-11, P-12) sont écartés — voir `plan.md` section "Pièges écartés".

### Cas de refus détectés
- **Aucun**. Le profil ne coche aucun des cas R-01 à R-07.

---

## Documents (phase 2)

### Fournis
- `bulletin-paie-dec.txt` — reçu 2026-04-23 — bulletin de paie décembre 2025 ACME SAS (avec cumul annuel).
- `IFU-banque-populaire.txt` — reçu 2026-04-23 — IFU 2025 Boursorama PEA n° 12345678.

### Extraits / saisis manuellement
- **Bulletin ACME SAS (cumul 2025)** :
  - Brut imposable cumul : 45 600 €.
  - **Net imposable cumul (1AJ) : 35 040 €** → case **1AJ**.
  - Prélèvement à la source cumul : 2 108 € (info, pas de case à saisir).
- **IFU Boursorama PEA 12345678** :
  - Aucune cession, aucun retrait.
  - Revenus en instance 120,45 € (non imposables, ne se déclarent pas).
  - Mention explicite : « Cases à reporter sur la déclaration 2042 : **AUCUNE** ».
  - → aucune case 2xx à remplir.

### Manquants (à demander à l'utilisateur)
- Aucun. Le profil phase 1 est couvert :
  - salarié unique employeur → bulletin ACME SAS présent (cumul suffisant pour 1AJ).
  - PEA → IFU Boursorama présent et confirme absence d'événement fiscal.
  - Livret A : non imposable, aucun doc nécessaire.

---

## Décisions

### D-01 — Option PFU vs barème progressif (case 2OP)
**Date** : 2026-04-23
**Piège rattaché** : P-04
**Contexte** : la détection stricte de P-04 suppose RCM > 0 ; ici les RCM sont nuls (PEA sans retrait, Livret A non imposable). La question a été posée à titre préventif pour tracer la préférence de l'utilisateur au cas où un événement fiscal surviendrait en cours de saisie (ex : oubli d'un dividende), et pour fixer la doctrine du foyer.
**Options envisagées** :
- (A) Cocher **2OP** : option barème progressif sur l'ensemble des RCM du foyer.
- (B) Ne pas cocher 2OP : **PFU 30 %** par défaut.
**Choix** : **(B) — PFU par défaut, 2OP non cochée**.
**Justification** : réponse utilisateur littérale « laisse PFU par défaut, pas de RCM de toute façon ». Le choix est sans effet pratique sur la déclaration 2025 (RCM = 0) mais il est consigné pour mémoire.
**Source** : pitfalls.md P-04 ; BOI-RPPM-RCM-20 (doctrine générale PFU/barème) — pas de WebFetch nécessaire (cas standard).
**Impact sur cases** : 2OP **non cochée** sur 2042. Aucune autre case impactée (2DC = 2TT = 2CH = 2BH = 2CK = 2CG = 0).

---

## Pré-flight check (phase 6)

- [x] **Aucun TODO résiduel dans `plan.md`** : vérifié, aucune occurrence de `TODO`.
- [x] **Abattement 10 % OU frais réels choisi une seule fois par déclarant** : abattement 10 % par défaut pour Martin Jean (aucune saisie 1AK). Pas de conjoint. OK.
- [x] **Reports étrangers corrects (8TK = somme CI 2044 + 2047)** : sans objet (pas de revenu étranger). 8TK = 0. OK.
- [x] **CEHR vérifiée si RFR > seuil** : RFR prévisionnel ~31 500 € << 250 000 € (seuil célibataire). 8HV non coché. OK.
- [x] **3916 présent pour chaque compte étranger identifié** : aucun compte étranger, aucun 3916 requis, 8UU non cochée. OK.
- [x] **Sommes redondantes cohérentes (1AJ ≈ IFU employeur)** : 1AJ = 35 040 € = « Net imposable cumul » du bulletin ACME SAS décembre 2025. Cohérent.
- [x] **Cases à cocher critiques non oubliées (2OP, 8UU, 7UF, 7UD…)** :
  - 2OP : non cochée (D-01).
  - 8UU : non cochée (pas de compte étranger).
  - 7UD / 7UF : non remplies (pas de don).
  - Aucune autre case à cocher critique omise.

→ **Pré-flight check : OK**. Aucun écart. Aucune incohérence.
