# Horizon jeux vidéo — cadrage v1

_Créé le 22 novembre 2026, à la suite de la décision de l'atelier du même jour (~19:53 EST, appel vocal). Ce document est un **cadrage de prudence**, pas un plan de production : il dit ce que la direction change dès aujourd'hui pour les choix techniques, et ce qui reste explicitement hors périmètre. **Version 2 — 22 novembre 2026 (~20:05 EST)** : la veille technologique lancée en parallèle a été livrée le même soir ; ses résultats sont intégrés au § 5, qui précise ce cadrage sur faits sourcés. Les §§ 1–4 restent la v1 (règles de prudence) ; en cas de tension, le § 5 prime pour les faits, et les règles de prudence restent en vigueur tant qu'elles ne sont pas explicitement amendées._

## 1. La direction, telle que décidée

Tout ce que le projet construit — un avatar **écrit** (personnage, script, bible), **modélisé** (feuille de modèle, modèle 3D) et **animé** (rig Live2D, chaîne vidéo) — ne doit pas rester limité aux stories et aux réseaux sociaux. À terme, **Onyx pourra être insérée dans des jeux vidéo**.

Instruction explicite de l'atelier, le même jour : **en tenir compte dans les choix techniques dès maintenant** — pas quand le premier jeu se présentera, mais dans chaque choix de format, d'outil et d'archivage fait à partir d'aujourd'hui. La veille technologique lancée en parallèle informera la suite ; la décision d'un moteur éventuel reste **réservée à l'atelier** et n'est pas prise ici.

## 1bis. Priorisation décidée le 22 novembre 2026 (~19:55 EST, appel vocal)

Dans la foulée de la direction, l'atelier tranche une **priorisation** — explicitement **pas un choix de moteur définitif** :

1. **Un modèle 3D propre et moteur-neutre est le socle commun prioritaire** : c'est lui qui sert tous les usages temps réel (contenu social 3D, live, et plus tard le jeu). Les règles du § 2 (masters moteur-neutres, exports ouverts, topologie propre, squelette standard, matériaux PBR documentés, textures sans perte) deviennent donc la priorité d'exécution de la voie 3D — le socle d'abord, les destinations ensuite.
2. **Unreal Engine 5 est l'horizon prioritaire pour le temps réel** : c'est le moteur vers lequel on regarde en premier pour le contenu social 3D temps réel et, plus tard, pour l'insertion en jeu. Ce statut est **prioritaire, pas définitif**. **Mise à jour v2 (~20:05 EST) : la veille a CONFIRMÉ ce statut pour le jeu et pour le contenu social produit (clips temps réel, rendus Sequencer, lives-événements), mais PAS pour le live interactif quotidien (Warudo/appels), qui reste sur la filière Unity/VRM — voir § 5.1.** La décision finale de moteur reste **réservée à l'atelier**. En attendant, aucun actif n'est produit dans un format propre à Unreal qui contredirait la règle § 2.2 — le socle moteur-neutre prime sur l'horizon.

## 2. Ce que ça change dès aujourd'hui — règles de prudence

Ces règles sont des **principes de prudence** déduits de la direction, formulés pour ne jamais fermer une porte qu'un moteur de jeu voudrait ouvrir. Elles s'appliquent aux choix techniques nouveaux ; elles ne rétroagissent pas sur ce qui est déjà verrouillé (apparence, palette, voix master, charte v1.0).

### 2.1 Formats d'échange ouverts et portables, toujours privilégiés

