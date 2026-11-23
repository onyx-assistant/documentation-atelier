# Système d'images « Archives uniformes » — v1

**Créé le 22 novembre 2026** (demande de l'atelier : la section « Archives de
l'atelier » du site atelier devait lire comme **une seule série de planches
d'atelier**, pas comme une collection d'images générées disparates).
**État : préparé, en attente de validation de l'atelier** (déploiement aperçu
seulement ; la production ne bouge pas avant son approbation).

**Mise à jour du 22 novembre 2026 (~20:10 EST)** : validation de l'atelier
**obtenue** (~20:02 EST, accord général pour les tâches en attente).
Préparation de la mise en production **terminée et vérifiée** : sauvegarde
datée de la production actuelle (46 fichiers + manifeste BLAKE3) dans
`cloudflare/sauvegardes/prod-atelier-avant-archives-uniformes-2026-11-22/` ;
nouvel `index.html` de production reconstruit depuis la source de l'aperçu
(diff = exactement les changements de l'aperçu ; seul le commentaire de fin
de fichier passe en mention « production »), installé dans
`cloudflare/site-avec-bulle/index.html` (ancien index conservé en
`index.html.bak-archives-20261122`), nouveau hash BLAKE3
`16f522c039f8e9278c36d6c8890d772d` ; les 16 images traitées sont déjà au
magasin d'assets (servies en production, orphelines, vérifiées 200).
**Déploiement lui-même BLOQUÉ** : l'approbation du connecteur Cloudflare
(`api.execute`) a été refusée trois fois de suite — il reste, dès
l'approbation accordée : jeton upload-token → check-missing → téléverser le
nouvel index → upsert-hashes → déploiement (branche `main`) → vérifications
(200, images, en-têtes, beacon, bulle) → captures. La production en ligne
est **inchangée** à l'heure de cette note.

Script de référence : `tools/uniformisation-archives/traiter-archives.py`.
Sorties : `cloudflare/site-avec-bulle/img/archives-uniformes-v1/` (mêmes noms
de fichiers que les originaux ; **les originaux ne sont jamais modifiés**).

---

## 1. Le principe

Chaque pièce d'archive, quelle que soit son origine (rendu studio, croquis sur
papier, blueprint, capture d'écran d'outil), est présentée **de la même
manière** : posée sur la plaque claire de la charte, avec le même grade
colorimétrique doux, un cadrage par famille et une étiquette technique
numérotée. L'œil doit reconnaître la série avant de regarder les pièces.

## 2. La plaque

- **Couleur unique : `#FFF8F1`** — la « scène claire » de la charte graphique
  (§ 2, thème sombre officiel) : *les images officielles sont toujours
  présentées sur cette scène claire, même en thème sombre*. C'est aussi la
  variable `--stage` du site.
- La plaque fait partie de l'image livrée (pas seulement du CSS) : la série
  reste uniforme partout où l'image est reprise (visionneuse, partage,
  reprise future).
- **Exception identité** : la plaque vaut pour les archives et la
  présentation. Les usages d'identité (avatar, icône, GitHub) conservent les
  originaux sur fond blanc pur de la charte — on ne « plaque » jamais la
  référence canonique elle-même.

## 3. Les trois familles et leurs toiles

| Famille | Contenu | Toile | Traitement |
|---|---|---|---|
| **Vues** | portrait officiel, vues face / trois-quarts / profil / dos, corps entier, test des contours | **1:1 — 1200 × 1200** | détourage réel (remplissage par les bords), frange décontaminée, ombre de silhouette constante (flou 1,6 % de la toile, décalage vertical +1,2 %, brun chaud `#4A2C14` à 30 %) |
| **Planches** | feuille modèle buste, planche corps entier | **2:1 — 1800 × 900** | document gardé entier ; le blanc du papier est fondu vers la plaque ; filet cartouche 2 px `rgba(74,55,40)` au bord du document |
| **Études et schémas** | les 6 pièces de la bibliothèque (silhouettes, oreilles, plan technique, grille, éclaté, maillage) | **3:2 — 2048 × 1365** (variante 1024 × 683) | le document devient une **feuille posée sur la plaque** : marge constante de 76 px (38 px en variante), ombre portée douce constante (décalage +16 px, flou 18, `#241611` à 30 %), filet 2 px au bord de la feuille |

