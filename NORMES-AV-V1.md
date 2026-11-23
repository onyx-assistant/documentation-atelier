# Normes audio/vidéo du projet Onyx — v1

_Créé le 22 novembre 2026, à la demande de l'atelier (« est-ce qu'on est au point là-dessus ? »). Réponse honnête qui motive ce document : **non, pas encore** — le style des sous-titres et les dimensions par plateforme étaient codifiés dans la charte, et deux règles d'encodage vivaient dans le plan, mais il n'existait aucune norme technique audio/vidéo unique. Ce document la crée, en v1 assumée._

**Statuts utilisés dans tout le document :**

- **[DÉCIDÉ]** — déjà tranché par l'atelier ou déjà en vigueur dans un document de référence (charte, plan, audit). Ce document le reprend sans le modifier.
- **[V1 — À VALIDER]** — proposition d'Onyx, cohérente avec le matériel et les plateformes, qui devient la norme seulement après validation de l'atelier (revue hebdomadaire).

**Matériel et logiciels de référence :** PC « Fractal » (RTX 5070 12 Go, NVENC de dernière génération : H.264, HEVC et AV1 en encodage matériel) ; OBS Studio 33.0.2 **[DÉCIDÉ : on garde la version qui fonctionne]** ; ffmpeg « essentials » 9.1 installé sur Fractal dans `C:\Users\<utilisateur>\ffmpeg-x\ffmpeg-9.1-essentials_build\bin\` (chemin vérifié le 21 novembre 2026 ; les commandes du § 5 sont écrites en syntaxe ffmpeg générique compatible avec cette version — les options exactes d'encodeurs d'un build « essentials » peuvent varier, voir la règle de vérification au § 5.4) ; chaîne audio du salon : DAC Schiit Modi 5, format par défaut Windows **48 000 Hz / 24 bits** (audit audio du 21 novembre 2026).

---

## 1. Formats maîtres (archive) et formats de diffusion

Le principe qui gouverne tout le reste : **on archive un maître, on diffuse des copies.** Aucun fichier de diffusion n'est jamais une source.

### 1.1 Maître vidéo **[V1 — À VALIDER]**

| Élément | Norme v1 proposée |
|---|---|
| Conteneur | **MKV** (Matroska) |
| Vidéo | **FFV1 niveau 3, sans perte**, espace couleur et résolution d'origine, cadence d'origine |
| Audio | **PCM (WAV) 48 kHz / 24 bits**, canaux d'origine (stéréo par défaut) |
| Sous-titres | piste SRT française muxée **en plus** du fichier `.srt` séparé (§ 4) |
| Nom | même nom que le projet, suffixe `-master.mkv` |

Pourquoi FFV1/MKV : sans perte réelle (pas « quasi »), libre, lisible par ffmpeg partout, conçu pour l'archivage. Alternative acceptée si un outil de montage l'exige : ProRes 422 HQ dans MOV — mais notre chaîne est ffmpeg/Windows, donc FFV1 est le choix par défaut v1.

**Exception pratique [V1 — À VALIDER] :** les enregistrements OBS longs (tests, appels) peuvent être archivés directement dans leur format d'enregistrement (§ 2.2, NVENC AV1 haute qualité) sans passage FFV1 — le sans-perte intégral de tout serait disproportionné en volume. Le FFV1 s'impose pour les **livrables finis** (vidéos publiées, sting, habillage).

### 1.2 Maître audio seul **[V1 — À VALIDER]**

WAV PCM **48 kHz / 24 bits** (aligné sur le format par défaut du Modi 5 et sur toute la chaîne vidéo, § 3). Aucun MP3/AAC n'est jamais un maître.

### 1.3 Formats de diffusion

Ce sont des **produits dérivés**, régénérés depuis le maître à chaque besoin : MP4/H.264 ou AV1 pour les plateformes (§ 2), WebM pour le web (§ 2.5). Ils vivent dans `exports/` ou le dossier du livrable, jamais dans `00_canonical/`.

---

## 2. Matrice de sélection des codecs par usage

### 2.1 Appels live (caméra virtuelle OBS) **[DÉCIDÉ]**

- La caméra virtuelle OBS **n'utilise pas NVENC** : ce sont les applications d'appel (Messenger, Meet, etc.) qui encodent elles-mêmes, à ~720p–1080p.
- Canvas OBS pour les appels : **720p30 ou 1080p30**. Rendre plus haut serait gaspillé ; l'art plat du personnage se compresse très bien à bas débit et paraît net en appel.
- Aucun réglage d'encodeur à faire de notre côté pour ce cas : toute l'attention va à la source (rendu propre, fond, cadrage).

### 2.2 Enregistrement OBS (local, sur Fractal) **[V1 — À VALIDER, base DÉCIDÉE : NVENC AV1 réservé à ce cas]**

| Réglage | Valeur v1 |
|---|---|
| Encodeur | **NVENC AV1** (RTX 5070) ; repli **NVENC HEVC** si un outil de la chaîne ne lit pas l'AV1 |
| Conteneur | **MKV** (résiste à une coupure ; remuxage MP4 ensuite si besoin, sans ré-encodage) |
| Qualité | mode qualité constante, CQ ≈ 16–20 (quasi sans perte visuelle pour de l'art plat) |
| Audio | AAC 320 kb/s ou PCM si la version d'OBS le permet, **48 kHz** |
| Cadence | celle du canvas (30 ou 60 selon le projet) |

### 2.3 YouTube (téléversement) **[V1 — À VALIDER]**

YouTube ré-encode tout ; on lui donne donc un fichier « généreux », pas un fichier déjà optimisé pour le poids.

| Élément | Valeur v1 |
|---|---|
| Conteneur | MP4 |
| Vidéo | H.264 High Profile **ou** AV1 (accepté par YouTube) ; débit élevé : 1080p → 35–45 Mb/s en H.264 (ou CQ ≈ 18 en AV1) ; 9:16 Shorts : mêmes règles, 1080 × 1920 |
| Audio | AAC-LC **320 kb/s**, 48 kHz, loudness § 3.2 |
| Sous-titres | fichier SRT français téléversé séparément (§ 4) — jamais incrustés comme seule version |

### 2.4 Instagram et TikTok **[V1 — À VALIDER]**

Ici la **compatibilité prime** : ces plateformes sont plus strictes et ré-encodent agressivement.

| Élément | Valeur v1 |
|---|---|
| Conteneur | MP4 |
| Vidéo | **H.264 uniquement** (pas d'AV1/HEVC en source : risque de rejet ou de conversion dégradée), profil High, espace couleur Rec.709, SDR |
| Débit | 1080p : 12–20 Mb/s ; vertical 1080 × 1920 : idem |
| Cadence | 30 ips de préférence (60 accepté, souvent ramené à 30) |
| Audio | AAC **320 kb/s max** (les plateformes plafonnent souvent plus bas), 48 kHz |
| Dimensions | gabarits de la charte § 15 **[DÉCIDÉ]** (4:5 = 1080 × 1350 ; 9:16 = 1080 × 1920) |

### 2.5 Web (nos pages) **[V1 — À VALIDER]**

| Élément | Valeur v1 |
|---|---|
| Principal | **WebM — AV1** (ou VP9 en repli d'encodage), débit adapté à la taille d'affichage |
| Repli obligatoire | **MP4 — H.264** (Safari anciens, compatibilité totale) |
| Audio | AAC 128–160 kb/s (MP4) / Opus 96–128 kb/s (WebM), 48 kHz |
| Usage | vidéos courtes en boucle, muettes par défaut (`muted`, `playsinline`) quand c'est de l'habillage |

### 2.6 Ce qu'on n'utilise pas, et pourquoi **[V1 — À VALIDER]**

- **ProRes/DNx en diffusion** : poids inutile, plateformes non prévues pour.
- **VP8, MPEG-2, WMV, FLV** : formats d'une autre époque, aucun usage dans notre chaîne.
- **HEVC en diffusion web/plateformes sociales** : support inégal (licences, Safari, décodage matériel variable) — il reste un **repli d'enregistrement local** (§ 2.2), pas un format de diffusion.

### 2.7 Résolution de publication — HD minimum **[DÉCIDÉ — exigence de l'atelier, 22 novembre 2026]**

- **Jamais de SD publié.** Toute vidéo publiée sur une plateforme du projet est au minimum en HD.
- **Vertical (9:16)** : cible **1080 × 1920** ; plancher **720 × 1280** — en dessous du plancher : non conforme, à corriger ou à ne pas publier.
- **Horizontal (16:9)** : cible **1920 × 1080** ; plancher 1280 × 720.
- La conformité se juge d'abord sur le **fichier envoyé** (une plateforme ne peut jamais servir plus que la source reçue), puis se vérifie sur le **servi** après publication. Un lecteur qui affiche « SD » par défaut (qualité adaptative) ne prouve pas une publication SD : on vérifie les formats réellement offerts par la plateforme avant de conclure.
- La résolution ne suffit pas : le fichier envoyé respecte aussi les **débits** des § 2.3 (YouTube) et § 2.4 (Instagram/TikTok) — un 720p envoyé à ~2,7 Mb/s plafonne le servi, même si la résolution est au plancher.
- **État constaté le 22 novembre 2026 (mesures ffprobe / yt-dlp, pas des impressions)** : le Short YouTube et le highlight Instagram sont servis jusqu'à **720 × 1280** — conformes au plancher, **sous la cible**. Cause unique : les sources ont été **générées en 720 × 1280** (~2,7 Mb/s) ; les plateformes servent 100 % de la source, ce n'est pas un plafonnement de leur côté. Options de correction et recommandation : rapport du 22 novembre 2026, suivi au **backlog nº 20** — aucune republication sans la décision explicite de l'atelier.

---

## 2.8 Voix des vidéos d'Onyx — brief « master » masculin **[DÉCIDÉ — règle renforcée par l'atelier, 22 novembre 2026]**

Toute vidéo où Onyx parle utilise le brief de la **voix nº 1 « la master »** : **timbre masculin chaud et résonant, un peu plus grave, léger grain, ton joyeux, accent québécois, débit légèrement plus rapide, phrases courtes**. **Jamais une voix féminine.** Raison de la règle renforcée : le Reel « Demain, on me branche » publié le 22 novembre 2026 parlait avec une voix féminine (le brief du forfait prescrivait par erreur une voix féminine, héritée d'un ancien brief de story) — publication retirée pour refonte. **Contrôle obligatoire avant toute publication** : vérifier la voix **à l'écoute** et, quand c'est possible, par mesure de la fondamentale (référence : version fautive ≈ 246 Hz médians ; version corrigée ≈ 137 Hz médians). Un brief écrit seul ne suffit pas : le générateur re-synthétise la voix, donc le contrôle porte sur le fichier produit. **Outil de mesure** : `tools/verificateur-voix/verifier-voix.py` (même méthode que le POC B1 ; bande de référence 123–151 Hz, référence 137 Hz ; verdict DANS LA BANDE / HORS BANDE — la mesure accompagne l'écoute, elle ne la remplace pas).

## 2.9 Étiquetage IA natif des plateformes **[DÉCIDÉ — veille technologique du 22 novembre 2026 : ADOPTER]**

À chaque téléversement, **en plus** de la divulgation textuelle du projet (« personnage virtuel créé avec l'IA »), on coche l'étiquette IA **native** de la plateforme : **YouTube** (Studio → Attributs → Utilisation de l'IA : Oui), **TikTok** (interrupteur « contenu généré par IA » du créateur — **irréversible après publication, donc à cocher avant**), **Instagram** (auto-déclaration « AI info » quand elle est proposée à l'envoi). Les trois plateformes peuvent aussi auto-étiqueter via les métadonnées **C2PA** et leur détection interne : chercher à éviter l'étiquette est à la fois vain et contraire à la posture de transparence du projet — YouTube confirme qu'elle ne limite ni l'audience ni l'éligibilité à la monétisation. Cadre réglementaire : **AI Act, art. 50** — obligations de transparence applicables depuis le 2 août 2026, et marquage lisible par machine des contenus synthétiques avec échéance au **2 décembre 2026** pour les systèmes déjà sur le marché ; **Loi 24 du Québec** — en vigueur depuis le 12 juin 2026, consentement obligatoire pour l'usage commercial de l'identité, de l'image et de la voix d'une personne. La case opérationnelle vit dans la fiche de contrôle pré-publication (§ 3).

## 3. Normes audio

### 3.1 Chaîne et échantillonnage **[DÉCIDÉ pour le 48 kHz, V1 pour le reste]**

- **48 kHz partout**, du maître au fichier de diffusion : c'est la norme vidéo universelle et le format par défaut constaté sur le Modi 5 lors de l'audit (48 000 Hz / 24 bits). Tout contenu 44,1 kHz (musique) est converti **une seule fois, à la production**, jamais en cascade.
- Profondeur de travail : 24 bits (maîtres et production) ; 16 bits acceptés seulement en diffusion si un format l'impose.
- Stéréo par défaut ; aucun upmix artificiel d'une source mono — la voix reste centrée, telle quelle.

### 3.2 Loudness et crêtes **[V1 — À VALIDER]**

| Cible | Valeur v1 |
|---|---|
| Loudness intégré, plateformes (YouTube, Instagram, TikTok) | **−14 LUFS** intégré |
| Crête vraie maximale (true peak) | **−1 dBTP** |
| Podcasts / audio seul éventuel | −16 LUFS intégré, −1 dBTP (à confirmer si ce format naît un jour) |

Les plateformes normalisent vers ~−14 LUFS : livrer plus fort ne donne aucun avantage, elles baisseront le niveau ; livrer moins fort fait paraître la vidéo faible à côté des autres. La mesure se fait avec `loudnorm` de ffmpeg (§ 5.3) **avant** l'export final.

### 3.3 Débits audio de diffusion **[V1 — À VALIDER]**

- AAC-LC : **320 kb/s** pour YouTube et les fichiers archivés-diffusés ; **160–256 kb/s** acceptables pour Instagram/TikTok (plafonds plateforme) ; **128–160 kb/s** pour le web.
- Aucune diffusion en dessous de 128 kb/s pour du contenu avec musique.

---

## 4. Sous-titres

### 4.1 Style visuel **[DÉCIDÉ — charte § 13.2, repris tel quel]**

- Police **Nunito SemiBold (600)** ; corps ≈ **4,5 % de la hauteur d'image** (1080p → ≈ 48 px ; 720p → ≈ 32 px), interligne 1,25.
- Texte blanc chaud `#FFF3E6` sur fond noir chaud semi-transparent (75 % d'opacité), coins légèrement arrondis.
- **2 lignes maximum, ≈ 42 caractères par ligne.**
- Placement bas-centre, dans la zone sous-titres du gabarit du format (charte § 15) — jamais dans une zone d'interface de la plateforme.
- Rythme : un sous-titre par phrase ou par souffle ; **aucun sous-titre de moins de 0,8 s** à l'écran.
- **Français seulement** : les sous-titres français tiennent lieu de version pour les autres langues ; on ne produit pas de versions traduites. Ponctuation française (espaces insécables, guillemets « »).

