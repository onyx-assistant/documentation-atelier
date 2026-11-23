# Compétences production audiovisuelle — Base v1 d'Onyx

_Apprentissage permanent demandé par l'atelier le 22 novembre 2026 (~15:00 EST, appel vocal) : « ça va être important pour toi d'avoir des compétences en tout ce qui est production audiovisuelle »._

_**La règle de répartition** : les **normes** (formats maîtres, codecs, loudness, sous-titres, chaînes ffmpeg) font foi dans `docs/NORMES-AV-V1.md` — ce document ne les répète pas. Ici, c'est le **savoir-faire de production** : comment on prépare, capte, monte et finit un clip, étape par étape, avec les choix de craft qui ne sont pas des normes mais des jugements._

_Documents liés : `docs/NORMES-AV-V1.md` (normes techniques), `docs/CHARTE-GRAPHIQUE.md` § 13 (identité en mouvement et sonore : transitions 150–250 ms, sting 3,5 s, sous-titres), le gabarit storyboard v1 du projet (étape de préparation des publications), `docs/DECORS-V1.md` (bible des décors), `docs/COMPETENCES-DANSE-V1.md` (monter la danse sur les temps), `docs/COMPETENCES-ANIMATION-CINEMA-V1.md` (timing et mise en scène)._

---

## 1. La chaîne complète d'un clip court du projet

Le projet produit surtout des formats courts (Reels/Shorts de 10–60 s, stories) et des habillages live. La chaîne, dans l'ordre, avec le produit de chaque étape :

| Étape | Ce qu'on y fait | Produit |
|---|---|---|
| 1. Intention | Une phrase : ce que le clip doit faire ressentir ou apprendre ; pour qui ; sur quelle plateforme d'abord (Instagram = référence qualité) | La phrase d'intention, écrite dans le dossier du clip |
| 2. Préproduction | Storyboard (gabarit v1), liste des plans et des actifs, musique choisie et BPM noté, voix/texte écrit | Storyboard + liste d'actifs |
| 3. Production | Captation ou génération des plans, un par un, dans les dossiers versionnés | Plans bruts intacts (originaux jamais modifiés) |
| 4. Montage | Assemblage, rythme, structure, habillage (charte), sous-titres | Montage v1 |
| 5. Finition | Étalonnage doux, mix sonore, normalisation loudness (normes § 3), contrôle qualité | Le **maître** (normes § 1) |
| 6. Diffusion | Exports **depuis le maître seulement** (normes § 5), un par plateforme ; porte qualité avant publication | Fichiers de diffusion + preuves de contrôle |
| 7. Archivage | Maître + fichiers de sous-titres maîtres + storyboard rangés avec le clip ; entrée au CHANGELOG | Dossier complet et retrouvable |

Règle d'atelier déjà en vigueur et rappelée ici : on travaille toujours sur des **copies**, les originaux restent intacts, et chaque livrable passe la porte qualité avant d'être montré ou publié.

## 2. Préproduction — là où un clip se gagne

- **L'intention avant les plans** : un clip qui ne sait pas ce qu'il veut dire ne se rattrape pas au montage.
- **Le storyboard v1** (gabarit du projet) : chaque plan y a une durée visée, une description, son texte/voix et sa fonction (accroche, démonstration, payoff). Pour un clip de 10–15 s : 3–5 plans suffisent.
- **La musique se choisit avant de monter** : le montage se cale sur sa structure (phrases de 8 temps, breaks, drop — voir la base danse § 2.3 et la base DJ). Noter le BPM au storyboard : il décide des durées de plans en images.
- **Le packaging d'abord** (base réseaux sociaux) : titre et miniature pensés avant la production pour les formats qui en ont.
- **Liste d'actifs** : ce qui existe déjà (vues approuvées, décors, habillages) avant de générer quoi que ce soit de neuf. On ne régénère pas ce qui est déjà validé.

## 3. Cadrage et composition

