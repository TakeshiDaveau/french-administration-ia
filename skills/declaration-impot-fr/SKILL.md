---
name: declaration-impot-fr
description: Use when preparing a French income tax return (déclaration d'impôt sur le revenu) from personal documents (IFU, relevés bancaires, échéanciers de crédit, attestations de garde, situation familiale). The skill runs a 6-phase interactive workflow — initial questionnaire, document inventory, follow-up questionnaire, planning, adaptive step-by-step filling, pre-flight check — producing three persistent artifacts (plan, journal of decisions, summary of boxes to fill) with pitfall detection and escalation to official notices (impots.gouv.fr) on doubt.
---

# Déclaration d'impôt sur le revenu (France)

## Overview

Skill d'assistance à la préparation d'une déclaration d'impôt sur le revenu française.
Ne télédéclare pas ; produit trois artefacts (`plan.md`, `journal.md`, `summary.md`) que
l'utilisateur utilise ensuite pour saisir sur impots.gouv.fr.

**Principe directeur :** poser des questions plutôt que deviner, particulièrement sur
les arbitrages (régime micro vs réel, PFU vs barème, frais réels vs abattement,
traitement conventionnel d'un revenu étranger).

## When to Use

- Préparation de la déclaration annuelle de revenus en France (particulier).
- Situations courantes à modérément complexes : salaires, RCM, plus-values, revenus
  fonciers France/étranger, SCPI, comptes à l'étranger, crédits/réductions d'impôt.

**Ne pas utiliser pour :** cessions d'entreprise, plus-values pro complexes, régime
d'impatriation (art. 155B), fonctionnaires internationaux, doubles résidences,
successions/donations — renvoyer vers expert-comptable ou centre des finances publiques.

## Workflow en 6 phases

```
Phase 1 — Questionnaire initial      (charger references/initial-questionnaire.md)
Phase 2 — Inventaire documentaire    (charger references/document-types.md)
Phase 3 — Questionnaire complémentaire
Phase 4 — Planification              (charger references/forms-catalog.md + pitfalls.md)
                                     (copier templates/*.md dans le répertoire de travail)
Phase 5 — Exécution adaptative       (charger references/online-mapping.md)
Phase 6 — Pré-flight check
```

### Phase 1 — Questionnaire initial

Avant toute ouverture de documents. Charger `references/initial-questionnaire.md` et
parcourir les 7 blocs (situation foyer, changements, revenus attendus, logement/emprunts,
enfants/famille, autres réductions, méta). Une question à la fois.

Sortie : profil fiscal préliminaire résumé dans `journal.md` (phase 4).

### Phase 2 — Inventaire documentaire

Demander à l'utilisateur de fournir les documents (PDF, images, texte, captures).
Charger `references/document-types.md` pour savoir quoi chercher dans chaque type.
Comparer au profil phase 1 et signaler les manques probables.

### Phase 3 — Questionnaire complémentaire

Questions ciblées déclenchées par ce que les docs ont révélé. Une question à la fois.
Mode adaptatif : case par case quand doute.

### Phase 4 — Planification

1. Créer le répertoire de travail `./declaration-impot-<année>/` (par défaut à la
   racine du projet courant).
2. Copier les trois templates (`plan.md`, `journal.md`, `summary.md`) depuis
   `templates/` vers le répertoire de travail.
3. Charger `references/forms-catalog.md` et `references/pitfalls.md`.
4. Remplir `plan.md` (checklist ordonnée) et l'entête de `journal.md` (profil +
   documents + décisions déjà prises).
5. **Gate utilisateur :** présenter le plan et attendre "ok on y va" avant phase 5.

### Phase 5 — Exécution adaptative

Charger `references/online-mapping.md`. Parcourir `plan.md` dans l'ordre :

1. Annoncer la section.
2. Donner le bloc de cases (numéro + libellé + valeur).
3. Attendre "ok c'est rempli" de l'utilisateur avant de passer à la suivante.
4. Sur case à doute : case par case, WebFetch de la notice officielle (escalade),
   poser la question, mettre à jour `plan.md` + `journal.md` + `summary.md`.

### Phase 6 — Pré-flight check

Relecture globale. Règles de cohérence automatiques :

- Aucun `TODO` résiduel dans `plan.md`.
- Une seule option entre abattement 10 % et frais réels par déclarant.
- Reports corrects (8TK = somme des CI étrangers).
- CEHR si RFR prévisionnel > 250 000 € (célibataire) / 500 000 € (couple).
- 3916 par compte étranger identifié.
- Sommes redondantes cohérentes.

En cas d'incohérence : message explicite + décision utilisateur consignée.

## Trois niveaux de vigilance

| Niveau | Quand | Action |
|---|---|---|
| Standard | case évidente, doc clair, pas de piège | remplir + consigner source |
| Vigilant | piège détecté via `pitfalls.md` | poser question + options + conséquences, consigner décision |
| Escalade | doute persistant, cas non couvert | WebFetch notice officielle + reformuler ; si doute demeure, refus de conclure |

## Règles non négociables

- Toujours demander avant de deviner sur un arbitrage fiscal.
- Toujours consigner les décisions dans `journal.md` avec ID (`D-01`, `D-02`…).
- Toujours afficher le disclaimer IA en tête de `summary.md`.
- Refuser de conclure sur les cas hors périmètre (voir section "When to Use") et
  renvoyer vers un professionnel.
