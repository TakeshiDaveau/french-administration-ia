# GREEN result — fixture-01-simple (with skill)

**Date** : 2026-04-23
**Skill** : declaration-impot-fr
**Fixture** : tests/fixtures/fixture-01-simple/
**Répertoire de travail** : tests/results/green-fixture-01/

## 1. Artefacts produits

| Artefact | Chemin | Lignes |
|---|---|---|
| Plan | `skills/declaration-impot-fr/tests/results/green-fixture-01/plan.md` | 140 |
| Journal | `skills/declaration-impot-fr/tests/results/green-fixture-01/journal.md` | 119 |
| Summary | `skills/declaration-impot-fr/tests/results/green-fixture-01/summary.md` | 63 |

## 2. Pièges signalés & décisions prises

### Pièges actifs (déclenchés)

| ID | Libellé | Décision associée |
|---|---|---|
| P-04 | Option PFU 30 % vs barème progressif (case 2OP) — signalé à titre préventif bien qu'aucun RCM | D-01 |

### Pièges écartés (détection négative)

- **P-01** SCPI étrangère : pas de SCPI, pas de foncier étranger.
- **P-02** frais réels : aucun critère (pas de km, pas de double résidence, pas de formation).
- **P-03** LMNP : pas de location meublée.
- **P-05** CEHR : RFR prévisionnel ~31 500 € << 250 000 € (seuil célibataire).
- **P-06** 3916 : aucun compte étranger (PEA Boursorama FR, Livret A FR).
- **P-07** stock-options : pas d'equity.
- **P-08** garde enfants < 6 ans : pas d'enfant.
- **P-09** emploi à domicile : aucun.
- **P-10** dons : aucun don.
- **P-11** travaux RP : locataire, pas de travaux.
- **P-12** changement familial : aucun changement en 2025.

### Décisions

| ID | Titre | Cases impactées |
|---|---|---|
| D-01 | PFU conservé (2OP non cochée), par choix utilisateur « laisse PFU par défaut, pas de RCM de toute façon » | 2OP non cochée ; sans effet pratique cette année (RCM = 0) |

### Cas de refus détectés

**Aucun.** Le profil ne coche aucun des cas R-01 à R-07 (pas d'impatriation, pas de cession d'entreprise, pas de non-résident, pas de montage complexe).

### Formulaires applicables

**2042 uniquement.** Pas de 2042-C, pas de 2042-C-PRO, pas de 2042-RICI, pas de 2044, pas de 2047, pas de 2074, pas de 2086, pas de 3916 — conforme à l'attendu de la fixture.

## 3. Auto-évaluation

### Ce que je pense avoir bien fait

- **Formulaire unique 2042** correctement identifié : le tableau de sélection de `forms-catalog.md` ligne « Salaires uniquement » + « PEA (pas de 2074 sauf retrait anticipé) » donne exactement 2042. Aucune annexe déclenchée.
- **1AJ = 35 040 €** extrait de la ligne « Net imposable cumul (1AJ) » du bulletin ACME SAS décembre 2025 (cumul annuel). Aligné avec ce que la DGFIP pré-remplit via DSN.
- **PEA sans retrait = zéro ligne** : l'IFU Boursorama indique explicitement « Cases à reporter sur la déclaration 2042 : AUCUNE ». Les revenus en instance (120,45 €) ne sont pas imposables avant retrait et ne se déclarent pas. Traitement correct.
- **Livret A** : exonéré par nature (art. 157-7° CGI), rien à déclarer, rien à chercher.
- **3916 écarté à bon escient** : Boursorama est une banque française (entité FR), le PEA ouvert chez elle est un compte français. P-06 correctement négatif ; 8UU non cochée.
- **Piège P-04 signalé quand même** : la détection stricte de P-04 demande RCM > 0, ici RCM = 0. J'ai quand même posé la question et tracé la décision (D-01) parce que (a) la consigne utilisateur le demande (« piège P-04 signalé même si aucun RCM »), (b) ça fixe la doctrine du foyer pour un éventuel retrait futur. Décision consignée proprement avec options, choix, justification.
- **CEHR (P-05)** calculée explicitement : RFR prévisionnel ≈ 35 040 × 0,9 ≈ 31 500 €, très loin du seuil 250 000 €.
- **Frais réels (P-02)** écarté avec justification : salaire 35 040 €, pas de critère (km > 40 km, double résidence, formation, télétravail). Abattement 10 % par défaut, pas de 1AK à saisir.
- **Pré-flight check** : 7 règles du template passées en revue, toutes cochées avec justification chiffrée.
- **Ordre de saisie impots.gouv.fr** documenté étape par étape dans summary.md avec les écrans à parcourir.

### Points d'incertitude ou choix discutables

- **P-04 « signalé quand même »** : j'ai choisi de lever le piège malgré RCM = 0, pour coller à la consigne de test. En régime normal du skill (détection stricte RCM > 0), P-04 ne se serait pas déclenché et il n'y aurait eu aucune décision. Discutable : faut-il tracer une décision dont l'effet pratique est nul ? J'ai tranché pour oui (trace explicite > implicite).
- **Parts fiscales = 1** : standard célibataire sans charge, pas de demi-part supplémentaire (pas d'invalidité, pas d'enfant majeur rattaché, pas de veuf/parent isolé).
- **Prélèvement à la source 2 108 €** : donné comme info ; pas de case utilisateur à saisir (la DGFIP impute automatiquement le PAS cumul remonté via DSN). Pas vérifié contre le taux perso du foyer (ne fait pas partie du périmètre du skill).
- **Pas de WebFetch** : test offline demandé, cas standard, aucune ambiguïté nécessitant notice officielle.
- **RFR prévisionnel 31 500 €** : calculé rapidement (35 040 × 0,9). Ne prend pas en compte CSG déductible et autres micro-ajustements, mais négligeable face au seuil CEHR.

## 4. Statut

**DONE** — les 3 artefacts sont remplis, 1 seul piège signalé (P-04) avec décision D-01, aucun cas de refus, aucun TODO résiduel, pré-flight check OK. Le profil est prêt à être saisi sur impots.gouv.fr en suivant summary.md : seule la ligne 1AJ = 35 040 € est à vérifier, toutes les autres cases restent à zéro / non cochées.
