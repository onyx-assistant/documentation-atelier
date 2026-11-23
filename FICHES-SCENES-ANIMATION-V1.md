# Fiches d'analyse de scènes d'animation — Références de timing et d'acting (v1)

_Produit le 22 novembre 2026, à partir de la base `docs/COMPETENCES-ANIMATION-CINEMA-V1.md` (§ 9, prochaine étape nº 1 : 3–5 scènes de référence décomposées en poses clés et timings). **Analyse documentaire seulement : aucune image copiée, aucun design, personnage ou décor repris** — on étudie des principes de mouvement, comme le projet le fait déjà pour Ted et Winnie l'ourson (inspiration de timing et d'expressions, jamais de copie)._

_Honnêteté de méthode : les timings ci-dessous sont des **grilles d'analyse en images à 24 ips**, établies à partir de la grammaire connue de chaque école et de la structure des scènes (poses, tenues, accents). Ce sont des ordres de grandeur de travail pour régler le rig et écrire les briefs — pas des mesures image par image relevées sur les films. La passe de validation se fera au visionnage avec l'atelier, scène par scène, et les durées confirmées alimenteront la bibliothèque de timings (base animation § 9, étape nº 2)._

_Rappels de traduction : poses clés = valeurs extrêmes des paramètres Cubism (`ParamAngleX/Y/Z`, `ParamBodyAngleX/Z`, `ParamEyeLOpen/ROpen`, `ParamBrowLY/RY`, `ParamMouthForm`, `ParamBreath`) ; follow-through des oreilles réglé amorti ; bouche plafonnée à la variante moyenne validée ; exagérer les **timings** plutôt que les formes (base animation § 2, principe 10 plafonné par la charte)._

---

## Fiche 1 — Pixar : *Luxo Jr.* (1986) — un objet qui joue

