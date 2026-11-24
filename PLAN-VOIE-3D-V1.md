# Plan de travail — Voie 3D (v1)

_Créé le 22 novembre 2026 (~20:30 EST), à la suite de la décision de l'atelier du même soir (~20:25 EST) : **la voie 3D devient la priorité nº 1 du projet**, devant le maillage Live2D. Chantier : backlog nº 8. Ce plan ordonne les étapes depuis l'état actuel jusqu'au **modèle passé à la porte stricte** — le jalon central. Il sépare nettement ce qui se fait **sans l'écran de l'atelier** (Blender headless, scripts, mesures) de ce qui **exige son écran et son œil** (sculpture, verdicts visuels)._

---

## 1. Point de départ (état réel au 22 novembre 2026)

- **Fiche modèle approuvée et finale** (21 nov. 2026) : hauteur totale **2,5 T** (T = crâne → menton, convention permanente), tête = **40 %** de la hauteur totale ; vues face / trois-quarts / profil / dos canoniques (`3d/model-sheet/`, spec `MODEL-SHEET-SPEC.md`).
- **Route de production décidée** : hybride, en interne — base de volume IA + **resculpt manuel dans Blender** ; pas de sous-traitance (backlog nº 8).
- **Brouillons Pixal3D jetables** : SV et MV **échouent à la porte stricte** (tête ~50 % de la hauteur au lieu de 40 %, pas de pincement de cou, couleurs ΔE 9–15) — résultat attendu pour des brouillons ; le **SV est retenu comme référence du test d'armature jetable** (dossier `3d/test-pixal3d-2026-11-22/`, rapport complet sur place).
- **Outil en place** : Blender **5.2.4 LTS** (portable) sur Fractal, `C:\Users\<utilisateur>\Blender\blender-5.2.4-windows-x64\blender.exe` ; aucun pilote GPU touché, travail headless en arrière-plan.
- **Porte stricte en vigueur** (décision finale du 22 nov.) : le modèle de production non riggé + matériau tissu doit passer le **test côte à côte** — **proportions ≤ 3 %, traits ≤ 2 %, couleurs de la charte** — **avant tout rig ou fourrure de production**. Seule exception autorisée : la **mini armature technique jetable** sur brouillon, pour valider la mécanique tôt.
- **Socle moteur-neutre** (cadrage jeux v2, `docs/HORIZON-JEUX-VIDEO-V1.md` § 5.3) : squelette humanoïde normalisé à **nommage figé** + chaînes supplémentaires **préfixées**, **UV unique**, cartes **PBR** avec **sources 4K**, exports **GLB/VRM/FBX régénérables**, **fichier de métadonnées** ; double mapping vers le mannequin UE5 testé par IK Retargeter ; FBX livraison de premier rang à presets figés. Posture moteur : fiche `docs/FICHE-DECISION-MOTEUR-V1.md` (option A recommandée — double filière ; **décision réservée à l'atelier, moment naturel = la porte stricte**).

## 2. Les étapes, dans l'ordre

### Étape 0 — Figer les conventions du socle · **SANS ÉCRAN**
- Table de nommage du squelette **gelée** : os humanoïdes aux noms **VRM** (`hips`, `spine`, `chest`, `upperChest`, `neck`, `head`, `leftShoulder`…`rightToes`) ; chaînes propres à la peluche **préfixées `onyx_`** (oreilles, toute chaîne non humanoïde future) ; suffixes de côté `.L` / `.R` réservés aux chaînes préfixées.
- Conventions écrites : UV unique par matériau de peau ; cartes PBR (albedo, normal, roughness, metallic, AO) + carte sheen/fuzz et masque SSS séparés ; sources de textures archivées en **4K sans compression destructive** ; gabarit du **fichier de métadonnées** (version, mesures de porte, presets d'export, table de correspondance UE5).
- **Produit** : la table et les conventions vivent dans ce plan (§ 5) et s'appliqueront au modèle réel ; le test d'armature de l'étape 1 les éprouve sur le brouillon.

### Étape 1 — Test d'armature jetable sur le brouillon Pixal3D SV · **SANS ÉCRAN**
- Poser l'**armature humanoïde normalisée** (noms de l'étape 0) sur `brouillon-sv.glb`, en **Blender headless sur Fractal** ; skinning automatique ; **tests de déformation de base** (tête, bras, tronc, jambe) ; **planches témoins chiffrées** (rendus + mesures).
- **But** : valider la **mécanique** (le squelette normalisé se pose, se déforme, s'exporte) — **pas** la fidélité : le brouillon reste un brouillon, il se jette après le test.
- **État : voir § 6** (exécuté le 22 nov. 2026 au soir, ou prérequis manquant constaté là).

### Étape 2 — Gabarits de mesure de la porte · **SANS ÉCRAN**
- Chaîne de rendus orthographiques reproductible (face / profil / dos, échelle en T) + script de mesures : proportions et traits contre la spec, couleurs échantillonnées contre la charte (ΔE), dans le même esprit que la méthode déjà validée sur la référence par le test Pixal3D.
- **Produit** : le gabarit qui servira à **chaque** passe de resculpt et au test de la porte — mêmes mesures, mêmes seuils, aucun jugement « à l'œil » non chiffré.

### Étape 3 — Base de volume de production · **MIXTE**
- Préparation sans écran : références montées, gabarits appliqués au brouillon le plus proche, mesures d'écart détaillées (où le volume doit bouger : pincement de cou à créer, tête à ramener de ~50 % à 40 %, jambes à épaissir).
- Le geste de sculpture lui-même : **ÉCRAN** (voir étape 4).

### Étape 4 — Resculpt manuel itératif · **ÉCRAN (blocs prêtés) + verdicts de l'atelier**
- Sessions Blender sur Fractal, par blocs d'écran prêtés, sur **copie de travail versionnée** ; à chaque passe : planches côte à côte chiffrées par le gabarit de l'étape 2 ; **l'atelier valide par planches groupées**, comme pour les autres chantiers visuels.
- Cibles, dans l'ordre : proportions d'abord (tête 40 %, pincement de cou, entraxe), traits ensuite (yeux, museau, sourire, couture), couleurs enfin (matériau tissu aux oranges de la charte).

### Étape 5 — LA PORTE STRICTE · **JALON CENTRAL — verdict de l'atelier**
- Test côte à côte formel du modèle **non riggé + matériau tissu** contre la référence canonique : **proportions ≤ 3 %, traits ≤ 2 %, couleurs de la charte** (ΔE dans la tolérance de la porte).
- **Verdict PASS/FAIL consigné** (journal des décisions + backlog nº 8). En cas d'échec : retour à l'étape 4, la porte ne se desserre pas.
- **Interdit tant que la porte n'est pas passée** : tout rig, toute fourrure, tout matériau de production sur le modèle réel.

### Étape 6 — Après la porte seulement : finition socle · **MIXTE**
- Matériaux PBR complets (+ sheen/fuzz, masque SSS) ; **rig de production** sur le squelette normalisé figé ; morphs aux noms compatibles UE ; exports **GLB / FBX / VRM régénérables** par script ; fichier de métadonnées rempli ; **test de validation UE** (import dans un projet UE5 nu, retarget d'une animation de référence, matériau Cloth de base, capture archivée) — c'est aussi le moment naturel de la **décision moteur** (fiche prête).
- Fourrure et couches de destination (groom, cloth simulé) : après, jamais comme définition du personnage.

## 3. Ce qui se fait sans écran / ce qui exige l'écran — résumé

| Sans l'écran de l'atelier | Exige son écran (et souvent son œil) |
|---|---|
| Étape 0 (conventions), étape 1 (armature jetable), étape 2 (gabarits de mesure), préparation de l'étape 3, exports et tests de l'étape 6 | Étapes 4 (resculpt) et 5 (porte : verdict), sessions Blender interactives |

## 4. Règles permanentes de la voie

- **Porte stricte intouchable** : aucun rig ni fourrure de production avant le PASS ; les seuils (≤ 3 % / ≤ 2 % / charte) ne se négocient pas en cours de route.
- **Le brouillon reste jetable** : aucun test sur brouillon ne devient le modèle final ; le modèle réel ne reçoit ni squelette ni fourrure avant la porte.
- **Socle moteur-neutre d'abord** : aucun actif du personnage dans un format propriétaire unique ; les exports se régénèrent depuis le master Blender.
- **Travail en arrière-plan** sur Fractal (headless, aucun vol de focus) ; l'écran ne se prend que sur blocs prêtés, comme les autres chantiers.

## 5. Table de nommage du squelette (gelée à l'étape 0)

Humanoïde (noms VRM) : `hips`, `spine`, `chest`, `upperChest`, `neck`, `head`, `leftShoulder`, `leftUpperArm`, `leftLowerArm`, `leftHand`, `rightShoulder`, `rightUpperArm`, `rightLowerArm`, `rightHand`, `leftUpperLeg`, `leftLowerLeg`, `leftFoot`, `leftToes`, `rightUpperLeg`, `rightLowerLeg`, `rightFoot`, `rightToes`.
Chaînes peluche préfixées : `onyx_ear.L`, `onyx_ear.R` (oreilles) — toute chaîne non humanoïde future suit le même préfixe `onyx_`.

## 6. État d'exécution

- **Étape 1 — test d'armature jetable : voir l'entrée du 22 novembre 2026 au § 6.1 ci-dessous** (mise à jour après exécution).
- **CLÔTURE — voir l'entrée du 24 novembre 2026 au § 6.1 : la modélisation est terminée et validée (planche 19, porte finale PASS).**

### 6.1 Journal d'exécution

_(Entrées datées ajoutées au fil de l'eau.)_

- **22 novembre 2026 (~20:33–20:40 EST) — Étape 1 (test d'armature jetable) EXÉCUTÉE : PASS mécanique.** Blender 5.2.4 headless sur Fractal, script `3d/test-armature-2026-11-22/test_armature.py` (v2), sur le seul brouillon Pixal3D SV (393 512 sommets, 699 664 triangles ; visage détecté vers −Y, confirmé visuellement). Armature de **24 os au nommage figé du § 5** construite par script. **Skinning : le bone heat ÉCHOUE totalement sur le mesh brut Pixal3D** (100 % de sommets sans poids, armature complète comme réduite — échec silencieux de Blender, démasqué par la mesure des poids ; leçon consignée au rapport) ; **les enveloppes (distance 0,40H, armature réduite à 20 os) réussissent : 0 sommet sans poids**. Déformations mesurées sur 4 poses (tête inclinée : max 0,142 ; tronc penché : max 0,198 et 97,9 % de sommets bougés ; bras levé et pas de jambe faibles — qualité d'enveloppe grossière, attendue sur un brouillon : la mécanique et le nommage sont validés, pas la qualité d'animation). Export `armature-test.glb` skinné produit ; planches témoins numérotées `planche-1-neutre-quatre-vues.png` et `planche-2-poses.png`. **Rapport complet : `3d/test-armature-2026-11-22/RAPPORT.md`.** Le modèle réel n'a pas été touché ; la porte stricte (étape 5) reste le jalon central. C: de Fractal revérifié après le test : 159,88 Go libres.
- **24 novembre 2026 — CLÔTURE DU CHANTIER : modélisation TERMINÉE et validée.** Le modèle final assemblé (géométrie finale, retopologie et UV figées, jambes séparées, rig de 24 os au nommage du § 5, visage en matériau séparé — museau distinct, bouche brodée animable à trois clés de forme — et fourrure hybride de **236 579 brins**) a passé la **porte finale** : intégrité du fichier maître (empreinte géométrie + UV identique à la version validée du visage, 13 actions intactes), proportions aux repères de la feuille dans les seuils (hauteur totale −0,13 %, largeur de tête −0,79 %), traits ≤ 2 %, couleurs de la charte exactes, composante « poil » stable en marche (DINOv2 à phase égale 0,990). La planche de clôture (planche 19) a été **validée par l'opérateur** le 24 novembre 2026 ; le chantier de modélisation (backlog nº 64) est clos. Exports neutres **GLB (4,8 Mo) et FBX (6,3 Mo) vérifiés par réimport et parsage** (10 objets, 24 os, 13 animations, clés de bouche) ; visionneuse web livrée (rotation au doigt/à la souris, boutons Repos / Marche / Course) ; copies du maître et des exports déposées chez l'opérateur, hachages vérifiés des deux côtés. Exclusion documentée : les brins ne voyagent dans aucun export (limite des formats) — le groom reste dans le fichier maître et la fourrure temps réel fera l'objet d'une conversion dédiée. Restent ouverts, hors de ce chantier : la décision moteur, le chantier animation et la reprise des poses de présentation.
