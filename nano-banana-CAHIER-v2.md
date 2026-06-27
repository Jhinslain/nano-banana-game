# 🍌 Nano Banana — Cahier des charges v2 (handoff Claude Code)

> Document maître pour la v2. Il **réunit les décisions** et **renvoie aux specs détaillées**.
> Cible : un bon jeu clicker/idle tycoon, **mobile-first**, **1 fichier `index.html` vanilla**, sauvegarde locale, avec **histoire + boss + fin**.
> ⚠️ **Sauvegarde une copie `index_backup.html` avant de commencer.** Ne pas casser l'existant (save, succès, prestige, hors-ligne, export/import).

---

## 0. Les specs (à lire dans cet ordre)

1. `nano-banana-equilibrage-gameplay.md` — équilibrage tycoon/idle, Frénésie, scaling par producteur.
2. `nano-banana-progression-double-condition.md` — actes + ères synchronisés (double condition).
3. `nano-banana-ameliorations-ui-ux.md` — refonte panneaux (bottom-sheet mobile), gating, tuto, polish.
4. `nano-banana-histoire-boss.md` — histoire d'Amine, dialogues, combat de boss Fruit Ninja.
5. `nano-banana-ameliorations.md` — backlog de mécaniques (v2+/optionnel).

**Ordre d'implémentation conseillé (un chantier à la fois, on teste entre chaque) :**
1) Équilibrage + scaling, 2) Double condition (actes/ères), 3) Refonte panneaux UI/UX, 4) Histoire + boss.

---

## 1. Décisions fermes

- **Boss ↔ idle : PAUSE.** Pendant le combat Fruit Ninja, la **production passive se met en pause** ET le **rendu du clicker est suspendu** (libère le CPU pour le canvas du boss sur mobile). Reprise normale à la fin du combat.
- **Durée cible : 2–3 h** pour la 1re run jusqu'à la NANO BANANA.
- **Re-tuning obligatoire après le scaling.** Les paliers de doublement par producteur changent énormément la courbe de revenus → **re-régler les coûts/seuils** pour tenir les 2–3 h (ni fini en 20 min, ni mur). Utiliser le **panneau debug** pour tester la fin de partie.
- **Frénésie** : verrouillée au départ, débloquée par amélioration, puissance réduite, ne booste que le clic (cf. spec équilibrage).
- **Scaling producteur** : prod unitaire qui **double par paliers de quantité** → améliorer les producteurs bas de gamme reste utile (cf. spec équilibrage).
- **Panneaux** : plus de drag. Mobile = **bottom-sheet** qui ne masque jamais la banane ; desktop = colonnes latérales fixes (cf. spec UI/UX).
- **Renommer « Ferme »** → « Production » / « Prod ».
- **Progression** : chaque acte = **double condition** (bananes gagnées + producteur requis possédé), et **l'ère visuelle suit l'acte** (cf. spec double condition).
- **Images Amine compressées** : déjà fait. Portrait de base = `amine2.png` (ignorer `amine.png`). Toujours prévoir un **fallback** (amine2 → emoji) si une image manque.

---

## 2. MAPPING MAÎTRE — actes / producteurs / ères / banane / Amine

Un acte se débloque quand **bananes ≥ seuil** ET **producteur requis possédé**. L'ère (skin de la banane) est dérivée de l'acte. Le portrait d'Amine affiché avec le message suit son humeur.

| Acte | Chapitre | Producteur requis | Seuil bananes | Ère | Skin banane | Humeur / portrait Amine |
|---|---|---|---|---|---|---|
| — | Intro (lancement) | — | — | 1 | banana-1 | `amine-friendly` |
| 1 | Le petit service | `hand` 🖐️ | 0 | 1 | banana-1 | `amine-joyeux` |
| 2 | Le petit business | `monkey` 🐒 | 350 | 1 | banana-1 | `amine-joyeux` |
| 3 | L'empire banane | `farm` 🌱 | 9 000 | 2 | banana-2 | `amine-joyeux` |
| 4 | L'obsession | `green` 💧 | 200 000 | 2 | banana-2 | `amine-greedy` |
| 5 | La grande usine | `plant` 🏭 | 5 M | 3 | banana-3 | `amine-greedy` |
| 6 | Le laboratoire secret | `lab` 🧪 | 120 M | 3 | banana-3 | `amine-mechant` |
| 7 | La conscience artificielle | `ai` 🤖 | 3 Md | 4 | banana-4 | `amine-mechant` |
| 8 | Le vrai plan | `nano` ⚛️ | 80 Md | 5 | banana-5 | `amine-mechant` |
| ★ | Trahison + assemblage NANO BANANA (coût 1e12) | — | — | 5 | banana-5 | `amine-mechant` → boss |