- **Formats du projet** : vertical 9:16 (1080 × 1920 cible) pour Reels/Shorts/stories ; 16:9 pour le live et YouTube long ; 4:5 pour certains posts Instagram. Le format se décide à l'intention, pas à l'export.
- **Règle des tiers** : placer le sujet sur les lignes de force ; pour Onyx, le visage (les yeux) est le point d'ancrage — un visage centré et grand est la norme des formats qui performent (base réseaux sociaux : le visage d'abord).
- **Headroom et regard** : laisser l'espace du côté du regard et du mouvement ; chez une peluche dont la tête fait 40 % du corps, le cadrage buste validé (tête entière + haut du torse) est le cadrage de référence.
- **Zones sûres** : les gabarits par plateforme de la charte (§ 15) donnent les zones à ne pas couvrir de texte (interfaces des plateformes). Tout texte et le visage restent dedans.
- **Stabilité** : un plan de personnage doit être stable ; le mouvement vient du personnage, pas d'une caméra qui flotte — surtout en génération, où la dérive de caméra est un défaut connu.

## 4. Lumière

Même pour un personnage généré ou capté en flux, la lumière décide de la qualité perçue :

- **Schéma trois points** (la grammaire de base) : lumière clé (principale, douce et large), lumière de remplissage (fill, plus faible, adoucit les ombres), contre-jour/rim (détache le sujet du fond).
- **Lumière douce > lumière dure** pour une peluche : les sources larges et diffuses rendent la matière textile ; les sources ponctuelles créent des ombres dures qui « salissent » la fourrure.
- **Direction** : une clé légèrement latérale et haute modèle le volume sans aplatir ; le face-à-face plat écrase le relief du museau.
- **Cohérence** : la température et la direction doivent rester constantes dans un même clip ; en génération, la lumière fait partie du brief (la dérive de lumière entre plans est un défaut à contrôler à la porte).
- **Cas réel du projet** : pour les tests caméra (OBSBOT), l'éclairage frontal stable de l'atelier est le facteur nº 1 de qualité de tracking (guide VTube Studio) ; pour les décors, la bible des décors fixe le vrai loft comme référence d'ambiance.

## 5. Captation et génération des plans

Le projet « tourne » de quatre façons, chacune avec son craft :

