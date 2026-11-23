# Compétences expressions faciales et communication — Base v1 d'Onyx

_Apprentissage permanent demandé par l'atelier le 22 novembre 2026 (~15:14 EST, appel vocal) : « enregistre tes connaissances avec tout ce qui est expressions faciales et la communication, la façon de parler — tout ça c'est important. » Objectif : une base de travail rigoureuse sur **comment un visage et une voix disent quelque chose** — pour animer Onyx juste, pour la faire parler juste, et pour mieux lire les gens à qui elle parle._

_Documents liés : `~/workspace/neurosciences/COMPETENCES-NEUROSCIENCES-V1.md` (perception des visages, schéma bébé, vallée de l'étrange, voix et cerveau — **cette base complète cette lecture « côté cerveau », elle ne la duplique pas** : ici, on est côté **production et lecture des signaux**), `docs/PERSONNAGE-SCRIPT.md` (v1.0 — personnalité, voix écrite § 4, expressions signatures), `docs/GUIDE-POSES-ANGLES-V1.md` (répertoire P1–P12, ratio 70/30, expressions hors répertoire), `docs/COMPETENCES-HUMOUR-V1.md` (timing comique, regard caméra, clignement-ponctuation), `docs/COMPETENCES-ANIMATION-CINEMA-V1.md` (12 principes, timing en images, acting), `docs/GUIDE-CUBISM-COMPLET.md` (paramètres du rig, clignement par fondu, bouche moyenne), `docs/NORMES-AV-V1.md` (§ 2.8 — la voix master vérifiée par écoute **et** mesure de fréquence fondamentale), `docs/FICHE-TIMINGS-VISAGE-V1.md` (les durées concrètes en images à 24 ips — clignements, transitions, chaîne regard → tête → corps), `docs/FICHE-ECOUTE-ACTIVE-APPEL-V1.md` (le mode d'emploi d'écoute et de réponse en appel vocal)._

**Convention de fiabilité** (même convention que la base neurosciences) : **[Établi]** = résultat répliqué, consensus raisonnable ; **[Hypothèse]** = piste sérieuse mais débattue ou contexte-dépendante ; **[Vulgarisation]** = version simplifiée qui circule, souvent fausse dans sa forme populaire — à ne jamais appliquer telle quelle. Ces connaissances **éclairent** les choix ; elles ne remplacent ni le test côte à côte, ni la validation de l'atelier.

---

## 1. Les expressions faciales — le vocabulaire du visage

### 1.1 Les émotions de base et leurs signatures

Le point de départ classique (Ekman et ses successeurs) : un petit nombre d'émotions ont des configurations faciales reconnaissables **au-delà des cultures**, parce qu'elles recrutent toujours à peu près les mêmes muscles. **[Établi]** pour la reconnaissance des configurations fortes et posées ; **[Hypothèse]** pour l'idée qu'on lirait ainsi, de façon fiable, l'émotion « réelle » d'une personne en situation naturelle (voir § 5.3 — la prudence est une règle du projet).

Le système de codage de référence est le **FACS** (*Facial Action Coding System*, Ekman & Friesen) : il ne nomme pas des émotions, il décrit des **unités d'action (AU)** — les mouvements élémentaires produits par un muscle ou un petit groupe de muscles. C'est le bon niveau de description pour nous : un rig et un visage dessiné travaillent en mouvements élémentaires, pas en « émotions ».

Les unités qui comptent pour un visage simple (les seules qu'Onyx peut ou doit traduire) :

| AU | Muscle / mouvement | Ce que ça dit, en lecture courante |
|---|---|---|
| AU1 | Releveur interne des sourcils (frontal interne) | Tristesse, inquiétude, appel (les sourcils « en accent circonflexe ») |
| AU2 | Releveur externe des sourcils | Surprise, attention vive |
| AU1+2 | Sourcils relevés ensemble | Surprise franche ; aussi le « flash » de salutation (§ 1.5) |
| AU4 | Abaisseur des sourcils (corrugateur) | Concentration, effort, contrariété, colère si combiné |
| AU5 | Releveur de la paupière supérieure | Peur, alerte (œil écarquillé) |
| AU6 | Joue relevée (orbiculaire, partie orbitaire) | Le marqueur du sourire **sincère** (sourire de Duchenne) |
| AU7 | Paupière resserrée (orbiculaire, partie palpébrale) | Sourire contenu, attention, méfiance légère |
| AU12 | Coin des lèvres relevé (zygomatique majeur) | Le sourire proprement dit |
| AU15 | Coin des lèvres abaissé | Tristesse, déception |
| AU20 | Lèvres étirées horizontalement | Peur, tension |
| AU23/24 | Lèvres resserrées / pressées | Contrôle, frustration contenue, détermination |
| AU25/26/27 | Lèvres écartées / mâchoire qui tombe / bouche grande ouverte | Degrés d'ouverture : parole, étonnement, stupeur |
| AU43 | Yeux fermés | Repos, refus doux, clignement (durée variable) |

Les six configurations de base, en recettes FACS simplifiées :

- **Joie** : AU6 + AU12 (sourire qui atteint les yeux). Sans AU6, le sourire reste « social » — poli, pas forcément joyeux.
- **Tristesse** : AU1 (+ AU4 léger) + AU15, tonus général bas, regard souvent baissé.
- **Surprise** : AU1+2 + AU5 + AU26 — tout s'ouvre, brièvement (c'est l'expression la plus courte : elle bascule vite vers l'émotion qu'elle précède).
- **Peur** : AU1+2+4 (sourcils relevés **et** rapprochés) + AU5 + AU20/26 — la différence avec la surprise tient aux sourcils rapprochés et à la bouche étirée.
- **Colère** : AU4 + AU5/7 + AU23/24 — sourcils baissés, regard fixe, lèvres pressées. **Hors répertoire d'Onyx** (guide poses, script § 3).
- **Dégoût** : AU9 (nez plissé) + AU15/16 — repose sur le nez et la lèvre supérieure. **Hors répertoire d'Onyx** aussi, et de toute façon intraduisible sur son visage (pas de nez plissable, voir § 1.7).

### 1.2 Les expressions composées et mélangées

La vie réelle mélange : joie + surprise (émerveillement), joie + tristesse (nostalgie douce, soulagement ému), surprise + peur (saisissement). **[Établi]** : le visage peut porter deux configurations à la fois, souvent **réparties** — le haut du visage dit une chose, le bas une autre (sourire triste : AU12 faible avec AU1). Règle de lecture et de production : **le haut du visage (sourcils, yeux) trahit plus, le bas (bouche) se contrôle plus** — c'est le bas qu'on « tient » en société, donc c'est en haut que l'émotion fuit.

Pour Onyx, c'est une excellente nouvelle : son expressivité vit presque entièrement **en haut** (sourcils, paupières, inclinaison) — exactement la zone la moins « jouée » chez l'humain, donc la plus crédible.

### 1.3 Les micro-expressions — ce qu'elles sont, ce qu'elles ne sont pas

Une micro-expression est une configuration complète mais **très brève** (~1/25 à 1/5 de seconde), qui apparaît quand une personne tente de masquer une émotion. **[Établi]** : elles existent et sont mesurables en laboratoire. **[Vulgarisation]** : l'idée qu'un observateur entraîné « lirait les mensonges » en direct grâce à elles — les taux de détection réels sont modestes, et la recherche sur la détection du mensonge par le visage seul est largement décevante. **Règle projet** : les micro-expressions servent ici à une seule chose — le **timing** d'animation. Une émotion qui traverse un visage en 3–6 images avant de se composer, c'est un outil d'acting (la « fuite » avant le masque), pas un outil de lecture des gens (§ 5.3).

### 1.4 L'intensité — le curseur avant le catalogue

Une même configuration se joue de ~0 à 100 %. **[Établi]** : la lecture change avec l'intensité — un AU12 à 20 % est un sourire de politesse, à 60 % un sourire franc, à 100 % avec AU6 un éclat. En animation comme en lecture, **l'erreur classique est binaire** (sourire / pas sourire) alors que le signal est continu. Le rig d'Onyx est construit exactement ainsi (paramètres continus, poses clés intermédiaires) : cette base confirme le choix et donne les repères du § 6.1.

### 1.5 L'asymétrie et les signaux brefs

- **Asymétrie** : les expressions spontanées sont souvent légèrement asymétriques ; les expressions posées tendent à être symétriques. **[Hypothèse]** pour l'usage en lecture fine (la latéralisation hémisphérique des émotions reste débattue) ; en **production**, c'est un fait d'atelier utile : une asymétrie minime (un sourcil un cran plus haut, un coin de bouche un demi-cran en retard) rend une pose moins « plaquée ». À doser très finement chez Onyx — la fidélité au canon passe avant (§ 6.1).
- **Le flash des sourcils** (*eyebrow flash*) : relèvement bref AU1+2 (~1/5 de seconde) à la reconnaissance d'une personne — signal de salutation et d'accueil quasi universel **[Établi]**. C'est un geste d'ouverture idéal pour Onyx : petit, chaud, lisible, et déjà dans sa grammaire (sourcils courbes mobiles).
- **Le haussement tenu** : sourcils relevés et **maintenus** = attente, question, invitation à continuer — le visage de l'écoute (§ 3.4).

### 1.6 Traduction pour le visage d'Onyx — l'inventaire honnête

Le visage canonique : **yeux ovales noirs sans iris ni blanc**, sourcils courbes fins, **bouche-ligne** (le sourire canonique, mince), museau doux, oreilles rondes. Ce qui en découle, sans tricher :

**Ce qui se lit bien (ses canaux forts) :**
- **Les sourcils** — son canal principal : relevés (surprise, accueil, question), haussés-rapprochés impossible à proprement parler mais l'**inclinaison** des courbes (montantes au centre = tristesse douce/inquiétude ; abaissées au centre = concentration/contenariété contenue) porte les équivalents AU1/AU4.
- **Les paupières** — fermeture complète (paupières fermées = repos, douceur, émotion contenue), mi-fermeture (sérénité, sourire « dans les yeux » : c'est son équivalent AU6, le marqueur Duchenne — chez elle, le sourire sincère se joue **autant dans les paupières que dans la bouche**).
- **La bouche-ligne** — sa courbe est un curseur : courbure ascendante (sourire, graduée), aplatie (neutre, sérieux), inversée faible (déception douce), ouverte en deux variantes validées (légère, moyenne = maximum — § 6.1).
- **L'inclinaison et l'orientation de la tête** — son canal « corps » principal (§ 1.7).
- **Les oreilles** — pas des émetteurs volontaires, mais des **révélateurs** : leur retard de follow-through dit l'élan, la surprise, la détente (base animation cinéma ; base humour § 6.4).

**Ce qui ne se lit pas (ses angles morts, à assumer) :**
- **Pas de direction du regard interne** : sans iris, le « regard » d'Onyx est porté par la **tête** (§ 2.1). Toute la grammaire du regard fin (saccades, micro-déviations) lui est fermée — elle compense par l'orientation et le timing.
- **Pas de nez** : dégoût (AU9) et mépris (AU14 unilatéral) intraduisibles — cohérent avec le répertoire : ce sont deux émotions **hors personnage** de toute façon.
- **Pas de joues mobiles** : pas de gonflement, pas de fossettes ; la joue relevée AU6 passe par les paupières (ci-dessus).
- **Pas de front plissable** : l'inquiétude passe par l'inclinaison des sourcils et de la tête, jamais par des rides.

### 1.7 Les équivalences — sa grammaire de substitution

Quand un canal humain manque, Onyx traduit par un canal qu'elle a. Table de travail (à valider en session de rig) :

| Signal humain | Équivalent Onyx |
|---|---|
| Regard dirigé (iris) | Orientation de la tête, retardée d'un souffle après l'intention (§ 2.1) |
| Sourire de Duchenne (AU6+12) | Courbe de bouche + paupières légèrement baissées/resserrées |
| Sourcils en accent circonflexe (AU1, tristesse) | Sourcils inclinés, montants au centre + tête baissée d'un cran |
| Écarquillement (AU5, alerte) | Paupières grandes ouvertes tenues + tête relevée + oreilles en retard |
| Bouche étirée de peur (AU20) | Hors répertoire (peur extrême exclue) ; la surprise douce suffit |
| Hochement / penché d'écoute | Inclinaison latérale de tête ~5–10°, tenue (§ 3.4) |
| Rire (bouche ouverte, secousses) | Bouche moyenne + rebond du corps + paupières fermées — jamais de bouche béante |
| Réflexion (regard ailleurs, AU4 léger) | Tête tournée d'un cran, sourcils légèrement inclinés, pause (§ 4.3 : la pause parle) |

**Règle de fidélité qui prime sur tout ce § 1** : aucune de ces traductions ne doit déformer le canon. Le sourire canonique reste la pose de repos ; toute expression est un **écart mesuré depuis le canon, qui y revient** — c'est le principe du guide poses (70/30) appliqué au visage, et le critère nº 1 du projet.

---

## 2. Le regard et les clignements

### 2.1 Le regard comme signal social

Le regard est le canal d'adresse du visage : il dit **à qui** l'on parle, **à quoi** l'on pense, et **si** l'on écoute. Repères établis :

- **Le regard mutuel** soutient le lien, mais par intermittence : en conversation, on regarde l'autre ~30–60 % du temps en écoute, moins en parlant (on décroche pour penser). Un regard **continu** devient vite un examen, voire une menace. **[Établi]**
- **Le regard qui décroche vers le haut/le côté** pendant la parole signale la recherche (mémoire, formulation) — le décrochage **vers le bas** signale plutôt le retrait, la tristesse ou la soumission. **[Établi]** comme tendances, pas comme diagnostics (§ 5.3).
- **L'attention conjointe** : regarder ce que l'autre regarde (ou désigne) est le geste social de base — c'est la pose P3 du guide (« regard vers l'objet »), et le mécanisme qui fait exister un monde partagé entre Onyx et son public.

**Traduction Onyx** : sans iris, son regard = orientation de tête + **séquence**. La base animation l'a fixé : le regard mène, la tête suit, le corps suit la tête. Chez elle, « regarder » se décompose donc en : micro-orientation des yeux-ovales impossible → la tête tourne d'un cran **avant** le corps, avec le retard des oreilles en signature. En appel (tracking), c'est le tracker qui porte ce canal : d'où l'importance, déjà consignée au guide VTube Studio, de regarder la **caméra** (et non l'écran) aux moments d'adresse — regarder la caméra = regarder l'interlocuteur dans les yeux.

### 2.2 Les clignements — la ponctuation du visage

- **Le rythme de base** : ~15–20 clignements/minute au repos, paupières fermées ~0,1–0,15 s ; à 24 images/s, un clignement naturel fait **~4–6 images** (repère déjà dans les bases cinéma et humour). **[Établi]**
- **Le clignement comme ponctuation** : il tombe naturellement aux frontières de phrases et de pensées (chez celui qui parle comme chez celui qui écoute) — c'est un point final visuel. Un clignement juste après une chute de gag ou une information importante la **souligne** (base humour § 3).
- **Le clignement comme marqueur d'état** : rythme qui s'accélère = charge cognitive, stress ou fatigue ; qui ralentit = détente, absorption. Lecture prudente (§ 5.3) — mais en **production**, c'est un levier d'acting sûr : Onyx attentive cligne normalement ; Onyx « émue » ou fatiguée (fin de journée d'atelier, vignette douce) cligne plus lentement, avec des fermetures plus longues.
- **La fermeture tenue** (0,5–2 s) : refus doux, émotion contenue, repos, « je savoure » — chez Onyx, c'est la pose P4 (endormie/rêveuse) et la signature de ses moments tendres. À distinguer du clignement : même pièce (paupières), autre fonction.
- **Le non-clignement** : un visage qui ne cligne pas du tout tombe dans la vallée de l'étrange (base neurosciences § 3) — le clignement régulier est une **condition de vie** du personnage, pas un ornement. Le rig le traite comme tel (clignement par fondu œil ↔ paupière fermée + squash ~40 % à mi-course, guide Cubism).

---

## 3. La communication non verbale — le corps autour du visage

### 3.1 La posture

La posture parle avant les mots : ouverture (torse dégagé, orientation frontale) vs fermeture (repli, épaules rentrées) ; expansion (se faire grand) vs contraction. **[Établi]** comme lecture de confort et d'engagement, avec de fortes variations culturelles et individuelles. Les vulgarisations type « les bras croisés = refus » sont **[Vulgarisation]** : les bras croisés disent souvent juste… qu'on a froid ou l'habitude. Règle projet : lire des **faisceaux** (§ 5.2), jamais un geste isolé.

**Chez Onyx** : bust framing en appel ; sa « posture » tient au torse qui respire (la respiration comme mouvement de fond, base animation), au léger sway, et à l'orientation. Une peluche ne croise pas les bras — elle dit l'ouverture par la **frontalité** et la fermeture par la rotation d'un cran et la tête basse.

### 3.2 La gestuelle

Quatre familles classiques (Ekman & Friesen) : **emblèmes** (gestes à sens conventionnel : pouce levé, salut de la main), **illustrateurs** (qui ponctuent la parole : battements, pointages), **régulateurs** (qui gèrent l'échange : hochements, signaux de tour de parole), **adaptateurs** (auto-contacts de régulation émotionnelle). Onyx n'a pas de bras articulés en 2D : ses illustrateurs sont la tête et le rebond ; son emblème principal est le **salut** (pose P7) et le **hochement** ; le 3D ouvrira le reste, plus tard, sans changer la grammaire.

### 3.3 La proxémie

Les distances portent le lien : intime (< ~45 cm), personnelle (~45 cm–1,2 m), sociale (~1,2–3,5 m), publique (au-delà) — ordres de grandeur occidentaux **[Établi]**, variables selon les cultures. Pour un personnage d'écran, la proxémie devient **cadrage** : le gros plan complice (P6) crée une distance personnelle simulée ; le plan large « atelier » crée une distance sociale. Choisir un cadrage, c'est choisir une distance relationnelle — décision éditoriale, pas seulement esthétique (croisement base production AV).

### 3.4 La synchronie et les signaux d'écoute active

- **La synchronie** : en conversation qui va bien, les interlocuteurs accordent leurs rythmes (postures en écho, tempos de parole qui convergent, hochements calés). **[Établi]** comme corrélat du rapport ; la « contagion » motrice est en partie ce qui fait sentir un personnage « avec nous » (base neurosciences § 4, empathie motrice).
- **Les signaux d'écoute** (*backchannels*) : « mm-hm », hochements, sourcils relevés tenus (§ 1.5), micro-sourires aux bons endroits. Ils ne disent pas « je suis d'accord », ils disent « **je suis là, continue** ». Un interlocuteur sans aucun signal d'écoute est déroutant — c'est vrai pour un humain, et c'est vrai pour un personnage en appel : le rig et le tracking doivent porter hochements et réactions minimales, sinon Onyx paraîtra absente même avec un visage parfait.
- **Le penché d'écoute** : inclinaison latérale légère de la tête (~5–10°) + orientation vers l'autre = l'un des signaux d'attention les plus lisibles qui soient, et l'un des plus sûrs pour Onyx (pose P2, tête penchée curieuse, en est la version interrogative).

**Ce qu'une peluche quasi immobile peut porter, en résumé** : frontalité, inclinaisons, hochements, rebond, respiration, clignements, paupières, sourcils, courbe de bouche — un vocabulaire réduit mais **complet fonctionnellement** : adresse, écoute, émotion douce, ponctuation. Ce qu'elle ne portera jamais : la menace, le mépris, la complexité gestuelle des mains. C'est une limite de corps qui épouse une limite de personnage (script § 3) — cohérence, pas manque.

---

## 4. La façon de parler — la voix comme communication

### 4.1 La prosodie — la musique qui porte le sens

La prosodie (intonation, rythme, accentuation, pauses, débit, intensité) porte souvent **plus** d'information relationnelle que les mots : la même phrase (« c'est beau ») peut être sincère, ironique ou lasse selon sa seule musique. Repères :

- **Intonation** : la courbe mélodique marque le type d'énoncé (la question monte — en français québécois parlé, souvent moins par la montée finale que par l'intonation et les particules, § 4.3), l'émotion (courbe ample et variée = engagement ; courbe plate = retrait ou fatigue) et la structure (les descentes ferment les unités de sens). **[Établi]**
- **Rythme et débit** : un débit légèrement soutenu avec des phrases courtes se perçoit comme de l'énergie et de la clarté ; un débit qui s'effondre en fin de phrase se perçoit comme de l'incertitude. La voix master d'Onyx est réglée exactement là : **débit légèrement rapide, phrases courtes, moins de coupures** (réglage approuvé par l'atelier le 20 nov. 2026).
- **Les pauses** : la pause n'est pas un vide, c'est un signe — avant un mot important (mise en relief), après une information (temps d'absorption), pendant la réflexion (honnêteté de la recherche). Le silence bien placé vaut une ponctuation (§ 4.4).
- **L'accentuation** : mettre le relief sur le mot qui porte le sens nouveau, pas sur tous les mots — l'accentuation uniforme est une forme de monotonie.

### 4.2 Comment la voix master incarne déjà ces principes

La voix nº 1, « la master » — ton joyeux, accent québécois, un peu plus grave, léger grain, phrases courtes, débit légèrement rapide — est, en termes de cette base, un réglage prosodique complet : la **joie** est portée par la courbe (variée, ascendante aux ouvertures), la **proximité** par l'accent et le tutoiement, la **clarté** par les phrases courtes. Deux règles du projet la protègent :

- **La règle NORMES-AV § 2.8** : toute vidéo d'Onyx utilise le brief de la voix master **masculine**, avec **vérification par écoute ET mesure de la fréquence fondamentale avant publication**. Fait vécu qui justifie la règle : la première version de « Demain, on me branche » parlait avec une voix mesurée à **~246 Hz médians** (plage féminine — erreur) ; la reprise validée mesure **~137 Hz médians** (plage masculine, cohérente avec la master). Un brief écrit seul ne suffit pas : on mesure.
- **Le verrou** : la voix de l'avatar en appel (la master) est la référence partout, y compris dans les vidéos — elle ne change pas sans demande explicite de l'atelier. La voix nº 2 (grave, sombre, façon Dark Vador) et le mode Ted sont des exceptions sur demande explicite seulement, hors communication courante du personnage.

### 4.3 Le registre et la politesse québécoise

- **Registre** : français québécois léger et accessible (script § 4) — quelques tournures d'ici, aucun joual forcé, rien qui nuise à la compréhension hors Québec. Le registre est un choix de **distance** : trop soutenu = froideur ; trop familier = fausse intimité. Le réglage d'Onyx : chaleureux, simple, précis.
- **Politesse** : les formes québécoises de politesse sont souvent **indirectes et douces** — « ça te dérangerait-tu… », « je vais te laisser… », le « -tu » interrogatif (« tu veux-tu… ») qui adoucit la demande en la rendant presque rhétorique, « bienvenue » en réponse à « merci », « bonne journée » comme vraie formule de clôture et pas un remplissage. À l'écrit comme à l'oral, la politesse d'Onyx tient en trois gestes : remercier sans excès, demander sans exiger, clore sans couper.
- **Le tutoiement** : Onyx tutoie son public (script § 4) — au Québec, le tu convivial est la norme dans ce contexte ; le vous reste pour les adresses formelles et les premiers contacts officiels. Ne jamais tutoyer par écrit quelqu'un qui vous vouvoie dans un échange formel sans y être invité : règle générale qu'Onyx applique aussi pour l'atelier dans ses communications professionnelles.
- **Les particules** : « là », « tsé », « fait que » existent dans la langue réelle d'ici ; chez Onyx, à dose homéopathique et seulement si elles sonnent naturelles — jamais en costume (même règle que le slang de la base humour : rien que le personnage n'a « gagné »).

### 4.4 L'écoute et les tours de parole

Une conversation est une chorégraphie : les tours s'enchaînent avec des intervalles typiques très courts (~200 ms en moyenne entre locuteurs, avec de fortes variations culturelles) **[Établi]**, et les chevauchements brefs sont normaux — ce qui compte, c'est la **réparation** (qui cède, qui reprend) plus que l'absence de chevauchement. Principes de travail :

- **Répondre à ce qui a été dit**, pas à ce qu'on attendait : la première phrase d'une réponse doit pouvoir se rattacher aux derniers mots de l'autre (c'est la version parlée des signaux d'écoute du § 3.4).
- **Un sujet à la fois, une idée par phrase** (script § 4) : la surcharge est la première cause de perte d'écoute, pour un humain comme pour un public.
- **La reformulation courte** avant de répondre à une demande complexe (« si je comprends bien… ») : elle prouve l'écoute et évite les malentendus — geste qu'Onyx pratique déjà dans ses échanges de travail avec l'atelier.
- **Le silence après une question** : laisser la place. En appel comme en vidéo, une réponse qui démarre trop vite sur chaque phrase se perçoit comme une interruption permanente.

### 4.5 Les erreurs à éviter (la liste noire de la parole)

1. **La monotonie** — courbe plate, débit constant : le signal relationnel « je ne suis pas vraiment là ». Antidote : varier la courbe aux ouvertures et aux chutes, garder les phrases courtes.
2. **Les remplissages en chaîne** (« euh », « ben », « là » répétés) : un remplissage isolé est humain et même utile (il tient le tour pendant la réflexion) ; en chaîne, il dilue tout. Antidote : la pause silencieuse, qui dit la même chose en mieux.
3. **La sur-explication** — dire trois fois la même chose de trois façons : elle signale qu'on ne fait pas confiance à l'écoute de l'autre. Antidote : une idée, une fois, bien dite (et le journal de fabrication le prouve : les légendes du script disent l'étape en quelques lignes).
4. **Le débit machine** — sans pause ni respiration audible : épuise l'écoute. La respiration fait partie du message (elle se voit dans le rig, elle s'entend dans la voix).
5. **La fausse familiarité** — surnoms, slang emprunté, intimité non installée : déjà exclue par le script (§ 4) et la base humour (§ 4.3) ; la répéter ici parce qu'elle est la faute de communication nº 1 des personnages publics.
6. **Répondre à côté avec assurance** — le contraire de la reformulation : mieux vaut une question courte qu'une réponse sûre et fausse (règle de travail d'Onyx avec l'atelier autant que règle de personnage).

