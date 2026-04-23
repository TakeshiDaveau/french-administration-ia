# GREEN result — fixture-02-mixte (with skill)

**Date** : 2026-04-23
**Skill** : declaration-impot-fr
**Fixture** : tests/fixtures/fixture-02-mixte/
**Répertoire de travail** : tests/results/green-fixture-02/

## 1. Artefacts produits

| Artefact | Chemin | Lignes |
|---|---|---|
| Plan | `skills/declaration-impot-fr/tests/results/green-fixture-02/plan.md` | 109 |
| Journal | `skills/declaration-impot-fr/tests/results/green-fixture-02/journal.md` | 166 |
| Summary | `skills/declaration-impot-fr/tests/results/green-fixture-02/summary.md` | 74 |

## 2. Pièges signalés & décisions prises

### Pièges actifs (déclenchés)

| ID | Libellé | Décision associée |
|---|---|---|
| P-01 | SCPI étrangère — traitement conventionnel (Corum XL, Allemagne) | D-01 |
| P-04 | Option PFU 30 % vs barème progressif (case 2OP) | D-02 |
| P-08 | Crédit d'impôt garde d'enfants < 6 ans (net après CMG) | D-03 |
| P-10 | Dons — 7UD (75 %) vs 7UF (66 %) | D-04 |

### Pièges écartés (détection négative)

- **P-02** frais réels : pas de frais professionnels évoqués → abattement 10 % par défaut.
- **P-05** CEHR : RFR ≈ 94 600 € << seuil couple 500 000 €.
- **P-06** 3916 : aucun compte étranger (Boursorama FR, Corum FR).
- **P-07** stock-options : pas d'equity mentionné.
- **P-11** travaux RP : pas de travaux.
- **P-12** changement familial : aucun changement en 2025.

### Décisions

| ID | Titre | Cases impactées |
|---|---|---|
| D-01 | SCPI Corum XL — convention France-Allemagne, méthode crédit d'impôt égal à l'impôt français | 4BK = 3 400 € ; 8TK = 3 400 € ; 2047 section VI Allemagne |
| D-02 | PFU conservé (2OP non cochée), TMI 30 %, préférence utilisateur | 2OP non cochée (2DC/2CK/2BH inchangés) |
| D-03 | Garde Léa : 7GA = 2 400 € (montant net après CMG 1 800 €, et non 4 200 € brut) | 7GA = 2 400 € |
| D-04 | Don Secours Catholique en 7UD (art. 200-1 ter CGI) et non 7UF | 7UD = 400 € |

## 3. Auto-évaluation

### Ce que je pense avoir bien fait

- **Structure complète des 3 artefacts** : plan.md hiérarchisé par formulaire (2042 → 2042-RICI → 2047), journal.md avec profil/documents/décisions/pré-flight, summary.md avec ordre de saisie en ligne + tableau + checklist.
- **Pièges identifiés proactivement** : les 4 pièges pertinents (P-01, P-04, P-08, P-10) ont été détectés et ont chacun leur décision formalisée. Les 6 pièges non pertinents sont explicitement écartés avec justification dans le plan et le journal.
- **SCPI Corum XL (P-01)** : traitement correct — 4BK + 8TK + ligne 2047 section VI, pays Allemagne, méthode "crédit d'impôt égal à l'impôt français" (et non 4BL/8TI qui serait la méthode exonération avec taux effectif). Note ajoutée sur le calcul CI effectif = 3 400 × taux moyen.
- **Garde Léa (P-08)** : montant net restant à charge (2 400 €) et non montant payé brut (4 200 €). Âge vérifié (4 ans au 1er janvier 2025, née 2021-06-15, donc < 6 ans → 7GA éligible).
- **Don Secours Catholique (P-10)** : qualification 7UD (art. 200-1 ter — 75 %) confirmée par le reçu fiscal. Distinction explicite avec 7UF.
- **PFU (P-04)** : décision documentée à partir de la réponse utilisateur (TMI 30 %, "je laisse par défaut") ; 2OP explicitement non cochée.
- **3916 / 8UU** : écartés à bon escient — la SCPI Corum XL est française (Corum AM), elle détient simplement des immeubles à l'étranger ; Boursorama est française. Aucun compte au sens art. 1649 A CGI.
- **Intérêts RP non déductibles** : traitement correct (depuis 2011 plus de déductibilité, pas d'emprunt 2007-2010 évoqué). Noté explicitement dans plan + summary.
- **Scolarité Paul (7EA)** : case bien identifiée (collège = 7EA, 61 € forfaitaire) ; distinction 7EC lycée / 7EF sup pour référence future.
- **Pré-flight check** : 7 règles du template passées en revue, chacune explicitement cochée avec justification.

### Points d'incertitude ou choix discutables

- **Code pays SCPI Corum XL (Allemagne)** : j'ai extrapolé la convention France-Allemagne art. 20 sans WebFetch (test offline). L'IFU confirme le traitement ; en contexte réel j'aurais fetch la notice 2047 et BOI-INT-CVB-DEU pour validation.
- **Parts fiscales** : j'ai estimé 3 parts (2 + 2 × 0,5) de façon standard pour un couple avec 2 enfants en charge plein (pas de garde alternée). Correct sauf cas particulier non évoqué.
- **Calcul CEHR** : j'ai fait un RFR prévisionnel approximatif (~94 600 €) basé sur salaires + RCM + foncier étranger. Pas de calcul précis (pas de simulation d'abattement, de CSG déductible, etc.) car largement sous le seuil — mais dans un cas limite il faudrait être plus rigoureux.
- **2BH** : l'IFU indique "PS 17,2 % déjà retenus 206,40 €". J'ai mis 2BH = 1 200 € (assiette = base sur laquelle les PS ont été prélevés). À re-vérifier : selon la notice 2042, 2BH correspond au montant des revenus déjà soumis aux PS (assiette), pas au montant des PS. C'est bien 1 200 €.
- **Choix 7GA vs 7GG** : j'ai utilisé 7GA (garde hors domicile, pas de garde alternée). 7GG serait le cas de garde alternée, 7GB/7GC non pertinents ici. Décision cohérente avec le profil.
- **TMI 30 % déclarée par l'utilisateur** : acceptée telle quelle pour la décision PFU vs barème ; en toute rigueur la TMI dépend des parts et du RFR, qu'il faudrait recalculer. Choix utilisateur explicite donc consigné sans recalcul.
- **Justificatif "Secours Catholique = aide aux personnes"** : je me suis appuyé sur le reçu (art. 200-1 ter) et la confirmation utilisateur. Bien.

## 4. Statut

**DONE** — les 3 artefacts sont remplis, les 4 pièges pertinents identifiés et documentés, le pré-flight check passe. Aucun TODO résiduel, aucune incohérence détectée. Le profil est prêt à être saisi sur impots.gouv.fr en suivant summary.md.