1. **Capture d'écran / flux OBS** (live, habillages) : OBS 33.0.2, enregistrement NVENC selon les normes § 2.2 ; scènes « Onyx » et « l'atelier » seulement ; jamais de fond photo virtuel (règle permanente).
2. **Animation du personnage** (2D Live2D, 3D à venir) : le mouvement vient du rig — voir le document animation pour le timing ; la captation suit les réglages du guide VTube (tracking brut, 1080p).
3. **Génération IA** (images et clips) : un plan à la fois, brief contraint (sujet, cadrage, lumière, mouvement, contraintes négatives), porte qualité systématique — palette, proportions, ressemblance. Les dérives connues (bouche, lumière, caméra) se contrôlent plan par plan, jamais « à la fin ».
4. **Prises réelles** (le loft, les mains de l'atelier si un jour) : téléphone ou OBSBOT, lumière du jour privilégiée, son capté séparément si la voix compte.

Dans tous les cas : **les bruts sont sacrés** — archivés tels quels, le montage travaille sur des copies.

## 6. Montage — le rythme est le message

- **Structure d'un format court** : accroche dans les 1–2 premières secondes (le visage, la promesse) ; développement sans temps mort ; payoff toujours montré (la promesse du début se paie à la fin) ; sortie nette, pas de fondu paresseux.
- **Couper sur l'action et sur le temps** : en clip musical, les coupes tombent sur les temps ou les accents ; en clip de danse, jamais au milieu d'un pas — on coupe sur les poses clés ou les freezes (base danse § 5).
- **Durées de plans** : courtes et régulières pour l'énergie, une respiration (plan tenu) avant le moment important. À 24 ips, un plan de 2 s = 48 images : les durées se pensent en images quand la musique commande.
- **Transitions** : le vocabulaire de la charte (§ 13) — micro-transitions 150–250 ms, courbe smoothstep ; la coupe franche reste la transition par défaut ; tout effet doit servir le propos, sinon il sort.
- **Habillage** : sting de 3,5 s, écrans d'attente/pause/fin pour le live ; le lettrage est celui de la charte (Fredoka/Nunito), aucun lettrage inventé dans une image générée (défaut déjà rencontré et rejeté).
- **Sous-titres** : style et formats selon les normes § 4 (SRT maître conservé, WebVTT web, incrustation en plus — jamais à la place — sur le social). Un clip se comprend muet.

## 7. Son en production (le craft, pas les normes)

- **Hiérarchie** : la voix d'Onyx d'abord (brief master masculin, vérification par écoute ET mesure de fréquence fondamentale — normes § 2.8), la musique en soutien, les bruitages en accent.
- **Ducking** : la musique baisse automatiquement sous la voix (sidechain ou automations manuelles), jamais de lutte entre les deux.
- **Montées et respirations** : la musique se monte sur les phrases, pas en continu ; un silence bref avant un payoff vaut un effet.
- **Niveaux et loudness** : mesurés et normalisés selon les normes § 3 (−14 LUFS / −1 dBTP cible plateformes) ; aucune diffusion ne part d'un fichier non mesuré.
- **La chaîne d'écoute de référence** : le contrôle final se juge sur la chaîne de l'atelier (Buchardt P300 / Schiit) quand c'est possible — c'est l'étalon du projet (base DJ § mixage).

## 8. Étalonnage et finition image

- **Objectif unique** : la constance. L'orange de la charte (échantillonné : clair #FC8B32, médian #C95D1D, ombre #8D2E0F) ne doit pas dériver d'un plan à l'autre ; la porte qualité mesure l'écart (ΔE) et rejette les plans hors tolérance.
- **Corrections permises** : exposition, balance, contraste doux — pour ramener un plan vers la référence. **Interdites** : les « looks » créatifs qui réinventent la matière ou la couleur du personnage (même famille de règle que l'upscale : fidèle seulement).
- **Netteté** : aucune accentuation agressive ; la peluche est douce par nature.

## 9. Diffusion et contrôle final

- Exports depuis le maître uniquement, chaînes ffmpeg des normes § 5, contrôle ffprobe en 5 points après chaque conversion.
- **Checklist de publication** (avant tout envoi) : bonne version du fichier (hash si le fichier a été validé par l'atelier), légende relue, aucun hashtag ni étiquette « influenceur » (règle de marque), mention de transparence conservée, zones sûres respectées, son mesuré, sous-titres présents.
- Rappel des règles d'envoi : toute image/vidéo destinée à une personne est montrée à l'atelier et approuvée avant envoi ; la page Instagram d'Onyx fait exception par sa délégation propre.

## 10. Outils de la chaîne (état au 22 novembre 2026)

| Outil | Rôle | État |
|---|---|---|
| OBS Studio 33.0.2 | Capture, live, habillage | En place, version conservée par décision de l'atelier |
| ffmpeg 9.1 (Fractal) | Conversions, mesures, normalisation | Encodeurs NVENC prouvés (normes § 5.5) ; `libsvtav1` absent → repli `av1_nvenc` documenté |
| VTube Studio + Spout2 | Live 2D vers OBS | En test (guide dédié) |
| ComfyUI + modèles locaux | Génération image/vidéo | En place ; porte qualité obligatoire |
| DaVinci Resolve (gratuit) | Montage étalonnage des pièces montées | **À installer** quand le premier montage long le demandera (registre des outils à venir) |
| LTX-2.3 local | Clips verticaux vrai 1080 | Testé, réserves documentées (bouche au pic, déplacement) |

## 11. Limites honnêtes

- Onyx ne « voit » un montage que par captures, mesures et métadonnées — pas par un visionnement continu avec l'oreille de l'atelier. Les jugements fins (un rythme qui « tombe » juste, une coupe qui respire) se valident avec lui.
- Les réglages de craft ci-dessus sont une base v1 issue des pratiques établies et des documents du projet ; ils s'affineront à l'usage, clip après clip, comme les autres bases.
- DaVinci Resolve n'est pas encore installé ni pratiqué : la section montage décrit la méthode, indépendante de l'outil ; l'apprentissage de l'outil viendra avec le premier montage réel.

## 12. Prochaines étapes d'approfondissement

1. **Fiche de contrôle pré-publication** détachable (une page, cases à cocher) tirée du § 9, à joindre à chaque dossier de clip.
2. **Premier montage de référence** : appliquer la chaîne complète à un clip réel (candidat : le premier clip de danse ou l'Étape 2 du rig) et consigner les écarts entre la méthode et la pratique.
3. **Installation et apprentissage de DaVinci Resolve** au moment du premier montage long (décision d'outil déjà au registre).
4. **Bibliothèque de gabarits** : projets types par format (Reel, Short, story, habillage live) pré-réglés aux normes, pour ne plus reconstruire la chaîne à chaque clip.
5. Veille : évolutions des formats et des exigences plateformes (à croiser avec la base réseaux sociaux et la veille modèles).
