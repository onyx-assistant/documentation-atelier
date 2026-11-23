# Guide Blender v1 — le resculpt 3D d'Onyx, de l'import du brouillon à la porte stricte

_Créé le 22 novembre 2026. Guide d'expertise du programme d'approfondissement (backlog nº 27, priorité nº 4). **Statut : PRÉPARATION seulement — le chantier 3D n'est pas ouvert.** Ce guide est écrit à froid, avant le premier sculpt, pour que le jour du démarrage on exécute au lieu de réfléchir. Il ne remplace ni `docs/PLAN-3D.md` (le plan du volet) ni la feuille de modèle (`3d/model-sheet/MODEL-SHEET-SPEC.md`, approuvée) : il les traduit en procédures Blender concrètes. Chaque valeur est soit **éprouvée** sur le projet (◆), soit tirée de la documentation Blender et des pratiques établies (○), soit une **recommandation de départ à valider en session** (◇)._

**Le contexte, en une page.** Blender **5.2.4 LTS**, édition portable officielle, installé et vérifié sur Fractal ◆ — rien d'autre n'est installé pour la 3D (Unreal Engine 5.8 viendra à l'ouverture du chantier, pour les rendus ; Warudo est le moteur live 3D futur, hors du périmètre de ce guide). La voie 3D du projet est la **route hybride** ◆ : une base volumique IA sert d'armature de départ, tout ce qui se voit à l'écran est **resculpté à la main dans Blender** contre la feuille de modèle. Critère nº 1, identique partout dans le projet : **FIDÉLITÉ** à la référence canonique (`references/onyx-canonique.webp`, MD5 `2615852ff83d36e6468342cfbd5c8a8b8`). Décision finale de l'atelier (22 nov. 2026) : **« porte stricte + mini armature de test jetable »** — aucun rig ni aucune fourrure sur le vrai modèle avant le passage de la porte stricte (§ 6), et un seul test mécanique jetable autorisé avant (§ 7). Production entièrement interne : pas de sous-traitance.

**Les sources qui font foi, par ordre :**
1. La référence canonique 2D (face) — intangible.
2. La feuille de modèle 3D approuvée le 21 nov. 2026 (pack canonique 3D v1 dans `00_canonical/3d-v1/`, manifeste SHA-256) — pour tout ce que la face ne montre pas (profil, dos, bas du corps).
3. La charte graphique — pour les couleurs : orange lumière `#FC8B32`, médian `#C95D1D`, ombre `#8D2E0F`, noir chaud des traits `#1A1512`.

---

## 1. Configuration et conventions du projet dans Blender

### 1.1 Unités et échelle

- Système d'unités : **Metric**, Unit Scale **1.0** ○. On travaille en « mètres Blender », avec la convention projet suivante ◇ : **1 T = 1,00 unité**. Le personnage mesure donc **2,50 unités** de haut (sol → sommet du crâne 2,42 ; sommet des oreilles 2,51 — voir § 2). Cette convention rend toutes les mesures de la feuille de modèle directement lisibles dans le panneau Transform (une largeur de tête de 1,17 T = 1,17 unité).
- **Vérification d'échelle à chaque import** (voir piège § 9.1) : après import d'un GLB, lire la dimension Z de l'objet ; elle doit être ramenée à la hauteur attendue du brouillon mesuré en T, jamais acceptée telle quelle.
- Avant tout export (FBX vers Unreal, Alembic de groom plus tard) : **appliquer rotation et échelle** (`Ctrl+A` → Rotation & Scale) ○ et vérifier que l'objet est à l'échelle 1,00 sur les trois axes. Côté Unreal, un personnage mis à l'échelle fausse des réglages physiques documentés (rayon des brins en Path Tracer, cf. `PLAN-3D.md` § 3.1) — l'échelle propre se règle ici, dans Blender, pas là-bas.
- Origine du personnage : **au sol, entre les pieds, sur l'axe de symétrie** ◇. Le plan de symétrie est le plan YZ (le personnage regarde vers −Y ou +Y — choisir une fois, le noter dans le fichier, ne plus changer ◇). La couture centrale vit sur ce plan.

### 1.2 Nommage

- Fichiers de travail : `onyx-3d-v<N>.blend`, incrément à chaque sauvegarde significative — **jamais d'écrasement d'une version antérieure** (même discipline que le rig 2D : v1/v2/v3 intouchables) ◇.
- Objets : `onyx_corps`, `onyx_tete` (si séparée), `onyx_oeil_g`, `onyx_oeil_d`, `onyx_sourcil_g`, `onyx_sourcil_d`, `onyx_bouche_repos`, `onyx_oreille_g`, `onyx_oreille_d`, `onyx_couture` ◇. Les pièces du visage sont des **objets séparés** dès le départ (§ 5.3) — jamais fusionnées au corps.
- Collections : `00_REFERENCE` (images de fond et planches, verrouillée en écriture par convention), `01_BASE_IA` (le brouillon importé, conservé intact pour comparaison), `02_SCULPT` (le travail), `03_TRAITS` (yeux, sourcils, bouche, coutures), `99_MESURES` (planes et repères de contrôle) ◇.

### 1.3 Dossiers versionnés — la règle des dossiers s'applique à la 3D

- `00_canonical/` : **lecture seule, pour toujours**. On n'y travaille jamais directement ; on y *dépose* une copie validée, avec manifeste. Le pack 3D v1 (feuille de modèle) y est déjà.
- `01_source/` : les brouillons IA bruts tels que générés (GLB Pixal3D SV/MV y ont leur place de référence, dossier `3d/test-pixal3d-2026-11-22/` ◆).
- `04_wip/` : tout le travail Blender en cours (fichiers `.blend`, rendus de contrôle datés).
- `03_approved/` : uniquement ce que l'atelier a validé à une porte.
- Rendus de validation : archivés **datés** dans le dossier de travail (`rendus-porte-AAAA-MM-JJ/`) — ils servent de preuve et de point de comparaison entre boucles de correction ◆ (pratique déjà en vigueur côté 2D).

---

## 2. Les mesures de référence (feuille de modèle approuvée ◆)

Convention permanente ◆ : **T = hauteur de tête, du sommet du crâne au menton** (= 803 px sur la référence). Toutes les mesures ci-dessous sont prises sur la référence elle-même et **priment** sur les orientations initiales du plan.

| Repère | Valeur |
|---|---|
| Hauteur totale (sol → crâne) | **2,42 T** ; sol → sommet des oreilles **2,51 T** ; tête = **40 %** de la hauteur totale |
| Décomposition | pieds + jambes **0,55 T** · torse sous le menton **0,87 T** · tête **1,00 T** · oreilles **+0,09 T** |
| Largeur max. de la tête (LT) | **1,17 T**, atteinte à ~0,58 T sous le crâne |
| Cou / pincement tête-torse | largeur min. **0,62 LT** — **pas de cou** : la tête fusionne au torse par un pincement doux |
| Yeux | ovales, rapport hauteur/largeur **≈ 1,6** ; hauteur ≈ 0,14 LT ; centres à **0,44 T** sous le crâne |
| Entraxe des yeux | **0,34 LT** |
| Sourcils | longueur ≈ 0,11 LT ; centres à 0,23 T sous le crâne |
| Museau | zone douce centrée ≈ (0,50 LT, 0,61 T) ; **profondeur : celle du profil retenu** (plutôt profond) ◆ tranchée à la porte nº 1 |
| Bouche (sourire de repos) | largeur **0,40 LT** ; point bas de l'arc à 0,74 T sous le crâne ; c'est une **ligne de surface**, pas une cavité |
| Oreilles | disques ronds épais, largeur ≈ 0,19 LT chacune ; bases à ~0,08 T sous le crâne ; asymétrie réelle de la référence ≈ 10 px (on vise une symétrie propre, esprit peluche conservé) |
| Bas du corps (inventé, approuvé) | ventre rond continu, couture centrale menton → bas du ventre → entre les jambes → dos ; jambes courtes et rondes ; pieds ronds ovales **sans doigts, coussinets ni semelle** ; mains en moufles **sans doigts** ; **aucune queue, aucun vêtement** |

Interdits de design, partout et toujours ◆ : cornes, crocs, nez dur ou narines, doigts/orteils dessinés, bouche ouverte par défaut.

---

## 3. Le flux de la route hybride, étape par étape

### Étape 0 — Préparer la scène de travail ◇

1. Nouveau fichier depuis le modèle de démarrage du projet (unités § 1.1, collections § 1.2).
2. Charger les planches de la feuille de modèle (`planche-buste.png`, `planche-corps-entier.png`, dans `00_canonical/3d-v1/`) comme **images de référence** (Reference Images) alignées sur les vues face / profil / dos, à l'échelle 2,5 T, dans `00_REFERENCE`.
3. Placer les **planes de mesure** dans `99_MESURES` : ligne de sol, ligne du crâne (2,42 T), ligne du menton (1,42 T), gabarits de largeur de tête (1,17 T) et d'entraxe (0,34 LT). Ces repères restent visibles en filaire pendant tout le sculpt.

### Étape 1 — Import du brouillon IA (GLB)

1. `File → Import → glTF 2.0` ○. Le brouillon arrive dans `01_BASE_IA`, **copie intacte conservée** : tout le nettoyage se fait sur un duplicata placé dans `02_SCULPT`.
2. **Contrôle d'échelle immédiat** (§ 9.1) : mesurer la hauteur réelle, la ramener à sa hauteur en T, appliquer l'échelle.
3. **Audit du brouillon** (à consigner dans le journal de session) : nombre de triangles (les brouillons Pixal3D font ~700 000 triangles ◆ — inutilisables tels quels pour un modèle propre, c'est attendu), matériaux présents, nature des textures (voir § 9.2 : presque toujours une **texture photo cuite**, pas un vrai jeu de cartes PBR).
4. Mesurer le brouillon contre § 2 dès l'import (hauteur de tête, entraxe, pincement de cou) : on sait ainsi, chiffres à l'appui, ce que le resculpt doit corriger. Pour mémoire, les écarts Pixal3D mesurés ◆ : tête ≈ 50 % de la hauteur au lieu de 40 % (+25 %), aucun pincement de cou, entraxe −4 à −11 %, couleurs décalées vers le jaune (§ 9.3).

### Étape 2 — Nettoyage

1. Supprimer tout ce qui n'est pas le personnage (socles, fonds, artefacts de génération) ○.
2. Fusionner les fragments épars du corps en un seul maillage de départ ; recalculer les normales vers l'extérieur (`Shift+N`) ○ ; supprimer les doublons de sommets (`Merge by Distance`) ○.
3. Vérifier la géométrie non-manifold (trous, arêtes internes) — un brouillon IA en contient presque toujours ◇ ; on bouche ce qui gêne le sculpt, sans chercher la perfection : la retopologie (§ 4) repartira d'une surface propre.

### Étape 3 — Resculpt contre la feuille de modèle

C'est l'étape où la fidélité se gagne ◆ (estimation du plan : c'est le gros du temps du volet).

1. Travailler en **Sculpt Mode** sur le duplicata, symétrie X **activée** ○ (le personnage est symétrique par design ; l'asymétrie de peluche viendra, si elle vient, à la toute fin et à la main).
2. Ordre de correction ◇ : **(a)** proportions globales d'abord (hauteur de tête à 40 %, pincement du cou à 0,62 LT, ventre et jambes selon la décomposition § 2) — au Grab et au Pose brush, contre les planes de mesure ; **(b)** volumes du visage ensuite (museau à la profondeur du profil retenu, joues) ; **(c)** détails en dernier (oreilles en disques épais, mains moufles, pieds ronds).
3. Contrôle permanent : basculer entre la vue du sculpt et les planches de référence ; rendre des captures de contrôle datées à chaque palier (face, profil, dos, 3/4).
4. La bouche, les yeux et les sourcils **ne se sculptent pas dans le corps** : ce sont des pièces séparées (§ 5.3). Le sculpt ne porte que le volume qui les reçoit (léger relief du museau, surface nette autour des yeux).

### Étape 4 — Retopologie pour une peluche

Objectif ◇ : un maillage propre, régulier, de densité modérée — pensé pour porter un matériau tissu et, **après la porte seulement**, un fuzz de silhouette et une déformation douce. Pas de cible de polycount de jeu vidéo : la destination est le rendu (Unreal) et le live futur (Warudo), pas un moteur de jeu temps réel contraint.

1. Retopologie manuelle ou semi-assistée (Quad Remesher / RetopoFlow ou outils natifs — choix à valider en session ◇) par-dessus le sculpt validé, le sculpt restant la référence de forme (Shrinkwrap + Multiresolution ou projection équivalente ○).
2. Règles de topologie ◇ : boucles d'arêtes qui suivent les volumes (contour des yeux, base des oreilles, pincement du cou, couture centrale) ; densité renforcée autour des traits et des zones de future déformation douce (oreilles, ventre) ; densité faible et régulière ailleurs ; aucun triangle visible dans les zones lisses ; symétrie exacte gauche/droite.
3. UVs propres ◇ : coutures d'UV placées là où la peluche a ses vraies coutures (plan de symétrie, dessous des bras, entre-jambes) — les coutures de texture deviennent invisibles parce qu'elles coïncident avec les coutures du personnage ; îles sans étirement sur le visage (contrôle par le damier).

### Étape 5 — Traits brodés, en pièces séparées ◆

Décision du plan, non négociable : le visage d'Onyx est une **broderie posée sur la peluche**, et se construit comme tel.

- **Yeux** : géométrie légèrement bombée posée sur la surface (relief ~1–2 mm à l'échelle du personnage ○), matériau noir chaud `#1A1512` quasi mat, micro-relief « fil brodé » ; aucun blanc, aucun reflet d'œil de personnage 3D classique.
- **Sourcils et bouche** : courbes fines en léger relief suivant la surface (tubes aplatis) ou texture haute résolution + normal map ○. La bouche de repos est le sourire de la référence (0,40 LT) ; les variantes d'expression (sourire large, bouche « o », yeux fermés) seront des **variantes de pièces / shape keys validées sur la feuille de modèle** — après la porte, au moment du rig (§ 8).
- **Coutures** : la couture centrale est **une seule ligne continue** ◆ qui fait le tour du personnage par le plan de symétrie (dessus de tête → face → ventre → entre-jambes → dos → arrière de tête). Rendu : albedo légèrement assombri + normal en creux + fil plus clair au centre ○, doublé au besoin d'un très léger creux géométrique.

### Étape 6 — Matériau tissu PBR (la matière d'abord)

Voir § 5 ci-dessous — c'est un livrable à part entière, validé avant la porte.

### Étape 7 — Éclairage de contrôle dans Blender

Avant tout passage dans Unreal, on itère dans Blender avec une scène de contrôle fixe ◇ :

- Fond blanc pur, lumière clé douce d'en haut à gauche (comme la référence), remplissage neutre ; **exposition et réglages figés et notés dans le fichier** — mêmes réglages = même écart partout, c'est ce qui rend les mesures de couleur comparables d'une boucle à l'autre ◆ (principe du § 5 du plan).
- Caméra de contrôle : focale et distance reproduisant le cadrage de la référence (tête entière + oreilles + haut du torse, pas de cou visible) ; vues systématiques : face, 3/4 gauche/droite, profils, dos, dessus.
- Ces rendus servent aux **pré-contrôles** de la porte (§ 6). Le rendu officiel de la porte couperet, lui, se fait dans **Unreal** (scène « Studio Onyx »), selon `PLAN-3D.md` § 5.1 — Blender prépare et pré-valide, Unreal juge.

### Étape 8 — Export vers Unreal (à l'ouverture du chantier seulement)

FBX avec morph targets (plus tard, pour les expressions), transforms appliqués (§ 1.1), échelle vérifiée à l'import dans Unreal avant tout réglage ◇. Aucun export n'a de sens avant le sculpt validé à la porte 2 (silhouette).

---

## 4. Retopologie — rappels propres à une peluche

- Une peluche n'a **ni articulations franches ni muscles** : la topologie suit des volumes doux et fermés. Le danger n'est pas l'anatomie, c'est le **pincement** (pinching) des ombrages sur les courbes tendues — contour de tête, museau, ventre. Densité régulière et boucles qui suivent la courbure sont la parade ○.
- Le **pincement du cou** (0,62 LT) est la zone la plus fragile : c'est là que la tête fusionne au torse, là que les ombres se lisent en premier sur la référence. Boucles concentriques propres, pas de pôle au milieu de la zone visible ◇.
- Prévoir dès la retopo les **zones de fuzz** (§ 5.2) : la silhouette tête/oreilles/torse doit rester lisible même avec le halo de fibres — éviter les micro-détails géométriques au contour, ils se perdraient sous le fuzz ◇.
- Garder le sculpt haute résolution **intact et séparé** : il sert de source pour les normal maps et pour toute reprise de forme. Le maillage retopologisé ne devient jamais la seule copie de la forme ◆ (discipline des dossiers, § 1.3).

---

## 5. Matériaux — le tissu d'abord, la fourrure en silhouette seulement

### 5.1 Matériau tissu PBR (couche principale, obligatoire) ◆

Principe directeur du plan : pour une peluche à poil court (type minky), le look fidèle est **d'abord un problème de matériau**, ensuite seulement un problème de poils.

Dans Blender (Principled BSDF) ○ :

- **Base Color** : les hex de la charte convertis sRGB → linéaire par Blender (renseigner les hex tels quels dans le sélecteur sRGB ; Blender convertit). Trois zones échantillonnées sur la référence servent de cibles de contrôle : front éclairé `#F4741E`, joue en mi-ombre `#AA4716`, torse `#D35B17` — ce sont des **valeurs de rendu sous éclairage de contrôle**, pas des valeurs d'albedo ; l'albedo se règle pour que le rendu retombe dessus ◆.
- **Roughness** élevée (≈ 0,9) ○ ; **Specular** faible.
- **Sheen** : activé (le modèle tissu/velours du Principled) — c'est lui qui produit le halo clair au contour, signature de la peluche ○.
- **Grain de peluche** : cartes de normal fines « grain » + carte de direction du poil (sens de couchage visible sur la référence) ○ ; subtil — le grain se devine, il ne se lit pas comme une texture plaquée.
- **Coutures** : voir § 3, étape 5.

### 5.2 Fourrure — fuzz de silhouette seulement, et après la porte ◆

- **Interdit avant la porte** (§ 8) : aucun groom, aucune fibre sur le modèle de production.
- Après la porte, la fourrure se limite à **casser la silhouette** (contour tête, oreilles, torse) : brins très courts et denses, créés en courbes dans Blender puis exportés en **Alembic** vers le Groom d'Unreal ○ (pipeline du plan § 3.1, couche 2). Ordre de grandeur du plan : 150–400 k brins courts sur tout le corps, à régler par tests sur les 12 Go de la 5070.
- Un groom complet « brins partout » est **hors programme** ◆ : c'est la façon la plus coûteuse et la plus fragile de rendre ce qui est essentiellement du velours court, et un poil trop long transformerait Onyx en une autre peluche (« shaggy »). Plan B documenté si le groom pose problème : shell texturing (coques décalées à masque alpha) ○.

### 5.3 Éclairage de contrôle pour le matériau

Le matériau se juge **uniquement** dans la scène de contrôle (§ 3, étape 7) et, pour la porte, dans la scène « Studio Onyx » d'Unreal. Jamais de réglage « à l'œil » sous un éclairage improvisé : la validation échantillonne le **rendu**, pas la valeur d'entrée ◆.

---

## 6. La porte stricte — mesurer, pas impressionner

Décision finale de l'atelier (22 nov. 2026, backlog nº 8) ◆ : **porte stricte maintenue pour le modèle de production**. Le modèle non riggé, habillé du **seul matériau tissu PBR** (aucun groom), doit passer le test côte à côte **avant** tout rig et toute fourrure de production. Si le test échoue, le problème est le modèle ou le matériau — **jamais le moteur** : on corrige le sculpt/matériau, ou on arrête le volet 3D là. Aucune heure coûteuse (rig, groom, animation) n'est engagée sur un personnage non prouvé fidèle à l'arrêt.

### 6.1 Les seuils ◆

| Critère | Seuil | Mesuré sur |
|---|---|---|
| Ratios de proportions (tableau § 2) | écart ≤ **3 %** | vue de face, rendu de validation |
| Position des traits (yeux, entraxe, sourire, sourcils) | écart ≤ **2 %** de la largeur de tête | vue de face, superposition |
| Couleurs | écart faible et **constant** contre la palette de la charte (`#FC8B32`, `#C95D1D`, `#8D2E0F`, `#1A1512`) | zones éclairées / médianes / ombres du rendu, éclairage officiel |
| Silhouette profil / dos | conformité à la **feuille de modèle approuvée** (elle fait foi, il n'y a pas de référence externe) | rendus profil et dos |

À titre de calibration honnête : les brouillons Pixal3D ont mesuré des ΔE couleurs de **10,3 à 13,5** ◆ — c'est l'ordre de grandeur d'un échec franc. Le modèle de production doit tomber **nettement en dessous**, avec un écart constant entre zones (un écart constant se corrige d'un réglage ; un écart variable signale un matériau faux) ◇.

### 6.2 Comment mesurer

1. **Scène de validation figée** ◆ : caméra à focale et distance reproduisant la référence, éclairage officiel, fond blanc. Rendus systématiques : face, 3/4 gauche/droite, profils, dos, dessus (+ turntable en images et vidéo pour la porte 2).
2. **Superposition — le test roi** ◆ : le rendu de face est superposé à la référence à **50 % d'opacité**, aligné sur les yeux. Écarts relevés point par point : contour de tête, position et taille des yeux, des oreilles, du museau, extrémités du sourire, couture centrale. C'est la même méthode que la porte qualité 2D du projet — un seul étalon de rigueur pour tout l'atelier.
3. **Mesures chiffrées** : proportions relevées sur le rendu (en T et en fractions de LT) contre le tableau § 2 ; couleurs échantillonnées sur le rendu dans les trois zones (éclairée, médiane, ombre) et comparées à la charte en ΔE.
4. **Boucle de correction** ◆ : tout écart hors tolérance → correction du sculpt ou du matériau → nouveau rendu de validation. Chaque boucle est archivée datée ; on ne compare jamais « de mémoire ».

### 6.3 La planche comparative côte à côte

Le livrable de la porte est une **planche unique** ◇, construite toujours pareil :

- À gauche : la référence canonique (face) et les vues de la feuille de modèle (profil, dos, corps entier).
- À droite : les rendus du modèle **au même cadrage, à la même échelle, sur la même ligne de sol**.
- Au centre ou en surimpression : la superposition 50 % de face.
- En pied de planche : le tableau des écarts mesurés (proportions, traits, ΔE) avec PASS/ÉCHEC par critère.
- La planche est présentée à l'atelier à la **porte 2 bis (jalon couperet)** : décision **go / corriger / arrêter**, ~10 minutes, avant tout budget rig ou fourrure.

### 6.4 Les portes l'atelier du volet 3D (rappel ◆)

1. **Porte 1 — feuille de modèle : FRANCHIE le 21 nov. 2026.** ✔
2. **Porte 2 — silhouette du sculpt** (turntable brut, sans fourrure) : à venir, fin de la phase sculpture.
3. **Porte 2 bis — jalon couperet** (cette section) : modèle non riggé + matériau tissu dans Unreal vs référence 2D.
4. **Porte 3 — lookdev final et premier lot de contenus** : avant toute publication 3D. **Aucune publication 3D avant la porte 3** ◆.

---

## 7. Le test d'armature jetable — périmètre exact autorisé ◆

Seule exception à l'interdiction du rig avant la porte, autorisée nommément par la décision du 22 nov. 2026 (backlog nº 8, volet 2) :

- **Support** : le brouillon **SV de Pixal3D** (`3d/test-pixal3d-2026-11-22/brouillon-sv.glb`), retenu pour sa surface et son visage plus fins. **Jamais** le modèle de production, jamais un sculpt corrigé.
- **Contenu** : une **mini armature technique** — squelette minimal sur mesure (racine, torse, tête, 2 oreilles, 2 bras ; la bouche n'est pas articulée, c'est une ligne) posée à la main dans Blender, skinning sommaire accepté.
- **Objectif unique** : valider tôt la **mécanique** — chaîne d'export FBX, lecture des morphs le cas échéant, retargeting et Control Rig dans Unreal. On teste un tuyau, pas un personnage.
- **Interdits dans ce test** : aucun travail de fidélité (pas de resculpt « pour en profiter »), aucun matériau de production, aucune fourrure, **aucune publication ni utilisation publique** de ses rendus.
- **Fin du test** : l'armature et ses fichiers de test sont **jetés** (archivés au dossier du test comme évidence datée, jamais promus). Le rig de production, après la porte, repart de zéro sur le vrai modèle — avec le squelette minimal du plan (une douzaine d'os ; Rigify serait surdimensionné, les auto-rigs humanoïdes de Meshy/Tripo sont écartés : proportions d'Onyx trop éloignées du standard humanoïde ◆).
- **Statut** : test **pas encore exécuté** ; il attend son tour derrière le chantier de fond actuel (le rig 2D, backlog nº 7) ◆.

---

## 8. Interdits avant la porte — la liste qui protège le budget ◆

Tant que la porte stricte (§ 6) n'est pas franchie, sont **interdits** sur le modèle de production :

1. **Rig définitif** (armature de production, skinning soigné, Control Rig final) — seule exception : le test jetable du § 7, sur le brouillon, jamais sur le modèle.
2. **Fourrure complète ou fuzz de silhouette** — le modèle se juge au matériau tissu seul ; c'est le matériau qui doit prouver la peluche.
3. **Expressions et morph targets de production** (sourire large, bouche « o », clignement…) — elles se construiront sur un modèle déjà validé au repos.
4. **Publication, sous quelque forme que ce soit, d'images du modèle non validé** — y compris en « coulisses » : le journal de bord raconte le processus, mais le personnage public reste la référence 2D tant que la porte 3 n'a pas validé le lookdev 3D.
5. **Travailler dans `00_canonical/`** ou y déposer quoi que ce soit sans validation à une porte.
6. **Écraser une version antérieure** d'un fichier `.blend` (§ 1.2).
7. **Toucher aux pilotes graphiques de Fractal** ou perturber l'usage de l'atelier — le travail 3D se fait en arrière-plan, comme tout le projet ◆.

---

## 9. Pannes et pièges connus

### 9.1 Échelle d'import GLB ◆ (piège générique, systématique)

Les exports GLB arrivent avec des échelles arbitraires (facteurs ×100 ou ×0,01 selon l'outil, brouillons Pixal3D compris) : le personnage peut mesurer 250 unités ou 0,025. **Réflexe** : dès l'import, lire la dimension Z, comparer à la hauteur attendue en T, appliquer un facteur propre, `Ctrl+A`. Un sculpt commencé à la mauvaise échelle fausse ensuite les brosses, les distances de retopo et l'export — on corrige à la minute 1, jamais après.

### 9.2 Texture cuite vs vrai PBR ◆

Les générateurs image→3D (Pixal3D : « PBR 2048 » annoncé) livrent en pratique une **texture photo cuite** : l'éclairage de la photo source est peint dans l'albedo (ombres et reflets figés), les cartes roughness/normal sont dérivées ou absentes. Conséquences : le modèle « s'éclaire mal » sous un nouvel éclairage, et ses couleurs ne sont pas réglables proprement. **Règle** : la texture d'un brouillon ne sert **jamais** de matériau de production. Le matériau tissu se construit de zéro (§ 5.1) sur la retopologie, aux couleurs de la charte.

### 9.3 Dérive jaune des générations ◆ (mesurée)

Sur les deux brouillons Pixal3D, les couleurs médianes mesurées (`#DD700A` / `#DD750C`) dérivent vers le jaune contre le médian de la charte (`#C95D1D`) : **ΔE 10,3 à 13,5**, et la gamme est compressée. Toute base IA doit être supposée **fausse en couleur** jusqu'à mesure contraire — d'où le matériau refait à la main et l'échantillonnage du § 6.2. Même vigilance sur la bouche (double trait « grenouille » sur le brouillon MV ◆) et l'entraxe des yeux (−4 à −11 % ◆) : les traits générés ne sont jamais repris tels quels.

### 9.4 Autres pièges consignés

- **Densité de brouillon** : ~700 000 triangles ◆ — ne pas tenter de « nettoyer pour garder » : on resculpte et on retopologise, on ne recycle pas le maillage.
- **Profondeur du museau** : indécidable depuis la seule face ◆ — c'est la feuille de modèle (profil retenu, plutôt profond) qui tranche, pas l'inspiration du moment.
- **Bouche de profil** : sur les vues générées elle devient une fente sous le museau ; en 3D la bouche reste un **trait de surface** ◆ — le profil final doit s'y conformer, pas l'inverse.
- **Pieds et mains hérités de génération** : pied « chaussure » et ébauche de pouce sur le profil généré ◆ — le design cible reste pied rond court, main moufle (§ 2).
- **Versions épinglées** ◆ : Blender 5.2 LTS (installé : 5.2.4) et Unreal 5.8 au programme ; aucune mise à jour en cours de production, aucune preview.
- **VRAM 12 Go** : le poste contraint viendra plus tard (groom + Path Tracer haute résolution dans Unreal) ; côté Blender, la discipline est simple — un seul gros modèle chargé à la fois, sculpt haute résolution fermé quand on retopologise ◇.

---

## 10. Prérequis et ordre de démarrage — le jour où le chantier 3D ouvre

**Prérequis (à vérifier la veille)** ◇ :

- [ ] Go explicite de l'atelier pour l'ouverture du chantier 3D (le chantier de fond actuel reste le rig 2D tant qu'il n'est pas clos).
- [ ] Blender 5.2.4 LTS fonctionnel sur Fractal (déjà installé ◆) ; espace disque vérifié (~180 Go libres au dernier audit — les modèles 3D et rendus sont lourds).
- [ ] Pack canonique 3D v1 relu (`00_canonical/3d-v1/` + `MODEL-SHEET-SPEC.md`) ; référence canonique 2D à portée de main.
- [ ] Ce guide relu ; journal de session ouvert dans le dossier de travail.

**Ordre de démarrage** (phases du `PLAN-3D.md` § 8, estimations « première fois », boucles de correction comprises) :

1. **3D-1 — Bases IA** (2–4 h) : si de nouvelles bases sont jugées utiles, génération et notation contre § 6 ; sinon, départ du brouillon SV existant ou bascule directe au plan B (sculpt de zéro sur sphère, feuille de modèle en fond). Les règles de licence du plan § 7 s'appliquent (jamais le modèle canonique sur un plan gratuit public/non commercial).
2. **Test d'armature jetable** (§ 7) : peut se placer tôt, en parallèle du sculpt — c'est sa raison d'être.
3. **3D-2 — Sculpture et maillage** (15–30 h, en arrière-plan) : flux du § 3, étapes 0 à 5. → **Porte 2** (silhouette, turntable brut).
4. **3D-3a — Matériau tissu + scène Unreal** (4–7 h) : matériau § 5.1, installation d'Unreal 5.8 (go d'installation à demander), scène « Studio Onyx », presets de rendu. → **Porte 2 bis — jalon couperet** (§ 6) : go / corriger / arrêter.
5. **Seulement après la porte** : 3D-3b fuzz de silhouette (4–8 h), 3D-4 rig et expressions (6–12 h), 3D-5 validation finale et premier lot (4–8 h). → **Porte 3** avant toute publication.
6. À terme, hors de ce guide : le **live 3D dans Warudo** réutilisera le modèle validé et son pack canonique 3D ; aucun choix fait dans Blender ne doit supposer Warudo avant que le modèle existe.

**Total estimé du volet** ◆ : ≈ 40–75 h de travail en arrière-plan, étalées sur plusieurs semaines, avec ~4 validations regroupées de l'atelier (dont 2 restent : portes 2/2 bis et 3).

---

_Guide v1 préparé le 22 novembre 2026 — à promouvoir en v2 après la première session réelle de sculpt : chaque panne réelle rencontrée y gagnera une fiche diagnostic, comme dans les autres guides du programme d'expertise._