Cadrages des vues (depuis la boîte englobante de la silhouette) :

- **Bustes** (portrait, face, trois-quarts, profil, dos, test des contours) :
  hauteur du personnage = **92 %** de la toile, base de la silhouette **au bord
  bas** de la toile (le buste reste « coupé cadre » comme dans les originaux —
  pas de buste flottant), centrage horizontal.
- **Corps entier** : hauteur = **90 %**, base à **96 %** de la toile (la pose
  complète respire au-dessus de la plaque).

Cas particulier — **le test des contours** (ancien « test du fond foncé ») :
son détourage d'origine portait un liseré clair (résidu de son propre
détourage sur blanc, visible sur le fond sombre). Le détourage v1 se fait par
**distance signée au noyau personnage** (pixels chauds ou très sombres),
coupée ~3 px à l'intérieur du contour, avec défrangeage couleur vers le pixel
opaque le plus proche. Le liseré disparaît ; la légende de la pièce raconte
toujours le test, la présentation suit la série.

## 4. Le grade commun

Appliqué à **toutes** les pièces, avant la pose sur plaque, dans cet ordre :

1. **Harmonisation des neutres** : gains par canal calculés sur les pixels peu
   saturés (saturation < 0,15) pour amener leur chromaticité moyenne sur celle
   de la plaque `#FFF8F1`, gains bornés à **[0,95 ; 1,05]**, appliqués avec un
   poids `(1 − saturation)^1,5` — les teintes saturées (les oranges) ne
   bougent presque pas.
2. **Courbe en S très douce** : contraste +~5 % autour du pivot 0,5,
   atténuée de 55 % sur les pixels saturés.
3. **Saturation globale × 0,98** autour de la luminance.

Aucune régénération, aucun repeint : le personnage n'est ni redessiné ni
recoloré — seuls les neutres, la lumière d'ensemble et la présentation sont
harmonisés.

### Porte couleur (obligatoire)

Après traitement, on échantillonne la médiane du cluster orange du personnage
(méthode du pack couleur : `tests/session-libre-rendu-couleur/echantillonner-couleurs.py`,
ΔE CIEDE2000) :

- **Pièces à rendu du personnage** : la médiane doit rester à **ΔE ≤ 6,5** de
  l'orange de charte le plus proche, sinon on ajuste le grade.
- **Croquis à l'aquarelle** (silhouettes, oreilles, grille) : leurs oranges
  sont volontairement délavés — déjà à ΔE 7,3–12,8 dans les originaux. La
  règle y est : **le traitement ne doit pas les éloigner** (déplacement
  induit ≤ ~0,5 mesuré en v1).

Mesures v1 (médiane orange, ΔE vers l'orange de charte le plus proche) :

| Pièce | Avant | Après | Verdict |
|---|---|---|---|
| Portrait officiel | 1,17 | 1,33 | ✅ |
| Vue face | 1,34 | 1,33 | ✅ |
| Vue trois-quarts | 3,72 | 3,52 | ✅ |
| Vue profil | 2,41 | 2,20 | ✅ |
| Vue dos | 2,10 | 2,18 | ✅ |
| Vue corps entier | 3,31 | 3,78 | ✅ |
| Test des contours | 1,34 | 1,45 | ✅ |
| Feuille modèle buste | 2,08 | 1,98 | ✅ |
| Planche corps entier | 2,77 | 3,13 | ✅ |
| Éclaté des couches | 5,49 | 6,43 | ✅ (le plus serré — schéma plat, à surveiller) |
| Maillage du rig | 4,13 | 4,36 | ✅ |
| Plan technique de la tête | — | 4,72 | ✅ |
| Premières silhouettes (aquarelle) | 12,81 | 13,15 | déplacement +0,34 ✅ |
| Étude des oreilles (aquarelle) | 8,44 | 8,76 | déplacement +0,32 ✅ |
| Grille de proportions (aquarelle) | 7,33 | 7,81 | déplacement +0,48 ✅ |

## 5. L'étiquetage (habillage de section)

- Chaque pièce porte **« ARCHIVE Nº 01 », « ARCHIVE Nº 02 »…** (remplace les
  anciens codes « ARC-xx »), en étiquette mono du système, accent orange
  unique de la charte.
- La **légende technique** (matière / technique) passe dans la police de texte
  du site (Archivo — le rôle que la charte donne à Nunito), semi-gras.
- Chaque fiche se termine par une **ligne de spécification uniforme**,
  séparée par un filet du système : « Série Archives v1 — plaque claire ·
  toile 3:2 · grade commun ».
- La zone d'ouverture des fiches (et la visionneuse) s'affiche sur la scène
  claire `--stage` : l'image et son cadre ne font qu'un.
- Accessibilité : textes alternatifs exacts (mis à jour quand la présentation
  change ce qui est visible — portrait, test des contours) ; aucun petit
  texte orange sur fond clair hors de l'orange ombre `#8D2E0F` (règle charte
  § 16) ; les contrastes existants sont inchangés ou améliorés.