- **Modèle 3D : masters moteur-neutres.** La source de vérité du modèle 3D reste le fichier **Blender** du chantier 3D ; à chaque jalon du modèle, produire en plus des **exports propres en formats d'échange ouverts — glTF et FBX** — archivés à côté de la source, jamais à sa place. Aucun format d'export ne devient la seule copie d'un état du personnage.
- **Topologie propre et régulière.** Le maillage du modèle 3D vise une topologie régulière, lisible et réutilisable (boucles propres, densité maîtrisée) : c'est ce qui rend un modèle déformable, réutilisable et portable d'un moteur à l'autre. Les brouillons générés (ex. Pixal3D) restent des brouillons — leur topologie n'engage rien.
- **Squelette à nommage standard.** Quand le squelette du modèle 3D viendra (après la porte stricte, voir § 3), ses os portent des **noms standard et documentés**, dans une hiérarchie conventionnelle : le retargeting d'animations entre moteurs et bibliothèques repose sur cette convention, et un nommage maison fermé le rendrait coûteux ou impossible.
- **Matériaux PBR documentés.** Les matériaux du modèle sont décrits en termes **PBR** (albedo, roughness, normal, etc.) et **documentés par écrit** (valeurs, intentions, références charte), de façon à pouvoir être reconstruits dans n'importe quel moteur — jamais seulement « réglés dans un logiciel » sans trace.
- **Textures archivées sans compression destructive.** Les textures maîtresses sont archivées en **haute résolution, sans compression destructive** (PNG/TIFF ou équivalent sans perte) ; les versions compressées pour un usage donné (web, vidéo, moteur) sont des **dérivés**, régénérables depuis le maître.

### 2.2 Ne jamais enfermer le personnage dans un format propriétaire unique

- Aucun actif du personnage (modèle, textures, animations, voix, textes) ne doit exister **que** dans un format lisible par un seul logiciel ou un seul moteur. Quand un outil impose son format de travail (ex. `.cmo3` pour Cubism), les **sources ouvertes qui l'ont produit** (PSD en couches, PNG) restent la référence archivée — c'est déjà la discipline du projet, elle devient ici une règle nommée.
- Tout choix d'outil nouveau s'évalue aussi à cette question : « si cet outil disparaît, que récupère-t-on, et sous quelle forme ? » La réponse s'écrit dans le document du chantier concerné.

### 2.3 Deux voies, deux rôles — inchangés

- **La voie Live2D reste la voie « appels / stories »** : rig 2D, VTube Studio, OBS — le personnage vivant en direct et en courtes pièces. Elle ne porte pas l'horizon jeu et n'a pas à s'en préoccuper au-delà de la règle § 2.2 (ses sources ouvertes sont déjà la référence).
- **La voie 3D (Blender, backlog nº 8) est celle qui porte l'horizon jeu.** C'est donc sur ses choix (topologie, squelette, matériaux, exports) que les règles du § 2 s'appliquent en premier.
- **La porte stricte 3D reste inchangée** : le modèle de production doit passer le test côte à côte contre la référence (proportions ≤ 3 %, traits ≤ 2 %, couleurs de la charte) **avant** tout travail de rig ou de fourrure de production ; la mini armature de test jetable reste autorisée pour la mécanique seulement. L'horizon jeu ne desserre **aucun** de ces seuils — au contraire : un modèle destiné à vivre dans un moteur a encore plus besoin d'être juste dès le sculpt.

### 2.4 Licences : une surface d'exploitation de plus, mêmes règles

- L'horizon jeu est **une surface d'exploitation de plus** au sens de la gouvernance du projet (`docs/GOUVERNANCE.md`) : toute insertion d'Onyx dans un jeu — le sien ou celui d'un tiers — passerait par les règles déjà en vigueur : **licences par composant, jamais de cession, approbation écrite préalable de l'atelier** sur toute représentation, aucune clause d'entraînement IA sans accord séparé. l'atelier conserve l'intégralité des droits sur le personnage.
- Conséquence pratique dès aujourd'hui : les licences des **composants tiers** qui entreraient dans le modèle 3D (textures, polices, bibliothèques, outils) continuent d'être consignées au fur et à mesure — un composant dont la licence interdirait l'usage en jeu fermerait la porte que cette direction ouvre ; le signaler dès l'adoption, pas à la première opportunité.

## 3. Explicitement HORS périmètre pour l'instant

- **Aucun développement de jeu n'est lancé** : pas de prototype, pas de boucle de gameplay, pas de design de jeu.
- **Aucun moteur de jeu n'est définitivement choisi** — la priorisation Unreal 5 (§ 1bis), confirmée et précisée par la veille au § 5.1, est un cap, pas un engagement de production. La décision finale reste **réservée à l'atelier**. Les règles du § 2 sont précisément faites pour que ce non-choix d'aujourd'hui ne coûte rien demain.
- **Aucun calendrier** : l'horizon jeu ne déplace ni le chantier de fond actuel (le rig Cubism), ni les priorités de la revue hebdomadaire. C'est un cap de conception, pas un chantier ouvert.
- **Rien n'est promis à qui que ce soit** : aucune annonce publique, aucun contact avec un studio ou une plateforme au nom de cet horizon.

