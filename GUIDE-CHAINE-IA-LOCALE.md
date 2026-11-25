# Guide chaîne IA locale complet — ComfyUI et les modèles d'Onyx sur Fractal

_Créé le 22 novembre 2026. Guide d'expertise complet nº 3 du programme d'approfondissement (backlog nº 27 ; base : `docs/EXPERTISE-STACK-V1.md` § 2.5–2.10). Il remplace les fiches v1 comme référence de travail pour toute génération locale d'images et de vidéos du personnage. Chaque valeur ci-dessous est soit **éprouvée** sur le projet (marquée ◆), soit tirée de la documentation officielle ou de pratiques établies (marquée ○), soit une **recommandation de départ à valider en session** (marquée ◇)._

**Le contexte, en une page.** Toute la génération IA du projet se fait **en local sur Fractal** (RTX 5070, **12 Go de VRAM**, 64 Go de RAM, pilote Game Ready 621.24 — **ne jamais toucher aux pilotes GPU sans l'accord explicite de l'opérateur** ◆). Socle : **ComfyUI 0.46.0** ◆ (venv Python 3.12.10, torch 2.8.0+cu128), qui écoute **en local seulement** (127.0.0.1:8188) ◆ — ne jamais l'exposer au réseau. Six modèles y vivent, chacun avec un rôle tranché et un statut clair :

