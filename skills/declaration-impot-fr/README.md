# Skill declaration-impot-fr

Assistant de préparation de déclaration d'impôt sur le revenu (France) pour particuliers.

## Installation

Copier le dossier `skills/declaration-impot-fr/` dans ton répertoire skills Claude
Code (`~/.claude/skills/` pour un skill personnel, ou `.claude/skills/` pour un
skill projet).

## Utilisation

Depuis Claude Code, lancer :

> "Aide-moi à préparer ma déclaration d'impôt 2025"

ou explicitement :

> "Invoque le skill declaration-impot-fr"

Le skill te guidera en 6 phases : questionnaire initial → inventaire des docs →
questionnaire complémentaire → plan → remplissage interactif → pré-flight check.

Trois artefacts sont produits dans `./declaration-impot-<année>/` :

- `plan.md` — checklist progressive des cases à remplir.
- `journal.md` — traçabilité des décisions fiscales (avec ID `D-NN`).
- `summary.md` — tableau final consolidé (format stable pour consommation future
  par un skill d'automatisation navigateur).

## Limitations

Le skill refuse de traiter :

- cessions d'entreprise / plus-values pro complexes,
- régime d'impatriation art. 155B,
- fonctionnaires internationaux,
- doubles résidences / conflits conventionnels,
- montages complexes (holdings patrimoniales, pactes Dutreil…).

Pour ces cas, consulter un expert-comptable.

## Structure

```
skills/declaration-impot-fr/
├── SKILL.md                            # point d'entrée (workflow 6 phases)
├── references/
│   ├── initial-questionnaire.md        # phase 1
│   ├── document-types.md               # phase 2
│   ├── forms-catalog.md                # phase 4
│   ├── pitfalls.md                     # pièges P-01..P-12 + refus R-01..R-07
│   └── online-mapping.md               # phase 5 (cases ↔ écrans impots.gouv.fr)
├── templates/
│   ├── plan.md
│   ├── journal.md
│   └── summary.md
└── tests/
    └── fixtures/
        ├── fixture-01-simple/          # salarié célibataire, PEA
        ├── fixture-02-mixte/           # couple + SCPI étrangère + crèche + don
        └── fixture-03-complexe/        # impat 155B refus + LMNP + crypto + IBKR
```

## Contribuer / itérer

- Fixtures de test dans `tests/fixtures/`.
- Pièges à ajouter dans `references/pitfalls.md` (convention `P-NN`).
- Cas de refus à ajouter dans `references/pitfalls.md` section "Cas
  déclenchant un REFUS" (convention `R-NN`).

Mise à jour annuelle recommandée (seuils, plafonds, nouveaux dispositifs).

## État actuel (v1)

- Squelette de workflow, 5 fichiers de référence, 3 templates, 3 fixtures.
- Tests RED/GREEN avec subagents sur fixtures : non encore exécutés (itération
  prévue).
- Génération d'un skill compagnon qui pilote le navigateur sur impots.gouv.fr :
  prévue, non développée.

## Documents de conception

- Spec : `docs/superpowers/specs/2026-04-23-skill-declaration-impot-fr-design.md`
- Plan d'implémentation : `docs/superpowers/plans/2026-04-23-skill-declaration-impot-fr.md`
