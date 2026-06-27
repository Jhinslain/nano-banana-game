# Assets — bananes du centre

Le jeu affiche une banane différente au centre selon l'**ère visuelle** courante.
Il charge ces 5 fichiers (style **Borderlands** : contours noirs encrés, cel-shading
comic, banane simple sans visage, vue de face, idéalement fond transparent en PNG) :

| Ère | Fichier attendu      | Look |
|-----|----------------------|------|
| 1. Juteux      | `banana-1.png` | banane jaune simple |
| 2. Bio dopée   | `banana-2.png` | veines vert acide, lueur bio |
| 3. Techno      | `banana-3.png` | circuits gravés, reflets métal |
| 4. Cyber       | `banana-4.png` | néon / holo, glow cyan-magenta |
| 5. Nano Banana | `banana-5.png` | coque chromée / robot, LED |

## Histoire & boss (Amine) — portraits par humeur

Le jeu choisit le portrait selon le moment (configuré dans `CONFIG.assets.moods`) :

| Humeur / fichier | Quand |
|------------------|-------|
| `amine-friendly.png` | Intro / recrutement · réconciliation finale |
| `amine-joyeux.png`   | Chapitres 1–3 (tout va bien) |
| `amine-greedy.png`   | Chapitres 4–6 (ça dérape, cupide) |
| `amine-mechant.png`  | Chapitres 7–8 · trahison · début de combat |
| `amine-furieux.png`  | Combat phase 2 (66 % PV) |
| `amine-peureux.png`  | Combat phase 3 (33 % PV) |
| `amine-moqueur.png`  | Bombe tranchée / défaite du joueur |
| `amine-defeated.png` | Amine vaincu / penaud (1res lignes de la victoire) |
| `amine-nano-banana.png` | Sprite du **boss** en haut de l'écran de combat |

**Fallback** : si une humeur manque → `amine2.png` (portrait de base), puis emoji
(🧔🏽 / 😈). Les portraits sont **préchargés juste avant** le moment où ils servent
(pas au démarrage) et affichés **tels quels** (pas de filtre/recolor).

## Fallback

Si un fichier manque (ou ne charge pas), le jeu affiche un **emoji 🍌** à la place :
le jeu reste 100 % jouable sans aucune image.

## Note technique — fond damier

Si une image est exportée **sans canal alpha** (le damier gris/blanc « transparence »
est alors *peint* dans les pixels), le jeu le détecte et **retire automatiquement** ce
fond clair au chargement (flood-fill sur `<canvas>`, depuis les bords). Pour un rendu
optimal, fournis quand même des PNG à fond réellement transparent (RGBA).

Ère visuelle : pilotée par les producteurs avancés possédés
(Serre → ère 2, Usine → ère 3, IA → ère 4, Trou de ver / prestige → ère 5).
La transition entre deux bananes est un crossfade doux.
