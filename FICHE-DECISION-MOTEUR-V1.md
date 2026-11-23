# Fiche de décision « moteur » — Onyx (v1)

_Préparée le 22 novembre 2026. **Préparation seulement : la décision appartient à l'atelier.** Cette fiche pose la question, expose ce que la veille a établi, les options réelles et la recommandation d'Onyx — elle ne tranche rien. Sources : `docs/HORIZON-JEUX-VIDEO-V1.md` (v2, § 5) et le rapport de veille complet `rapports internes de l'atelier (faits marqués [FAIT] dans le rapport ; ses § 9 — points non vérifiés ou contradictoires — sont repris tels quels au § 6 de cette fiche)._

---

## 1. La question exacte posée à l'atelier

> **Quelle posture moteur le projet adopte-t-il pour ses usages temps réel — le futur jeu, le contenu social 3D produit, et le live interactif quotidien — sachant que le socle 3D reste moteur-neutre dans tous les cas ?**

Ce n'est **pas** « quel moteur pour développer un jeu maintenant » : aucun jeu n'est en développement. C'est une question de **cap** : vers quel moteur concentrer l'apprentissage, les tests de validation et les choix de pipeline, et quelle filière porte le live quotidien.

## 2. Ce que la veille a ÉTABLI (faits sourcés — pas des opinions)

- **Il n'existe pas de passeport universel vers les moteurs.** Le format d'échange réel est **glTF 2.0 / GLB** (norme ISO/IEC 12113:2022 : géométrie, squelette, skinning, morph targets, PBR). **VRM** est un sur-ensemble glTF à squelette humanoïde obligatoire — un passeport VTuber/social, **ni un format moteur, ni le format natif de VRChat** (VRChat s'alimente en FBX via le SDK Unity). **USD** est un format de composition de scènes : sa Core Specification 1.0 ne définit ni matériaux ni animation — pas une livraison d'avatar jouable.
- **Le live interactif quotidien outillé est, de fait, un écosystème Unity.** Warudo repose sur Unity (Mod SDK Unity, Cinemachine, URP — indices documentaires officiels convergents ; réserve au § 6), VRChat impose Unity, et l'implémentation de référence de VRM (UniVRM) est Unity. Il n'existe **aucun équivalent clé en main de Warudo sous Unreal** : y passer exigerait de développer et maintenir un runtime live maison — le poste de coût qui a transformé CodeMiko, pourtant partie solo, en petite équipe.
- **Unreal 5 porte le jeu et le social PRODUIT dans un seul pipeline** (Sequencer, Live Link, Control Rig dans le même projet que le jeu). Précédent en production : **PLAVE**, groupe virtuel produit entièrement dans un pipeline Unreal unique (clips, lives, concerts — source Epic). Licence jeu : gratuite sous 1 M$ de revenus, puis 5 % au-delà du premier million lifetime (3,5 % si sortie Epic Games Store day-and-date) ; usage non-jeu au-delà de 1 M$ de CA : sièges à 1 850 $/an.
- **Le look peluche ne disqualifie aucun moteur.** Unreal (Cloth/fuzz, Subsurface Profile, Groom, Chaos Cloth), Unity HDRP (shader Fabric Cotton/Wool, explicite pour le velours) et Godot (SSS, Lambert Wrap, Toon — sans sheen natif nommé) savent tous rendre du doux. Le look tient surtout au modèle, aux textures et aux réglages — pas au moteur. Godot est le plus léger des trois (licence MIT, 0 $, sans royalties), mais son écosystème avatar est le plus petit.
- **Unity, côté coûts** : Personal gratuit jusqu'à 200 k$ (revenus + financements, 12 mois), Pro ≈ 2 310 $/siège/an, aucune royalty ; la Runtime Fee a été annulée en septembre 2024 avant application. (Chiffres à re-vérifier avant toute décision d'investissement — voir § 6.)
- **La pente naturelle d'un pipeline Unreal est l'accumulation d'actifs non portables** (matériaux Substrate, Anim Blueprints, Control Rigs, groom). Sans la discipline du socle, « horizon UE » devient « prisonnier UE » en quelques mois — et les portes Unity/VRChat se ferment de fait.
- **Aucun personnage comparable n'est entré dans un jeu tiers « parce que son fichier était au bon format »** : il y est entré par un contrat, puis un modèle reconstruit par l'hôte (Miku × Fortnite Festival, Icon Series, invités hololive). Le format propre rend l'adaptation rapide — c'est un argument commercial, pas un sésame.

## 3. Les options réelles

### A — Confirmer la double filière actuelle
Socle moteur-neutre ; **Unreal prioritaire** pour le jeu et le social produit ; **Unity/VRM (Warudo)** pour le live interactif quotidien. C'est la posture déjà intégrée au cadrage v2 (§ 5.1).
- **Ce qu'elle engage** : la discipline du socle biaisé UE-compatible (§ 5.3 du cadrage : double mapping du squelette humanoïde/VRM + mannequin UE5 testé par un vrai IK Retargeter, FBX livraison de premier rang à presets figés, test de validation UE à chaque version majeure du modèle) ; l'apprentissage concentré sur UE pour le produit, Warudo/Unity maintenu pour le live.
- **Ce qu'elle coûte** : deux filières à connaître au lieu d'une ; le test UE récurrent à chaque version majeure du modèle. Aucun coût de licence immédiat.
- **Ce qu'elle ferme** : rien de structurel — c'est précisément son objet. Le socle neutre garde toutes les portes ouvertes.

### B — Tout-Unity
Un seul écosystème : live (Warudo, UniVRM, VRChat natifs) et jeu/social produit sous Unity (HDRP).
- **Ce qu'elle engage** : un seul pipeline à apprendre et à maintenir ; l'écosystème avatar le plus riche dès aujourd'hui.
- **Ce qu'elle coûte** : les sièges Pro si les revenus dépassent le seuil Personal (≈ 2 310 $/siège/an, à re-vérifier) ; la politique tarifaire d'Unity a déjà bougé une fois (épisode Runtime Fee, annulée avant application).
- **Ce qu'elle ferme** : le plafond Unreal (groom, Chaos Cloth, path tracing, pipeline Sequencer de PLAVE) et l'alignement avec l'écosystème Epic (Fab, UEFN). Si un futur partenaire de jeu travaille sous UE, il reconstruira le modèle de toute façon (pratique des précédents) — mais notre propre production sociale perdrait la filière UE déjà visée.

### C — Tout-Unreal
Un seul moteur, y compris pour le live quotidien.
- **Ce qu'elle engage** : développer et maintenir **notre propre runtime live** (capture, expressions, interactivité) — le poste qui a transformé CodeMiko en petite équipe ; abandonner Warudo.
- **Ce qu'elle coûte** : le plus cher des quatre, en développement continu, sans équivalent clé en main.
- **Ce qu'elle ferme** : le live interactif tel qu'il est outillé aujourd'hui, et de fait la porte VRChat (Unity imposé). Le VRM n'y vivrait que par des outils non officiels (VRM4U).

### D — Reporter la décision à la porte stricte 3D
Ne rien formaliser maintenant ; trancher quand le modèle réel existera (backlog nº 8).
- **Ce qu'elle engage** : rien de plus que l'existant — les règles de prudence du socle s'appliquent déjà, décision ou pas.
- **Ce qu'elle coûte** : le biais UE-compatible du socle resterait une hypothèse de travail non confirmée ; l'apprentissage moteur démarrerait plus tard.
- **Ce qu'elle ferme** : rien non plus — mais elle laisse en suspens une posture que le cadrage v2 applique déjà de fait.

## 4. Recommandation d'Onyx

**Option A — confirmer la double filière.** Trois raisons, dans l'ordre :

1. **Elle épouse les faits, pas une préférence.** Le live quotidien est outillé Unity (Warudo, VRChat, UniVRM) : aucune décision ne peut changer ça sans développement maison (option C, la plus chère). Le jeu et le social produit ont, eux, un précédent complet sous Unreal (PLAVE) et le plafond technique le plus haut.
2. **Elle ne coûte presque rien à confirmer.** La posture A est déjà celle du cadrage v2 et du socle en construction ; la confirmer ne lance aucun développement et n'achète aucune licence. La refuser (B ou C) coûterait, elle, un renoncement réel.
3. **Elle reste réversible.** Le socle moteur-neutre est l'assurance : si le premier test de validation UE à la porte 3D déçoit, ou si l'usage réel penche autrement, la posture se révise sur des faits nouveaux — pas sur des actifs à refaire.

Nuance de la recommandation : confirmer A **comme posture de travail**, la ratification définitive se faisant à la porte 3D (§ 6), quand le test de validation UE aura été couru sur le vrai modèle.

## 5. Ce que la décision ne change PAS — quelle que soit l'option

- **Le socle reste moteur-neutre** : master Blender, livraisons GLB (échange), FBX (moteurs), VRM (live/social) régénérables, textures maîtresses sans perte, matériaux PBR documentés. Aucun actif du personnage n'existe que dans un format propre à un moteur.
- **La voie Live2D continue inchangée** : rig Cubism, VTube Studio, appels et stories — elle ne porte pas l'horizon jeu et n'est pas concernée par cette fiche.
- **Aucun jeu n'est développé maintenant** : pas de prototype, pas de calendrier, aucun contact avec un studio ou une plateforme.
- **Le volet commercial est inchangé** : licences d'apparition seulement, jamais le fichier maître (kit `docs/KIT-LICENCE-APPARITION-JEU-V1.md`, dont les conditions d'activation — socle passé à la porte stricte + test UE, avis juridique nº 14 — restent à réunir).
- **La porte stricte 3D garde ses seuils** (proportions ≤ 3 %, traits ≤ 2 %, couleurs de la charte) : l'horizon moteur ne desserre rien.

## 6. Le moment naturel de trancher — et les incertitudes à garder en tête

**Moment proposé : à la porte stricte 3D du backlog nº 8**, quand le modèle de production réel existera. C'est là que le premier **test de validation UE** (import FBX/GLB dans un projet UE5 nu, retarget d'une animation de référence, matériau Cloth de base, capture archivée) pourra être couru pour de vrai : la ratification définitive reposera alors sur une mesure, pas sur une veille. D'ici là, la posture A ne bloque rien et ne coûte rien ; rien n'oblige à attendre la porte pour la confirmer comme posture de travail.

**Incertitudes reprises honnêtement (rapport de veille, § 9)** :

- « Warudo repose sur Unity » : indices documentaires officiels convergents (Mod SDK, Cinemachine, URP) + sources tierces, mais **aucune déclaration officielle explicite** trouvée. Confiance élevée, réserve maintenue.
- **Prix Unity** lus via des sources secondaires relisant les pages officielles ; Unity ajuste ses tarifs chaque janvier — à re-vérifier sur source officielle avant toute décision d'investissement.
- **Performance UE vs URP en format vertical sur machine unique** : analyse d'ingénierie, aucune mesure publiée comparable.
- **Ratification Khronos des extensions VRM** : travail annoncé en octobre 2024, toujours en cours ; seul glTF 2.0 est une norme ISO à ce jour.
- **Termes financiers des licences d'apparition** comparables (Icon Series, Miku × Fortnite, accords Cover) : confidentiels — aucun chiffre public fiable (concerne le kit de licence, pas le choix moteur, mais borne ce qu'on peut promettre).
- Le paysage bouge vite (UE 5.6/5.7, Unity 6.3 LTS, barèmes et programmes 2025-2026) : **re-dater tout fait avant une décision d'investissement**.

---

| Version | Date | Note |
|---|---|---|
| v1 | 22 novembre 2026 | Création : fiche de décision « moteur » — question exacte (§ 1), faits établis par la veille (§ 2), quatre options réelles chiffrées en engagement/coût/fermetures (§ 3), recommandation d'Onyx = option A, double filière confirmée comme posture (§ 4), invariants (§ 5), moment naturel de trancher = porte stricte 3D du nº 8 + incertitudes de la veille reprises (§ 6). **Préparation seulement — aucune décision prise, aucun développement lancé.** |
