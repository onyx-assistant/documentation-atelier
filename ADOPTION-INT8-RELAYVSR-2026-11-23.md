# Adoption INT8 ConvRot + RelayVSR — rapport (23 novembre 2026)

_Décision de l'opérateur, 23 novembre 2026 ~14:15 EST : adopter les deux technologies
recommandées par la veille `docs/VEILLE-SOLUTIONS-HF-REDDIT-GITHUB-2026-11-23.md`
(§1.1, §1.6, §5.1) qui n'étaient pas encore dans la stack, puis mettre à jour
les sites et plateformes en conséquence, avec documentation. Périmètre
confirmé par l'opérateur : INT8 ConvRot et RelayVSR seulement — le reste de la
veille est déjà en usage (TeaCache à l'essai, faster-whisper adopté, BiRefNet,
SeedVR2 image, SageAttention via POC B3) et n'a été ni ré-adopté ni re-testé._

_En résumé : **RelayVSR est adopté** (upscaleur vidéo de référence) ;
**INT8 ConvRot n'est pas adopté** (porte non franchie, aucun gain de fidélité,
44 % plus lent chez nous) ; **NVFP4 est interdit** dans toute la stack._

---

## 1. RelayVSR — ADOPTÉ (upscaleur vidéo de référence)

Dispositif : POC B5 (fiche `tests/poc-b5-relayvsr-seedvr2/FICHE-POC-B5.md`),
clip Flat Eric essai 1 (1088×1920, 24 i/s, 121 trames), upscale ×2 →
2176×3840, mesures contre la source. Exécution via la file GPU le
23 novembre 2026 (fiche `20261123-142317-poc-b5.json`).

