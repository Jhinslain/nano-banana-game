# 🍌 Nano Banana — Équilibrage & orientation tycoon/idle

> Refonte de l'équilibrage : renforcer le côté **tycoon/idle**, rendre les **auto-fermes vivantes**, faire en sorte que **chaque producteur reste utile à améliorer**, et **rééquilibrer la Frénésie** (trop forte + à débloquer).
> Toutes les valeurs vont dans `CONFIG` (ajustables). Cible : 1re run ~2–3 h.

---

## 1. Philosophie : plus tycoon, plus idle

- **Répartition visée : ~70 % idle / 30 % clic actif** (aujourd'hui le clic + Frénésie pèsent trop).
- Le **clic** sert surtout en **early game** et pour les coups de boost ; ensuite, ce sont les **auto-fermes** qui font le jeu.
- Le plaisir doit venir de **voir sa production tourner et grossir**, d'optimiser ses producteurs, pas de spammer le clic.

---

## 2. Frénésie — à débloquer + rééquilibrée

**Problème.** La Frénésie (×7 BPC, dispo dès le départ, se remplit en cliquant) est **trop puissante** et casse le rythme idle.

**Solution.**
1. **La verrouiller derrière une amélioration** à acheter : `🔥 Mode Frénésie` (déblocage unique). Avant ça, pas de jauge → early game = clic simple, lisible.
2. **Baisser la puissance** et la rendre **améliorable par paliers** plutôt que forte d'un coup :

| Niveau d'amélioration | Effet Frénésie | Coût |
|---|---|---|
| Déblocage `🔥 Mode Frénésie` | ×2 BPC, 6 s, cooldown 25 s | moyen |
| Frénésie II | ×3 BPC, 7 s | élevé |
| Frénésie III | ×4 BPC, 8 s, cooldown 20 s | très élevé |

3. **Important :** la Frénésie ne doit booster que le **clic (BPC)**, jamais la prod passive — sinon elle redevient dominante. En fin de partie, le clic étant minoritaire, elle reste un bonus sympa sans déséquilibrer.

**Config de départ proposée**
```
frenzy:{ unlocked:false, gainPerClick:0.05, decayPerSec:0.04, idleDelay:0.5,
         mult:2, duration:6, cooldown:25 }   // mult/duration montent via améliorations
```

---

## 3. Producteurs : scaling par niveau (le cœur du tycoon)

**Objectif.** Qu'**améliorer la Main cueilleuse (producteur 1) reste utile même en fin de partie**. Pour ça, la production d'un producteur **dépend du nombre possédé** via des **paliers de doublement** (modèle Cookie Clicker).

### 3.1 Paliers de doublement (milestones)
À chaque seuil de quantité possédée d'**un même producteur**, sa **production unitaire double** (×2).

**Seuils proposés :** 10, 25, 50, 100, 150, 200, 250, 300, 400, 500 (→ jusqu'à ×1024 par unité au max).

```
prodUnitaire(p) = p.rate × 2 ^ (nbPaliersAtteints(count[p]))
prodTotale(p)   = count[p] × prodUnitaire(p) × multGlobaux
```

**Effet concret.** Acheter encore des producteurs « bas de gamme » continue de **doubler** leur rendement → ils ne deviennent jamais inutiles, et viser le prochain palier (« encore 6 Singes pour ×2 ») devient un mini-objectif satisfaisant.

### 3.2 Améliorations ciblées qui scalent avec le niveau
En plus des doublements automatiques, des **améliorations achetables par producteur** dont le gain **dépend du nombre possédé** :

- Ex. « Gants ergonomiques » : **+1 % de prod de la Main cueilleuse par Main possédée** (donc plus tu en as, plus l'upgrade rapporte).
- Ex. « Bananes plus mûres » : la prod du producteur ciblé **×2**, débloquée à 50 unités.
- Synergies (cf. doc améliorations) : « chaque Singe booste les Bananeraies de +1 % ».

> Principe clé demandé : **le gain d'une amélioration croît avec le niveau du producteur** → améliorer les producteurs 1-2-3 reste pertinent tout du long.

### 3.3 Affichage (lisibilité)
Sur chaque carte de producteur, montrer :
- quantité possédée + **prochain palier de doublement** (« 44/50 → ×2 »),
- **prod unitaire actuelle** et **prod totale** de ce producteur,
- **part dans la prod totale** (%) → aide à décider quoi acheter.

---

## 4. Rendre les auto-fermes « vivantes » (feel tycoon)

**Problème.** Les producteurs sont juste des lignes de boutique ; on ne « voit » pas qu'ils bossent.

**Solutions (visuel + feedback) :**
- **Pulse de production** : à chaque tick, la carte d'un producteur qui produit fait un léger flash/scale, ou une petite **barre de cycle** qui se remplit puis « pop » (= une fournée de bananes).
- **Mini-bananes qui s'envolent** depuis la carte ou depuis la banane centrale, proportionnelles à la prod (effet « ça tourne »).
- **Compteur animé** : le compteur principal **égrène** la prod/s en continu (tween fluide) au lieu de sauter — on sent l'argent rentrer.
- **Bandeau « ÇA PRODUIT »** : sous la banane, un petit ticker « +X 🍌/s » qui vit.
- **Producteur le plus rentable** mis en avant (badge « ⭐ top prod »).
- **Scène centrale évolutive** : optionnel — quelques petites icônes d'ouvriers/drones qui apparaissent autour de la banane selon tes producteurs dominants (purement cosmétique, gros effet tycoon).

> Tout doit rester **léger sur mobile** : animations CSS simples, pas de surcharge en clic rapide (respecter `prefers-reduced-motion` + plafond de particules).

---

## 5. Hors-ligne / idle renforcé

- **Cap hors-ligne** de départ **8 h**, **augmentable** via amélioration `🔋 Batterie à bananes` (→ 12 h, 24 h…). Donne une raison d'investir dans l'idle.
- **Écran de retour** clair et gratifiant : « Pendant ton absence (3 h 12), tes fermes ont produit **X 🍌** » + répartition par producteur si possible.
- Option **rendement hors-ligne** : commencer à **70 %** du rendement en ligne (encourage à revenir jouer) — réglable, mettre 100 % si on veut full idle-friendly.

---

## 6. Multiplicateurs globaux (rester sous contrôle)

Empilement actuel : palier (×1.30/étape), succès (+1 %/succès), ADN (+2 %/🧬). C'est bien, mais **surveiller l'explosion** quand ça se cumule avec les doublements par producteur.

- Garder les **% globaux modestes** (les gros sauts viennent des paliers de doublement par producteur, c'est plus lisible).
- **Coût des producteurs** : conserver `costGrowth = 1.15` (signature du genre). Vérifier que la **courbe revenu vs coût** garde le rythme (toujours « presque » capable de s'offrir le prochain palier → accroche).
- **Prestige** : `ADN = floor(sqrt(total / 1e12))`. Vérifier qu'un 1er compostage arrive naturellement vers la fin de la 1re run (≈ 2–3 h), pas avant.

---

## 7. Réglages de config proposés (point de départ)

```js
// scaling par producteur
producerMilestones:[10,25,50,100,150,200,250,300,400,500], // chaque seuil : ×2 prod unitaire
// frénésie (verrouillée au départ)
frenzy:{ unlocked:false, mult:2, duration:6, cooldown:25, gainPerClick:0.05, decayPerSec:0.04 },
// idle
offlineCapHours:8, offlineRate:0.7,   // 70% du rendement hors-ligne
// globaux (inchangés, à surveiller)
costGrowth:1.15, eraBonusPerStep:1.30, achBonus:0.01, adnBonus:0.02, prestigeDivisor:1e12,
```

---

## 8. Checklist d'équilibrage (à tester)

- [ ] Sans Frénésie, l'early game (paliers 1-2) est **fluide au clic** et bascule vite en idle.
- [ ] La Frénésie débloquée est un **plus**, pas l'arme principale.
- [ ] Acheter le producteur 1 à 100+ unités **double encore** sa prod et reste **rentable**.
- [ ] On **voit** clairement que les fermes produisent (anim + ticker + compteur qui égrène).
- [ ] La 1re **NANO BANANA** (fin) tombe vers **2–3 h** de jeu mêlé.
- [ ] Pas d'explosion incontrôlée des multiplicateurs (vérifier en mode debug fin de partie).
- [ ] Fluide sur mobile, même en clic rapide.
```