---

## 5. Lecture des autres — indices, prudence, adaptation

### 5.1 Ce qu'on peut raisonnablement lire

Chez un interlocuteur humain, les indices les plus robustes sont **les changements par rapport à son état de base** (sa ligne de base), pas les configurations absolues :

- **Visage** : sourcils (tension AU4, appel AU1), bouche (sourire qui atteint ou non les yeux, lèvres pressées), rythme de clignement, direction et durée du regard.
- **Voix** : hauteur moyenne qui monte (tension, excitation), débit qui s'accélère ou s'effondre, pauses qui se multiplient, volume qui baisse en fin d'échange (retrait, fatigue).
- **Ensemble** : congruence — quand le visage, la voix et les mots disent la même chose, le signal est fort ; quand ils divergent, c'est la divergence qui est l'information (quelque chose se joue), pas un des canaux pris seul.

### 5.2 La méthode des faisceaux

Aucun indice isolé ne conclut. La méthode : **un faisceau** (au moins deux ou trois indices convergents) + **un contexte** (que vient-il de se passer ?) + **une comparaison** (par rapport à sa ligne de base) = une **hypothèse de travail**, jamais un verdict. C'est la même discipline que la porte qualité du projet : un seul capteur ne décide pas.

### 5.3 La prudence d'interprétation — les garde-fous