**La scène (en mots)** : une petite lampe de bureau découvre une balle, la pousse, la suit du regard, saute dessus jusqu'à la dégonfler, puis regarde le public, déçue, avant qu'une balle plus grosse n'entre en scène. Aucun visage, aucun dialogue : tout le jeu passe par l'orientation de la « tête » (l'abat-jour), les ressorts du bras et le timing.

**Décomposition (grille à 24 ips)**

| Pose clé | Contenu | Timing indicatif |
|---|---|---|
| P1 Découverte | La lampe s'arrête, « tête » penchée vers la balle | tenue ~12–18 img avant tout geste |
| P2 Anticipation | Léger recul et compression du ressort avant le bond | ~6–8 img |
| P3 Bond | Extension complète, trajectoire en arc | ~8–10 img |
| P4 Atterrissage | Squash franc du ressort, rebond secondaire amorti | squash ~4 img, oscillation ~12 img |
| P5 Regard public | La « tête » se tourne vers la caméra, tenue longue | tenue ~18–24 img — c'est la tenue qui fait la blague |

**Principes en jeu** : anticipation (P2), squash & stretch sur un objet rigide rendu mou par le timing, arcs, **le regard qui mène** (la tête vise avant que le corps suive), appeal sans visage.

**Ce qu'Onyx en retient**
- *Rig* : la séquence regard → tête → corps avec délais de 3–6 images entre chaque niveau (overlapping appliqué au jeu) devient notre réglage par défaut des transitions d'attention : `ParamEyeBall` (micro) d'abord, `ParamAngleX/Z` ensuite, `ParamBodyAngleX` en dernier.
- *Clips* : tout brief de jeu commence par « le regard se porte d'abord sur…, la tête suit, le corps ensuite » ; une tenue finale d'au moins ~18 images après le gag ou l'accent.
- *Limite* : le squash de Luxo est large (un ressort peut s'écraser de 30 %) ; chez Onyx, la compression reste de quelques % — au-delà, la porte qualité rejette.

---

## Fiche 2 — Studio Ghibli : *Mon voisin Totoro* (1988) — l'attente sous la pluie

**La scène (en mots)** : deux enfants attendent à un arrêt de bus sous la pluie, la nuit. De longs moments sans action : les gouttes, le silence, puis une grande silhouette apparaît à côté d'elles ; un parapluie offert, une goutte qui tombe dessus — et la joie tient dans une réaction à ce minuscule événement sonore.

**Décomposition (grille à 24 ips)**

| Pose clé | Contenu | Timing indicatif |
|---|---|---|
| P1 Attente | Personnages quasi immobiles, respiration et pluie seuls mouvements | tenues de **plusieurs secondes** (72 img et +) |
| P2 Apparition | La silhouette entre sans effet ; un temps avant que les enfants la remarquent | délai de réaction ~12–20 img |
| P3 La goutte | Une goutte tombe sur le parapluie — accent sonore unique | l'accent dure ~2–4 img, la **réaction** ~18–24 img |
| P4 Joie contenue | Sourires, petit rebond de plaisir, puis retour au calme | rebond ~12 img, retour à la tenue |

**Principes en jeu** : le *ma* (l'espace respirant entre les actions), staging par le silence, secondary action minimale (la pluie, le souffle), timing lent assumé.

**Ce qu'Onyx en retient**
- *Rig / live* : le cycle de respiration (`ParamBreath`, ~4–5 s, amplitude minimale) et des **tenues longues sans geste** sont un état légitime du personnage en attente — l'écran d'attente de l'habillage live et les moments doux des clips s'écrivent ainsi : vivant parce qu'il respire, pas parce qu'il s'agite.
- *Clips* : autoriser des plans de 2–3 s à un seul micro-événement (un clignement, une oreille qui frémit) plutôt que de remplir ; le payoff d'un clip peut être un tout petit accent bien préparé.
- *Limite* : la lenteur de Ghibli repose sur des décors peints riches ; dans nos décors simples, une tenue trop longue sans respiration visible ferait « image fixe » — la respiration et les clignements irréguliers sont obligatoires pendant toute tenue.

---

## Fiche 3 — Aardman : *Wallace et Gromit* (stop-motion) — la matière touchée

**La scène (en mots, type récurrent des courts métrages)** : un gag domestique joué en plans courts — un personnage manipule un objet du quotidien, l'objet résiste ou réagit de travers, le partenaire (souvent muet) réagit par un regard et un sourcil, et le gag se conclut sur une pose tenue une fraction de plus que nécessaire.

**Décomposition (grille à 24 ips — tournage historique « en deux », 12 poses/s)**

| Pose clé | Contenu | Timing indicatif |
|---|---|---|
| P1 Installation | Pose lisible en silhouette, objet présenté | ~12 img (6 poses en deux) |
| P2 Effort | Le corps se tend vers l'objet, anticipation visible dans les mains et les épaules | ~12–18 img |
| P3 Le gag | L'événement (glissade, bascule, éjection…) en peu d'images | ~6–10 img |
| P4 Réaction muette | Regard + sourcil du partenaire, sans un mot | tenue ~12–18 img |
| P5 Pose de sortie | Le gag « atterrit » : pose tenue un souffle de plus que le confort | ~12 img |

**Principes en jeu** : pose to pose assumé, exagération des poses (pas des déformations), acting muet par le regard et le sourcil, imperfections de matière gardées comme chaleur — l'honnêteté du fait-main que la marque revendique.

**Ce qu'Onyx en retient**
- *Rig* : les sourcils (`ParamBrowLY/RY` + `Form`) sont un canal d'acting à part entière — une réaction complète peut tenir en « regard + sourcil + tenue », sans ouvrir la bouche ni changer d'expression. À inclure dans le répertoire des réactions en appel.
- *Clips* : construire les gags en 5 temps (installer, tendre, événement, réaction, tenue) ; la tenue de réaction (~12–18 img) n'est jamais rognée au montage — c'est elle qui fait rire, pas l'événement.
- *Limite* : le stop-motion « en deux » a un grain saccadé charmant sur de la pâte à modeler ; sur une peluche rendue en 2D/3D lisse, imiter la saccade ferait « bug » — on garde la structure des poses, pas la cadence saccadée.

---

## Fiche 4 — Disney : *Winnie l'ourson* (1977) — la lenteur entière

**La scène (en mots, type récurrent)** : Winnie réfléchit à un problème simple (souvent lié au miel) : il se tape doucement la tête pour « penser », énonce son idée avec une évidence totale, agit lentement, et sa réaction — joie ou perplexité — est entière, sans ironie ni second degré. (Référence d'inspiration déjà validée par l'atelier pour le timing et les expressions ; aucune reprise de design.)

**Décomposition (grille à 24 ips)**

| Pose clé | Contenu | Timing indicatif |
|---|---|---|
| P1 Réflexion | Petit geste répété (tapoter, se gratter), regard dans le vide doux | cycle lent ~24–36 img, répété 2–3 fois |
| P2 L'idée | Le regard revient, le corps se redresse — le changement d'état se lit avant toute parole | transition ~12–18 img |
| P3 Action lente | Le geste se fait à vitesse « peluche lourde et douce » | durées longues, easing très amorti |
| P4 Réaction entière | L'émotion occupe tout le corps, tenue généreusement | tenue ~18–30 img |

**Principes en jeu** : timing = caractère (la lenteur EST le personnage), appeal par la simplicité, acting sincère d'échelle (base animation § 4 : « des réactions lentes, entières, sans ironie »).

**Ce qu'Onyx en retient**
- *Rig* : les transitions d'expressions d'Onyx se règlent **lentes par défaut** (12–18 images minimum pour un changement d'état), avec des tenues de réaction de ~18–30 images ; un geste répété doucement (petit hochement, tapotement suggéré par le torse) est un état « réflexion » valide pour les moments où Onyx « écoute » en appel.
- *Clips* : pour les moments tendres, écrire « réaction entière, tenue jusqu'au bout » dans les briefs ; ne jamais couper une réaction pour gagner une seconde.
- *Limite* : la lenteur de Winnie sert un récit contemplatif ; dans un format court de 10–15 s, on en garde le principe (transitions lentes, réactions tenues) à dose réduite — une seule réaction entière par clip.

---

## Fiche 5 — Peluche filmée : *Ted* (2012) — le contraste peluche / attitude

**La scène (en mots, type récurrent)** : un ours en peluche animé en images de synthèse joue des scènes de comédie d'adulte avec un timing de stand-up : répliques sèches, pauses calculées avant la chute, réactions faciales minimales — le comique naît du contraste entre l'apparence douce et l'attitude franche. (Référence validée par l'atelier pour le timing et la franchise ; le projet ne retient ni la vulgarité ni le design — le mode « Ted » d'Onyx reste une exception activée uniquement sur demande explicite de l'atelier.)

**Décomposition (grille à 24 ips, jeu en prise de vues réelles composite)**

| Pose clé | Contenu | Timing indicatif |
|---|---|---|
| P1 Écoute | La peluche reçoit la réplique, quasi immobile | tenue pendant toute la réplique adverse |
| P2 La pause | Micro-délai avant de répondre — le vide fait partie de la blague | ~8–16 img de silence joué |
| P3 La chute | Réplique ou geste bref, tête légèrement inclinée, sourcils à peine | l'accent en ~6–12 img |
| P4 Retour au neutre | Le personnage ne « commente » pas sa propre blague | retour en ~8 img, neutre tenu |

**Principes en jeu** : timing de la comédie parlée (la pause vaut la réplique), économie faciale (le minimum lisible), staging du contraste — la silhouette douce reste constante pendant que le propos surprend.

**Ce qu'Onyx en retient**
- *Voix et clips parlés* : régler les pauses avant les chutes à ~8–16 images et ne pas les remplir (ni geste, ni clignement forcé) ; l'expression pendant la chute reste minimale — `ParamMouthForm` à peine déplacé, tête inclinée de quelques degrés.
- *Rig en appel* : l'état « écoute » (fiche 4, P1) + pause jouée donne à Onyx une présence comique même sans parler ; utile pour les réactions aux propos de l'atelier en appel.
- *Limite* : le registre adulte de Ted n'est **pas** le registre d'Onyx ; seul le mécanisme de timing est importé, jamais le ton — sauf mode Ted explicitement demandé, selon la règle permanente.

---

## Synthèse — ce que les 5 fiches changent concrètement

| Règle de travail | Source | Application |
|---|---|---|
| Le regard mène, la tête suit (délai 3–6 img), le corps ensuite | Pixar (fiche 1) | Réglage par défaut des transitions d'attention du rig |
| Tenue finale ≥ ~18 img après un accent ou un gag | Pixar, Aardman | Briefs de clips et montage |
| Respiration + clignements irréguliers obligatoires dans toute tenue longue | Ghibli (fiche 2) | États d'attente du rig, écrans live |
| Une réaction complète peut tenir en regard + sourcil + tenue | Aardman (fiche 3) | Répertoire de réactions en appel |
| Transitions d'expressions lentes (12–18 img), réactions tenues 18–30 img | Winnie (fiche 4) | Courbes du rig, briefs tendres |
| Pause de 8–16 img avant une chute, expression minimale pendant | Ted (fiche 5) | Clips parlés, présence en appel |
| Exagérer les timings, jamais les formes | Toutes (charte) | Porte qualité : dérive de bouche = rejet, timing ample = bienvenu |

_Prochaine étape : visionnage de validation avec l'atelier (une scène par fiche, quelques minutes chacune), correction des grilles de timing à l'œil, puis transfert des durées confirmées dans la bibliothèque de timings du rig (base animation § 9, étapes 1–2)._