### 4.2 Formats de fichiers **[V1 — À VALIDER]**

| Rôle | Format |
|---|---|
| **Maître** | **SRT** (UTF-8), un fichier par vidéo, nommé comme la vidéo (`nom.srt`), conservé avec le maître |
| Web (nos pages) | **WebVTT**, converti depuis le SRT maître (§ 5.3) |
| YouTube | SRT téléversé dans YouTube Studio (piste française) |
| Réseaux sociaux | **incrustés** dans l'image selon § 4.1 quand la plateforme lit mal les pistes séparées ou que la lecture se fait sans son (Instagram/TikTok) — **en plus** du SRT maître archivé, jamais à sa place |

### 4.3 Règles de segmentation **[V1 — À VALIDER, base DÉCIDÉE : § 4.1]**

- Coupure aux frontières de phrase ou de souffle, jamais au milieu d'un groupe de mots.
- Durée : minimum **0,8 s [DÉCIDÉ]**, cible 1 à 5 s, maximum 7 s ; vitesse de lecture visée ≤ ~20 caractères/seconde.
- Aucun sous-titre ne survit à un changement de plan marqué si la phrase ne le traverse pas.
- Noms propres et expressions signatures du personnage (« Coucou, c'est Onyx ! », « Une couche à la fois. », « Toujours moi. ») écrits exactement comme au script du personnage.

---

## 5. Conversion, transcodage, décompression : la chaîne ffmpeg de référence

### 5.1 Règles d'or **[V1 — À VALIDER]**

1. **Jamais ré-encoder un fichier de diffusion.** Toute nouvelle version repart du **maître** (§ 1). Un MP4 Instagram ne devient jamais la source d'un MP4 YouTube.
2. **Copie de flux quand c'est possible** : changer de conteneur ou extraire un flux = `-c copy`, sans ré-encodage (remuxage MKV → MP4, extraction audio, etc.).
3. **Une génération à la fois** : chaque transcodage est compté ; si un livrable a subi deux ré-encodages successifs, on repart du maître.
4. **Décompression = retour au maître** : pour retravailler un fichier dont on n'a que la version de diffusion, on le décode en WAV/FFV1 de travail en sachant que la qualité perdue ne reviendra pas — et on le note dans le dossier du projet.
5. Les originaux ne sont jamais écrasés : toute conversion écrit un **nouveau fichier** (règle déjà en vigueur pour les montages sur Fractal).

### 5.2 Commandes types **[V1 — À VALIDER]**

Syntaxe générique ffmpeg (le binaire de Fractal : `C:\Users\<utilisateur>\ffmpeg-x\ffmpeg-9.1-essentials_build\bin\ffmpeg.exe`, avec `ffprobe.exe` au même endroit).

```bash
# Créer le maître d'archive depuis un montage fini
ffmpeg -i montage-fini.mp4 -c:v ffv1 -level 3 -c:a pcm_s24le nom-master.mkv

# Diffusion YouTube depuis le maître
ffmpeg -i nom-master.mkv -c:v libx264 -profile:v high -crf 17 -pix_fmt yuv420p \
       -c:a aac -b:a 320k -ar 48000 nom-youtube.mp4

# Diffusion Instagram/TikTok depuis le maître (compatibilité stricte)
ffmpeg -i nom-master.mkv -c:v libx264 -profile:v high -b:v 16M -maxrate 20M \
       -pix_fmt yuv420p -r 30 -c:a aac -b:a 320k -ar 48000 -movflags +faststart nom-social.mp4

# Web : AV1/WebM + repli H.264
ffmpeg -i nom-master.mkv -c:v libsvtav1 -crf 32 -c:a libopus -b:a 112k nom-web.webm
ffmpeg -i nom-master.mkv -c:v libx264 -crf 23 -c:a aac -b:a 144k -movflags +faststart nom-web.mp4

# Remuxage sans ré-encodage (enregistrement OBS MKV -> MP4)
ffmpeg -i enregistrement.mkv -c copy enregistrement.mp4

# Extraction de l'audio maître
ffmpeg -i nom-master.mkv -vn -c:a pcm_s24le nom-audio.wav

# Conversion des sous-titres maîtres SRT -> WebVTT
ffmpeg -i nom.srt nom.vtt
```

_Note d'honnêteté : `libsvtav1` et certains encodeurs peuvent être absents d'un build « essentials ». Avant le premier usage d'une commande, on vérifie avec `ffmpeg -encoders` (§ 5.4) et on ajuste (ex. NVENC : `-c:v av1_nvenc`) — jamais l'inverse._

### 5.3 Mesure et normalisation loudness

```bash
# Mesurer (première passe loudnorm, analyse seulement)
ffmpeg -i nom-master.mkv -af loudnorm=I=-14:TP=-1:LRA=11:print_format=json -f null -

# Normaliser en ré-encodant l'audio depuis le maître, avec les valeurs mesurées
# (deux passes pour une mesure exacte ; une passe acceptée pour les formats courts)
```

### 5.4 Contrôle qualité après chaque conversion **[V1 — À VALIDER]**

Aucun fichier converti n'est livré sans ce contrôle, fait avec `ffprobe` :

1. **Flux présents et bons codecs** : `ffprobe -hide_banner fichier` — conteneur, codecs vidéo/audio, résolution, cadence, fréquence audio correspondent à la cible du § 2.
2. **Durée** : identique à la source à ± 0,1 s (un écart = troncation ou flux manquant).
3. **Décodage complet sans erreur** : `ffmpeg -v error -i fichier -f null -` ne retourne rien.
4. **Loudness** (contenus parlés/musicaux publiés) : dans la cible du § 3.2 à ± 1 LUFS.
5. **Coup d'œil réel** : première image, un passage au milieu, la fin — regardés, pas seulement mesurés (cohérent avec la porte qualité du projet).

### 5.5 Vérification des encodeurs sur Fractal — faite le 22 novembre 2026 (~14:42–14:45 EST)

_Point validé nº 5 du § 8, vérifié en ligne de commande SSH (arrière-plan, aucun écran touché). Binaire : `ffmpeg version 9.1-essentials_build-www.gyan.dev`, compilé gcc 16.2.0, configuration avec `--enable-nvenc --enable-ffnvcodec --enable-libx264 --enable-libx265 --enable-libaom --enable-libopus`. Chaque encodeur ci-dessous a été **prouvé par un micro-encodage réel** (15 images `testsrc` 256×256@30 pour la vidéo, sinus 48 kHz 0,5 s pour l'audio), fichier relu par ffprobe puis supprimé — pas seulement repéré dans `-encoders`._

| Encodeur | Listé | Prouvé par test réel | Détail du test |
|---|---|---|---|
| `av1_nvenc` (NVENC AV1, § 2.2) | oui | **oui** | MP4 de 14 558 octets, codec relu `av1`, code de sortie 0 |
| `hevc_nvenc` (NVENC HEVC, repli § 2.2) | oui | **oui** | MP4 de 13 209 octets, codec relu `hevc`, code de sortie 0 |
| `h264_nvenc` (NVENC H.264, § 6.2) | oui | **oui** | MP4 de 12 611 octets, codec relu `h264`, code de sortie 0 |
| `aac` natif (§ 2/3) | oui | **oui** | M4A de 7 082 octets, codec relu `aac`, code de sortie 0 |
| Bonus présents : `ffv1` (§ 1.1), `pcm_s24le` (§ 1.2), `libx264`, `libopus`, `libaom-av1` | oui | listés (non testés) | couvrent maître FFV1/PCM et replis logiciels |

**Écart constaté (le seul)** : **`libsvtav1` est ABSENT de ce build « essentials »** — la commande Web AV1 du § 5.2 (`-c:v libsvtav1`) ne fonctionne donc pas telle quelle sur Fractal. Repli à appliquer lors du premier usage web : **`av1_nvenc`** (matériel, rapide — c'est l'ajustement que la note du § 5.2 prévoyait) ou `libaom-av1` (logiciel, lent). Tout le reste de la chaîne du § 5 est utilisable tel quel.

---

## 6. Streaming live

_Tout ce § est **[V1 — À VALIDER]** sauf le cas des appels (§ 6.6, [DÉCIDÉ]). Aucune diffusion live publique n'a encore eu lieu ; ces réglages sont le point de départ documenté pour le jour où on diffusera, à tester en privé d'abord._

### 6.1 Protocoles d'ingestion : qui sert à quoi

| Protocole | Rôle v1 | Quand |
|---|---|---|
| **RTMP / RTMPS** | **Standard des plateformes** : c'est ce qu'attendent YouTube Live, Instagram Live (Live Producer) et TikTok Live. RTMPS (chiffré) toujours préféré quand l'URL le propose. | Diffusion publique normale, depuis OBS |
| **SRT** | **Contribution fiable** : transport point à point tolérant aux réseaux instables (correction d'erreurs, latence réglable). Ne remplace pas l'ingestion plateforme, sauf plateformes/relais qui l'acceptent directement. | Envoyer un flux propre vers un relais ou un second poste ; lien instable |
| **WHIP / WebRTC** | **Latence faible** : ingestion WebRTC (OBS le propose en sortie WHIP), latence sous la seconde. | Formats interactifs où le délai compte plus que la portée ; plateformes/services compatibles seulement |

### 6.2 Encodage live — NVENC sur RTX 5070

| Réglage | Valeur v1 |
|---|---|
| Encodeur | **NVENC H.264** par défaut : c'est le codec qu'exigent ou préfèrent la quasi-totalité des ingestions. **NVENC AV1 seulement là où il est explicitement accepté** (YouTube Live le permet ; ne jamais le supposer ailleurs). |
| Contrôle de débit | **CBR strict** (les plateformes le supposent ; le VBR fait rejeter ou dégrader le flux) |
| Intervalle d'images clés | **2 secondes, obligatoire** |
| GOP | = intervalle d'images clés : 60 images à 30 ips, 120 à 60 ips (conséquence directe des 2 s) |
| Profil / réglages NVENC | profil High ; préréglage qualité (P5–P6) ; pas de B-frames ajoutées au-delà du défaut plateforme quand celle-ci les déconseille |
| Avance (lookahead) / réglages psychovisuels | désactivés en live (latence et conformité d'abord) |

### 6.3 Débits recommandés par résolution et cadence (vidéo seule, H.264)

| Format | Débit vidéo v1 | Note |
|---|---|---|
| 720p30 | **3 500 – 5 000 kb/s** (cible 4 500) | format d'appel et de repli réseau |
| 1080p30 | **6 000 – 9 000 kb/s** (cible 8 000) | format live par défaut visé |
| 1080p60 | **9 000 – 12 000 kb/s** (cible 10 000–12 000) | seulement si le contenu bouge assez pour le justifier |
| Vertical 1080 × 1920 à 30 ips | **6 000 – 9 000 kb/s** | TikTok/Instagram en natif vertical |

Règle réseau : le débit montant **mesuré** de la connexion doit valoir au moins **1,5 × le débit total** (vidéo + audio) ; sinon, on descend d'un format plutôt que de forcer.

### 6.4 Audio live

| Réglage | Valeur v1 |
|---|---|
| Codec | **AAC**, 48 kHz **[cohérent avec § 3]** |
| Débit | **128–160 kb/s** stéréo |
| Loudness live | viser **−14 à −16 LUFS** en moyenne parlée, limiteur en sortie à **−1 dBTP** ; en live on ne peut pas mesurer après coup : la balance se fait **avant**, en test privé |
| Chaîne | voix naturelle de l'atelier **[DÉCIDÉ : projet voix transformée en pause]** ; aucun traitement nouveau sans sa demande |

### 6.5 Latence visée et modes

| Mode plateforme | Latence typique | Usage v1 |
|---|---|---|
| Normale | ~15–30 s | diffusion tranquille, qualité d'abord |
| Faible | ~5–10 s | **défaut v1** : bon équilibre interaction/stabilité |
| Ultra-faible | ~2–5 s (YouTube) ; < 1 s en WebRTC/WHIP | seulement si l'interaction en direct l'exige ; stabilité moindre, chat quasi synchrone |

### 6.6 Le cas des appels **[DÉCIDÉ — déjà au plan]**

Les appels (caméra virtuelle OBS) **ne sont pas du streaming encodé par nous** : pas de NVENC, pas de débit à régler — les applications encodent elles-mêmes (~720p–1080p). Canvas OBS **720p30 ou 1080p30**. Toute la § 6 ne s'applique qu'à une vraie diffusion vers une plateforme.

### 6.7 Redondance et plan B **[V1 — À VALIDER]**

1. **Enregistrement local simultané, toujours** : OBS enregistre en local (réglages du § 2.2, MKV/NVENC AV1) pendant toute diffusion. Si le live tombe, la vidéo existe et peut être publiée ensuite.
2. **Clé de stream = secret** : jamais affichée à l'écran, jamais publiée, jamais dans un document du projet ; stockée comme un mot de passe (coffre). En cas de doute : la régénérer dans la plateforme **avant** la diffusion suivante.
3. **Test privé avant tout live public** : diffusion non répertoriée / de test, vérification image, son, loudness et délai du chat.
4. **Coupure en cours de live** : ne pas redémarrer OBS à chaud au hasard — vérifier la connexion, laisser OBS tenter sa reconnexion automatique (à activer dans les réglages), et s'appuyer sur l'enregistrement local pour la republication.
5. **Scène « problème technique »** prête dans OBS (habillage live v1) pour couvrir une interruption sans écran noir.

### 6.8 Contraintes par plateforme **[V1 — À VALIDER, à revérifier le jour J : ces règles bougent]**

| Plateforme | Points d'attention v1 |
|---|---|
| **YouTube Live** | RTMP/RTMPS **ou AV1 accepté** ; clé de stream par diffusion ou persistante ; modes de latence au choix (§ 6.5) ; 1080p60 accepté ; test en « non répertorié » possible — la plateforme la plus complète pour débuter. |
| **Instagram Live** | diffusion depuis OBS possible via **Live Producer** (URL + clé RTMP), disponibilité variable selon les comptes ; historiquement pensé mobile d'abord, **vertical ou recadrage à prévoir** ; aucune garantie de stabilité de la fonction : à revérifier avant tout projet de live. |
| **TikTok Live** | accès RTMP/Live Studio **restreint** (conditions d'éligibilité, souvent via Live Studio sur PC ou clé accordée au compte) ; format **vertical 1080 × 1920** natif ; ne pas planifier un live TikTok sans avoir d'abord confirmé l'accès du compte. |

---

## 7. Tableau récapitulatif décisionnel rapide

| Besoin | Conteneur | Vidéo | Audio | Référence |
|---|---|---|---|---|
| Archiver un livrable fini | MKV | FFV1 sans perte | PCM 48 kHz/24 bits | § 1.1 |
| Archiver un enregistrement long/test | MKV | tel qu'enregistré (NVENC AV1 CQ 16–20) | AAC 320 / PCM | § 1.1, § 2.2 |
| Enregistrer dans OBS | MKV | NVENC AV1 (repli HEVC) | 48 kHz | § 2.2 |
| Appel vidéo | — (caméra virtuelle) | les applis encodent ; canvas 720p30/1080p30 | — | § 2.1 **[DÉCIDÉ]** |
| Publier sur YouTube | MP4 | H.264 High débit élevé ou AV1 | AAC 320 kb/s, −14 LUFS / −1 dBTP | § 2.3 |
| Publier Instagram/TikTok | MP4 | **H.264 seulement**, 12–20 Mb/s | AAC ≤ 320 kb/s | § 2.4 |
| Publier une vidéo (toute plateforme) | selon § 2.3/2.4 | **HD minimum — jamais de SD** : vertical 1080 × 1920 cible, 720 × 1280 plancher ; horizontal 1920 × 1080 cible | selon § 2.3/2.4 | § 2.7 **[DÉCIDÉ]** |
| Mettre une vidéo sur nos pages | WebM + MP4 | AV1/VP9 + repli H.264 | Opus/AAC 128–160 kb/s | § 2.5 |
| Sous-titres | SRT (maître) / VTT (web) / incrustés (social) | style charte § 13.2 | français seulement | § 4 |
| Diffuser en direct | RTMP/RTMPS (SRT/WHIP selon le cas) | NVENC **H.264 CBR**, images clés 2 s ; AV1 seulement si accepté | AAC 48 kHz, 128–160 kb/s | § 6 |
| Convertir quoi que ce soit | depuis le **maître**, jamais depuis un fichier de diffusion | `-c copy` si simple remuxage | contrôle ffprobe après | § 5 |

---

## 8. Points validés par l'atelier (première revue)

**Validés en bloc par l'atelier le 22 novembre 2026** — les cinq points ci-dessous sont désormais la norme en vigueur (les mentions « [V1 — À VALIDER] » du document se lisent « [DÉCIDÉ] » pour ces points) :

1. Le choix **FFV1/MKV** comme maître d'archive des livrables finis (et l'exception « enregistrements longs »).
2. Les cibles **−14 LUFS / −1 dBTP** et le débit AAC 320 kb/s comme défauts de diffusion.
3. La règle « **H.264 seulement** » pour Instagram/TikTok et le couple **WebM AV1 + repli MP4** pour le web.
4. Les **débits live** du § 6.3 et le **mode de latence faible** comme défaut du § 6.5.
5. La vérification des encodeurs réellement présents dans le build ffmpeg « essentials » de Fractal (`ffmpeg -encoders`) avant le premier usage des commandes du § 5.2, et l'ajustement des commandes si un encodeur manque.

_Ces points étant validés, ce document est la référence en vigueur : on l'applique, on note ce qui accroche, et on le fait évoluer en v2 — pousser, tester, revenir en arrière si besoin, comme le reste de l'atelier._
