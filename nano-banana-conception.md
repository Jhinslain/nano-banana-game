# 🍌 Nano Banana — Doc de conception

> Jeu **navigateur** (desktop + **mobile**), UI/UX simple et moderne. Sauvegarde **locale**, pas de BDD ni de compte.
> Pur esprit **clicker / idle** (Cookie Clicker, AdVenture Capitalist). Pas de mine : le sujet, c'est **la banane**.
> Question fondatrice : **« on fait quoi avec plein de bananes ? »** → la réponse EST la progression.
> Thème : on commence **juteux/cartoon**, ça évolue vers le **cyber/techno/nano**.
> ⚑ **La v1 a une VRAIE FIN** (condition de victoire), tout en restant complet et rejouable.

---

## 0. Contraintes du livrable (v1)

- **Plateforme :** navigateur, responsive, **jouable au pouce sur téléphone** (gros bouton central, UI verticale, zones tactiles ≥ 44 px).
- **Sauvegarde :** 100 % locale (localStorage) + export/import. Aucune base de données.
- **Ton :** drôle, vannes banane/tech, un brin novateur.
- **Optimisé :** 1 fichier, vanilla JS, boucle légère, fluide sur mobile.
- **Fin de jeu :** objectif final atteignable → écran de victoire. Le joueur peut « finir » cette v1 (puis continuer / prestige en bonus).

---

## 1. Pitch

Tu tapes sur une banane pour en récolter. Avec tes bananes, tu débloques **des façons de plus en plus folles d'en faire quelque chose** : les manger, les vendre, les planter, embaucher des singes, fonder une république bananière, monter un labo génétique, alimenter des villes, puis des planètes… jusqu'à créer la **NANO BANANA** ultime. Pur clicker/idle : le clic satisfait, l'idle fait tourner, et l'**échelle bananière** donne le fil et la fin.

**Public :** un pote, pour le fun. Pas de monétisation, pas de serveur, pas de compte.

---

## 1bis. L'échelle bananière — « on fait quoi avec plein de bananes ? »

Le fil conducteur (et ce qui mène à la fin) est une **échelle d'usages** de plus en plus absurdes et tech. Chaque palier se débloque avec un seuil de bananes totales, change l'ère visuelle, ouvre de nouveaux producteurs et balance une vanne.

| Palier | Usage | Idée / ton | Ère visuelle |
|---|---|---|---|
| 1 | **Manger** | snack, tu prends des forces | Juteux 🍌 |
| 2 | **Vendre** | stand → empire commercial 🍌💰 | Juteux |
| 3 | **Planter** | plantations automatiques | Juteux/Bio |
| 4 | **Nourrir des singes** | main-d'œuvre simiesque 🐒 | Bio dopée |
| 5 | **Banana Republic** | tu diriges un pays à la banane | Transition |
| 6 | **Banane-science** | labo génétique, nano-banane | Techno |
| 7 | **Banane-énergie** | tu alimentes des villes 🔌 | Cyber |
| 8 | **Banane cosmique** | terraformer des planètes, galaxie 🌌 | Quantique |
| ★ | **NANO BANANA** | la singularité bananière = **FIN** | Quantique+ |

C'est un **axe horizontal de déblocages thématiques** (pas de profondeur/mine), parfaitement dans l'esprit clicker — et il fournit naturellement la condition de victoire.

---

## 1ter. Mécanique banane-native (le petit twist novateur) : la **Maturité**

Les bananes **mûrissent puis pourrissent** avec le temps :
- Banane fraîche = plein gain. Trop stockée = elle noircit → léger malus si on accumule sans rien faire.
- Ça pousse à **dépenser/convertir** au lieu de juste regarder le tas grossir (anti-AFK passif, plus de décisions).
- Les bananes pourries deviennent du **compost** → alimente le **prestige (« Composter »)**. Boucle thématique cohérente.
- *Optionnel v1 : peut être simplifié ou désactivable si ça complexifie trop le premier livrable.*

---

## 2. Boucle de jeu (game loop)

```
CLIC → bananes → acheter améliorations → production passive (idle)
   ↑                                              ↓
   └──────── prestige (reset + bonus) ←───────────┘
```

Trois échelles de temps qui se superposent (principe clé des bons idle) :
- **Seconde** : le clic, satisfaction immédiate (« juice »).
- **Minutes/heures** : la prod passive qui tourne, on revient voir.
- **Jours/semaines** : le prestige, la vraie durée de vie.

Cible d'équilibrage classique : **~60 % de la progression vient de l'idle, ~40 % du clic actif**. Ça garde le jeu accessible sans punir ceux qui s'investissent.