| Modèle | Rôle dans le projet | Statut |
|---|---|---|
| **Qwen-Image-Edit-2511** (GGUF Q4_0, Apache 2.0) | Éditeur d'images par référence — poses et variantes du personnage | **Production** (recette éprouvée, § 3) |
| **See-through** (NF4) | Décomposition en couches — point de départ seulement | **Retiré** — désinstallé le 23 nov. 2026 (§ 4 conservé comme retour d'expérience) |
| **SAM 3 + BiRefNet** | Segmentation et détourage des actifs | **Production**, sous règle de licence permanente (§ 5) |
| **SeedVR2** (Apache 2.0) | Upscaling **fidèle** des **images** (masters image) | **Production restreinte** (§ 6) |
| **RelayVSR** (Apache 2.0) | Upscaling **vidéo** de référence | **Production** (§ 6) — adopté le 23 nov. 2026 |
| **LTX-2.3** (GGUF distillé Q4_K_M) | Vidéo locale en vrai 1088×1920 | **Production**, usage ponctuel (§ 7) |
| **LTX-2.5** (GGUF Q4, POC B7) | Évaluation vidéo LTX de nouvelle génération | **Chaîne d'évaluation de référence adoptée — pas de production** (§ 7.5) |
| **Pixal3D** (MIT) | Brouillons 3D jetables | **Jetable seulement** — échec documenté à la porte stricte (§ 8) ; poids **délestés sur D:** le 23 nov. 2026 (jeu complet, réinstallable) |

**La règle qui gouverne tout le guide** : aucune sortie de ces modèles n'est montrée à l'opérateur sans être passée par la **porte qualité automatique** (§ 9) **et** un contrôle à l'œil. La fidélité au personnage canonique (`references/onyx-canonique.webp`) est le critère nº 1 du projet ; la vitesse ne passe jamais devant.

**Interdiction NVFP4** ◆ : le format de quantification **NVFP4 est interdit dans toute la stack** (tout usage, pas seulement les pièces soumises à la porte) — décision de l'opérateur du 23 novembre 2026, consignée dans `docs/ADOPTION-INT8-RELAYVSR-2026-11-23.md`. C'est le format qui dérive le plus en fidélité ; aucun poids NVFP4 n'est téléchargé ni utilisé, sauf décision contraire future de l'opérateur.

---

## 1. Architecture et séquencement VRAM — un seul gros modèle à la fois

### 1.1 Le plafond réel

- VRAM totale : **12 227 Mio** ◆ (mesuré pendant le test LTX-2.3). Un modèle vidéo comme LTX-2.3 (GGUF de 16,5 Go sur disque) **ne tient pas en entier** dans les 12 Go : l'offload dynamique de ComfyUI déborde vers la RAM système ◆ — ça passe, mais juste (pic mesuré **11 679 / 12 227 Mio** ◆).
- Conséquence opérationnelle ◆ : **un seul gros modèle chargé à la fois**. Qwen, LTX-2.3 et Pixal3D ne doivent **jamais** se disputer la VRAM ; les générations se **séquencent** dans la file ComfyUI, qui les exécute une par une.
- Preuve vécue ◆ : pendant la passe des poses du 22 nov. 2026, la RTX partageait la machine entre Qwen, LTX-2.3 et Pixal3D — les temps de génération Qwen ont varié de ~10 à ~24 min selon la file, **sans aucune concurrence VRAM ni erreur**, parce que tout passait par la même file séquencée.

### 1.2 Ordre de travail d'une session de génération

1. **Vérifier que la file est vide** avant de soumettre (un job oublié retarde tout et charge la VRAM pour rien) ◆.
2. **Un modèle, une tâche** : soumettre, attendre la fin, récupérer la sortie, **puis seulement** passer au modèle suivant ◇.
3. **Libérer entre deux gros modèles** : si une génération suivante part sur un autre modèle, laisser ComfyUI décharger (ou relancer le serveur proprement si un doute persiste) ◇ — ne jamais empiler deux chargements « pour gagner du temps » : le dépassement VRAM coûte plus cher que l'attente ◆.
4. **Lancer les gros jobs quand l'écran de Fractal est libre** et que l'opérateur ne joue pas : le GPU est partagé avec tout le reste de la machine ◆ (règle de la maison : le gaming passe avant).
5. **Sessions SSH courtes = piège** ◆ : les sessions SSH directes se coupent après ~30 s (constaté deux fois pendant le test LTX). Tout travail long sur Fractal passe par des **tâches planifiées cachées** (`run-hidden.vbs`), jamais par une session SSH tenue ouverte.

### 1.3 Ce qui cohabite sans danger

- La **porte qualité** tourne sur la machine d'Onyx (CPU, DINOv2 en ONNX) — elle ne touche pas la VRAM de Fractal ◆.
- Les petits modèles utilitaires (BiRefNet pour un détourage ponctuel) restent légers, mais la règle reste : **en cas de doute, séquencer** ◇.

---

## 2. ComfyUI — le socle d'exécution

- **Version en place** ◆ : ComfyUI **0.46.0**, venv Python 3.12.10, torch 2.8.0+cu128 ; ComfyUI-GGUF présent (requis pour Qwen et LTX en GGUF) ; tous les nœuds LTX-2.3 sont dans le cœur installé — aucune installation de nœud n'a été nécessaire pour le test LTX ◆.
- **Écoute locale uniquement** ◆ (vérifié à l'audit cybersécurité du 22 nov. 2026) : malgré les nœuds tiers installés (Pixal3D), le serveur ne répond que sur 127.0.0.1. **Ne jamais** le relancer avec une écoute réseau.
- **Soumission par l'API** ◆ : les générations longues sont soumises au serveur via son API et pilotées par tâches planifiées cachées sur Fractal (patron éprouvé au test LTX : `OnyxLtxDownload`, `OnyxLtxRun`).
- **Nœuds tiers = vigilance licence** ◆ : avant d'utiliser le détourage interne d'un pipeline tiers, vérifier quel modèle il appelle par défaut (voir § 5 — la famille TRELLIS/Pixal3D détoure par défaut avec un modèle **non commercial**).
- **Workflows versionnés** ◆ : chaque recette qui marche est conservée en JSON dans le dossier du chantier (ex. `tests/ltx23-test-2026-11-22/workflow_ltx23_i2v_gguf.json`, gabarit officiel gardé à côté). Ne jamais écraser un workflow éprouvé : en créer une copie versionnée.

---

## 3. Qwen-Image-Edit-2511 — l'éditeur de poses (production)

### 3.1 Ce que c'est, et pourquoi lui

- **Qwen-Image-Edit-2511**, quantification **GGUF Q4_0** dans ComfyUI ◆, licence **Apache 2.0** ◆. C'est l'éditeur par référence le plus fidèle qui tienne dans 12 Go, avec **jusqu'à 3 images de référence** ○.
- **Ne pas migrer vers Qwen-Image-2.1** ◆ : le successeur ouvert de la lignée est passé en licence « Research » **non commerciale** (veille du 22 nov. 2026). La 2511 reste le dernier éditeur Qwen librement utilisable ; à re-vérifier seulement si un successeur Apache 2.0 paraît.

### 3.2 La recette éprouvée ◆

| Paramètre | Valeur | Statut |
|---|---|---|
| Pas (steps) | **40** | ◆ recette du test de fidélité, réutilisée pour les poses v1 |
| CFG | **4,0** | ◆ idem |
| Référence | L'image canonique, toujours en première référence | ◆ |
| Sortie | Passe **systématiquement** à la porte qualité + œil avant d'être montrée | ◆ règle du projet |

Temps observés ◆ : ~10 à 15 min par génération à file libre ; ~24 min pour la toute première (chargement des modèles inclus) ; davantage si la file est partagée (voir § 1.1).

Résultats de référence ◆ (poses-expressions v1, 22 nov. 2026) : variante « Sérieux » **PASS complet** (palette ΔE 5,08 / couverture 95 %, proportions écart 8 %, DINOv2 0,9251) ; variante « En mouvement » **PASS complet** (ΔE 5,84, DINOv2 0,8307, ~2,4 têtes mesurées à l'œil contre 2,5 cibles) ; variante « Joyeux » retenue **au réessai** avec réserve (voir § 3.3). Dossier : `poses-expressions-v1/RAPPORT-PORTE.md`.

### 3.3 La dérive de bouche — la panne signature de Qwen ◆

**Symptôme** ◆ : Qwen **épaissit et élargit le sourire** — arc noir épais au lieu du trait fin brodé de la référence — et peut **noircir/épaissir les sourcils** dans le même mouvement. C'est la dérive nº 1 de l'outil sur ce personnage, observée dès le premier test de fidélité (personnage fidèle **sauf la bouche, qui souriait un peu trop**) puis confirmée aux poses v1 (essai « Joyeux » nº 1 rejeté à l'œil malgré palette et DINOv2 au vert : sourcils épais noirs + sourire en arc épais).

**Comment le brief la contient** ◆ (procédure éprouvée, dans cet ordre) :

1. **Verrouiller la bouche dans le brief dès le premier essai** : décrire le sourire canonique en termes négatifs explicites — sourire **fin, discret, peu profond, ligne brodée**, bouche **fermée** ; sourcils **fins, brun moyen, courbes douces** ; interdire nommément « thick », « black arc », « bold ».
   Formulations éprouvées au réessai « Joyeux » ◆ : *« eyebrows stay thin, light brown… not thick, not black »* ; *« mouth stays a thin, subtle, shallow curved stitched line… not a bold black arc »*.
2. **Un seul réessai à brief resserré** si la dérive apparaît ◆ : le réessai corrige (sourire redevenu fin et canonique sur « Joyeux »). **Pas d'acharnement** : jamais de retouche manuelle d'un rejet, jamais de troisième essai en boucle — on documente et on passe.
3. **Surveiller le pendule inverse** ◆ : le brief resserré peut pousser trop loin — sur le réessai « Joyeux », les sourcils sont sortis **trop pâles** (beige clair contre brun moyen canonique), forme et épaisseur justes, teinte un cran trop claire. La porte n'a pas vu cette réserve : **le contrôle à l'œil reste obligatoire**, même quand tout est PASS.
4. **Ne jamais conclure sur les métriques seules** ◆ : l'essai rejeté avait palette PASS (ΔE 5,21) et DINOv2 PASS (0,943). La dérive de bouche/sourcils est précisément ce que les chiffres ne voient pas.

### 3.4 Autres limites connues

- L'indice « proportions » de la porte peut afficher REJET sur une pose **bras levés** (les bras occupent la zone de mesure) : c'est un **faux rejet documenté**, non applicable à cette pose — le contrôle dur devient alors indice + mesure à l'œil (tête ≈ 1,17 T de large) ◆.
- Les poses éloignées de la référence (corps entier, mouvement) font baisser DINOv2 (0,83 sur « En mouvement ») sans que le personnage soit faux : le seuil reste 0,80, et l'œil tranche ◆.

---

## 4. See-through — la décomposition en couches (accélérateur, pas une finition)

> **⚠️ RETIRÉ — alignement du 24 novembre 2026** : See-through a été **désinstallé le 23 novembre 2026** (décision de l'opérateur, double confirmation ; la voie de découpe retenue est **Qwen-Image-Layered + découpe fine manuelle**). Le dossier `C:\Users\<utilisateur>\see-through`, ses poids du cache HF et le nœud `ComfyUI-See-through` ont été supprimés (absences revérifiées le 24 nov. 2026). La présente section est conservée **comme retour d'expérience** — ses limites prouvées restent la raison du choix actuel ; elle ne décrit plus un outil en place.

- **Installation (historique)** ◆ : `C:\Users\<utilisateur>\see-through` sur Fractal (torch 2.8.0+cu128, quantification **NF4** via bitsandbytes).
- **Ce qu'il fait bien** ◆ : il produit vite un PSD en couches + les fichiers par partie (55 fichiers à la phase 1 : face, head, ears, eyebrow, nose, mouth, parties vestimentaires, cartes de profondeur…). C'est un **point de départ** de découpe, pas un résultat.
- **Sa limite prouvée sur une peluche** ◆ (phase 1, 21 nov. 2026) : See-through n'a **pas vraiment séparé** le personnage. La plupart des couches nommées sont **vides ou quasi vides** ; le personnage tient dans **~3 couches réelles**. La séparation sémantique, pensée pour des personnages humains habillés, ne sait pas quoi faire d'une peluche d'une seule pièce.
- **Deuxième limite** ◆ : See-through **régénère les pixels** — sa sortie n'est pas les pixels d'origine. La recomposition à plat de sa sortie a été vérifiée (erreur moyenne **0,48/255** contre la référence), mais la voie de production reste : reprojection des pixels originaux + **découpe fine manuelle (14 couches)**, dont l'erreur de recomposition est environ **moitié moindre** (MAE 0,208, PSNR 38,83 dB) et le zoom visage à 200 % indiscernable de l'original ◆.
- **Troisième limite** ◆ : son PSD de sortie n'est **pas importable tel quel dans Cubism** (écriture binaire que Cubism lit sans textures — voir le guide Cubism, piège pytoshop) : passer par la réécriture **ag-psd**.
- **Panne d'exécution documentée** ◆ : le script par défaut (`group_offload=True`) échoue avec `CUBLAS_STATUS_ALLOC_FAILED` pendant l'encodage CLIP — le group offload de diffusers casse cet encodage avec les modules NF4. **Configuration qui marche : aucun offload.** Ni le contexte d'exécution ni bitsandbytes n'étaient en cause (tests d'isolation réussis).

**Règle d'usage** ◆ : See-through sert à dégrossir une nouvelle image (premier découpage rapide). Toute couche destinée au rig passe ensuite par la découpe fine manuelle et la vérification de recomposition.

---

## 5. SAM 3 + BiRefNet — segmentation et détourage (production, sous règle de licence)

- **Usage projet** ◆ : segmentation (SAM 3) et détourage (BiRefNet) des actifs — fonds, pièces détachées, préparation des masques.
- **RÈGLE PERMANENTE DE LICENCE** ◆ : toujours invoquer **`rembg -m birefnet-general`**. Le modèle **par défaut** actuel de rembg (Bria RMBG-2.0) est devenu **non commercial** : l'utiliser par défaut exposerait tout le projet. Ne jamais lancer un `rembg` nu.
- **La règle s'étend aux pipelines tiers** ◆ : tout outil qui détoure en interne doit être forcé sur BiRefNet. Cas prouvé : le pipeline Pixal3D/TRELLIS détoure par défaut avec RMBG-2.0 → le test Pixal3D a été mené avec **BiRefNet forcé** ◆.
- **À surveiller** ○ : SAM 3.1 (suivi vidéo multi-objets ~2× plus rapide) — déclencheur d'évaluation : premier besoin de segmentation vidéo, pas avant. **État au 24 nov. 2026** : le fichier `sam3.1_multiplex_fp16.safetensors` a été **délesté sur D:** le 23 nov. 2026 (`D:\Onyx-Stockage\modeles-delestes\sam3\`, SHA-256 vérifié) — hors de l'arbre actif ; seul `sam3.safetensors` sert la production.

---

## 6. Upscaling fidèle — RelayVSR pour la vidéo, SeedVR2 pour l'image

- **Ce que c'est** ◆ : SeedVR2, upscaler vidéo/image (3B, GGUF + BlockSwap pour tenir en basse VRAM), licence **Apache 2.0** ; RelayVSR, upscaleur vidéo (×2, poids INT8 ConvRot + VAE Flash fp32), licence **Apache 2.0**, nœuds intégrés à ComfyUI.
- **Usage autorisé** ◆ : l'**upscaling fidèle seulement** — augmenter la résolution d'un contenu existant sans réinterpréter le personnage.
- **Répartition adoptée le 23 novembre 2026** ◆ (POC B5 + décision d'adoption de l'opérateur ; rapport `docs/ADOPTION-INT8-RELAYVSR-2026-11-23.md`) : **RelayVSR est l'upscaleur vidéo de référence** ; **SeedVR2 garde les masters image**. Mesures B5 sur le clip Flat Eric essai 1 (1088×1920 → 2176×3840, 121 trames) : RelayVSR **1 581,8 s**, pic VRAM 11 867 Mio, palette au niveau de la source (ΔE 5,04–5,23), DINOv2 minimum **0,9815** sur les témoins et **0,9336** sur la série complète, aucun motif de fourrure inventée à l'œil (planches M3/M5) ; la voie SeedVR2 vidéo n'a produit **aucune sortie en plus de 2 h 50** sur le même clip (timeout du pilote à 120 min ; le prompt, devenu orphelin, a poursuivi seul sur le serveur et une demande d'interruption lui a été envoyée) — c'est ce coût qui avait déjà motivé le déclencheur de réévaluation.
- **Interdit permanent** ◆ : les **upscalers créatifs/diffusion** sur le personnage. Ils **inventent de la fourrure** et des détails qui n'existent pas dans la référence : le résultat a l'air « plus riche » et n'est plus Onyx. Même prudence pour tout upscaler à diffusion : le risque d'invention sur la fourrure est moindre qu'un créatif, **pas nul** — la porte qualité décide, en comparaison directe.
- **Doctrine upscaleur (adoptée par l'opérateur le 23 novembre 2026, sur proposition de la veille imagerie/vidéo du même jour — `docs/VEILLE-IMAGERIE-VIDEO-2026-11-23.md`)** ◆ : **tout upscaleur est suspect par défaut**. Aucun upscaleur — y compris les deux adoptés ci-dessus — ne s'applique au personnage sans le **contrôle « fourrure inventée »** préalable : recadrages 200 % sur les zones de poil comparés à la source, et, en vidéo, la série temporelle du protocole M3 (recherche du motif de cadence des images clés). Un upscaleur qui invente du grain, lisse le minky ou fait « nager » la texture est écarté, même si la porte classique passe. Corollaire mesuré : **SeedVR2 sur-génère du détail sur des entrées peu dégradées** (son propre README le déconseille en upscale vidéo) — il reste réservé aux masters image ; RelayVSR garde son contrôle M3 à chaque usage vidéo de production.
- **Cas d'usage consignés** ◆ :
  - **Plaques de fond du loft** (backlog nº 17) : si une photo horizontale dédiée manque, l'upscale fidèle SeedVR2 est le **repli autorisé** pour atteindre le plancher HD — jamais d'extension générative du lieu.
  - **Short publié en 720×1280** (backlog nº 20, option B) : upscale fidèle + ré-encodage aux débits des normes — option **toujours ouverte mais déconseillée seule** (elle ne crée pas le détail d'un vrai 1080 ; la chaîne LTX-2.3, elle, le génère nativement, § 7).
- **Déclencheur de réévaluation** ◆ : **réévaluation faite** (POC B5, 23 nov. 2026) — licence Apache 2.0 confirmée sur le dépôt d'origine, A/B mené via la porte sur nos propres clips ; verdict et mesures dans le rapport d'adoption cité plus haut. La répartition RelayVSR (vidéo) / SeedVR2 (image) est la règle en vigueur.

---

## 7. LTX-2.3 — la vidéo locale en vrai 1080 vertical (chaîne testée et validée)

### 7.1 Le test de référence ◆ (22 nov. 2026, dossier `tests/ltx23-test-2026-11-22/`)

Objectif : produire un **vrai** vertical ~1080×1920 du personnage — le générateur hébergé plafonne à 720×1280, et fabriquer du « faux 1080 » par upscale seul est déconseillé (§ 6). **Verdict : RÉUSSI, avec deux réserves** (voir § 7.3). Licence lue en entier ◆ : non bloquante à notre échelle (licence payante seulement à partir de 10 M$US de revenus annuels ; les sorties appartiennent au projet ; aucune exclusion territoriale).

### 7.2 La recette qui a marché ◆

- **Chaîne** : ComfyUI + **GGUF distillé Q4_K_M** (LTX-2.3 22B distilled 1.1) + **upscaleur latent ×2** (modèle 1.1), gabarit officiel `video_ltx2_3_i2v` en deux temps. Poids totaux téléchargés : **~29,6 Go**.
- **Entrée** : référence canonique 1600×1600 **complétée, jamais rognée**, sur toile blanche 1088×1920 ; `LTXVPreprocess` compression 18.
- **Étape 1** : 544×960, 73 images, `LTXVImgToVideoInplace` force 0,7, échantillonneur euler, CFG 1, sigmas distillés en 8 pas, graine 123456789.
- **Étape 2** (après upscale latent ×2) : 1088×1920, force 1,0, sigmas « 0,85, 0,7250, 0,4219, 0,0 », graine 42 ; décodage VAE en tuiles (512/64, temporel 64/8) ; **24 fps**.
- **Brief du test** : peluche orange de face, fond blanc studio, léger balancement + clignement, caméra **totalement fixe** ; négatif : cornes, crocs, déformation, morphing, texte, filigrane.

### 7.3 Mesures et limites ◆

| Mesure | Valeur |
|---|---|
| Sortie | **1088×1920**, H.264, 24 fps, 3,04 s (73 images), ~4,28 Mb/s |
| Temps de génération | **585 s pour 3,04 s** de vidéo ≈ **3,2 min par seconde** (chargement inclus) |
| VRAM de pointe | **11 679 / 12 227 Mio** (offload RAM actif — le GGUF de 16,5 Go ne tient pas en entier) |
| Porte qualité (début/milieu/fin) | Palette PASS (ΔE 4,79–4,92, couverture 95 %) ; proportions PASS (écarts 0–3 %) ; DINOv2 **0,94–0,99** ; oreilles rondes PASS ; aucun corne/croc |

- **Limite 1 — muet** ◆ : LTX-2.3 ne produit **aucune voix** (la branche audio a été retirée du test). Tout clip LTX destiné à parler passe par la voie voix du projet : brief voix master + **vérification par écoute ET par mesure de fréquence fondamentale avant publication** (NORMES-AV § 2.8).
- **Limite 2 — dérive de bouche atténuée** ◆ : au pic de mouvement (image du milieu), le sourire **s'élargit et se creuse légèrement**, commissures relevées — pas de dents, pas d'ouverture ; début et fin conformes. Verdict de la porte : bouche « à surveiller », pas un rejet.
- **Limite 3 — le mouvement obéit mal** ◆ : au lieu du balancement sur place demandé, le personnage **avance doucement vers la caméra** (bras qui s'ouvrent) puis revient presque à la pose de départ. Le brief de mouvement doit être **contraint davantage** (caméra fixe répétée, interdire l'avancée) — à valider au prochain essai ◇.
- **Limite 4 — le coût croît avec la durée** ◆ : à ~3,2 min/s, un clip de 10 s coûte plus d'une demi-heure de GPU exclusif. Réserver LTX aux clips courts qui exigent le vrai 1080.
- **Piège d'installation documenté** ◆ : le fichier d'encodeur texte Gemma (~9,4 Go) existe en miroirs **tronqués à ~5,3 Go** — vérifier la taille complète avant de conclure à une corruption du workflow.

### 7.4 Statut

La chaîne est **validée comme capable** ; le clip de test n'a **rien publié**. Verdict rendu (backlog nº 20) : le Short actuel reste en ligne tel quel, et le volet republication est **annulé définitivement depuis le 23 novembre 2026 (~21:19 EST)** — ne jamais reproposer de republication du Short, sous aucune forme ◆.

---
### 7.5 LTX-2.5 / POC B7 — chaîne d'évaluation de référence adoptée, hors production ◆

- **Statut décidé le 23 novembre 2026 (~18:49 EST)** : l'opérateur adopte le POC B7 « selon les recommandations » d'Onyx. La chaîne **LTX-2.5 au format GGUF Q4** devient donc la **chaîne d'évaluation de référence** pour cette génération du modèle.
- **Aucune entrée en production** : la génération mesurée passe la palette (ΔE 4,77–4,96, PASS ×5), mais présente des creux DINOv2 en cours de clip — témoins à **0,78 / 0,83 / 0,77**, contre **0,9846** au premier témoin. La **porte de fidélité complète n'est pas franchie**.
- **Production inchangée** : **LTX-2.3 demeure la chaîne vidéo de production**.
- **Comparaison des formats** : l'INT8 ConvRot, testé en A/B strict contre B7, n'a montré aucun avantage mesuré et était **44 % plus lent** ; il reste **non adopté** (voir `docs/ADOPTION-INT8-RELAYVSR-2026-11-23.md`).
- **Condition de promotion** : une future génération LTX-2.5 ne pourra être re-proposée à l'opérateur pour la production que si elle franchit la porte complète — **palette + DINOv2 + inspection paupières/bouche**.
- **Disque** : la chaîne GGUF LTX-2.5 (27,94 Go sur C:) est **conservée** pour l'évaluation ; les poids INT8 non adoptés relèvent de la suppression séparée déjà lancée.

### 7.6 TeaCache (POC B8) — adopté pour les brouillons LTX, production en attente ◆

- **Statut décidé le 23 novembre 2026 (~19:49 EST, appel vocal)** : TeaCache est **adopté dès maintenant pour les générations LTX de brouillon / essai / test, au seuil conservateur** (0,03–0,06 ; la mesure B-plein du POC tournait au seuil publié 0,06, cache sur CPU) — **brouillons LTX seulement**.
- **Production : pas encore.** Le verdict production est **séparé** et attend la mesure de fidélité sur les sorties du POC B8 (ΔE palette + DINOv2 + inspection paupières/bouche sur les témoins A contre B) — aucune génération de production ne passe par TeaCache tant que l'opérateur n'a pas rendu ce verdict.
- **Mesure de fidélité produite le 23 novembre 2026 (~19:57 EST)** (`tests/poc-b8-teacache/RAPPORT-FIDELITE.md`) : sur les pleins, **B passe la porte au même niveau que A** (PASS × 5 des deux côtés ; ΔE max 5,18 contre 5,39 ; DINOv2 min 0,9314 contre 0,9319 ; inspection paupières/bouche/fourrure sans signalement) ; transparence de la greffe parfaite (Bmini = A0, SSIM 1,000). **Réserve chiffrée** : pas bit-identique — divergence de trajectoire en milieu de clip (PSNR 15,14 dB / SSIM 0,860 au témoin 192). Le verdict production reste à l'opérateur sur ces chiffres.
- **Gain de temps mesuré (POC B8)** : plein 257 images — A (sans cache) 13 710,8 s (3 h 48) contre B (avec cache) 9 287,8 s (2 h 35), soit **−32 % / 1,48×** ; courts 33 images — A0 380,3 s, Bmini (seuil ~0, transparence) 350,3 s, Bmax (seuil 5,0) 100,1 s.
- **Implémentation** : paquet maison `ComfyUI-OnyxTeaCache` (le paquet publié est inutilisable sur notre ComfyUI 0.46.0 — voir `tests/poc-b8-teacache/FICHE-POC-B8.md` § 1bis) ; retrait = supprimer le dossier du paquet et redémarrer l'instance.

## 8. Pixal3D — les brouillons 3D jetables (jamais une base de production)

- **Ce que c'est** ◆ : générateur image→3D (licence **MIT** confirmée), nœuds ComfyUI natifs, mode basse VRAM (modèles int8 convrot, upsample TRELLIS à 1024 ; ~9,9 Go annoncés, mesuré compatible 12 Go). Rapport complet : `3d/test-pixal3d-2026-11-22/RAPPORT-TEST-PIXAL3D.md`.
- **Ce qu'il livre** ◆ : en **~2 minutes** par brouillon (vue unique ≈ 120 s ; 4 vues ≈ 118 s), un **GLB complet ~700 000 triangles**, textures **PBR 2048** (couleur + normale + rugosité/métal), immédiatement reconnaissable comme Onyx (oreilles rondes, yeux ovales, sourire, couture centrale, ventre rond, pas de queue).
- **La porte stricte 3D : ÉCHEC des deux brouillons** ◆ (critères : proportions ≤ 3 %, traits ≤ 2 %, couleurs charte) :
  - **Tête ≈ 50 % de la hauteur totale au lieu de 40 %** (+25 %) sur les deux brouillons ;
  - **Aucun pincement de cou mesurable** — la tête fond dans le torse ;
  - **Traits hors tolérance** : entraxe des yeux −11,2 % (vue unique) / −4,1 % (4 vues) ; hauteur d'œil −17,9 % / −3,6 % ; **bouche de la version 4 vues fausse** (double ligne « grenouille ») ;
  - **Couleurs décalées vers le jaune** : médianes #DD700A / #DD750C contre #C95D1D à la charte, **ΔE 10,3–13,5** — nettement visible ;
  - **Surface = texture photo cuite**, pas un vrai matériau tissu PBR ni de fuzz de silhouette ; couture et détails inégaux malgré les ~700 k triangles.
  - Points passés, pour être juste : largeur de tête du brouillon vue unique à +1,3 % ✓ ; placement vertical des yeux 4 vues à −1,5 % ✓.
- **Statut permanent** ◆ : Pixal3D est **validé comme générateur de brouillons jetables** — le brouillon « vue unique » est conservé comme base du **test d'armature mécanique jetable** autorisé (backlog nº 8), qui sera jeté après usage. Il est **écarté comme base directe de production** : la voie 3D reste **hybride** (base de volume + **resculpt manuel dans Blender**), derrière la porte stricte (voir le plan 3D).

---

## 9. La porte qualité automatique — le passage obligé de chaque sortie

Outil maison : `tools/qa-gate/` (documentation : son `README.md`). Principe ◆ : transformer « générer 50 images, en regarder 50 » en « générer 50 images, regarder les quelques-unes qui passent ». **Chaque** image produite par la chaîne locale (et chaque frame échantillonnée d'une vidéo) est comparée à la référence canonique **avant** d'être montrée à l'opérateur. Fonctionne hors ligne, sur CPU, ~2–3 s par image ◆.

### 9.1 Les trois contrôles ◆

1. **Palette — ΔE CIEDE2000** (déterministe). Le personnage est isolé (alpha ou soustraction du fond), aplati sur le fond blanc de la charte ; chaque pixel est rattaché à la couleur charte la plus proche (#FC8B32 / #C95D1D / #812900, noir chaud, blanc). Métriques : **ΔE médian** et **couverture conforme** (part des pixels à ΔE ≤ 10 d'un orange charte). **PASS si ΔE médian ≤ 6,5 et couverture ≥ 85 %** ; À SURVEILLER si ΔE ≤ 10 et couverture ≥ 65 % ; REJET au-delà. Calibration honnête : la référence elle-même mesure **ΔE 4,83 / couverture 96 %** — le plancher intrinsèque est ≈ 4,8, les seuils gardent une marge de ×1,35. C'est la **couverture** qui rejette les teintes décalées (un décalage de +18° de teinte la fait tomber à 7 %).
2. **Proportions — indice de silhouette** (heuristique, à lire honnêtement). Plus grande largeur de silhouette dans la zone 2 %–40 % de la hauteur, comparée aux cadrages de la charte : **buste ≈ 0,701**, **corps entier ≈ 0,466**. PASS si écart ≤ 15 %, À SURVEILLER ≤ 40 %. Ce n'est **pas** la mesure anatomique des 2,5 T : sans repères fiables (menton, crâne hors oreilles), aucun script simple ne la mesure sur une peluche. L'indice ne vaut que pour une **vue de face, personnage entier, fond uni** ; pose dynamique ou bras levés → il peut dévier sans que le personnage soit faux (faux REJET documenté, § 3.4). Masque non fiable → verdict **NON MESURABLE** plutôt qu'un chiffre inventé.
3. **Ressemblance — DINOv2-small** (embedding visuel, ONNX). Cosinus du vecteur CLS contre la référence : **PASS ≥ 0,80** ; À SURVEILLER ≥ 0,60 ; REJET en dessous. Limite assumée ◆ : un embedding global est presque **insensible à la couleur et aux petits détails** (la référence décalée de +70° de teinte obtient 0,9923 — c'est la palette qui la rejette). DINOv2 écarte vite un personnage différent ou déformé ; il ne certifie **ni les couleurs, ni les sourcils, ni la bouche**.

### 9.2 Les verdicts et la conduite à tenir ◆

- **PASS** — l'image peut être montrée. Le contrôle à l'œil reste requis pour les traits fins (bouche, sourcils) : voir § 3.3, point 3.
- **À SURVEILLER** — un contrôle est en zone limite ou non mesurable : **œil humain obligatoire** avant toute présentation comme résultat ; documenter ce que l'œil a vu.
- **REJET** — l'image ne doit **pas** être montrée comme un résultat valide. Elle est **conservée** dans le dossier du chantier (rien ne s'efface — les ratés documentés nourrissent les séries « les ratés de l'atelier » et l'apprentissage), avec la raison du rejet.
- **Codes de sortie** pour l'enchaînement en script : 0 = tout PASS, 1 = au moins un À SURVEILLER, 2 = au moins un REJET ou une erreur.
- **Pour la vidéo** ◆ : échantillonner au minimum **début / milieu / fin** du clip et passer chaque frame à la porte (patron du test LTX, § 7.3) ; le **pic de mouvement** est la frame à risque pour la bouche — l'inclure explicitement si elle diffère du milieu.

### 9.3 Ce que la porte ne fait pas ◆

Elle ne juge ni la mise en page des planches composites (contrôle à l'œil, une à une), ni les traits fins (bouche, sourcils, coutures), ni le mouvement entre les frames (stabilité vérifiée à l'œil sur les échantillons). La porte est un **pré-filtre** ; la validation d'identité finale reste : porte + œil + — pour toute publication — **l'opérateur**.

### 9.4 La composante « poil » (adoptée par l'opérateur le 23 novembre 2026) ◆

Sur proposition de la veille imagerie/vidéo (`docs/VEILLE-IMAGERIE-VIDEO-2026-11-23.md`), la porte comporte désormais une **composante « poil » permanente**, parce que le grain du minky ras (~3 mm) vit sous l'échelle de compression de nos modèles et échappe aux trois contrôles classiques : la palette moyenne les pixels, DINOv2 est insensible aux micro-textures, et un VLM est **écarté comme juge** (perception des textures mesurée trop grossière — il dirait « même peluche » là où le grain a dérivé).

- **En image** : recadrages **200 %** sur les zones de poil (oreille, joue, bord de museau) comparés à la référence, plus une mesure d'**énergie haute fréquence** du recadrage contre celle de la référence — un grain lissé (énergie effondrée) ou inventé (énergie gonflée sans correspondance) signale l'écart. Protocole et seuils : fiche `tests/poc-imagerie-video/FICHE-POC-IMAGERIE.md` (« le plancher du poil »).
- **En vidéo** : la question n'est pas la netteté mais la **stabilité temporelle du grain** — le VAE de LTX re-synthétise la texture image par image, le défaut signature est le poil qui « nage » ou scintille. Contrôle par **séries temporelles** sur les frames échantillonnées (début / milieu / fin + pic de mouvement), selon le protocole de la fiche `tests/poc-imagerie-video/FICHE-POC-VIDEO.md` (« le poil qui nage », protocole M3-génération).
- **Statut des seuils** : la composante est adoptée comme partie permanente de la porte ; ses seuils chiffrés seront calibrés par l'exécution des deux fiches POC (sur go séparé de l'opérateur) — d'ici là, tout écart visible à l'œil sur les recadrages 200 % vaut **À SURVEILLER** et bloque la présentation comme résultat.

---

## 10. Pannes connues et diagnostics, par outil

Méthode permanente ◆ : **une hypothèse à la fois, vérifiée par une mesure ou une capture** — jamais de conclusion sur la seule présence d'un processus ou d'un fichier.

| Outil | Symptôme | Cause prouvée / piste ordonnée | Correction |
|---|---|---|---|
| Chaîne entière | Génération qui « rame » ou échoue en dépassement VRAM | Deux gros modèles en concurrence, ou file non vidée (§ 1) ◆ | Vider la file, séquencer, un modèle à la fois |
| Chaîne entière | Le travail long « disparaît » sans résultat | Session SSH coupée après ~30 s ◆ | Tâches planifiées cachées (`run-hidden.vbs`), jamais de session tenue |
| ComfyUI | Le serveur répond ailleurs que sur la machine | Relance avec écoute réseau ○ | Relancer en écoute locale seulement ; ne jamais exposer |
| Qwen | Sourire épais/élargi, sourcils noirs épais | Dérive signature de l'outil (§ 3.3) ◆ | Brief verrouillé dès le 1er essai ; **un** réessai resserré ; œil obligatoire |
| Qwen | Sourcils trop pâles après réessai | Le brief resserré a sur-corrigé ◆ | Consigner la réserve pour la revue de l'opérateur ; ne pas boucler |
| Qwen | Porte : proportions REJET, personnage pourtant conforme | Pose bras levés — indice non applicable ◆ | Classer « non applicable à cette pose », mesurer à l'œil |
| See-through | Échec `CUBLAS_STATUS_ALLOC_FAILED` à l'encodage CLIP | `group_offload=True` par défaut, incompatible NF4 ◆ | Lancer **sans offload** |
| See-through | Couches nommées vides, personnage en ~3 couches | Limite prouvée sur une peluche (§ 4) ◆ | Découpe fine manuelle ; ne pas insister sur la sortie brute |
| See-through | PSD refusé/sans textures dans Cubism | Écriture binaire incompatible ◆ | Réécrire le PSD avec **ag-psd** (voir guide Cubism) |
| rembg / pipelines | Détourage « gratuit » suspect dans un outil tiers | Modèle par défaut **non commercial** (RMBG-2.0) ◆ | Forcer `-m birefnet-general` partout, y compris en interne des pipelines |
| LTX-2.3 | Workflow en erreur dès le chargement de l'encodeur texte | Fichier Gemma téléchargé **tronqué** (~5,3 Go contre ~9,4 Go) ◆ | Vérifier la taille exacte du fichier avant tout diagnostic |
| LTX-2.3 | Sourire élargi au pic de mouvement | Dérive atténuée propre à la chaîne ◆ | Porte sur la frame du pic ; verdict « à surveiller » documenté |
| LTX-2.3 | Le personnage avance au lieu de balancer sur place | Brief de mouvement insuffisamment contraint ◆ | Resserrer le brief (caméra fixe, avancée interdite) au prochain essai ◇ |
| Pixal3D | Brouillon « presque Onyx » mais tête énorme, pas de cou, couleurs jaunes | Limites prouvées du modèle (§ 8) ◆ | Ne pas corriger : statut **jetable** ; la production passe par Blender |
| Porte qualité | Tout passe, mais « elle a changé » à l'œil | La porte ne voit pas les traits fins ◆ | L'œil tranche ; consigner la réserve ; ne jamais publier sur les seuls chiffres |

---

## 11. Checklist de session de génération locale

1. ☐ Écran de Fractal libre, aucun jeu en cours ; pilotes GPU intacts (ne pas y toucher).
2. ☐ File ComfyUI vide ; un seul modèle prévu à la fois ; ordre des jobs écrit.
3. ☐ Jobs longs lancés par tâches planifiées cachées, jamais en session SSH tenue.
4. ☐ Qwen : recette 40 pas / cfg 4,0 ; brief **avec verrou bouche + sourcils** dès le premier essai.
5. ☐ Détourage : `rembg -m birefnet-general` — vérifier aussi l'intérieur des pipelines tiers.
6. ☐ Upscale : fidèle seulement — RelayVSR pour la vidéo, SeedVR2 pour les images ; aucun upscaler créatif sur le personnage.
7. ☐ Vidéo LTX : brief de mouvement contraint ; prévoir ~3,2 min de GPU par seconde de clip.
8. ☐ Chaque sortie → **porte qualité** (vidéo : début / milieu / pic / fin), puis **contrôle à l'œil** documenté.
9. ☐ Rejets conservés et étiquetés avec leur raison ; un seul réessai à brief resserré par dérive.
10. ☐ Rien n'est montré à l'opérateur sans porte + œil ; rien n'est publié sans son approbation après visionnement.

---

## 12. Documents liés

- `docs/EXPERTISE-STACK-V1.md` — l'inventaire de la stack et les fiches des autres outils.
- `docs/GUIDE-CUBISM-COMPLET.md` — le guide nº 1 (le PSD, ag-psd, le rig) ; `docs/GUIDE-OBS-COMPLET.md` — le guide nº 2 ; `docs/GUIDE-VTUBE-STUDIO-REGLAGES.md` — le guide VTube Studio.
- `docs/DECISIONS-OUTILS-2026-11-22.md` — les adoptions et règles permanentes (SAM 3/BiRefNet, SeedVR2, Qwen 2.1 écarté).
- `docs/VEILLE-MODELES-HF-2026-11-22.md` — la veille modèles (RelayVSR, successeurs, licences).
- `poses-expressions-v1/RAPPORT-PORTE.md` — la passe Qwen de référence (recette, dérive, réessai).
- `tests/ltx23-test-2026-11-22/RAPPORT-TEST-LTX23.md` — le test LTX-2.3 complet (licence, recette, mesures).
- `3d/test-pixal3d-2026-11-22/RAPPORT-TEST-PIXAL3D.md` — la porte stricte Pixal3D, mesures à l'appui.
- `docs/PHASE1-RUN-2026-11-21.md` — l'exécution See-through de la phase 1 (et sa panne d'offload).
- `tools/qa-gate/README.md` — la porte qualité : seuils, calibration, limites assumées.
- `docs/NORMES-AV-V1.md` — les normes de diffusion que les sorties vidéo doivent respecter (§ 2.7 : HD minimum ; § 2.8 : la règle voix).
