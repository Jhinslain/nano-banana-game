# 🍌 Nano Banana — Améliorations UI/UX & design (v2)

> Liste d'améliorations à appliquer à `index.html` (clicker/idle vanilla, mobile-first).
> Validées toutes ensemble. Triées par priorité. Chaque point : **problème → solution → notes d'implémentation**.
> ⚠️ Ne pas casser l'existant (save, succès, prestige, gains hors-ligne, export/import).

---

## 🔴 P1 — Refonte des panneaux (le plus important)

**Problème.** Les widgets sont des **fenêtres flottantes déplaçables** (drag/replier/fermer). Sur mobile c'est casse-pieds : un panneau ouvert **recouvre la banane** (placée vers 38 % de la hauteur), et gérer 5 fenêtres + dock demande trop d'effort. Le drag n'apporte rien à un clicker.

**Solution — supprimer le drag, repenser le placement :**

### Sur mobile (largeur < 980px)
- Le panneau actif devient une **bottom-sheet** : plein largeur, **ancrée en bas**, juste au-dessus du dock, qui **glisse depuis le bas**.
- **Un seul panneau ouvert à la fois.** Taper un onglet du dock ouvre sa feuille ; retaper la ferme.
- La feuille a une **hauteur plafonnée** (ex. `max-height: 52dvh`) et **ne recouvre JAMAIS la banane** : la zone haute (compteur + banane + jauge) reste toujours visible et cliquable.
- Contenu scrollable à l'intérieur de la feuille. Poignée visuelle en haut (petite barre) + bouton fermer.
- **Plus de drag, plus de cascade, plus de chevauchement.**

### Sur desktop (largeur ≥ 980px)
- Panneaux **ancrés sur les côtés**, **fixes** (pas de drag) : la production/améliorations à gauche, succès/stats à droite, par exemple — placés pour **ne pas gêner la banane centrale**.
- Repli/fermeture OK, mais **positions fixes** (colonnes latérales), pas de déplacement libre.

**Notes d'implémentation.**
- Retirer toute la logique de **drag** (`pointerdown/move/up` sur `.widget-bar`, `setPointerCapture`, classe `.drag`).
- Remplacer `defaultLayout()` / `applyWidget()` / `clampPos()` par : un mode **desktop = colonnes latérales fixes** et un mode **mobile = bottom-sheet** (position via CSS, pas via `left/top` calculés en JS).
- Garder le **dock** comme barre d'onglets (déjà bien) : il pilote l'ouverture d'une seule feuille à la fois sur mobile.
- CSS bottom-sheet : `position:fixed; left:0; right:0; bottom:[hauteur dock]; transform:translateY(100%)` fermé → `translateY(0)` ouvert, `transition:transform .25s ease`.
- Conserver la **persistance** de quel panneau est ouvert dans le save (`S.ui`), mais sans coordonnées x/y.
- Respecter `env(safe-area-inset-bottom)` (déjà fait pour le dock).

---

## 🟠 P2 — Renommer « Ferme » (piège de langue)

**Problème.** Le widget « Auto-ferme » et l'onglet « Ferme » se lisent comme *fermer* (close) avant *ferme* (farm).

**Solution.** Renommer partout en **« Production »** (ou « Récolte » / « Auto 🏭 »).
- Widget titre : `🏭 Auto-ferme` → **`🏭 Production`**.
- Dock : `🏭 Ferme` → **`🏭 Prod`** (label court).

**Notes.** Simple remplacement de libellés dans le HTML (lignes du widget `w-prod` et du `#dock`). Aucun impact logique.

---

## 🟠 P3 — Dévoilement progressif des systèmes

**Problème.** Tous les `CONFIG.features` sont actifs dès le départ (météo, quêtes, talents, collection, banane pourrie…). Trop pour un nouveau joueur. La découverte progressive = plus de fun + early game lisible.

**Solution — gating par progression :**