> `reqN` (quantité requise du producteur) = 1 par défaut, réglable. Les seuils `at` sont à **re-tuner** après le scaling.

---

## 3. MAPPING MAÎTRE — combat de boss (phases)

Le boss affiché = `amine-nano-banana.png`. Détails et équilibrage chiffré dans `nano-banana-histoire-boss.md`.

| Phase | PV Amine | Portrait d'humeur | Objets/s | % bombes | Vitesse |
|---|---|---|---|---|---|
| 1 | 100 → 66 % | `amine-mechant` | 2,0 | 15 % | lente |
| 2 | 66 → 33 % | `amine-furieux` | 3,0 | 20 % | moyenne |
| 3 | 33 → 0 % | `amine-peureux` | 4,0 | 25 % | rapide |
| Joueur tranche une bombe / perd une vie | — | `amine-moqueur` | — | — | — |
| Défaite du joueur (game over) | — | `amine-moqueur` | — | — | — |
| Amine vaincu (juste battu, penaud) | 0 % | `amine-defeated` | — | — | — |
| Réconciliation finale (« on partage ») | — | `amine-friendly` | — | — | — |

**Rappels combat** (détail dans la spec) : 3 vies, trancher banane = charge → tir auto sur Amine, trancher bombe = −1 vie. Défaite → re-payer ~25 % de la transformation, on garde les améliorations. Trahison = vol de toutes les bananes (compteur → 0), récupérées à la victoire. **La prod idle est en pause pendant tout le combat.**

---

## 4. Fichiers d'images (assets/)

- Bananes : `banana-1.png` … `banana-5.png` (skins par ère).
- Amine portraits : `amine-friendly`, `amine-joyeux`, `amine-greedy`, `amine-mechant`, `amine-furieux`, `amine-peureux`, `amine-moqueur`, `amine-defeated` (+ base/fallback `amine2.png`).
- Boss : `amine-nano-banana.png`.
- **Fallback obligatoire** si une image manque (→ `amine2.png`, puis emoji) pour rester jouable.
- Précharger les portraits juste avant leur usage (pas tout au démarrage).

---

## 5. Garde-fous techniques

- **Migration save** : bumper `saveVersion` (1 → 2). Compléter les vieux saves (nouveaux champs : frénésie verrouillée, gating, paliers de doublement, `req` producteurs) sans planter. Ne jamais **rétrograder** un `S.palier` déjà atteint.
- **Mobile** : tout reste fluide en clic rapide ; bottom-sheet ne masque pas la banane ; `touch-action:none` sur le canvas du boss ; respecter `prefers-reduced-motion`.
- **Perf** : pause du rendu clicker pendant le boss ; plafond de particules plus bas sur mobile.
- **Tout en config** : seuils de gating, double condition, équilibrage boss, scaling — réglables sans toucher au moteur.
- **Tester sur vrai téléphone** : clic rapide, swipe du boss, bottom-sheet, transitions d'ère.

---

## 6. Checklist de livraison

- [ ] Early game fluide au clic, bascule idle rapide (Frénésie pas dispo d'entrée).
- [ ] Améliorer un producteur bas de gamme reste rentable en fin de partie (doublements).
- [ ] On **voit** les fermes produire (anim + ticker + compteur qui égrène).
- [ ] Chaque acte se débloque en **double condition**, et la banane change d'ère **avec** l'histoire.
- [ ] Les textes d'Amine collent au producteur possédé + bon portrait d'humeur.
- [ ] Boss jouable, équilibré, idle en pause, gagnable avec un peu de préparation.
- [ ] Fin (NANO BANANA + boss vaincu) atteinte vers **2–3 h**.
- [ ] Save migrée sans casse ; fluide sur mobile.