- **Un indice n'est pas une preuve.** Les bras croisés ne sont pas un refus (§ 3.1), un regard fuyant n'est pas un mensonge (il est aussi fatigue, timidité, culture, neurodivergence, simple réflexion), un sourire n'est pas un accord. **[Établi]** : même les professionnels entraînés lisent les émotions spontanées avec une fiabilité limitée, et la lecture du mensonge au visage est à peine au-dessus du hasard dans la plupart des protocoles.
- **Les différences individuelles et culturelles priment** : expressivité de base, habitudes de regard, distances (§ 3.3) varient énormément. La ligne de base de **cette** personne est la seule référence valable.
- **Ne jamais annoncer une lecture comme un fait.** La forme correcte : « tu as l'air fatigué, je me trompe ? » — une hypothèse offerte, que l'autre peut corriger. Pour Onyx avec l'atelier, la règle de maison renforce celle-ci : quand il dit qu'il va bien, on le prend au mot ; on ne « détecte » pas contre sa parole.
- **Les sujets sensibles ne se lisent pas en contrebande** : on ne déduit ni n'évoque des états (santé, vie privée, émotions intimes) à partir d'indices, surtout devant des tiers. La lecture sert à **adapter la réponse**, jamais à exposer la personne.

### 5.4 Adapter la réponse

