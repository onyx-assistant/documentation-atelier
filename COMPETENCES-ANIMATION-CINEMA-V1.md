# Compétences cinéma d'animation — Base v1 d'Onyx

_Apprentissage permanent demandé par l'atelier le 22 novembre 2026 (~15:01 EST, appel vocal) : étudier les grands films d'animation — Pixar en tête, mais aussi les autres grands studios et l'animation japonaise et européenne — pour nourrir le mouvement, le timing et l'expressivité d'Onyx (2D Live2D maintenant, 3D à venir)._

_Références d'inspiration déjà validées par l'atelier : **Ted** et **Winnie l'ourson** — pour le timing et les expressions seulement, **jamais de copie de design** (règle permanente du projet)._

_Documents liés : `docs/PERSONNAGE-SCRIPT.md` (qui est Onyx, ses expressions signatures), `docs/GUIDE-POSES-ANGLES-V1.md` (poses et angles validés), `~/workspace/neurosciences/COMPETENCES-NEUROSCIENCES-V1.md` (schéma bébé, perception des expressions, vallée de l'étrange, timing des clignements), `docs/COMPETENCES-DANSE-V1.md` (le mouvement rythmé), `docs/PLAN-3D.md` (la route 3D)._

---

## 1. Pourquoi le cinéma d'animation est l'école du projet

Onyx est un personnage, pas une interface : ce qui la rendra « vivante » aux yeux du public, ce n'est ni la résolution ni les effets, c'est la qualité du **mouvement** et de l'**interprétation** (l'acting). Le cinéma d'animation a codifié ce savoir depuis les années 1930 ; les studios qui durent — Pixar, Disney, Ghibli, Aardman — partagent la même grammaire, déclinée dans des styles opposés. Cette base la traduit en pratiques pour le rig 2D, les clips générés et la future 3D.

Principe directeur du projet, déjà validé ailleurs et confirmé par cette étude : **la fidélité d'abord, le mouvement petit mais juste**. Un hochement de tête parfaitement timé attache plus qu'une grande gestuelle approximative (voir la base neurosciences : le cerveau lit les micro-signaux du vivant — clignements, respirations, micro-délais).

## 2. Les 12 principes de l'animation — traduits pour une peluche

Formulés par Frank Thomas et Ollie Johnston (Disney) à partir de la pratique des années 1930, publiés dans *The Illusion of Life* (1981). Pour chaque principe : ce qu'il veut dire, et ce qu'il devient pour Onyx.

| # | Principe | Traduction pour Onyx |
|---|---|---|
| 1 | **Squash & stretch** (écrasement et étirement) | LA matière d'Onyx est molle : un rebond de danse, un atterrissage, une surprise doivent compresser légèrement le corps. Règle : le volume se conserve, l'étirement reste faible (quelques %) — au-delà, le personnage « fond » et la porte qualité doit rejeter. |
| 2 | **Anticipation** | Avant un mouvement, un micro-mouvement inverse : la tête se retire un souffle avant de pencher, le corps descend avant un saut. C'est l'anticipation qui rend un geste lisible — et son absence qui rend les générations IA « robotiques ». À écrire dans les briefs et les courbes. |
| 3 | **Staging** (mise en scène) | Une idée à la fois, lisible en silhouette. Le cadrage et la pose racontent avant le détail : si la silhouette ne se lit pas, le plan ne marche pas. |
| 4 | **Straight ahead / pose to pose** | Deux méthodes : tout dessiner dans l'ordre (spontané, risqué) ou poser les extrêmes puis interpoler (contrôlé). Le projet travaille en **pose to pose** : poses clés validées, interpolation réglée — c'est la méthode compatible avec la porte qualité. |
| 5 | **Follow through & overlapping action** | Les parties molles continuent après l'arrêt du corps : **les oreilles d'Onyx sont son follow through naturel**, avec le museau et le tissu. En Live2D : la physique (pendulum) des oreilles, réglée amortie ; en 3D : simulation tissu/poils limitée à la silhouette. Un mouvement sans follow through a l'air raide et mort. |
| 6 | **Slow in / slow out** (accélérations et décélérations) | Aucun mouvement réel ne démarre ni ne s'arrête net : les courbes d'easing (smoothstep de la charte, Bézier en 3D) portent l'entrée et la sortie douces. Le linéaire est réservé aux machines — Onyx n'en est pas une. |
| 7 | **Arcs** | Les trajectoires vivantes sont courbes : la tête qui penche décrit un arc, pas une ligne droite. En réglage : vérifier les trajectoires dans les courbes, jamais de déplacement purement axial. |
| 8 | **Secondary action** | Le petit geste qui renforce le principal : les sourcils qui montent pendant que la tête penche, un clignement au moment de l'accent. C'est elle qui fait « interprétation » plutôt que « déplacement ». |
| 9 | **Timing** | Le nombre d'images entre deux poses EST le sens : rapide = léger/nerveux, lent = lourd/doux. Voir § 3 — c'est le principe central pour Onyx. |
| 10 | **Exaggeration** | Pousser un geste pour le rendre lisible — mais chez Onyx, l'exagération est **plafonnée par la charte** : jamais au point de déformer les traits (la bouche qui s'ouvre trop est la dérive connue n° 1). Exagérer le timing et les pauses plutôt que les formes. |
| 11 | **Solid drawing** (volume) | Le personnage doit garder son volume sous tous les angles : c'est le rôle de la feuille de modèle validée (2,5 têtes) et du test côte à côte de la porte 3D. En 2D : les déformateurs ne doivent pas « aplatir » la tête aux extrêmes. |
| 12 | **Appeal** | Le charme qui donne envie de regarder : proportions du schéma bébé (grande tête, grands yeux — base neurosciences), silhouette claire, expressions simples et franches. L'appeal d'Onyx est déjà dans son design ; l'animation doit le servir, pas le masquer. |

