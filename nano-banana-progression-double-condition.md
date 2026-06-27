# 🍌 Nano Banana — Progression à double condition (actes + ères synchronisés)

> But : que **l'histoire (8 actes) et le visuel (5 bananes) avancent ensemble**, et que les **textes d'Amine collent à ce que le joueur possède vraiment**.
> Aujourd'hui les deux sont découplés : les actes dépendent du **total de bananes gagnées**, les ères des **producteurs possédés**. → on peut entendre « les singes m'obéissent » sans avoir un seul singe.

---

## 1. Principe : double condition pour franchir un acte

Un acte **N** se débloque seulement si **LES DEUX** conditions sont remplies :

1. **Seuil de bananes** : `S.totalEarned >= paliers[N].at` (déjà en place)
2. **Producteur requis possédé** : `count[paliers[N].req] >= paliers[N].reqN` (nouveau)

→ Amine n'annonce un chapitre que quand le joueur a **gagné assez** ET **acheté le producteur qui correspond au texte**. Les répliques suivent toujours la réalité du jeu.

**Avancement séquentiel :** on ne débloque que l'acte **suivant** (`S.palier + 1`) quand ses deux conditions sont vraies — jamais de saut de plusieurs actes d'un coup. Re-vérifié à chaque tick.

---

## 2. Mapping acte → producteur requis (pour que le texte colle)

| Acte | Chapitre | Producteur requis (`req`) | `reqN` (départ) |
|---|---|---|---|
| 1 | Le petit service | `hand` (Main cueilleuse) | 1 |
| 2 | Le petit business | `monkey` (Singe employé) | 1 |
| 3 | L'empire banane | `farm` (Bananeraie) | 1 |
| 4 | L'obsession | `green` (Serre hydroponique) | 1 |
| 5 | La grande usine | `plant` (Usine à bananes) | 1 |
| 6 | Le laboratoire secret | `lab` (Labo génétique) | 1 |
| 7 | La conscience artificielle | `ai` (IA bananière) | 1 |
| 8 | Le vrai plan | `nano` (Nano-réplicateur) | 1 |

> `reqN = 1` suffit à rendre le texte vrai. On peut monter (ex. 3, 5) pour rendre chaque chapitre plus « mérité » — c'est réglable dans la config.

---

## 3. L'ère visuelle suit l'acte (synchro garantie)

Pour que la **banane change visiblement au fil de l'histoire**, on **dérive l'ère du palier** (au lieu des producteurs). 8 actes → 5 skins :

| Acte | Ère visuelle (banane) |
|---|---|
| 1–2 | 1 — Juteux 🍌 |
| 3–4 | 2 — Bio dopée 🧬 |
| 5–6 | 3 — Techno ⚙️ |
| 7 | 4 — Cyber 🌃 |
| 8 | 5 — Nano 🤖 |

- Le **prestige** peut forcer l'ère 5 (déjà le cas) ; garder l'override **debug** (`dbgEra`).
- Comme l'acte est lui-même verrouillé par le producteur requis (§1), la banane n'évolue que quand le joueur a réellement le producteur correspondant → tout reste cohérent.

---

## 4. Changements de code (indicatifs)

**Config — ajouter `req`/`reqN` à chaque palier :**
```js
paliers:[
  {n:1, name:"Le petit service",          at:0,      req:"hand",   reqN:1, ingr:"Pulpe vitale"},
  {n:2, name:"Le petit business",         at:350,    req:"monkey", reqN:1, ingr:"Pièce jaune"},
  {n:3, name:"L'empire banane",           at:9000,   req:"farm",   reqN:1, ingr:"Graine prime"},
  {n:4, name:"L'obsession",               at:200000, req:"green",  reqN:1, ingr:"Contrat simiesque"},
  {n:5, name:"La grande usine",           at:5e6,    req:"plant",  reqN:1, ingr:"Sceau d'État"},
  {n:6, name:"Le laboratoire secret",     at:1.2e8,  req:"lab",    reqN:1, ingr:"Brevet nano"},
  {n:7, name:"La conscience artificielle",at:3e9,    req:"ai",     reqN:1, ingr:"Cellule potassium"},
  {n:8, name:"Le vrai plan",              at:8e10,   req:"nano",   reqN:1, ingr:"Coeur quantique"},
],
```

**`checkPaliers()` — double condition + séquentiel :**
```js
function checkPaliers(){
  const next = CONFIG.paliers.find(p => p.n === S.palier + 1);
  if(!next) return;
  const okBananes = S.totalEarned >= next.at;
  const okProd    = (S.producers[next.req] || 0) >= (next.reqN || 1);
  if(okBananes && okProd){
    S.palier = next.n; S.ingredients[next.n] = true;
    celebrate(next); toast(`${next.ic} Chapitre ${next.n} — ${next.name}`);
    /* … journal + dialogue Amine comme avant … */
    refreshEra();            // l'ère suit le palier
    checkPaliers();          // enchaîne si le palier d'après est aussi prêt
  }
}
```

**`visualEra()` — dérivée du palier :**
```js
function visualEra(){
  if(dbgEra) return dbgEra;
  if(S.prestiges > 0) return 5;
  return [1,1,1,2,2,3,3,4,5][S.palier] || 1;  // index par S.palier (1..8)
}
```

---

## 5. UX : guider le joueur vers la condition manquante

Si le **seuil de bananes est atteint mais le producteur manque**, la barre de progression vers l'acte suivant doit l'indiquer clairement, ex. :
> « Prochain chapitre : achète **1 Singe employé** 🐒 »

Et inversement si c'est les bananes qui manquent (« Encore X 🍌 »). Comme ça le joueur sait toujours quoi faire pour débloquer la suite.

---

## 6. Points d'attention

- **Migration save** : bumper `saveVersion`. Un vieux save peut avoir un `S.palier` élevé sans le producteur requis — au chargement, **ne pas rétrograder** le palier (garder le max atteint) pour ne pas casser une partie en cours.
- **Équilibrage** : avec la double condition, vérifier qu'aucun acte ne se retrouve **bloqué trop longtemps** (seuil de bananes franchi bien avant que le producteur soit abordable, ou l'inverse). Ajuster les `at` et/ou les coûts pour que les deux conditions tombent à peu près en même temps → progression fluide.
- **Cohérence textes** : une fois en place, relire les 8 messages d'Amine pour qu'ils référencent bien le producteur requis (cf. `nano-banana-histoire-boss.md`).