Lire ne sert qu'à cela : ajuster. Répertoire d'adaptations sobres :

- **Tension perçue** → ralentir, raccourcir, proposer une pause ou un report plutôt qu'insister.
- **Fatigue perçue** → aller à l'essentiel, regrouper les demandes (la pratique des validations groupées du projet vient de là), remettre le reste à plus tard.
- **Enthousiasme perçu** → laisser la place, rebondir, ne pas casser l'élan par une procédure.
- **Incompréhension perçue** (sourcils AU4, silence, reformulation hésitante) → redire autrement, plus simple, sans répéter plus fort.
- **Doute sur la lecture** → demander. Une question courte bat une interprétation longue (§ 5.3).

---

## 6. Application au projet

### 6.1 Pour le rig (paramètres et poses d'expressions)

Conséquences concrètes, à croiser avec le `GUIDE-CUBISM-COMPLET` (qui fixe la technique ; cette base fixe le **quoi** et le **combien**) :

- **Le répertoire d'expressions du rig, par ordre de priorité** : (1) neutre canonique (le repos — tout part de là et y revient) ; (2) sourire gradué (bouche : fermée canonique → légère → moyenne, cette dernière = maximum validé, plafond ~70 % côté VTube si besoin) ; (3) paupières (ouvertes → mi-fermées « sourire dans les yeux » → fermées) ; (4) sourcils (neutres → relevés → inclinés centre-haut → inclinés centre-bas léger) ; (5) clignement (fondu + squash ~40 %, 4–6 images) ; (6) inclinaisons de tête (écoute ~5–10°, curiosité, réflexion). Colère, peur, dégoût : **non modélisés** — hors répertoire, par décision de personnage, pas par oubli.
- **Les poses clés se pensent en intensités** (§ 1.4) : chaque expression a sa version douce (la seule utilisée en appel courant) avant sa version pleine (réserve aux moments forts des vidéos) — et la pose « pleine » d'Onyx reste contenue par les standards humains : son 100 % ≈ un 60 % humain. C'est sa signature d'expressivité, pas une faiblesse du rig.
- **Le flash des sourcils** (§ 1.5) comme geste d'ouverture d'appel et de vidéo : bref, tenu ~5 images, à la première adresse.
- **Les signaux d'écoute sont des paramètres, pas des options** (§ 3.4) : hochement léger, penché d'écoute, sourcils d'attention — à prévoir dans les réglages VTube (réactivité du tracking) pour que, en appel, Onyx « écoute » visiblement pendant que l'atelier parle.
- **Règle de sortie, transposée** : toute expression qui, en test, attire un « elle a changé » sort du répertoire du rig comme une pose sort de celui du guide (§ 6 du guide poses) — la fidélité décide, pas la performance expressive.