| Mesure | RelayVSR | Référence |
|---|---|---|
| Temps de génération | **1 581,8 s** (≈ 26 min) | SeedVR2 : aucune sortie en > 2 h 50 sur le même clip |
| Pic VRAM | 11 867 Mio | — |
| M1 palette (ΔE médian / couverture) | 5,04–5,23 / 0,928–0,957 | source : 4,91–5,23 / 0,925–0,954 |
| M2 DINOv2 (témoins 0/30/60/90/120) | 0,9942 / 0,9898 / 0,9864 / 0,9818 / 0,9815 (min **0,9815**) | — |
| M3(a) série complète (121 trames) | moyenne 0,9813, min 0,9336 ; clés 0,9856 vs hors clés 0,9809 ; dent de scie +0,0134 (aucun creux à la cadence 15) | — |
| M3(b) œil (fourrure / couture / museau) | aucune fourrure inventée ; couture centrale stable ; continuité clés/voisines parfaite | planches dans `poc-b5\resultats\` ; copie locale `tests/adoption-int8-relayvsr/b5-reparation/` |
| M5 œil (traits brodés à 200 %) | sourcils, yeux, sourire préservés sans remodelage | idem |

**Verdict** : qualité tenue à tous les contrôles, vitesse nettement
supérieure → **RelayVSR devient l'upscaleur vidéo de référence ; SeedVR2
garde les masters image** (règle consignée au guide de la chaîne, § 6).
La grille de la fiche B5 prévoyait une « proposition à l'opérateur » ; sa
décision directe d'adoption du 23 novembre la remplace — les planches
restent à sa disposition pour le contrôle à l'œil (règle montrer-d'abord).

Incidents du run (pilote, pas les modèles) : la collecte de la sortie
RelayVSR a échoué sur un bug de balayage d'historique (le nœud LoadVideo y
déclare le fichier d'entrée) — sortie récupérée sans regénérer, mesures
relancées, rapport régénéré (`RESULTATS-2026-11-23-1702.md`). La voie
SeedVR2 a dépassé le timeout du pilote (120 min) ; son prompt orphelin a
poursuivi seul sur le serveur 8188 jusqu'à l'interruption demandée à
17:29 EST (effective à la frontière de lot suivante, ~17:43) — **aucune
sortie SeedVR2 produite**, ce qui borne son coût à « > 2 h 50, sans
résultat » contre 26 minutes complètes pour RelayVSR. Détail :
`tests/adoption-int8-relayvsr/b5-reparation/NOTE-REPARATION-B5.md`.

## 2. INT8 ConvRot (LTX-2.5) — NON ADOPTÉ

Dispositif : A/B strict contre le POC B7 — mêmes entrée, brief, graines
(123456789/42), 1088×1920, 121 trames ; seule change la chaîne de poids.
Poids officiels `Lightricks/LTX-2.5` téléchargés sur le D: et vérifiés
(tailles exactes + SHA-256, résultat `conforme = vrai`) : transformeur
distilled INT8 ConvRot 21 504 034 224 octets (`c4279eef…c44334`),
encodeur Gemma INT8 ConvRot 15 372 969 374 octets (`6ce688a0…f91487f`) —
`D:\Onyx-Stockage\modeles\ltx-2.5-int8-convrot\`. Exécution via la file GPU
(fiches `20261123-142738-adoption-int8.json`, puis relance
`20261123-181200-adoption-int8.json` après correctif), instance ComfyUI
dédiée sur le port 8189 (production 8188 jamais touchée).

Premier essai (17:44) : échec en 10 s sur le nœud 351
(`ResizeImageMaskNode … missing … 'resize_type'`) — **le même bug que la
première tentative du POC B7** ; le correctif appliqué à B7 à l'époque
(forme aplatie `resize_type` + `resize_type.longer_size`) n'existait que
dans le workflow déployé sur Fractal, pas dans la source locale. Correctif
reporté dans `wf-int8.json` (source locale et déployée), relance à 18:11,
**succès technique (code 0)** à 18:21.

| Mesure | INT8 ConvRot (23 nov.) | GGUF Q4 — POC B7 (22 nov.) |
|---|---|---|
| Temps de génération | 566,3 s | **392,0 s** |
| Pic VRAM | 11 840 Mio | 11 736 Mio |
| Palette ΔE médian (témoins 0/30/60/90/120) | 4,83 / 4,77 / 4,86 / 4,85 / 4,96 — PASS ×5 | 4,70–4,89 — PASS ×5 |
| Couverture conforme | 0,914–0,953 | 0,920–0,953 |
| DINOv2 témoin 0 | 0,9849 PASS | 0,9846 PASS |
| DINOv2 témoin 30 | **0,7708 À SURVEILLER** | **0,7775 À SURVEILLER** |
| DINOv2 témoin 60 | 0,9055 PASS | 0,9311 PASS |
| DINOv2 témoin 90 | 0,8098 PASS | 0,8292 PASS |
| DINOv2 témoin 120 | **0,7691 À SURVEILLER** | **0,7728 À SURVEILLER** |
| Proportions | PASS ×4 ; témoin 30 écart 0,181 (À SURVEILLER) | PASS ×4 ; témoin 30 écart 0,185 (À SURVEILLER) |
| Porte (code de sortie) | 1 — non franchie | 1 — non franchie |

Contrôle à l'œil (planche numérotée
`tests/adoption-int8-relayvsr/resultats-int8/planche-comparaison-1-gguf-q4-2-int8-convrot.png`,
**1 = GGUF Q4, 2 = INT8 ConvRot**) : les deux versions sont visuellement
jumelles ; aux témoins 30 et 120 (salut profond), la distorsion de
mouvement est la même des deux côtés — yeux ouverts, pas de paupière
mi-close, bouche floue par le mouvement dans les deux cas. Le creux
DINOv2 à 30/120 est donc une propriété de la génération LTX-2.5 distillée
sur ce brief, **pas du format de quantification**.

**Verdict, selon le critère écrit d'avance** (adoption seulement si la
porte passe partout ET avantage mesuré, ou égalité avec meilleure
fidélité) : **INT8 ConvRot n'est pas adopté.** La porte n'est pas franchie
(2 témoins sous 0,80, comme le Q4), la fidélité est égale ou légèrement
inférieure (témoins 60/90), et le temps est **44 % plus long** à VRAM
égale. La chaîne GGUF actuelle reste donc en place ; il n'y a **aucun
échange d'état final** (les poids INT8 restent sur le D:, documentés au
registre nº 2.9, disponibles pour un éventuel nouvel essai — par exemple
après une migration de pile cu130) et la règle « jamais de GGUF Q4 pour
une pièce soumise à la porte » **n'est pas consignée**, puisqu'elle était
conditionnelle à l'adoption. La planche 1/2 reste à disposition de l'opérateur
pour son propre œil (elle couvre aussi l'arbitrage visuel du POC B7).

## 3. Règle NVFP4 (consignée)

**NVFP4 est interdit dans toute la stack, tout usage** (décision de
l'opérateur, 23 novembre 2026) — consigné au guide de la chaîne IA locale
(introduction) et aux décisions outils (section « Écarté »). Le
transformeur NVFP4 du dépôt LTX-2.5 n'a pas été téléchargé. Sauf décision
contraire future de l'opérateur.

## 4. Documentation mise à jour

- Le présent rapport : `docs/ADOPTION-INT8-RELAYVSR-2026-11-23.md`.
- `docs/GUIDE-CHAINE-IA-LOCALE.md` : tableau des modèles (SeedVR2 →
  images ; RelayVSR ajouté), interdiction NVFP4, § 6 réécrit (répartition
  adoptée + mesures B5), check-list finale.
- `docs/DECISIONS-OUTILS-2026-11-22.md` : RelayVSR en « Adopté », SeedVR2
  précisé « images », déclencheur RelayVSR marqué fait, NVFP4 en
  « Écarté ».
- `docs/REGISTRE-REVERSIBILITE-FRACTAL.md` : nº 1.19 (espace de test
  adoption INT8 + tâches OnyxDlInt8/OnyxProbeVtube), nº 2.9 (poids INT8
  sur D:, avec l'issue du test), ligne 2.8 RelayVSR mise à jour (adopté),
  comptes § 5.1 et § 8 (39 éléments ; 34 classe A / 5 classe B).
- `docs/CHANGELOG.md` : entrée de la mission (adoptions, verdicts,
  incidents).
- POC B5 : note de réparation et verdict
  (`tests/adoption-int8-relayvsr/b5-reparation/NOTE-REPARATION-B5.md`) ;
  `resultats-generation.json` corrigé sur Fractal, rapport B5 régénéré.

## 5. Plateformes

- **Site de l'atelier** (section « Stack technique ») : retouche préparée
  et validée aux sources (une phrase ajoutée à la cellule 03 « Vidéo » :
  RelayVSR nommé upscaleur vidéo fidèle de l'atelier ; INT8 non adopté :
  rien à ajouter pour LTX-2.5, le site ne prétend rien d'autre). Arbre de
  travail : `archives-v2-travail/adoption-stack-2026-11-23/` (source +
  manifeste). **Déploiement NON promu** : à l'aperçu, le nouvel
  `index.html` était servi en base64 — l'upload direct par curl avait omis
  les champs `base64: true` et `metadata.contentType` que la route
  `execute` des campagnes précédentes incluait ; le magasin d'assets étant
  write-once par clé, la clé du nouvel index (et, par le même upload, les
  clés de 9 images inchangées) sont empoisonnées pour tout NOUVEAU
  déploiement. **La production n'a jamais été touchée** : revérifiée saine
  après l'incident (page 200, images aux octets RIFF conformes, contenu
  identique). La promotion exige des clés fraîches (contenus régénérés
  selon la procédure de la campagne d'alignement) — à faire par la voie de
  déploiement habituelle, pas en forçant celle-ci. Déploiements d'aperçu
  créés pendant l'essai : `8180f037-204a-46f0-84d0-0bff9f35afc2` et
  `67d2cdea-7c8b-4c3a-98ba-d4ac6e9ee58d` (branche `archives-v2`, sans
  promotion).
- **Hugging Face** : le README dit déjà « RelayVSR — Upscale vidéo » et
  « SeedVR2 — Upscale d'image fidèle » (conforme à l'adoption). La ligne
  LTX-2.5 (« Passe présentement ses examens d'entrée ») mérite une
  retouche après ce verdict — texte proposé : « LTX-2.5 | Évalué en INT8
  ConvRot et en GGUF — pas encore en production ». L'édition passe par
  l'éditeur web connecté (le jeton Fractal est en lecture seule).
- **GitHub** (`documentation-atelier`) : guide de la chaîne mis à jour et
  le présent rapport poussés via le connecteur.
- **Site de liens / autres plateformes** : aucune page ne décrit les
  formats d'upscale ni de quantification — aucune contradiction relevée,
  rien à corriger.
