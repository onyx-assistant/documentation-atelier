# Spécification des couches — modèle Onyx (Phase 1)

Source : `assets/onyx-reference.png` (1600 × 1600, PNG sans perte, tiré de la référence canonique).
Base de travail : `layers/onyx-alpha.png` — détourage alpha du personnage (fait le 21 nov. 2026, sans installation : flood-fill du fond blanc + rampe anti-frange). Contrôle visuel fait (`layers/check-sur-fonce.jpg`, zooms 200 %) : silhouette complète et propre ; il reste une fine frange claire (1–3 px, les fibres claires du bord de la peluche) — acceptable comme base : chaque partie sera de toute façon prolongée/repeinte sous ses voisines pour le rigging (voir « prolongements »).

## Parties à séparer (de l'arrière vers l'avant)

1. **Torse** — corps complet avec couture centrale ; sert d'ancre basse.
2. **Bras gauche / Bras droit** — séparés du torse pour un léger balancement indépendant.
3. **Oreille gauche / Oreille droite** — derrière le haut du crâne ; physique douce prévue.
4. **Tête (base)** — crâne et joues, SANS les traits (les zones sous les yeux, le museau et la bouche sont pleines).
5. **Museau** — la zone centrale saillante (son ombre douce le distingue des joues).
6. **Bouche fermée** — le sourire (trait sombre) sur sa propre couche.
7. **Bouche ouverte** — à créer : intérieur de bouche dessiné à la main dans le style peluche (cavité sombre chaude, langue), + lèvre inférieure ; la mâchoire basse de la tête doit exister en dessous.
8. **Œil gauche / Œil droit** — les ovales noirs brodés.
9. **Paupières fermées G/D** — variantes clignement (courbes douces, déjà prototypées en 2D le 21 nov.).
10. **Sourcil gauche / Sourcil droit** — les arcs brodés.

## Prolongements à peindre (zones cachées à compléter)

- Sous la tête : un « cou »/haut de torse prolongé (la tête pivote sans trou).
- Sous les oreilles : le haut du crâne complété en plein.
- Sous les yeux et le museau : la fourrure des joues prolongée (regard et expressions sans couture visible).
- Sous la bouche : le menton/mâchoire complété pour l'ouverture.
Règle fidélité : partout où l'original est visible à plat, on garde SES pixels (reprojection) ; la peinture ne sert que là où l'original ne montre rien.

## Paramètres de rig visés (Phase 2)

Tête Angle X / Y / Z · Œil ouverture G/D · Regard X/Y · Sourcil montée G/D · Bouche ouverture · Bouche forme (sourire / « o » / « e ») · Corps balancement X/Z · Physique : oreilles, léger rebond de peluche.

## Reste à faire en Phase 1 (avec installations, après son go)

- Séparation fine des parties (See-through en première passe + retouches main), prolongements peints, intérieur de bouche dessiné, test de recomposition à plat = identique à l'original (contrôle 200 %).
- Question 4K : test comparatif à valider par l'atelier (la source fait 1600 × 1600).
