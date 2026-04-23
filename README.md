# french-administration-ia

Skills Claude Code pour les démarches administratives françaises.

## Skills

### `declaration-impot-fr`

Assistant interactif pour préparer la déclaration d'impôt sur le revenu à partir
de documents perso (IFU, bulletins de paie, relevés, échéanciers, attestations…).
Produit trois artefacts persistants dans `./declaration-impot-<année>/` :

- `plan.md` — checklist des cases à remplir.
- `journal.md` — décisions fiscales prises avec justification.
- `summary.md` — tableau final des cases pour recopie sur impots.gouv.fr.

**Workflow en 6 phases** : questionnaire initial → inventaire docs → questions
complémentaires → plan → remplissage interactif case par case → pré-flight check.

**Couverture** : salaires, RCM, plus-values, revenus fonciers France/étranger,
SCPI, comptes étrangers, crédits/réductions usuels. Refuse les cas complexes
(impatriation 155B, cession d'entreprise, non-résident, montages) → renvoi expert.

**Catalogue** : 12 pièges pré-codés (SCPI étrangère, PFU vs barème, LMNP, 3916…)
+ 7 cas de refus + CERFA courants + mapping cases ↔ écrans impots.gouv.fr.

**Installation** : déjà en place dans `skills/declaration-impot-fr/`, ou copier
dans `~/.claude/skills/` pour usage global.

**Utilisation** : "aide-moi à préparer ma déclaration d'impôt".

**Conception** : [spec](docs/superpowers/specs/2026-04-23-skill-declaration-impot-fr-design.md) · [plan](docs/superpowers/plans/2026-04-23-skill-declaration-impot-fr.md).

## Licence

Voir [`LICENSE`](LICENSE).
