# Mapping CERFA ↔ écrans impots.gouv.fr — Phase 5

Correspondance entre les cases des formulaires CERFA papier et les écrans de la
télédéclaration sur impots.gouv.fr. Utilisé en phase 5 pour guider l'utilisateur
dans sa saisie en ligne.

**Principe** : sur impots.gouv.fr, la déclaration se fait en 3 étapes (la vue
cases papier est aussi disponible via "Afficher la vue cases" mais moins ergonomique) :

1. **Étape "État civil et situation de famille"** → correspond aux en-têtes 2042.
2. **Étape "Revenus et charges"** → écrans par catégorie (salaires, RCM, fonciers,
   étranger, réductions).
3. **Étape "Récap et signer"**.

## Étape 1 — État civil et situation de famille

Écrans successifs :

- **Situation personnelle** :
  - Case à cocher A-F (marié/pacsé/célibataire/divorcé/veuf).
  - Date de mariage/PACS/divorce si changement en N.
- **Personnes à charge** :
  - Nombre d'enfants (cases F, G, H, I, J de la 2042).
  - Garde alternée (case H).
  - Enfant majeur rattaché (case N).
- **Adresse au 1er janvier et au 31 décembre**.

## Étape 2 — Revenus et charges

Navigation par catégorie, dans cet ordre généralement :

### Écran "Traitements, salaires, pensions, rentes"
- Cases **1AJ / 1BJ** : salaires nets imposables (pré-remplis par DSN employeur).
- Cases **1AK / 1BK** : option frais réels (saisie manuelle → formulaire de détail).
- Cases **1AP / 1BP** : pensions.
- Cases **1AA / 1BA** : allocations chômage.
- **Cocher si option quotient** (1 MAJOR = revenu exceptionnel).

### Écran "Revenus de capitaux mobiliers"
- Cases **2DC, 2TT, 2CH, 2BH** (pré-remplis par IFU).
- Cases **2CK, 2CG** : prélèvements (crédit d'impôt) pré-remplis.
- **Case 2OP** : option barème progressif (à cocher ou non).
- **Si revenus étrangers** (ex : dividendes broker US) : renvoyer vers la 2047.

### Écran "Plus-values et gains divers"
- **Case 3VG** : plus-value nette (report de 2074).
- **Case 3VH** : moins-value nette.
- **Case 3AN** : plus-value crypto (report 2086).
- **Case 3WB** : plus-value avec abattement durée détention.

### Écran "Revenus fonciers"
- Si micro-foncier (recettes ≤ 15 000 €, location nue) : saisie directe sur 2042
  ligne **4BE**.
- Si régime réel : annexe **2044** (lien dédié en haut de l'écran).
- **4BK** : revenus fonciers étrangers avec CI (report de 2047).
- **4BL** : revenus fonciers étrangers exonérés (report de 2047).
- **4BB** : déficit imputable sur revenu global.

### Écran "Revenus étrangers"
- Accès : "Revenus encaissés à l'étranger" → **formulaire 2047**.
- Pour chaque revenu : préciser pays, type (foncier, RCM, PV, salaire),
  convention applicable.
- Reports automatiques vers 2042 cases 4BK/4BL, 8TI/8TK, etc.

### Écran "Charges déductibles"
- **6DD** : pensions alimentaires.
- **6NS / 6NT** : versements PER.

### Écran "Réductions et crédits d'impôt" (2042-RICI)
- **7DB** : emploi à domicile.
- **7GA/7GB/7GC/7GG** : garde enfants < 6 ans.
- **7EA/7EC/7EF** : scolarité collège/lycée/sup.
- **7UD / 7UF** : dons.
- **7AC / 7AE** : cotisations syndicales.
- **7QA à 7QR** : dispositifs Pinel.

### Écran "Divers" / "Éléments divers"
- **8UU** : compte à l'étranger (à cocher).
- **8HV / 8HW** : CEHR option lissage.
- **8TK / 8TI** : reports étrangers.

### Écran "Comptes à l'étranger" (annexe 3916)
- Un bloc 3916 par compte (ajout via "+ Ajouter un compte").

## Étape 3 — Récap et signer

- Vérifier le **total imposable** et l'**impôt calculé**.
- Ne pas signer avant d'avoir rapproché chaque case du `summary.md`.
- En cas d'écart : retour étape 2 via "Modifier".

## Correspondance "vue cases" vs "écrans guidés"

Sur impots.gouv.fr, bouton "Afficher les cases" en haut à droite : permet une vue
formulaire papier, utile pour suivre le `summary.md` case par case.

## Règles en phase 5

1. Lire `summary.md` dans l'ordre.
2. Pour chaque ligne → indiquer à l'utilisateur l'écran cible + case + valeur.
3. Attendre "ok c'est rempli".
4. Passer à la suivante.
5. Si la case n'existe pas à l'écran indiqué (mise à jour du site) → basculer en
   "vue cases" et chercher par code.