| Système | Se débloque quand… | Indice |
|---|---|---|
| Banane (clic) + Production | dès le départ | tuto d'Amine |
| Améliorations | 1er producteur acheté | — |
| Succès | dès le départ (discret) | — |
| Quêtes du jour | **palier 2 atteint** | toast « Quêtes débloquées ! » |
| Banane dorée | palier 2 | — |
| Banane pourrie | palier 3 | — |
| Météo | palier 4 | — |
| Frénésie (combo clic) | dès le départ (cœur du jeu) | — |
| Prestige (Composter) | 1re victoire / seuil élevé | — |
| Arbre de talents ADN | **après le 1er prestige** | — |
| Collection (bananes spéciales) | 1re banane spéciale obtenue | — |

**Notes.**
- L'onglet/dock d'un système **caché** est masqué ou grisé tant que non débloqué ; il **apparaît** avec un petit toast au déblocage.
- Garder les `features` comme **interrupteurs maîtres** (on/off global), et ajouter une couche « unlocked » pilotée par la progression (`S.palier`, `S.prestiges`, etc.).
- Ne pas réinitialiser les déblocages au prestige (sauf logique voulue).

---

## 🟠 P4 — Tutoriel d'intro par Amine

**Problème.** 5 onglets + plusieurs systèmes = confusion au 1er lancement.

**Solution.** **Mini-tuto en 2-3 bulles d'Amine** au tout premier lancement (cf. dialogues d'intro dans `nano-banana-histoire-boss.md`) :
1. Bulle 1 : Amine se présente + « clique sur la banane » (flèche/halo sur la banane).
2. Bulle 2 : « tes bananes servent à acheter ça » (pointe l'onglet Production).
3. C'est tout. Le reste se découvre via le gating P3.

**Notes.**
- Déclenché si `S.totalClicks===0` et pas de save (1re partie).
- Overlay léger non bloquant + surbrillance de l'élément ciblé (halo). Bouton « OK / Suivant ».
- Stocker `S.tutoDone=true` pour ne pas le rejouer.
- Réutiliser le portrait d'Amine (`assets/amine2.png`) avec fallback emoji.

---

## 🟡 P5 — Célébrer le changement d'ère

**Problème.** La banane évolue (skin + thème) en douceur, mais le moment fort passe presque inaperçu.

**Solution.** À chaque passage d'ère :
- **Flash** lumineux bref sur la banane + petit **screen-shake** léger.
- **Toast** « 🍌 La banane évolue ! » (ou un texte par ère : « Banane TECHNO ! », « NANO BANANE ! »).
- **Petit son** (si son activé).

**Notes.**
- Brancher sur la fonction qui change `curEra` / `refreshEra()`.
- Respecter `prefers-reduced-motion` (pas de shake si réduit).

---

## 🟢 P6 — Détails / polish

- **Contraste ères 4-5** : sur fonds sombres + néon, vérifier la lisibilité des petits textes, surtout `--muted` (gris). Au besoin l'éclaircir sur les ères sombres.
- **Banane dorée (z-index & zone tactile)** : elle apparaît en `position:fixed` aléatoire — garantir qu'elle est **toujours au-dessus** des panneaux (z-index) et **jamais sous** la bottom-sheet/dock ni hors zone touchable. La cantonner à la zone visible safe.
- **Particules au clic** : OK (pooling + `prefers-reduced-motion`), mais **surveiller le clic rapide sur vieux mobiles** — prévoir un plafond de particules vives plus bas en mode mobile / si FPS chute.
- **Cohérence** : après la refonte des panneaux, vérifier que les overlays (victoire, retour hors-ligne, import/export) restent bien centrés et au-dessus de tout.

---

## Ordre d'implémentation conseillé

1. **P1** (panneaux : drag out, bottom-sheet mobile, colonnes desktop) — la grosse pièce.
2. **P2** (renommage « Ferme ») — 2 min, à faire en passant.
3. **P3** (gating progressif) — structurant pour l'onboarding.
4. **P4** (tuto Amine) — dépend des dialogues d'intro déjà spécifiés.
5. **P5** (feedback d'ère) puis **P6** (polish).

> Tout doit rester **mobile-first**, en un seul `index.html` vanilla, sans dépendance. Les réglages (seuils de gating, etc.) vont dans la **config**.
