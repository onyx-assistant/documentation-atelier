---
language:
- fr
license: other
---

# documentation-atelier — le journal technique de l'atelier Onyx

Onyx est une peluche orange virtuelle, fabriquée couche par couche dans un
atelier à Montréal : dessin et découpe en couches, rig Live2D, voie 3D,
vidéo générée en local, voix verrouillée, publication quotidienne. Ce dépôt
est la **bibliothèque technique publique** de ce chantier — les guides, les
normes et les savoirs que l'atelier a construits en fabriquant le
personnage, écrits pour être relus et réutilisés.

Le code des outils vit dans les dépôts voisins (`porte-fidelite`,
`file-gpu`, `gabarits-porte-3d`, `onyx-agent`, `charte-graphique`) ; ici,
c'est le **pourquoi** et le **comment** documentés.

## La chaîne et ses normes

| Document | Sujet |
| --- | --- |
| `GUIDE-CHAINE-IA-LOCALE.md` | La chaîne IA 100 % locale de l'atelier : modèles, rôles, ordre des étapes, de l'image à la vidéo. |
| `NORMES-AV-V1.md` | Les normes audiovisuelles de publication : −14 LUFS / −1 dBTP, H.264, vrai 1080 × 1920, étiquetage IA. |
| `FICHE-CONTROLE-PRE-PUBLICATION-V1.md` | La fiche de contrôle que chaque pièce passe avant de sortir. |
| `SYSTEME-IMAGES-ARCHIVES-V1.md` | Le système qui uniformise les images d'archive du site (plaque claire unique, grade commun, porte couleur). |
| `PROVENANCE-V1.md` | La provenance des contenus : signatures et manifestes, pour prouver ce qui sort de l'atelier. |
| `COUCHES-SPEC.md` | La spécification des couches du personnage (découpe, nommage, ordre) pour le rig 2D. |
| `VEILLE-MODELES-RAPIDES-HF-2026-11-23.md` | La veille des modèles rapides : candidats mesurés, retenus, écartés — et pourquoi. |

## La voie 3D et les jeux

| Document | Sujet |
| --- | --- |
| `PLAN-VOIE-3D-V1.md` | Le plan de la voie 3D : étapes 0 à 6, de la fiche modèle au modèle passé à la porte stricte. |
| `GUIDE-BLENDER-V1.md` | Le guide Blender de l'atelier (modélisation, armature, mesures) pour ce chantier précis. |
| `FICHE-DECISION-MOTEUR-V1.md` | La fiche de décision moteur : la recommandation double voie, et le moment où elle se tranche. |
| `HORIZON-JEUX-VIDEO-V1.md` | L'horizon jeux : socle 3D moteur-neutre, Unreal 5 prioritaire, live quotidien en Unity/VRM, mascotte en licences d'apparition seulement. |

## Les savoirs du personnage

Les bases de connaissances que l'atelier entretient pour écrire et animer
juste — principes des studios, filtrés pour l'humour chaud et universel
d'Onyx :

| Document | Sujet |
| --- | --- |
| `COMPETENCES-ANIMATION-CINEMA-V1.md` | Principes d'animation et de cinéma appliqués au personnage. |
| `COMPETENCES-EXPRESSIONS-COMMUNICATION-V1.md` | Expressions et communication non verbale d'une peluche. |
| `COMPETENCES-HUMOUR-V1.md` | Les ressorts d'humour retenus pour Onyx — et ceux écartés. |
| `COMPETENCES-SCENARISATION-PRODUCTION-AV-V1.md` | Scénarisation et production des formats courts. |
| `COMPETENCES-PRODUCTION-AV-V1.md` | Production audiovisuelle : tournage, son, montage, finition. |
| `FICHES-SCENES-ANIMATION-V1.md` | Les fiches de scènes types du personnage. |
| `FICHE-TIMINGS-VISAGE-V1.md` | Les timings du visage : clignements, regards, micro-mouvements. |

## Comment ces documents vivent

Chaque document est versionné dans le dépôt de travail de l'atelier avant
d'arriver ici ; les versions publiées sont des **copies publiques
assainies** (aucun identifiant personnel, aucun détail d'infrastructure
interne). Si un document et la pratique divergent, c'est le document qui
est corrigé — jamais l'inverse en silence.

## Licence

Documentation publiée pour lecture et référence. Les textes restent la
propriété de l'atelier ; le personnage Onyx, ses fichiers maîtres et ses
références canoniques ne sont publiés dans aucun dépôt (voir
`LICENCE.md`).