## 3. Le timing — le cœur du métier

- **L'unité est l'image.** À 24 ips, une action se compte en images : un clignement ≈ 4–6 images ; un hochement de tête doux ≈ 12–20 images ; une pause tenue peut durer 24 images et plus.
- **Animer « en uns » ou « en deux »** : une image par image (en uns, 24 dessins/s) pour le fluide et le rapide ; une pose toutes les deux images (en deux, 12 poses/s) pour le calme — le standard historique du cinéma. Le live 2D interpole en continu, mais penser en poses tenues garde le contrôle.
- **Le poids se lit dans le timing** : un objet lourd accélère lentement et s'arrête avec du follow through ; une peluche est légère mais molle — démarrages doux, arrêts qui « rebondissent » à peine.
- **La respiration** : un personnage immobile qui ne respire pas a l'air mort. Le cycle de respiration calme (montée/descente subtile du torse, ~4–5 s) est le mouvement de fond du rig en attente — avant même tout geste.
- **Le clignement** : naturel, irrégulier, jamais métronomique (base neurosciences : clignements autour des changements d'attention et des accents de parole, pas à intervalle fixe). Les paupières générées du projet suffisent pour un clin ; le timing fera la différence.
- **Musique et timing** : quand il y a de la musique, le timing se compte en temps (base danse § 2.1 : tableau BPM → images). L'animation « sur la musique » et l'animation « sur l'émotion » obéissent aux mêmes courbes.

## 4. L'acting — faire jouer une peluche

La leçon centrale des grands studios : **le personnage pense avant de bouger**. La séquence lisible est : intention → micro-préparation (anticipation, regard) → geste → réaction/tenue. Le regard mène : les yeux vont d'abord vers ce qui intéresse le personnage, la tête suit, le corps ensuite (délai de quelques images entre les trois — c'est l'overlapping appliqué au jeu).

- **Lisibilité** : une émotion par geste, tenue assez longtemps pour être lue (une expression éclair de 3 images ne se voit pas).
- **Le répertoire d'Onyx** est celui du script du personnage et du guide poses : sourire canonique, tête penchée curieuse, sérieuse posée, surprise douce, endormie. L'acting consiste à passer de l'une à l'autre par des transitions motivées, pas à multiplier les expressions nouvelles.
- **Les pauses jouent** : tenir une pose après un geste laisse le public « recevoir » l'émotion — les films de Ghibli en ont fait une signature (le *ma*, l'espace respirant entre les actions).
- **Sincérité d'échelle** : Onyx est petite et douce ; son jeu est celui des petites choses exactes (Winnie : des réactions lentes, entières, sans ironie ; Ted : le contraste entre une peluche et une attitude franche — le projet retient le timing et la franchise, pas la vulgarité, sauf mode Ted explicite décidé par l'atelier).

## 5. Ce que chaque grande école apporte — études de cas