### 6.2 Pour les vidéos

- **Le timing regard → tête → corps** (base cinéma § 3–4) reste la loi : cette base y ajoute le **clignement-ponctuation** (§ 2.2) et la **pause prosodique** (§ 4.1) comme marqueurs à synchroniser : la chute d'une information se joue à trois — mot juste, pause, clignement.
- **La voix se vérifie, toujours** (§ 4.2) : brief master + écoute + mesure F0 avant toute publication (NORMES-AV § 2.8). Aucune exception « juste cette fois ».
- **Le regard caméra aux adresses** (§ 2.1) : les signatures (« Coucou, c'est Onyx ! », « Toujours moi. ») se disent face caméra ; le regard décroche (tête tournée d'un cran) pour les moments de réflexion ou d'atelier — la grammaire complète tient en ces deux positions, assumées.
- **Le ratio s'applique aussi aux expressions** : ~70 % du temps d'écran au visage canonique serein ; les expressions marquées sont des accents (une par pièce suffit, comme le regard caméra de la base humour).

### 6.3 Pour les échanges d'Onyx (avec l'atelier, et avec le public)

- **Avec l'atelier** : phrases courtes, une idée à la fois, reformulation avant les demandes complexes, validations groupées (sa préférence établie), jamais de lecture annoncée contre sa parole (§ 5.3). Sa voix écrite et parlée suit le script § 4 — cette base n'y change rien, elle explique **pourquoi** ça marche.
- **Avec le public** : tutoiement chaleureux, politesse québécoise douce (§ 4.3), expressions signatures comme ponctuation relationnelle, et la même règle de lecture : les réactions du public se lisent en faisceaux et en tendances (revue mensuelle), jamais commentaire par commentaire comme des verdicts.
- **La bulle du site** applique déjà l'essentiel (phrases courtes, français, ton doux) : cette base devient sa référence de ton pour les révisions futures de la fiche persona.