---

## 3. Monnaie & ressources

| Ressource | Symbole | Rôle |
|---|---|---|
| Bananes | 🍌 | Monnaie principale (clic + prod) |
| Bananes/sec (BPS) | — | Production passive cumulée |
| Bananes/clic (BPC) | — | Gain par clic |
| ADN de banane | 🧬 | Monnaie de prestige (rare, permanente) |

---

## 4. Améliorations achetables

Deux familles :

**A. Producteurs** (génèrent du BPS automatiquement) — chacun débloque visuellement une étape de l'évolution :

| # | Producteur | Ère / thème | BPS base | Coût base |
|---|---|---|---|---|
| 1 | Main cueilleuse | Juteux 🌴 | 0,1 | 15 |
| 2 | Singe employé | Juteux 🐒 | 1 | 100 |
| 3 | Bananeraie | Juteux 🌱 | 8 | 1 100 |
| 4 | Serre hydroponique | Transition 💧 | 47 | 12 000 |
| 5 | Drone récolteur | Techno 🛸 | 260 | 130 000 |
| 6 | Usine à bananes | Techno 🏭 | 1 400 | 1,4 M |
| 7 | Labo génétique | Cyber 🧪 | 7 800 | 20 M |
| 8 | IA bananière | Cyber 🤖 | 44 000 | 330 M |
| 9 | Nano-réplicateur | Quantique ⚛️ | 260 000 | 5 Md |
| 10 | Trou de ver à bananes | Quantique 🌀 | 1,6 M | 75 Md |

**B. Boosts de clic / multiplicateurs** (achat unique ou par paliers) :
- Gants en peau de banane : +1 BPC
- Cyber-gant : BPC ×2
- Améliorations ciblées : « double la prod de la Bananeraie », etc.
- Upgrades globales : « toute la production ×2 » à des paliers de bananes.

---

## 5. Maths d'équilibrage

**Coût d'un producteur** (croissance exponentielle, la signature du genre) :

```
coût(n) = coût_base × 1.15^(nombre_déjà_possédés)
```

Facteur **1.15** = valeur de Cookie Clicker, éprouvée. (Entre 1.07 et 1.15 selon les références.) Production qui grandit en multiplicatif, coûts en exponentiel → début généreux, puis chaque achat « coûte de plus en plus », ce qui crée l'accroche.

**Gating / découverte :** on ne montre pas tout d'un coup. Chaque producteur apparaît seulement quand le joueur approche de son coût (ex. visible à partir de 50 % du prix). La complexité se dévoile = moteur de « fun par la découverte ».

---

## 6. Production passive & hors-ligne (idle)

- Boucle de jeu : tick toutes les ~100 ms, `bananes += BPS × dt`.
- **Gains hors-ligne :** à la fermeture on enregistre `lastSeen` (timestamp). Au retour : `gains = BPS × secondes_écoulées`.
- **Cap hors-ligne :** plafonné (ex. **8 h** au début, augmentable via upgrade « batterie à bananes »). Encourage à revenir sans punir une nuit de sommeil.
- Au retour, écran clair : « Pendant ton absence (3 h 12), tes bananes ont produit **X 🍌** ». Feedback = rétention.

---

## 6bis. 🏁 Condition de victoire (la FIN de la v1)

Le jeu a une ligne d'arrivée nette :

- Gravir toute l'**échelle bananière** jusqu'au palier 8, puis réunir de quoi **assembler la NANO BANANA ultime** (coût final énorme en bananes + un ingrédient débloqué à chaque palier).
- À l'assemblage → **écran de victoire** : animation, stats de run (temps total, clics, bananes dorées), message drôle (« Tu as atteint la singularité bananière »).
- **Après la fin :** rien n'est bloqué. On propose 2 voies douces : continuer en bac à sable, ou **Composter (prestige)** pour recommencer plus vite. La fin existe, mais la rejouabilité reste.

Estimation de durée jusqu'à la fin (à équilibrer en test) : **première run ~2–4 h de jeu actif/idle mêlés**. Assez long pour un premier livrable, assez court pour que le pote « gagne » et en parle.

---

## 7. Prestige / Rebirth (bonus post-fin, rejouabilité)

Débloqué une fois la fin atteinte (ou un seuil élevé). Le joueur peut **« Composter »** sa partie : reset des bananes et producteurs, mais gain d'**ADN de banane 🧬** permanent.

**Formule (racine carrée du total gagné) :**

```
ADN_gagné = floor( sqrt( bananes_totales_de_la_run / 1e12 ) )
```