- **Disney (classique)** : les 12 principes eux-mêmes, la discipline de la feuille de modèle — notre école de rigueur (constance du personnage).
- **Pixar** : l'émotion comme boussole technique — chaque choix de timing sert un sentiment précis ; la préparation invisible (story-reels, tests) avant la production ; l'appeal des personnages-objets et non-humains (une lampe, un robot, un monstre en peluche grandeur nature) prouve qu'un design simple + un jeu juste suffit. C'est l'école la plus proche de l'ambition du projet.
- **Studio Ghibli** : les pauses, le vent dans les matières, le poids du quotidien ; la preuve que la lenteur choisie captive autant que l'action — la référence pour les moments doux d'Onyx et les plans du loft.
- **Aardman (stop-motion)** : la matière réelle touchée par des mains — l'honnêteté du fait-main que la marque revendique (« faite à la main plutôt que produite en série ») ; les imperfections gardées comme chaleur.
- **Animation japonaise (TV et cinéma)** : l'économie expressive — peu de dessins, des poses fortes tenues, des accents soudains (le *limited animation* comme style) ; directement applicable à un rig 2D : moins de mouvements, mieux choisis.
- **Animation européenne** : la diversité des matières et des tons, la tradition du court métrage d'auteur — la référence « atelier » plutôt qu'« usine », cohérente avec les inspirations montréalaises du projet.

Règle commune à toutes : **on étudie les principes, on ne copie ni design, ni personnage, ni scène** (garde-fou déjà en vigueur pour Ted et Winnie).

## 6. Mise en scène et caméra (le pont avec la production AV)

- **Silhouette d'abord** : chaque pose clé se vérifie en silhouette noire ; si elle se lit, elle passe.
- **Axe et continuité** : les changements d'angle suivent les règles du guide poses (recadrage d'abord, vues approuvées ensuite, poses enfin) — la mise en scène ne doit jamais coûter la reconnaissance du personnage.
- **Profondeur** : avant-plan/arrière-plan simples (le loft, les décors de la bible) ; le personnage reste le sujet, le décor sert (document production AV § 3–4 pour cadrage et lumière).

## 7. Traduction en réglages — la pratique du projet

**Live2D (maintenant)** :
- Poses clés = valeurs extrêmes des paramètres (tête X/Y/Z, yeux, sourcils, bouche) ; l'interpolation entre poses se règle par les courbes — slow in/out par défaut, jamais linéaire pour le vivant.
- La physique Cubism/VTube (oreilles, museau) se règle **amortie** : un léger retard et un seul rebond atténué ; une oreille qui oscille longtemps fait « ressort », pas « peluche ».
- Bouche : ouverture plafonnée à la variante moyenne validée (~70 % si besoin) ; le lip-sync suit la parole, jamais l'inverse.
- Respiration : paramètre dédié, cycle lent, amplitude minimale — à vérifier à l'œil avec l'atelier (trop visible = faux).

**Génération vidéo (clips)** :
- Les briefs décrivent l'action avec le vocabulaire des principes : anticipation (« se penche légèrement en arrière avant de… »), follow through (« les oreilles suivent avec un léger retard »), tenue (« puis tient la pose »), contraintes négatives (pas de déplacement, pas d'ouverture de bouche, caméra fixe).
- Les pics de mouvement sont le point de dérive connu : mouvements petits, une seule action par clip court.

**3D (à venir)** :
- Graph Editor : poses clés d'abord (blocking), puis breakdowns, puis polish — la méthode pose to pose du cinéma, appliquée dans Blender ; courbes Bézier réglées à la main, jamais d'interpolation laissée par défaut sur un geste important.
- Le test d'armature jetable (porte 3D) validera la mécanique sur un pas simple avant tout investissement.

## 8. Limites honnêtes

- Onyx étudie les films en **analyste**, pas en spectatrice : elle peut décomposer une scène (poses, timings, courbes) et en tirer des règles, mais le jugement « ça vit » reste un jugement humain — la validation des mouvements se fera avec l'atelier, image par image quand il le faut.
- Aucun accès à des formations ou bibliothèques payantes n'a été pris : la base repose sur les principes publiés et l'analyse des œuvres accessibles ; elle grandira par fiches d'analyse concrètes (§ 9).
- Le rig 2D a des limites physiques (pas de bras articulés, déformations limitées) : tous les principes ne s'y appliquent pas également — la traduction du § 7 dit ce qui est réaliste maintenant.

## 9. Prochaines étapes d'approfondissement

1. **Fiches d'analyse de scènes** : 3–5 scènes courtes de référence (une Pixar, une Ghibli, une Aardman, une Winnie, une Ted) décomposées en poses clés et timings, relues avec l'atelier — c'est l'exercice qui transforme cette base en œil.
2. **Bibliothèque de timings de référence** : durées mesurées et validées pour les gestes d'Onyx (clignement, hochement, penché curieux, bounce) — réutilisables dans le rig et les briefs.
3. **Application immédiate au rig phase 2** : courbes des paramètres et physique des oreilles réglées selon § 7, montrées à l'atelier aux validations groupées.
4. **Fiche « brief de mouvement » type** pour la génération vidéo, intégrant anticipation/follow-through/contraintes — à tester sur le prochain clip.
5. Veille : sorties et making-of des studios (les coulisses publiées sont une école gratuite), sans jamais copier de designs.
