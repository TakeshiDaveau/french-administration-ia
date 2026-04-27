<p align="center">
  <img src="docs/assets/banner.png" alt="French Admin IA — Your AI shortcut through French paperwork" />
</p>

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

### `pajemploi-declaration-fr`

Assistant à la déclaration mensuelle Pajemploi pour parents employeurs d'une
assistante maternelle agréée (convention IDCC 2395). Produit un fichier mensuel
prêt à recopier sur pajemploi.urssaf.fr, au format stable pour une éventuelle
automatisation navigateur ultérieure.

Artefacts persistants dans `./pajemploi/` :

- `contrat-<prénom-enfant>.md` — résumé du contrat (écrit une fois en mode A).
- `YYYY-MM.md` — déclaration du mois : tableau des 11 champs Pajemploi à
  saisir, hypothèses retenues, checklist pré-saisie.

**Workflow en 3 modes** : (A) setup contrat unique au premier passage, (B)
déclaration mensuelle récurrente, (C) pointeur attestation fiscale annuelle
(case 7GA).

**Couverture** : CDI mensualisé année complète, modes CP juin/prise/lissé, IE
avec prorata accueil < 9 h, heures complémentaires/majorées par semaine civile.
Refuse les cas R-01 à R-04 (fin de contrat, garde partagée, CDD particulier,
activité partielle de l'assmat) → renvoi conseiller Pajemploi.

**Catalogue** : 8 pièges pré-codés (mode CP, absences enfant/assmat, plancher
IE 85 % MG, HC vs HM, année incomplète, abrogation DFS 2024, lien case 7GA,
fin de contrat) + 4 cas de refus + 3 fixtures de test (cas nominal, HC/HM,
versement CP annuel).

**Installation** : déjà en place dans `skills/pajemploi-declaration-fr/`, ou
copier dans `~/.claude/skills/` pour usage global.

**Utilisation** : "aide-moi à préparer ma déclaration Pajemploi du mois".

**Conception** : [spec](docs/superpowers/specs/2026-04-24-skill-pajemploi-declaration-fr-design.md) · [plan](docs/superpowers/plans/2026-04-24-skill-pajemploi-declaration-fr.md).

## Licence

Voir [`LICENSE`](LICENSE).