## 4. Suite prévue

1. **Veille technologique** (lancée le 22 nov. 2026, en parallèle) — **LIVRÉE le même soir (~20:05 EST)** : rapport complet `rapports internes de l'atelier (standards, moteurs comparés, écosystèmes fermés, précédents, verdict Unreal, avenue commerciale) ; résultats intégrés au § 5 ci-dessous.
2. **V2 de ce document** — **FAITE (le § 5)** : règles du § 2 précisées sur faits (socle biaisé UE-compatible, § 5.3). Une éventuelle **fiche de décision « moteur »** reste à préparer pour l'atelier le moment venu, sans rien trancher à sa place.
3. **Application immédiate, sans attendre la v2** : les règles du § 2 s'appliquent dès le prochain choix technique concerné — en premier lieu les jalons du chantier 3D (backlog nº 8) quand il démarrera.

_Suivi : backlog nº 46. Décision consignée au `docs/JOURNAL-DECISIONS.md` (22 novembre 2026)._

## 5. Veille technologique — résultats et cadrage v2 (22 novembre 2026, ~20:05 EST)

_Source : rapport de veille complet `rapports internes de l'atelier (état 2025-2026 ; dans le rapport, les faits sourcés sont distingués des recommandations). Cette section intègre ses quatre verdicts sans les déformer et précise le cadrage v1 sur faits._

### 5.1 Unreal Engine 5 — statut prioritaire CONFIRMÉ, avec une frontière nette

- **Confirmé pour le jeu et pour le contenu social PRODUIT** : clips temps réel, rendus Sequencer, lives-événements. Un seul pipeline Unreal peut porter le jeu et ce social-là ; précédent documenté : **PLAVE**, groupe virtuel produit entièrement dans un pipeline Unreal unique (clips, lives, concerts). La licence est favorable au petit créateur (gratuite sous 1 M$ ; 5 % au-delà du premier million pour un jeu).
- **PAS confirmé pour le live interactif quotidien** (Warudo, appels vidéo) : **Warudo repose sur Unity**, et il n'existe **aucun équivalent clé en main sous Unreal** — y passer exigerait de développer et maintenir un runtime live maison (le poste de coût qui a transformé CodeMiko en petite équipe). L'écosystème avatar social est d'ailleurs centré Unity : VRChat impose Unity, et VRM vit par UniVRM (Unity).
- **Statut en vigueur, formulé exactement** : **Unreal 5 = prioritaire confirmé par la veille, pour le jeu et le social produit ; le live interactif reste sur la filière Unity/VRM (Warudo).** Prioritaire ≠ exclusif ≠ immédiat : aucun moteur n'est choisi définitivement, aucun développement n'est lancé. Et la discipline du socle (§ 5.3) est précisément ce qui empêche « horizon UE » de devenir « prisonnier UE » — la pente naturelle d'un pipeline UE est l'accumulation d'actifs non portables (matériaux Substrate, Anim Blueprints, Control Rigs, groom).

### 5.2 Standards — qui sert à quoi

- **glTF 2.0 / GLB = le format d'échange de référence** (norme ISO/IEC 12113:2022) : géométrie, squelette, skinning, morph targets, matériaux PBR. C'est la livraison générique principale du socle.
- **VRM = passeport VTuber/social** : sur-ensemble glTF à squelette humanoïde normalisé (expressions et visèmes normalisés, spring bones, licence embarquée) — la livraison du live et du social temps réel, **dérivée du master**. Ce n'est **ni un format moteur, ni le format natif de VRChat** : VRChat s'alimente en **FBX** via le SDK Unity, expressions et physique recâblées dans le SDK.
- **USD / OpenUSD = composition de scènes** (film, DCC, archivage de scènes) — **pas** un format de livraison d'un avatar jouable.
- Conséquence pour la peluche : VRM exige un **squelette humanoïde complet** même en proportions chibi/mascotte ; les éléments non humanoïdes (oreilles, museau, ventre) vivent en os supplémentaires, hors mapping humanoïde. La règle § 2.1 (squelette à nommage standard) se précise donc : nommage **humanoïde/VRM dès le master**.

