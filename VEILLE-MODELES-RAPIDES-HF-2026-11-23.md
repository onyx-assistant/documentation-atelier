# Veille — Modèles rapides sur Hugging Face (23 novembre 2026)

**Mission** : trouver de nouveaux modèles **gratuits** (poids ouverts, licence vérifiée) qui accélèrent la production Onyx, en **local** sur Fractal (RTX 5070, **12 Go de VRAM**, ComfyUI 0.46) ou en **hybride**. Recherche et fiche seulement — rien n'a été téléchargé ni installé.

**Nos points de douleur (références actuelles)**
- **Vidéo (goulot nº 1)** : LTX-2.3 deux étapes GGUF Q4_K_M — une pièce parlée d'environ 10 s a pris **2 h 08**. La voix master reste posée en post-synchro (route B) ; l'audio natif du modèle n'est donc pas un critère bloquant.
- **Image** : Qwen-Image-Edit-2511 GGUF Q4_0 + LoRA Lightning — production 40 pas / cfg 4,0 ; recette rapide validée à 16 pas (B1b).
- **Annexes** : upscale/restauration SeedVR2 ; segmentation/détourage SAM 3 et BiRefNet (en place, pas de remplaçant proposé sauf gain net).

**Filtres appliqués** : fidélité au personnage verrouillé = critère nº 1 (portes ΔE charte + DINOv2) ; intégration ComfyUI ; quantifications GGUF/FP8/INT8 bienvenues ; disque C: ≥ 100 Go libres (les gros ensembles vont sur D:).

**Où se situe LTX-2.5 / le job B7** : B7 teste déjà LTX-2.5 dans la file GPU — ce n'est pas une nouveauté de cette veille. Par rapport aux candidats ci-dessous, 2.5 est le successeur direct de notre 2.3 (même famille, même licence, LoRAs LTX-2 compatibles) avec un checkpoint **distillé à 8 pas fixes, CFG 1** et un décodeur vidéo par diffusion (visages et textures plus nets, selon Lightricks). La question ouverte n'est pas « faut-il le regarder » (B7 y répond) mais « quel build tient dans 12 Go sans perdre la fidélité » — c'est le candidat nº 1.