Chaque 🧬 donne un bonus permanent, ex. **+2 % à toute la production** (cumulatif). La racine carrée garantit des resets de plus en plus exigeants (doubler son ADN demande ~4× plus de bananes) → progression infinie mais qui reste « méritée ».

**Boutique d'ADN** (bonus permanents qui survivent au reset) :
- Démarrage rapide : commence chaque run avec X producteurs.
- Doigts bioniques : BPC permanent ×2.
- Pouce vert quantique : prod ×2.
- Débloque le mode visuel cyber dès le départ.

---

## 8. Succès & événements

**Banane dorée 🌟 (golden banana) :** apparaît aléatoirement à l'écran (~toutes les 1-5 min), reste quelques secondes. Cliquer dessus → bonus aléatoire :
- « Frénésie » : prod ×7 pendant 30 s.
- « Pluie de bananes » : gros lot instantané (= X min de prod).
- « Clic chargé » : BPC ×77 pendant 15 s.
(Clin d'œil au 7 porte-bonheur de Cookie Clicker.)

**Succès (achievements)** — objectifs qui donnent un petit bonus global et de la satisfaction :
- Premiers pas : 100 / 10k / 1M / 1Md bananes.
- Cliqueur fou : 100 / 1 000 / 10 000 clics.
- Doré : cliquer 1 / 7 / 77 bananes dorées.
- Renaissance : 1er prestige, 10e prestige.
- Idle : récupérer un gros lot hors-ligne.

Les compteurs (clics totaux, bananes dorées) **persistent à travers les prestiges**.

---

## 9. Évolution visuelle (le twist « juteux → cyber »)

Le palier visuel est piloté par une variable `era` (dérivée du total de bananes ou du nombre de producteurs avancés possédés). À chaque ère, on change : la banane centrale, la palette, le fond, la typo, les sons.

| Ère | Déclencheur (~) | Banane | Palette | Ambiance |
|---|---|---|---|---|
| 1. Juteux | départ | 🍌 cartoon brillante | jaunes chauds, dégradés juteux | rebondi, doux |
| 2. Bio dopée | producteur 4 | banane veinée verte | jaune + vert acide | « labo nature » |
| 3. Techno | producteur 6 | banane avec circuits | jaune + bleu acier | reflets métal |
| 4. Cyber | producteur 8 | banane néon / hologramme | fond sombre, glow néon | scanlines, glitch léger |
| 5. Quantique | producteur 10 / 1er prestige | nano-banane particules | violet/cyan, fond spatial | particules, lueur |

Transitions douces (CSS transitions sur les variables de couleur). **Implémentation simple :** un thème = un set de variables CSS (`--bg`, `--accent`, `--banana-emoji`/SVG), on swap la classe sur `<body>`.

---

## 10. « Juice » / game feel (peu coûteux, gros impact)

- Banane qui **rebondit/scale** au clic (transform).
- **Nombres flottants** « +12 🍌 » qui montent et s'effacent au point du clic.
- Petit **shake** ou pulse sur les gros gains.
- Sons courts optionnels (toggle mute) : pop au clic, chime sur banane dorée.
- Compteur animé (tween) plutôt que saut brut.
- Effet de particules de banane sur les jalons.

---

## 9bis. Plan d'assets visuels

**Bananes du centre (5 images fournies par l'utilisateur)** — style Borderlands (contours encrés + cel-shading), banane simple sans visage, fond transparent (PNG). Le jeu charge ces fichiers selon l'ère courante :

| Ère | Fichier attendu | Look |
|---|---|---|
| 1. Juteux | `assets/banana-1.png` | banane jaune simple |
| 2. Bio dopée | `assets/banana-2.png` | veines vert acide, lueur bio |
| 3. Techno | `assets/banana-3.png` | circuits gravés, reflets métal |
| 4. Cyber | `assets/banana-4.png` | néon/holo, glow cyan-magenta |
| 5. Nano Banana | `assets/banana-5.png` | coque chromée/robot, LED |

> Le code doit gérer le cas où une image manque (fallback : emoji 🍌 ou banane SVG) pour rester jouable sans les assets. Transition douce (crossfade) au changement d'ère.

**Producteurs & améliorations : pas d'images.** On utilise des **emojis** comme icônes (🖐️ 🐒 🌱 💧 🛸 🏭 🧪 🤖 ⚛️ 🌀 pour les producteurs ; gants/éclair/✨ pour les upgrades). Léger, cohérent, remplaçable plus tard par des icônes SVG sans toucher au moteur.

**Prompts de génération des 5 bananes (référence, style Borderlands) :** voir fichier séparé ou annexe — banane simple sans visage, contours noirs encrés, cel-shading comic, vue de face centrée, fond transparent, pas d'ombre, PNG isolé ; évolution bio → techno → cyber → chromée/robot pour la n°5.

---

## 10bis. UI/UX mobile-first

- **Layout vertical** : en haut le compteur + barre de progression vers le prochain palier bananier ; au milieu la **grosse banane** (cible de clic, ~40 % de l'écran) ; en bas un **dock à onglets** (Producteurs / Améliorations / Succès / Échelle).
- Boutique en **liste scrollable** d'une colonne, cartes larges, prix + gain bien lisibles, bouton « Acheter ×1 / ×10 / max ».
- **Zones tactiles ≥ 44 px**, pas de hover indispensable, feedback au `touchstart` (réactivité).
- Banane dorée = bulle flottante facile à toucher.
- `meta viewport` verrouillé, pas de zoom involontaire, `user-select:none` sur les boutons (anti sélection au spam-clic).
- Teste le **clic rapide répété** sur mobile (pas de délai 300 ms, pas de surbrillance bleue).

---

## 11. Sauvegarde (sans compte)

- **localStorage**, clé `nanoBananaSave`, objet JSON.
- Auto-save toutes les ~10 s + à la fermeture (`beforeunload`).
- Contenu : bananes, producteurs possédés, upgrades, ADN, succès, compteurs, `lastSeen`, `era`, réglages (mute).
- **Export / Import** : bouton qui sort/charge le save en base64 → le pote peut sauvegarder son fichier ou changer de navigateur sans compte.
- Versionner le save (`saveVersion`) pour migrer si on change le format.

---

## 12. Stack technique

- **1 seul fichier `nano-banana.html`** : HTML + CSS + JS vanilla, zéro dépendance, zéro build.
- Tout en mémoire + localStorage. Aucun backend.
- Marche en double-clic sur le fichier ou hébergé n'importe où (GitHub Pages, Netlify…).
- Boucle via `requestAnimationFrame` ou `setInterval(100ms)` ; les gros nombres : on commence en `Number` (suffit jusqu'à ~1e15), formatage « K / M / Md / T… » pour l'affichage. Notation scientifique au-delà.

---

## 13. Roadmap de dev (proposée)

1. **MVP** : banane cliquable + compteur + barre vers le 1er palier + 3 producteurs + save localStorage. *(jouable de bout en bout)*
2. Coûts exponentiels + gating d'apparition + formatage des nombres.
3. **Échelle bananière** (les 8 paliers d'usage, déblocages par seuils) → c'est le fil vers la fin.
4. Idle + gains hors-ligne avec écran de retour.
5. Les 10 producteurs + upgrades de clic + multiplicateurs globaux.
6. **Condition de victoire + écran de fin** (assemblage Nano Banana). ← indispensable au 1er livrable.
7. Bananes dorées + succès + compteurs persistants.
8. Évolution visuelle (5 ères, liées aux couches) + juice (nombres flottants, anims) + UI mobile.
9. Prestige (ADN) post-fin + export/import save + réglages (mute) + polish.

**Priorité v1 livrable :** étapes 1→8 (jeu complet avec une fin, mobile, drôle). Étape 9 = bonus si le temps le permet.

---

## 14. Questions ouvertes à trancher avant de coder

- Nom des bananes dorées / textes : on garde le ton « blague entre potes » ? (références, vannes perso ?)
- Sons : on en met (légers, mutables) ou silencieux par défaut ?
- Nombres : on plafonne « réaliste » ou on assume l'absurde (bananes par googol) ?

---

### Références
Bonnes pratiques courbes de coût & équilibrage (1.07–1.15, gating, 60/40 idle/actif) ; maths du prestige (racine carrée du total) ; gains hors-ligne & cap ; bananes dorées / succès « chiffre 7 » & rétention de Cookie Clicker. Sources :
- [The Math of Idle Games, Part III — Game Developer](https://www.gamedeveloper.com/design/the-math-of-idle-games-part-iii)
- [Numbers Getting Bigger: The Design and Math of Incremental Games — Tuts+](https://code.tutsplus.com/numbers-getting-bigger-the-design-and-math-of-incremental-games--cms-24023a)
- [Math — the backbone of Idle Games — Medium](https://medvescekmurovec.medium.com/math-the-backbone-of-idle-games-part-1-f46b54706cf1)
- [Idle Clicker Games: Best Practices — Mind Studios](https://games.themindstudios.com/post/idle-clicker-game-design-and-monetization/)
- [Golden Cookie Achievements — Cookie Clicker Wiki](https://cookieclicker.wiki.gg/wiki/Golden_Cookie_Achievements)
