# État de la stack — page de référence unique

_Dernière vérification : **24 novembre 2026** (~03:25 EST), par mesure directe sur Fractal (SSH, lecture seule) pendant la passe d'alignement post-nettoyage. Cette page est la **référence unique de l'état courant** : modèles, outils, versions et emplacements, tels que mesurés — jamais recopiés d'un ancien texte. À remettre à jour à chaque nettoyage ou changement de stack (règle permanente d'alignement)._

## Machine

- **Fractal** — Windows 11 (EN), session Windows de l'opérateur ; RTX 5070, **12 227 Mio de VRAM**, 64 Go de RAM.
- **Disques mesurés le 24 nov. 2026** : **C: 291,72 Go libres** · **D: 375,03 Go libres**. Cible C: ≥ 100 Go libres (plancher absolu 80 Go) — largement respectée.

## Chaînes de production (sur C:, dans `ComfyUI\models\`)

| Élément | Format / version mesurée | Rôle |
|---|---|---|
| **Qwen-Image-Edit-2511** | GGUF Q4_0 (11,04 Go) + encodeur Qwen 2.5 VL 7B fp8 (8,74 Go) + VAE Qwen (0,24 Go) | **Production image** (champion) |
| **LTX-2.3** | GGUF Q4_K_M (16,54 Go) + Gemma 3 12B fp4 (8,80 Go) + VAE vidéo (1,35 Go) + connecteurs (2,15 Go) | **Production vidéo** |
| **RelayVSR** | INT8 ConvRot (4,81 Go) + Flash VAE fp32 (0,12 Go) | **Upscale vidéo de référence** (adopté le 23 nov. 2026) |
| **SeedVR2** | GGUF Q8_0 (3,41 Go) + VAE EMA fp16 (0,47 Go) | **Masters image seulement** — jamais d'upscale vidéo de production |
| **SAM 3 + BiRefNet** | `sam3.safetensors` (3,21 Go) ; BiRefNet via le nœud ComfyUI-RMBG | Segmentation et détourage de production (règle : `rembg -m birefnet-general`) |
| **TeaCache** | Nœud maison `ComfyUI-OnyxTeaCache` | **Brouillons LTX seulement** (seuil 0,03–0,06) ; verdict production en attente |
| **faster-whisper** | modèle `small` | Sous-titres locaux (avec relecture) |

Nœuds ComfyUI actifs mesurés : `ComfyUI-GGUF`, `ComfyUI-OnyxTeaCache`, `ComfyUI-RMBG`, `ComfyUI-SAM3`, `ComfyUI-SeedVR2_VideoUpscaler`. Aucun autre.

## Chaîne d'évaluation (sur C:, hors production)

| Élément | Format / version mesurée | Statut |
|---|---|---|
| **LTX-2.5** | GGUF Q4_K_XL (13,04 Go) + Q5_K_XL (14,90 Go) + Gemma 4 12B bf16 (24,46 Go) + VAE vidéo ×2 (1,37 + 1,35 Go) + VAE audio (0,34 Go) + encodeur Gemma 4 E2B INT8 (4,84 Go) | **Évaluation seulement** (POC B7 = chaîne d'évaluation de référence) — **n'entre pas en production** |

## Outils

| Outil | Version mesurée | Note |
|---|---|---|
| ComfyUI | **0.46.0** | 127.0.0.1:8188 seulement ; venv Python 3.12.10, torch 2.8.0+cu128 |
| File GPU maison | — | Tâche `OnyxFileGPU` (cycle 3 min) ; gardes : file ComfyUI vide, VRAM < 6 000 Mio, C: ≥ 100 Go |
| Blender | **5.2.4 LTS** (portable) | Voie 3D |
| OBS Studio | **33.0.2** | Scènes « Onyx » et scène personnelle de l'opérateur |
| Live2D Cubism | 5.3.05 | Voie live 2D (en attente derrière la 3D) |
| VTube Studio | 1.35.10 | Idem |
| ffmpeg | essentials + full 9.1 (`libsvtav1`) | Encodage : NVENC AV1 (YouTube/web), H.264 piloté VMAF (Instagram/TikTok) |
| Jellyfin | 12.2.0 | **Installation personnelle préexistante de l'opérateur — hors projet.** Serveur en marche au contrôle du 24 nov. ; aucune relance automatique (la tâche projet a été supprimée le 23 nov. à la demande de l'opérateur) |

## Délesté sur D: (`D:\Onyx-Stockage\modeles-delestes\`) — réinstallable par recopie

| Jeu | Contenu | Taille |
|---|---|---|
| `wan22\` | Chaîne Wan 2.2 complète (2 experts GGUF Q5_K_M, umt5-xxl Q8_0, clip_vision_h, VAE Wan 2.1) — **Wan 2.2 NON adopté** | 27,14 Go |
| `pixal3d\` | Jeu Pixal3D complet (modèles INT8, VAE Trellis 2, encodeur DINOv3) — brouillons jetables seulement | ~12,30 Go |
| `sam3\` | SAM 3.1 multiplex (suivi vidéo, jamais utilisé) | 1,63 Go |
| `qwen-image-layered\` | Copie du GGUF Qwen-Image-Layered (délestage C1) | 11,04 Go |

## Retiré définitivement (n'existe plus sur la machine)

- **FireRed-Image-Edit-1.1** — non adopté ; archive D: supprimée au palier S3 du 23 nov. 2026 (re-téléchargeable).
- **Poids INT8 ConvRot LTX-2.5** — non adoptés, supprimés le 23 nov. 2026 (34,34 Go sur D:).
- **See-through** (application + poids + nœud ComfyUI) — désinstallé le 23 nov. 2026.
- **Shotcut 26.11.15** — désinstallé le 23 nov. 2026 ; **veadotube mini** — retiré le même soir.
- **Archives d'installation ComfyUI** sur D: — supprimées le 23 nov. 2026 (31,06 Go).
- **Street Fighter 6** — désinstallé le 23 nov. 2026 (jeu personnel ; reste dans sa bibliothèque Steam, sauvegardes intactes).

## Interdits et règles en vigueur

- **NVFP4 banni** dans toute la stack.
- Tout upscaleur est **suspect par défaut** : contrôle « fourrure inventée » avant tout usage sur le personnage (guide de la chaîne § 6) ; composante « poil » de la porte de fidélité (§ 9.4).
- Un seul gros modèle / une seule charge lourde à la fois sur Fractal.
