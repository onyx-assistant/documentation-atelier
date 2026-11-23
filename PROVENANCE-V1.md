# Provenance des images — v1 (Content Credentials / C2PA)

Chantier « Métadonnées de provenance » inscrit au plan le 22 novembre 2026 et commencé le même jour, en **version 1 assumée** (décision de l'atelier : commencer dès maintenant ce qui manque, quitte à améliorer au fur et à mesure). Objectif : pouvoir signer les images publiées du projet Onyx avec des **Content Credentials (norme C2PA, Content Authenticity Initiative)**, dans le même esprit de transparence que les pratiques déjà adoptées (#Influenceurvirtuel, mention « générée par IA »).

**Statut v1 : SIGNÉ ET VÉRIFIÉ en environnement de TEST.** La mécanique complète fonctionne de bout en bout sur notre machine. L'identité de signature, elle, est un certificat **auto-signé de test** — voir les limites plus bas, elles sont importantes.

---

## 1. Ce qui a été fait (22 novembre 2026)

### Outil installé

- **c2patool 0.29.0**, l'outil en ligne de commande officiel open source de la Content Authenticity Initiative (dépôt `contentauth/c2patool`), binaire Linux x86_64 officiel, installé **localement** dans le dossier du projet — aucune installation système, aucun service, aucun coût :
  - `<racine-du-projet>/tools/provenance/bin/c2patool/c2patool`
- Dossier de travail du chantier : `<racine-du-projet>/tools/provenance/` (`bin/`, `certs/`, `test-output/`, `manifest-test-v1.json`).

### Certificat de signature — DE TEST

Une mini-infrastructure de test a été créée avec OpenSSL (dossier `certs/`) :

- une **autorité racine auto-signée de test** : `TEST - Onyx atelier Root CA (auto-signe)` ;
- un **certificat feuille** de signature (ES256 / ECDSA P-256, valide 2 ans) émis par cette racine, dont le sujet dit explicitement ce qu'il est :
  `CN = TEST - Onyx atelier (auto-signe)`, `O = Onyx atelier`, `OU = Certificat de TEST - identite non verifiee` ;
- clés privées en permissions restreintes (600).

Deux leçons techniques au passage, consignées pour la suite :

1. c2patool refuse un certificat feuille directement auto-signé (« the certificate is invalid ») : il faut une **chaîne** émise par une CA (même une CA de test créée pour l'occasion), comme dans les certificats de test officiels de la CAI.
2. L'usage étendu de clé (EKU) doit figurer dans la liste admise par C2PA — le certificat utilise `emailProtection` (1.3.6.1.5.5.7.3.4), comme le certificat de test officiel ; `codeSigning` seul a été rejeté.

### Signature d'une copie (jamais d'un original)

- Image signée : une **copie** du composite reprojeté (`layers/reprojection-v1/composite-reprojete-rgba.png`), copiée dans `tools/provenance/test-output/source-a-signer.png` puis signée vers `test-output/composite-signe-test.png`.
- **L'original n'a pas été touché** (empreinte MD5 de l'original et de la copie de travail identiques : `a3037cf4ebee62d98e83b27e59cc181e`) ; rien n'a été écrit dans `00_canonical`.
- Manifeste embarqué (`manifest-test-v1.json`) : générateur déclaré « Onyx atelier - pipeline provenance v1 (TEST) », horodatage via le TSA public de DigiCert, et deux assertions :
  - `c2pa.actions` : action `c2pa.created` avec `digitalSourceType = trainedAlgorithmicMedia` — la norme IPTC pour « contenu généré par IA », cohérent avec notre divulgation publique ;
  - `onyxatelier.provenance` : assertion maison qui répète dans le manifeste même que l'environnement est TEST et l'identité **non vérifiée**.
- Fait mesuré à connaître : le manifeste embarqué pèse **832 Ko**, soit 41 % du fichier signé (2 024 884 octets contre 1 192 868 pour la source). À garder en tête pour les formats où le poids compte.

### Vérification

Relecture du fichier signé avec c2patool (preuve complète : `test-output/PREUVE-VERIFICATION.txt`, rapport brut : `test-output/verification-manifeste.json`, chaîne de certificats : `test-output/verification-certificats.txt`) :

- **`validation_state : Valid`** — la signature cryptographique est valide et les empreintes du fichier correspondent au manifeste ;
- émetteur lu dans le manifeste : « Onyx atelier » (le certificat de test) ;
- horodatage présent : 2026-11-22T08:32:08Z ;
- assertions ci-dessus présentes et lisibles ;
- **seul avertissement : `signingCredential.untrusted`** — attendu, voir §3.

---

## 2. Ce que la signature prouve — et ne prouve pas

Une signature C2PA valide prouve :

- **l'intégrité** : le fichier n'a pas été modifié depuis la signature (toute modification casse les empreintes) ;
- **l'auto-déclaration de l'émetteur** : celui qui détient la clé privée associée au certificat a signé ce manifeste, avec les assertions qu'il contient (générateur, actions, type de source) ;
- **le moment** de la signature, si un horodatage d'autorité (TSA) est inclus — c'est le cas ici.

Elle ne prouve **pas** :

- que les assertions sont vraies (un manifeste déclare ; il ne certifie pas la réalité du contenu) ;
- que l'image est « authentique » au sens artistique ou factuel ;
- **qui** est l'émetteur, tant que le certificat n'est pas adossé à une identité vérifiée — c'est exactement notre cas en v1.

## 3. La limite du certificat auto-signé (le point d'honnêteté central)

N'importe qui peut créer un certificat auto-signé portant n'importe quel nom. Le nôtre dit « TEST » partout précisément pour ne jamais être confondu avec une identité vérifiée. Concrètement, tout outil de lecture (c2patool, visionneuses Content Credentials) affichera l'émetteur comme **non fiable** (`signingCredential.untrusted`) : la signature démontre la mécanique et l'intégrité, **pas** une identité. Aucune image signée avec ce certificat ne doit être présentée au public comme « certifiée ».

## 4. Le chemin vers une signature « production » (rien acheté, rien engagé)

Pour qu'un lecteur affiche l'émetteur comme fiable, il faudra, le moment venu :

1. **Un certificat de signature émis par une autorité de certification reconnue** et inscrite à l'écosystème de confiance C2PA (la liste de confiance officielle est tenue dans le cadre du programme de conformité C2PA ; des AC publiques comme DigiCert ou GlobalSign proposent des certificats adaptés à la signature de contenu/documents). Le certificat portera alors l'identité vérifiée du détenteur (personne ou organisation — à arrimer à la structure du projet le moment venu).
2. **Un horodatage d'autorité** adossé à cette chaîne (le TSA public de DigiCert utilisé en test est gratuit ; en production, on utilisera celui de l'AC retenue).
3. **Une gestion de clé sérieuse** : clé de production gardée séparée des postes de travail courants, procédure d'incident (révocation) — à aligner avec le document GOUVERNANCE.md.

Coûts : non chiffrés ici, car ils varient selon l'AC et le type de certificat (historiquement, ce type de certificat se situe dans une fourchette de quelques centaines de dollars par an) ; **aucun achat n'a été fait ni n'est nécessaire pour la v1**, et toute dépense devra être présentée à l'atelier avant, selon la doctrine du projet.

## 5. Application au moment de publier (Instagram, YouTube…)

Ce que dit la documentation de l'écosystème C2PA/CAI, et ce qu'il faut en retenir pour nous :

- **Les plateformes bougent, dans le bon sens mais sans garantie de conservation.** Meta et YouTube ont annoncé la prise en charge des Content Credentials pour **détecter et étiqueter** le contenu généré ou modifié par IA. Mais un manifeste **embarqué** peut être perdu ou invalidé quand une plateforme recompresse l'image à l'envoi : la conservation des métadonnées n'est pas garantie d'une plateforme à l'autre, ni dans le temps.
- **La pratique robuste, dès la v1** :
  1. signer la version **finale** de l'image juste avant publication, avec le flux ci-dessus ;
  2. **archiver l'original signé** dans nos dossiers versionnés (c'est lui qui porte la preuve vérifiable) ;
  3. considérer la copie plateforme comme un dérivé : si son manifeste survit, tant mieux ; sinon, la preuve reste chez nous, montrable sur demande ;
  4. garder les **divulgations visibles** comme signal principal (#Influenceurvirtuel, mention « générée par IA », #pub/#commandite le cas échéant) : elles, au moins, ne dépendent d'aucune métadonnée.
- **Piste ultérieure (hors v1)** : les « Content Credentials durables » de la spécification (dépôt de manifestes en nuage, empreintes perceptuelles, filigranes) permettent de retrouver la provenance même après stripping — à évaluer si le projet publie à plus grande échelle.

## 6. Rejouer la v1

```bash
cd <racine-du-projet>/tools/provenance
# signer une COPIE (jamais un original)
./bin/c2patool/c2patool <copie.png> -m manifest-test-v1.json -o <copie-signee.png> -f
# verifier
./bin/c2patool/c2patool <copie-signee.png>
```

---

*Document rédigé le 22 novembre 2026. Statut : v1 fonctionnelle en test ; passage en production subordonné à l'obtention d'un certificat d'AC reconnue (décision et budget à valider par l'atelier).*
