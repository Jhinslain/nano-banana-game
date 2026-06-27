# 🍌 Nano Banana

Un clicker / idle sur le thème de la banane. Tu tapes une banane, tu débloques des
façons de plus en plus folles d'en faire quelque chose (les manger, les vendre, les
planter, embaucher des singes, fonder une république bananière, un labo génétique,
alimenter des villes, des planètes…) jusqu'à assembler la **NANO BANANA** ultime — la
condition de victoire de cette v1.

Style **comic / Borderlands** (contours encrés, jaune banane), juice à fond
(banane qui rebondit, nombres flottants, particules), **mobile-first** et jouable au pouce.

## ▶️ Lancer le jeu

- **En local :** double-clique sur `index.html` (s'ouvre dans le navigateur, aucun serveur requis).
- **Hébergé :** dépose le dossier sur GitHub Pages / Netlify / n'importe quel hébergeur statique.
  Tout est dans un seul fichier `index.html` (HTML + CSS + JS vanilla, zéro dépendance, zéro build).

> Astuce : les gains hors-ligne ne fonctionnent que si tu rouvres le **même** navigateur
> (la sauvegarde est dans le `localStorage`). Utilise **Menu → Exporter** pour transférer ta partie.

## 🎮 Comment jouer

1. **Clique la banane** au centre pour récolter des 🍌.
2. Achète des **producteurs** (colonne de gauche / onglet Ferme) pour produire en automatique (idle).
3. Achète des **améliorations** (boosts de clic + multiplicateurs globaux).
4. Franchis les 8 paliers de l'**échelle bananière** (chaque palier débloque un ingrédient).
5. Au palier 8, **assemble la NANO BANANA** → écran de victoire.
6. Bonus post-fin : **Composter** (prestige) pour repartir avec de l'ADN 🧬 permanent.

Attrape les **bananes dorées 🌟** qui apparaissent aléatoirement pour des bonus temporaires
(frénésie ×7, pluie de bananes, clic ×77).

### Mécaniques avancées

- **Frénésie de clic** : clique vite pour remplir la jauge sous la banane → BPC ×7 quelques secondes.
- **Synergies** : certains producteurs en boostent d'autres (singes → bananeraies, usines → drones, labos → clic).
- **Arbre de talents ADN** (dans le Menu, après un prestige) : 3 branches (Bio / Force / Tech) à débloquer avec ton ADN.
- **Bananes spéciales rares** 🔵⬛☢️👻 : bonus **permanents** (1 fois par partie), rangées dans une **collection**.
- **Banane pourrie** 🤢 : ressemble à une dorée mais inflige un malus — n'y touche pas !
- **Événements à choix** 🎲, **météo bananière** (chip en haut à gauche) et **quêtes du jour** (onglet Stats).
- **Journal du savant** 📖, **titres** évolutifs et **succès cachés** 🕵️ à débloquer.

### Histoire & combat de boss (Amine)

- **Intro** : ton pote **Amine** te recrute pour ramasser « toutes les bananes » (sert de tuto).
- **8 chapitres** : à chaque palier, un message d'Amine — il bascule doucement de mentor sympa à mégalo inquiétant.
- **La trahison** : quand tu assembles la NANO BANANA, Amine te **vole toutes tes bananes** et se transforme en **AMINE-BANANE**.
- **Combat de boss « Banana Ninja »** (plein écran, mobile-first) : **tranche** les bananes au doigt/souris pour charger le canon 🍌, **évite les bombes** 💣 (−1 vie sur 3), tire pour réduire les PV d'Amine. 3 phases de difficulté croissante.
- **Préparation** : entre deux tentatives, achète des **améliorations de combat** permanentes avec tes bananes (vie en plus, aimant, canon, munitions, gilet anti-bombe, ralentisseur). Perdre n'est pas un mur : on farme, on revient plus fort.
- **Vraie fin** : battre Amine-Banane = victoire, récupération des bananes volées (et plus) + réconciliation. Puis bac à sable, prestige, ou **rejouer le combat**.
- **Mode debug** 🐞 dans le Menu (donner des bananes/ADN, changer de thème, gagner instantanément…).

> Chaque mécanique peut être désactivée individuellement via `CONFIG.features` en haut du `<script>`.

## 💾 Sauvegarde

- 100 % locale (`localStorage`, clé `nanoBananaSave`). Auto-save toutes les 15 s + à la fermeture.
- **Export / Import** en base64 via le Menu (pas de compte, pas de serveur).

## ⚙️ Équilibrage

Toutes les valeurs (coûts, gains, seuils des paliers, coût final, durée des bonus…) sont
regroupées dans l'objet `CONFIG` en haut du `<script>` de `index.html`, faciles à ajuster.
Cible : première run ≈ 2–3 h (clic + idle mêlés).

## 🖼️ Assets

Voir [`assets/README.md`](assets/README.md). Les 5 images de banane sont optionnelles :
le jeu fonctionne sans elles (fallback emoji 🍌).
