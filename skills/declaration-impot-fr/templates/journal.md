# Déclaration <ANNÉE> — Journal

**Déclarant(s)** : <nom(s)>
**Foyer fiscal** : <composition>
**Campagne** : <année N+1>
**Démarré** : <YYYY-MM-DD>

---

## Profil fiscal préliminaire (phase 1)

### Situation du foyer
- <résumé bloc A>

### Changements en N
- <résumé bloc B>

### Revenus et patrimoine attendus
- <résumé bloc C>

### Logement et emprunts
- <résumé bloc D>

### Enfants et famille
- <résumé bloc E>

### Autres réductions/crédits
- <résumé bloc F>

### Méta
- <résumé bloc G>

---

## Documents (phase 2)

### Fournis
- `<fichier>` — reçu <YYYY-MM-DD> — <type> (IFU / relevé / échéancier / attestation…)

### Extraits / saisis manuellement
- <résumé par doc : cases clés extraites ou saisies>

### Manquants (à demander à l'utilisateur)
- <document attendu mais non fourni>

---

## Décisions

### D-01 — <titre court>
**Date** : <YYYY-MM-DD>
**Piège rattaché** : P-NN (ou aucun)
**Contexte** : <explication situation>
**Options envisagées** : <option A / option B>
**Choix** : <option retenue>
**Justification** : <pourquoi>
**Source** : <notice CERFA / BOI / WebFetch impots.gouv.fr du YYYY-MM-DD>
**Impact sur cases** : <liste cases + formulaires impactés>

### D-02 — …

---

## Pré-flight check (phase 6)

À remplir lors du check final. Pour chaque règle : OK / écart détecté.

- [ ] Aucun `TODO` résiduel dans `plan.md`.
- [ ] Abattement 10 % OU frais réels choisi une seule fois par déclarant.
- [ ] Reports étrangers corrects (8TK = somme CI 2044 + 2047).
- [ ] CEHR vérifiée si RFR > seuil.
- [ ] 3916 présent pour chaque compte étranger identifié.
- [ ] Sommes redondantes cohérentes (1AJ ≈ IFU employeur).
- [ ] Cases à cocher critiques non oubliées (2OP, 8UU, 7UF, 7UD…).