### 5.3 Socle — implications concrètes (rapport § 6.3) : moteur-neutre, biaisé UE-compatible

Le socle reste moteur-neutre (§ 2), mais ses livraisons et ses tests sont désormais **biaisés UE-compatibles**, sans jamais devenir UE-propriétaires :

- **Double mapping du squelette** : nommage humanoïde/VRM dans le master **et** table de correspondance écrite vers le squelette du mannequin UE5 (Manny/Quinn), **testée par un vrai IK Rig + IK Retargeter** — pas seulement déclarée sur papier.
- **FBX = livraison de premier rang**, au même niveau que le GLB, avec **presets d'export figés** (échelle, axes — Unreal travaille en centimètres, glTF en mètres : le preset absorbe la conversion, pas l'opérateur).
- **Matériaux traduisibles en UE** : maps PBR complètes (albedo, normal, roughness, metallic, AO) **plus une carte sheen/fuzz séparée et un masque SSS séparé**, pour que le matériau maître UE (Cloth/Substrate) se construise par branchement des mêmes maps. Le look de référence est alors défini et photographié **dans UE** ; les autres runtimes (MToon, toon Unity/Godot) en sont des approximations documentées — pas l'inverse.
- **Morph targets aux noms compatibles UE** : noms sans espaces ni caractères spéciaux, liste gelée ; pas de morphs « correctifs » combinés que seul Blender saurait évaluer sans bake.
- **Test de validation UE récurrent** : à **chaque version majeure du modèle** — import FBX/GLB dans un projet UE5 nu, retarget d'une animation de référence, matériau Cloth de base, capture d'écran archivée. C'est ce test répété qui transforme « horizon UE » en fait vérifié au lieu d'une intention.
- **Interdits confirmés** : **pas de MetaHuman comme base** (humains réalistes, étrangers au design de la peluche) ; **pas de groom ni de cloth simulé comme définition du personnage** (ce sont des couches de destination, jamais l'identité) ; pas d'expressions câblées uniquement dans un graphe moteur, sans morphs dans le fichier d'échange.
- Ces exigences sont renvoyées au chantier de la voie 3D (**backlog nº 8**), à appliquer quand elle avancera — la porte stricte (§ 2.3) reste sa première étape.

### 5.4 Commercial — licences d'apparition, jamais le fichier maître

- **Fait de veille** : les détenteurs de personnages comparables — **Crypton (Hatsune Miku)** et **Cover (hololive)** — ne vendent **jamais le fichier maître** de leur personnage. Leur modèle : des **licences d'apparition négociées** (les apparitions connues — Miku × Fortnite Festival, Icon Series — sont des licences, avec un modèle reconstruit par l'hôte), le non-commercial de fans étant encadré à côté (Piapro Character License ; programme examiné holo Indie pour les jeux de fans monétisés).
- **La vente de fichiers existe, mais c'est une autre économie** : les marketplaces d'assets génériques — **Fab** (88 % au vendeur), **Unity Asset Store** (70 %), **Booth**, **itch.io** (part libre) — où un personnage stylisé riggé générique se vend typiquement dans une bande médiane de **~20–60 $** la pièce : un revenu d'appoint, assorti d'une **perte de contrôle pratique** dès le premier téléchargement (extraction, ré-upload, incarnations non contrôlées ; les recours existent mais sont réactifs, lents et incomplets).
- **Position du projet (recommandation de la veille, intégrée au cadrage)** : **la mascotte est en licence-only**, avec un **kit de licence prêt** à préparer (renders, fiche technique du socle, conditions types) pour le jour où un studio ou une plateforme se présentera ; la **vente de fichiers est réservée aux assets NON identitaires** (props, animations génériques, environnements, bases différentes de la mascotte), **Fab d'abord** si elle a lieu un jour. La vente du fichier du personnage n'est donc **pas** une avenue retenue. Cette position rejoint et précise le § 9 de la stratégie de monétisation (« vendre le personnage ni ses actifs canoniques ») ; l'avenue est inscrite à son registre (`docs/STRATEGIE-MONETISATION-TECHNIQUE-V1.md` § 6bis) — **sans lancement**, comme tout le reste de cette stratégie.