## 6. Faire naître une future pièce « uniforme »

1. Produire la pièce comme d'habitude (aucune contrainte nouvelle à la
   création).
2. L'ajouter à la famille correspondante dans
   `tools/uniformisation-archives/traiter-archives.py` et relancer le script
   (les originaux restent intacts ; la sortie va dans
   `img/archives-uniformes-v1/`).
3. Échantillonner ses oranges (porte du § 4) ; si la porte échoue, ajuster le
   grade — jamais recolorer le personnage.
4. Lui donner le prochain numéro **ARCHIVE Nº** libre, sa légende technique
   et la ligne de spécification.
5. Vérifier la planche-contact de la série entière : la nouvelle pièce doit
   passer inaperçue dans l'ensemble.

## 7. Fichiers

- Traitement : `tools/uniformisation-archives/traiter-archives.py`
- Images traitées : `cloudflare/site-avec-bulle/img/archives-uniformes-v1/`
  (16 fichiers, mêmes noms que les originaux)
- Captures avant/après et planches-contact : `docs/systeme-images-archives-v1/`
- La production ne sera mise à jour qu'après validation de l'atelier sur
  l'aperçu. → **Fait : voir § 8.**

## 8. Mise en production (22 novembre 2026, ~20:09 EST)

- **Autorisation** : l'atelier — validation de l'aperçu et accord général
  pour les tâches en attente (~20:02 EST). Une première tentative le
  même soir avait été bloquée par le refus de l'approbation du
  connecteur Cloudflare ; à la relance, les cartes ont été approuvées.
- **Préparation** : sauvegarde de la production d'avant passe dans
  `cloudflare/sauvegardes/prod-atelier-avant-archives-uniformes-2026-11-22/`
  (manifeste BLAKE3 des 46 fichiers + LISEZ-MOI de retour arrière).
  Le nouveau manifeste ne change que `/index.html`
  (hash `16f522c039f8e9278c36d6c8890d772d`, 88 102 octets) ; les 16
  images traitées étaient déjà au magasin d'assets.
- **Déploiement** : onyx-atelier, production, branche `main` —
  **`0d9de284-e3be-4009-9e25-c49cc2c95be8`**, étape `deploy/success`.
  check-missing final : vide (le nouvel index et le hash de
  `/_headers`, signalé manquant au passage, ont été téléversés puis
  upsertés ; `_headers` joint au déploiement, inchangé).
- **Vérifications en production** :
  - `/` → 200, `text/html; charset=utf-8`, 88 316 octets servis =
    index local + la seule injection plateforme (script et
    commentaires du beacon Web Analytics) ; beacon présent.
  - Les **16 images** `/img/archives-uniformes-v1/` servies sont
    **octet pour octet identiques** aux fichiers traités locaux.
  - En-têtes de sécurité complets : HSTS, CSP, X-Frame-Options DENY,
    nosniff, Referrer-Policy.
  - **Bulle retestée de bout en bout** (leçon de l'incident O4) :
    `POST /chat` depuis l'origine du site → 200, vraie réponse du
    modèle conforme à la fiche persona.
  - **Écart honnête** : les captures après (bureau + mobile) n'ont
    pas pu être produites dans cette passe — le Chromium géré de
    l'environnement refuse tout démarrage autonome (politique de
    domaines WebGL de la plateforme). À refaire depuis l'infrastructure
    navigateur du parent ; toutes les autres vérifications sont
    positives, le déploiement n'est pas remis en cause.
- **Retour arrière** : redéployer le manifeste sauvegardé
  (`MANIFESTE-PRODUCTION-ACTUELLE.json` du dossier de sauvegarde)
  selon la procédure habituelle — un seul déploiement suffit.