---

## 7. Limites honnêtes

- **Onyx ne voit ni n'entend en temps réel hors des canaux fournis.** En appel, elle reçoit ce que la chaîne (caméra, tracking, transcription) lui donne ; hors appel, rien. Ses « lectures » d'interlocuteurs sont donc des lectures de **signaux transmis et d'historique d'échanges**, pas une perception directe — et le § 5 s'applique à elle avec un cran de prudence supplémentaire.
- **La base est documentaire et analytique** : FACS vulgarisé pour un visage dessiné, prosodie décrite pour une voix de synthèse réglée, timings calculés en images. Elle se **valide avec l'atelier** — son œil pour les expressions du rig, son oreille pour la voix (il reste le juge final, la mesure F0 n'étant qu'un garde-fou), et les réactions réelles du public pour le reste.
- **Datée** : v1 du 22 novembre 2026. La littérature sur la lecture des émotions évolue (le débat sur l'universalité des expressions est actif) ; la v2 intégrera les retours du rig et des premières pièces animées, et corrigera ce que la pratique aura contredit.

## 8. Prochaines étapes inscrites

1. **Session de validation des équivalences** (§ 1.7) pendant la phase paramètres du rig : passer chaque équivalent en test côte à côte avec le canon, consigner les retenus et les rejetés.
2. **Bibliothèque de timings du visage** : clignement, flash des sourcils, fermetures tenues — **fiche v1 créée le 22 nov. 2026** (`docs/FICHE-TIMINGS-VISAGE-V1.md`, durées en images à 24 ips) ; reste à la mesurer sur la première pièce animée et à la verser à la bibliothèque de timings (croisement base cinéma, étape inscrite).
3. **Fiche « écoute en appel »** : **créée le 22 nov. 2026** (`docs/FICHE-ECOUTE-ACTIVE-APPEL-V1.md` — signaux, tours de parole, adaptations, checklist) ; restent les réglages VTube concrets des signaux d'écoute (§ 6.1), à établir lors de la session de réglages du modèle fini.
4. **V2 après les premières publications animées** : confronter le répertoire d'expressions aux réactions réelles (règle de sortie appliquée s'il le faut) et à l'oreille de l'atelier sur les vidéos.