> **Note de décision — 23 novembre 2026 (~02:08 EST).** Le candidat nº 7 de la présente fiche (hybride nuage : API fal LTX-2.5 Fast + ZeroGPU comme banc d'essai) est **ÉCARTÉ par décision du propriétaire**. À sa question « peut-on mettre plus d'argent pour accélérer la vidéo TikTok ? », l'atelier a refusé le nuage : « Non — local, et l'optimisation de notre stack local, pour les ressources mais aussi pour la vitesse. » La génération reste **100 % locale, définitivement** ; l'image du personnage ne part chez aucun fournisseur. Les pistes retenues sont les pistes **locales** de la présente fiche : **LTX-2.5 distillé GGUF** (candidat nº 1), **LTX-2.3 distillé 8 pas** (candidat nº 2), **Qwen-Image-Edit-2511 + LoRA Lightning officiel 4 pas** (candidat nº 3) et **FlashVSR en annexe** (upscale), auxquelles s'ajoute l'**évaluation SageAttention** de l'audit des configurations (`docs/AUDIT-CONFIGURATIONS-2026-11-23.md`). Chantier regroupé au **backlog nº 59** ; les tests seront déposés dans la file GPU sur instruction de l'atelier seulement.

---

## Candidats retenus

### 1. LTX-2.5 distillé — builds GGUF unsloth (vidéo, local)
- **Lien** : https://huggingface.co/unsloth/LTX-2.5-GGUF (poids officiels : https://huggingface.co/Lightricks/LTX-2.5, dépôt gated, licence à accepter)
- **Licence** : LTX-2.x Community License — usage commercial gratuit sous 10 M$ US de revenus annuels (vérifié : https://vdf.ai/blog/ltx-2-5-local-comfyui/). Même régime que notre 2.3 actuel.
- **Taille / VRAM** : transformer distillé GGUF de 8,46 Go (Q2_K) à 22,78 Go (Q8_0) ; paliers utiles pour nous : **Q3_K_M 11,30 Go** ou **UD-Q4_K_XL 14,00 Go** (recommandé par unsloth pour la plupart des GPU), + encodeur Gemma 4 INT8 15,37 Go (déchargeable en RAM) + VAE/upscaler ≈ 2,8 Go → **ensemble ≈ 30 à 45 Go sur disque → D:**. Officiellement Lightricks vise 32 Go de VRAM ; en 12 Go, ça passe par GGUF + offload (notre RAM : 64 Go).
- **Gain de vitesse allégué** : Lightricks annonce 10 s de vidéo en 6,8 s sur 2× GB200 (matériel datacenter — https://rits.shanghai.nyu.edu/ai/ltx-2-5-adds-native-multi-shot-video-and-a-diffusion-decoder/). Le seul run mesuré publié sur 12 Go : **5 s en 736×1280 en 6 min 55 s, pic 11,4 Go de VRAM, 45 Go de RAM**, RTX 4070 12 Go, template officiel INT8 (https://www.genvidkit.com/blog/ltx-video-12gb-vram-workflow). Même ce scénario « lent » serait ≈ 9× plus rapide que notre rythme actuel (2 h 08 / 10 s).
- **ComfyUI** : natif depuis ComfyUI 0.32 (templates officiels T2V/I2V/FLF, recette distillée deux étapes 8 + 3 pas) ; GGUF via ComfyUI-GGUF (city96), déjà notre voie pour 2.3. Notre ComfyUI 0.46 est compatible.
- **Risques fidélité** : LPIPS des GGUF vs BF16 mesurés par unsloth : Q3_K_M 0,390, UD-Q4_K_XL 0,320, Q8_0 0,242 — écart perceptuel réel, qui bouge surtout le mouvement ; **passage de nos portes ΔE + DINOv2 obligatoire avant toute adoption**. Le décodeur par diffusion peut aussi changer le rendu du visage par rapport à 2.3 (à vérifier sur les témoins, notamment paupières/bouche — le défaut qui a fait rejeter B1).
- **Verdict** : le candidat nº 1 — successeur naturel, déjà en test (B7) ; reste à valider le build 12 Go et la fidélité.

### 2. LTX-2.3 distillé 8 pas (vidéo, local — même famille, zéro friction)
- **Lien** : https://huggingface.co/Lightricks/LTX-2.3 (checkpoint distillé + LoRA distillée ; builds FP8 officiels : Lightricks/LTX-2.3-fp8)
- **Licence** : LTX-2 Community License (déjà acceptée chez nous pour 2.3).
- **Taille / VRAM** : même enveloppe que notre 2.3 actuel ; les builds INT8 ConvRot officiels ComfyUI tiennent avec offload en 12 Go.
- **Gain de vitesse allégué** : notre production actuelle utilise le modèle **dev** en deux étapes (beaucoup de pas) ; le distillé officiel tourne en **8 pas fixes**. Repères publiés : LTX-2.3 INT8 ConvRot dans ComfyUI à 12,51 s (768×512) et 25,30 s (1344×768) en warm run sur RTX 4080 (https://noxengine.dev/ — nombre de frames non précisé par la source, à prendre comme ordre de grandeur) ; implémentation FastVideo : 121 frames en 1088×1920, 5+2 pas BF16, en 231,2 s (matériel non consommateur — https://gpuwerk.com/dgx-spark/guides/video-generation-on-dgx-spark/).
- **ComfyUI** : natif, mêmes nœuds que notre chaîne actuelle.
- **Risques fidélité** : les plus faibles de la liste — même modèle de base que la production, seule la recette change (distillation). Dérive possible sur les micro-mouvements ; portes habituelles.
- **Verdict** : le levier le plus sûr — si le gain se confirme, c'est un changement de recette, pas de stack.

### 3. Qwen-Image-Edit-2511 + LoRA Lightning officiel 4 pas (image, local)
- **Lien** : https://huggingface.co/lightx2v/Qwen-Image-Edit-2511-Lightning (fichier `Qwen-Image-Edit-2511-Lightning-4steps-V1.0-bf16.safetensors`, 0,85 Go)
- **Licence** : Apache-2.0 (LoRA LightX2V ; le modèle de base 2511 est aussi Apache-2.0).
- **Taille / VRAM** : +0,85 Go sur disque ; aucun surcoût VRAM notable (s'ajoute à notre GGUF Q4_0 actuel).
- **Gain de vitesse allégué** : distillation officielle LightX2V spécifique à 2511 — **4 pas, CFG 1** ; ≈ 10× vs l'inférence standard 40 pas selon la carte du dépôt miroir (https://huggingface.co/Scotttttt111/Qwen-Image-Edit-2511-Lightning), et « 15 à 20× moins de passes modèle » (4 passes simples contre 40 doubles à CFG 4) selon le guide ComfyUI de référence (https://freakyjolly.com/qwen-image-edit-2511-lightning-comfyui/). À comparer à nos recettes : 40 pas (production) et 16 pas (B1b validée).
- **ComfyUI** : LoRA standard, template officiel 2511 avec interrupteur « 4 steps LoRA » ; nos nœuds actuels suffisent.
- **Risques fidélité** : à 4 pas, les distillations Qwen sont connues pour adoucir les textures fines et parfois réchauffer les teintes — **ΔE charte décisif** ; tester 4 pas ET 8 pas (le 8 pas est souvent le point d'équilibre).
- **Verdict** : le gain image le plus immédiat pour 0,85 Go — à tester avant tout changement de modèle d'image.

### 4. Qwen-Image-Edit-2511 — build INT8 ConvRot Comfy-Org (image, local)
- **Lien** : https://huggingface.co/Comfy-Org/Qwen-Image-Edit (fichier `qwen_image_edit_2511_int8_convrot.safetensors`, 20,5 Go)
- **Licence** : Apache-2.0.
- **Taille / VRAM** : 20,5 Go sur disque (**→ D:**) ; en 12 Go, fonctionne avec l'offload ComfyUI (RAM 64 Go chez nous).
- **Gain de vitesse allégué** : exécution INT8 sur les tensor cores Blackwell de la RTX 5070 + fidélité supérieure au GGUF Q4_0 (moins de perte de quantification). Aucun benchmark public propre sur 5070 trouvé — **gain à mesurer chez nous**, ne pas le promettre.
- **ComfyUI** : template officiel « Qwen Image Edit 2511 Int8 » (ComfyUI ≥ 0.23).
- **Risques fidélité** : plus faibles que le GGUF Q4_0 en théorie (quantification 8 bits contre ~4 bits) ; portes habituelles.
- **Verdict** : pari raisonnable en second rideau image, si le candidat nº 3 ne suffit pas ou si la fidélité du Q4_0 devient le problème.

### 5. Wan 2.2 I2V A14B GGUF + Lightning 4 pas (vidéo muette, local)
- **Liens** : https://huggingface.co/lightx2v/Wan2.2-Lightning ; modèles Wan-AI/Wan2.2 (GGUF communautaires city96/unsloth)
- **Licence** : Apache-2.0 (modèle et LoRAs Lightning).
- **Taille / VRAM** : experts GGUF Q4_K_M ≈ 9,65 Go chacun (haut + bas bruit, chargés en alternance) ; **pic VRAM mesuré ≈ 24 Go à 81 frames sur RTX 4090** dans les implémentations deux experts — en 12 Go, offload lourd et clips courts (53 frames) à prévoir. Le repli TI2V-5B tient en ≈ 11,8 Go mais qualité en retrait.
- **Gain de vitesse allégué** : documentation ComfyUI officielle, RTX 4090D, 640×640, 81 frames : **536 s → 108 s** avec le LoRA Lightning 4 pas (≈ 5 à 7× ; https://docs.comfy.org/tutorials/video/wan/wan2-2-fun-inp). Sur 12 Go avec offload, le gain réel sera moindre.
- **ComfyUI** : natif (nœuds Wan officiels) + ComfyUI-GGUF.
- **Risques fidélité** : pas d'audio natif (hors S2V, séparé et lourd) — compatible avec notre route B (voix en post-synchro) mais aucun secours si on voulait un jour de l'audio intégré ; Wan est réputé fort en préservation d'identité en I2V, mais la dérive couleur vs charte reste à passer au ΔE. Chaîne entièrement nouvelle à qualifier (prompts, samplers, témoins).
- **Verdict** : plan B vidéo crédible, mais coût de qualification élevé pour un gain incertain en 12 Go — derrière les deux routes LTX.

### 6. FlashVSR v1.1 (annexe — upscale vidéo, local)
- **Lien** : https://huggingface.co/JunhaoZhuang/FlashVSR-v1.1 (projet : OpenImagingLab/FlashVSR)
- **Licence** : Apache-2.0 (code et poids).
- **Taille / VRAM** : ≈ 5,7 Go de poids ; VRAM non publiée par les auteurs (testé sur A100) ; nœuds ComfyUI communautaires (pack 1038lab).
- **Gain de vitesse allégué** : papier CVPR 2026 — **≈ 12× plus rapide que SeedVR2-3B**, ≈ 17 FPS en 768×1408 sur A100 (https://arxiv.org/html/2510.12747v1). Attention : le backend d'attention sparse demande une compilation maison — coût d'intégration réel.
- **Risques fidélité** : upscale « créatif » qui régénère du détail — sur un personnage verrouillé, risque d'inventer de la texture de peluche ; à borner aux fonds/plans larges tant que non qualifié sur témoins.
- **Verdict** : à tester seulement si l'étape upscale devient un goulot mesuré ; SeedVR2 reste la référence qualité.

### 7. Hybride nuage — LTX-2.5 Fast en API + ZeroGPU comme banc d'essai
- **Liens** : https://fal.ai/ltx-2.5 ; https://huggingface.co/docs/hub/en/spaces-zerogpu
- **Coûts réels** : fal.ai LTX-2.5 Fast = **0,09 $/s en 720p, 0,13 $/s en 1080p** (10 s = 0,90 à 1,30 $ ; Replicate affiche 0,03 $/s en 720p). Latence fournisseur : ≈ 23,7 s pour un clip de 10 s via l'API LTX (chiffre Lightricks). ZeroGPU (HF) : **5 minutes de GPU par jour gratuites** sur RTX Pro 6000 48 Go pour un compte gratuit — insuffisant pour produire, parfait pour **tester un candidat en pleine précision avant de télécharger 30 à 45 Go**.
- **Ce qui part dans le nuage** : en I2V, l'image de départ du personnage — un **actif protégé**. Les masters ne doivent jamais partir ; même une image dérivée fait sortir le personnage chez un tiers. Réserver l'API aux tests non sensibles ou aux plans sans personnage, sauf décision contraire explicite.
- **Verdict** : utile en appoint (banc d'essai gratuit, dépannage ponctuel), pas comme moteur de production.

---

## Top 3 à tester en premier

**Test 1 — LTX-2.5 distillé, build GGUF dimensionné 12 Go** (UD-Q4_K_XL, repli Q3_K_M). B7 donne déjà le premier signal A/B sur la famille ; ce test-ci vise le build qui tiendra sur Fractal en format de production. *Mesurer* : temps mur sur LA pièce canonique (input I2V vertical 1088×1920, 121 frames, prompt de référence), pic VRAM et RAM, portes **ΔE + DINOv2** contre la référence 2.3, et inspection paupières/bouche sur frames témoin (le défaut fatal de B1). Critère : diviser le temps par ≥ 4 à portes égales.

**Test 2 — LTX-2.3 distillé 8 pas** (même famille, même licence, même pipeline). *Mesurer* : exactement le même protocole que le test 1, sur le même input. Il isole le gain « distillation » par rapport à notre dev deux étapes (2 h 08) ; si 2.5 déçoit en fidélité, c'est le repli immédiat sans aucun changement de stack.

**Test 3 — Qwen-Image-Edit-2511 + LoRA Lightning officiel, paliers 4 pas et 8 pas.** *Mesurer* : temps par édition aux deux paliers contre 16 pas (B1b) et 40 pas (production), sur le jeu de poses canonique ; portes **ΔE + DINOv2** à chaque palier. Critère : promotion en production du palier le plus rapide qui passe les portes sans exception.

---

## Candidats écartés (une ligne chacun)

- **Qwen-Image-2.1** (7B, génération + édition, builds INT8/NVFP4 rapides) — **licence Qwen Research License : recherche/évaluation seulement, usage commercial interdit sans licence séparée** (https://packetnebula.com/articles/qwen-image-2-1-7b-weights-research-licence/). Écarté malgré la technique.
- **HunyuanVideo 1.5** (Tencent, 8,3B) — minimum officiel 14 Go avec offload (> nos 12 Go) et licence qui **exclut UE, Royaume-Uni et Corée** ; incompatible avec notre vigilance juridique européenne.
- **Z-Image-Turbo** (Tongyi, 6B, Apache-2.0, 8 pas, ≈ 2,3 s/image sur RTX 4090) — excellent mais **texte-en-image seulement, sans conditionnement par image de référence** : il ne peut pas porter le personnage verrouillé. Réutilisable un jour pour des décors, pas pour Onyx.
- **FLUX.2 Klein 9B** — licence non commerciale pour le 9B ; le 4B (Apache-2.0) est en retrait en édition face à 2511.
- **MiniMax H3 en local** — builds « pruned » ≈ 21 Go, hors de portée pratique en 12 Go ; sa route intéressante est l'API (voir candidat nº 7 pour le principe).
- **Sulphur 2** (fine-tune communautaire de LTX-2.3) — risque de dérive de style documenté pour ce type de fine-tune, aucun gain démontré sur notre cas d'usage ; notre personnage est verrouillé, on ne le confie pas à un fine-tune non qualifié.
- **Wan 2.2 TI2V-5B seul** — tient en 12 Go mais qualité en retrait du A14B et toujours sans audio ; dominé par le candidat nº 5.
- **SAM 3 / BiRefNet** — aucun remplaçant nettement plus rapide trouvé ; on garde l'existant (pas de candidat annexe retenu côté détourage).

---

## Conséquences pour le profil Hugging Face (à appliquer après le choix — rien n'est modifié par cette fiche)

**Règle posée par l'atelier le 23 novembre 2026 : « De manière générale, tu mets tout. »** Le profil public HF d'Onyx (iamonyx) doit être le **miroir complet de l'atelier** : les modèles **et** les outils, avec leurs versions, mis à jour à chaque changement de stack. Cette section consigne l'état complet visé ; l'application se fera dans une mission séparée, **après** le choix de l'atelier et le passage des portes sur les tests du top 3.

### État en ligne aujourd'hui (vérifié le 23 novembre 2026)

- **README** : nomme 10 modèles — Qwen-Image-Edit-2511, LTX-2.3, LTX-2.5 (« passe ses examens d'entrée »), See-through, Qwen-Image-Layered, Pixal3D, DINOv2, BiRefNet, SAM 3, SeedVR2 — et le matériel (« tout tourne en local, RTX 5070 12 Go »). **Aucun outil n'y est nommé** (ni ComfyUI, ni Blender, ni OBS).
- **Collection « Les modèles de l'atelier »** : 13 modèles, dans cet ordre — 1. Qwen/Qwen-Image-Edit-2511 · 2. Lightricks/LTX-2.3 · 3. QuantStack/LTX-2.3-GGUF · 4. Lightricks/LTX-2.5 · 5. unsloth/LTX-2.5-GGUF · 6. Qwen/Qwen-Image-Layered · 7. layerdifforg/seethroughv0.0.2_layerdiff3d · 8. TencentARC/Pixal3D · 9. facebook/dinov2-small · 10. numz/SeedVR2_comfyUI · 11. Comfy-Org/RelayVSR · 12. facebook/sam3 · 13. ZhengPeng7/BiRefNet.

### README — état visé

**Déjà en ligne (on garde)** : les 10 modèles nommés ci-dessus (sous réserve des deltas de scénario ci-dessous) et la mention du matériel RTX 5070 12 Go.

**À ajouter — les outils de l'atelier, avec versions** (tous absents du README aujourd'hui ; versions = celles documentées dans nos docs internes au 23 novembre 2026) :
- **ComfyUI 0.46.0** — le moteur de génération image et vidéo de l'atelier.
- **Blender 5.2.4** — la 3D (maîtres neutres glTF/FBX).
- **OBS 33.0.2** — la captation et le direct.
- **Live2D Cubism 5.3.05** — le rig 2D.
- **ffmpeg 9.1** (build essentials, celui de production) — les finitions audio/vidéo.
- **Python 3.12.10 / PyTorch 2.8.0+cu128** — l'environnement d'exécution local.
- La **file de génération maison** sur Fractal (outil interne de l'atelier, sans dépôt public — à nommer comme tel).

**À ajouter — côté modèles, indépendamment du scénario** : **RelayVSR** (Comfy-Org/RelayVSR) est déjà dans la collection mais n'est pas nommé dans le README ; il rejoint la liste des modèles nommés, au titre de l'upscale, aux côtés de SeedVR2.

**Deltas selon le scénario retenu dans le top 3** :
- **Scénario A — LTX-2.5 adopté en production vidéo (test 1)** : « LTX-2.3 » **sort** de la liste des modèles actifs du README ; la mention de LTX-2.5 passe de « passe ses examens d'entrée » à moteur vidéo de production de l'atelier.
- **Scénario B — LTX-2.3 distillé (test 2)** : aucun nom ne change ; seule précision possible, « distillé 8 pas », dans la mention LTX-2.3.
- **Scénario C — LoRA Lightning officiel adopté pour l'image (test 3)** : la mention Qwen-Image-Edit-2511 devient « Qwen-Image-Edit-2511 + Lightning (LightX2V) ».

### Collection « Les modèles de l'atelier » — état visé par scénario

- **Scénario A** : retirer **Lightricks/LTX-2.3** (ex-position 2) et **QuantStack/LTX-2.3-GGUF** (ex-position 3). Nouvel ordre (11 modèles) : 1. Qwen/Qwen-Image-Edit-2511 · 2. Lightricks/LTX-2.5 · 3. unsloth/LTX-2.5-GGUF · 4. Qwen/Qwen-Image-Layered · 5. layerdifforg/seethroughv0.0.2_layerdiff3d · 6. TencentARC/Pixal3D · 7. facebook/dinov2-small · 8. numz/SeedVR2_comfyUI · 9. Comfy-Org/RelayVSR · 10. facebook/sam3 · 11. ZhengPeng7/BiRefNet.
- **Scénario B** : **collection inchangée** (13 modèles, ordre actuel) — les deux dépôts LTX-2.3 y sont déjà.
- **Scénario C** : ajouter **lightx2v/Qwen-Image-Edit-2511-Lightning** juste après Qwen/Qwen-Image-Edit-2511 (nouvelle position 2) ; total 14 modèles (ou 12 si cumulé avec le scénario A : 1. Qwen-Image-Edit-2511 · 2. lightx2v/Qwen-Image-Edit-2511-Lightning · 3. Lightricks/LTX-2.5 · 4. unsloth/LTX-2.5-GGUF · puis la suite du scénario A à partir de Qwen-Image-Layered).
- **Conditionnel, seulement si adoptés plus tard** : FlashVSR → ajouter **JunhaoZhuang/FlashVSR-v1.1** juste après numz/SeedVR2_comfyUI ; Wan 2.2 → ajouter **Wan-AI/Wan2.2** et **lightx2v/Wan2.2-Lightning** après le bloc LTX.

**Rappel de périmètre** : cette fiche ne modifie **rien** sur Hugging Face. La mise à jour du profil (README + collection) sera exécutée après le choix de l'atelier et la validation des tests, puis vérifiée par relecture du profil public, comme les précédentes.