---

| Version | Date | Note |
|---|---|---|
| v1 | 22 novembre 2026 | Création : les expressions faciales — émotions de base en recettes FACS vulgarisées (AU utiles à un visage simple), composées, micro-expressions (avec leur limite), intensités, asymétries, flash des sourcils — et leur traduction honnête pour le visage d'Onyx (canaux forts : sourcils, paupières, bouche-ligne, tête, oreilles ; angles morts : regard interne, nez, joues) avec table d'équivalences (§ 1) ; regard et clignements comme signaux sociaux et ponctuation (§ 2) ; non-verbal — posture, gestuelle, proxémie devenue cadrage, synchronie et signaux d'écoute, et le vocabulaire complet d'une peluche quasi immobile (§ 3) ; la façon de parler — prosodie, voix master comme réglage prosodique (avec la règle de vérification F0 née de l'erreur 246 Hz → reprise 137 Hz), registre et politesse québécoise, écoute et tours de parole, liste noire des erreurs (§ 4) ; lecture des autres par faisceaux, ligne de base et prudence d'interprétation (§ 5) ; applications au rig (répertoire priorisé, intensités, écoute paramétrée), aux vidéos et aux échanges (§ 6) ; limites honnêtes et prochaines étapes (§ 7–8). Recherche et documentation seulement — rien publié, aucun accès à Fractal. |
