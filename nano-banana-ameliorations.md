# 🍌 Nano Banana — Spec détaillée des améliorations

> Complément au doc de conception (`nano-banana-conception.md`).
> Détaille les mécaniques « profondeur / événements / narratif ». Chaque bloc : **concept → fonctionnement → chiffres → UI → implémentation**.
> Toutes les valeurs chiffrées sont des **points de départ à équilibrer**, à mettre dans la config JSON.

---

# A. Mécaniques de profondeur

## A1. Combos de clic (jauge de Frénésie)

**Concept.** Cliquer vite remplit une jauge ; pleine, elle déclenche une « Frénésie » qui booste le gain par clic quelques secondes. Récompense le clic actif sans jamais l'imposer (l'idle reste viable).

**Fonctionnement.**
- Chaque clic ajoute de la charge à la jauge. La jauge **décroît** quand on ne clique pas → il faut maintenir le rythme.
- Jauge pleine → état **Frénésie** pendant une durée fixe, puis cooldown (la jauge se vide et repart à zéro).
- Pendant la Frénésie : **BPC ×N** (et option : le clic compte aussi un petit % du BPS, pour rester utile en fin de partie où le clic devient négligeable).

**Chiffres de départ.**
- Gain de jauge par clic : +4 % (≈ 25 clics rapides pour remplir).
- Décroissance : −3 %/s si pas de clic depuis 0,5 s.
- Frénésie : **BPC ×7**, durée **8 s**, cooldown **20 s**.
- Variante fin de partie : pendant Frénésie, chaque clic = BPC + 1 % du BPS.

**UI.** Barre fine sous la banane (ou anneau autour). Couleur qui monte vers le « chaud » à mesure qu'elle se remplit ; flash + changement de teinte de la banane pendant la Frénésie ; petit texte « FRÉNÉSIE ! ».

**Implémentation.** Un float `frenzyGauge` 0–1, mis à jour dans le tick. État `frenzyActive` + timer. Multiplicateur appliqué dans le calcul du BPC. ~30 lignes.

---

## A2. Synergies entre producteurs

**Concept.** Certains producteurs **boostent** d'autres producteurs. But : casser la règle « j'achète toujours le plus cher » et créer de vraies décisions.

**Fonctionnement.** Chaque synergie est une règle `source → cible` : « chaque unité de SOURCE augmente la production de CIBLE de X % ». Cumulatif.

**Exemples thématiques.**
- Chaque **Singe** : +1 % à la prod des **Plantations** (les singes récoltent).
- Chaque **Usine** : +0,5 % à la prod des **Drones** (chaîne logistique).
- Chaque **Labo génétique** : +2 % au **BPC** (bananes plus nourrissantes).
- Bonus « miroir » débloquable en upgrade : les Plantations boostent aussi les Singes en retour.

**Chiffres de départ.** Garder les % petits (0,5–2 %) car ils se multiplient vite avec des centaines d'unités. Tester pour éviter l'explosion exponentielle.

**UI.** Sur la carte d'un producteur, une ligne « Synergies : +X % (de la part des Singes) ». Au survol/clic, détailler. Mettre en valeur quand une synergie devient significative.

**Implémentation.** Table de synergies en config :
```
synergies = [
  { source: "monkey", target: "plantation", perUnit: 0.01 },
  { source: "factory", target: "drone", perUnit: 0.005 },
]
```
Dans le calcul de prod d'un producteur : `mult = 1 + Σ(count[source] * perUnit)`. Recalculé à chaque achat, pas à chaque tick (perf).

---

## A3. Arbre de talents ADN (prestige non-linéaire)

**Concept.** Au lieu d'une boutique de prestige en liste, un **arbre** où l'ADN 🧬 débloque des nœuds avec des prérequis. Donne des **builds** différents → forte rejouabilité.

**Structure.** 3 branches thématiques :
- **🌿 Bio** (focus idle/production passive) : +prod %, démarrage avec producteurs, réduction des coûts.
- **👊 Force** (focus clic actif) : +BPC %, Frénésie plus longue/forte, clic qui scale sur le BPS.
- **⚡ Tech** (focus méta/confort) : +durée du cap hors-ligne, bananes dorées plus fréquentes, gains de prestige ×.

**Fonctionnement.**
- Chaque nœud coûte de l'ADN et **requiert** le(s) nœud(s) précédent(s).
- Nœuds « keystone » en bout de branche = gros bonus qui définit un style (ex. Force : « le clic vaut 5 % du BPS »).
- Respec possible contre une partie de l'ADN (ou gratuit, à décider).

**Chiffres de départ.** ~15–20 nœuds pour la v1. Premiers nœuds à 1–3 ADN, keystones à 20–50 ADN. Rester lisible.

**UI.** Écran dédié (onglet « ADN » / « Compostage »). Arbre simple : nœuds reliés par des traits, verrouillés/grisés tant que prérequis non remplis, surbrillance des nœuds achetables. Sur mobile : arbre scrollable vertical par branche.

**Implémentation.** Graphe de nœuds en config (`id`, `cost`, `requires[]`, `effect`). État = set des nœuds possédés. Les effets s'agrègent dans les multiplicateurs globaux. Commencer simple (liste de branches) et enrichir.

---

## A4. Bananes spéciales rares (bonus permanents)

**Concept.** De temps en temps, une **banane spéciale** unique apparaît (différente de la banane dorée temporaire). La cliquer = **bonus permanent** + une entrée de « collection ».

**Catalogue de départ.**
| Banane | Apparition | Effet permanent | Ton |
|---|---|---|---|
| 🔵 Banane bleue | rare | +5 % prod globale | « elle vient d'un autre régime » |
| ⬛ Banane carrée | très rare | +10 % BPC | « a refusé de courber l'échine » |
| ☢️ Banane radioactive | rare | double la fréquence des bananes dorées | « ne pas manger » |
| 🌗 Banane à moitié mûre | peu rare | annule le malus de maturité 10 min | — |
| 👻 Banane fantôme | très rare | +1 % prod par succès débloqué | apparaît puis disparaît vite |

**Fonctionnement.** Tirage indépendant de la banane dorée, beaucoup plus rare. Chaque type ne peut être obtenu **qu'une fois par run** (ou par partie selon l'effet). Affichées dans une **collection** (album) → objectif de complétion.

**Chiffres de départ.** Probabilité d'apparition très faible (ex. 0,5 % par fenêtre d'event), avec garantie « pity » après X temps pour ne pas frustrer. Durée à l'écran courte (incite à rester attentif).

**UI.** Sprite distinct + petite anim d'apparition (clignote, traverse l'écran). Album dans l'onglet Succès : cases révélées = banane colorée, non obtenues = silhouette.

**Implémentation.** Liste en config avec `rarity`, `effect`, `oncePer: run|game`. Réutilise le système d'événements/spawn de la banane dorée. Bonus stockés dans le save.

---

# B. Événements & fun

## B1. Événements aléatoires à choix

**Concept.** Pop-up occasionnelle proposant un **dilemme** à 2 options. Micro-décision rapide, souvent drôle, parfois risquée.

**Exemples.**
- « Un singe propose un deal : **prod ×2 pendant 1 min** OU **+50 bananes dorées de valeur** ? »
- « Un savant fou veut tester un sérum : **+15 % prod permanente** mais **−50 % de tes bananes actuelles**. »
- « Une caisse mystère : l'ouvrir (aléatoire : jackpot ou rien) OU la revendre (gain sûr modéré) ? »
- « Grève des singes : les payer (coût en bananes) OU négocier (50/50 : prod boostée ou ralentie 2 min) ? »

**Fonctionnement.** Déclenchement périodique aléatoire (pas trop souvent). 2 boutons. Effet appliqué immédiatement. Possibilité d'**ignorer** (timeout → rien, ou option neutre).

**Chiffres de départ.** 1 événement toutes les ~5–10 min de jeu actif. Effets bornés pour ne pas casser l'équilibrage (gains temporaires plutôt que permanents énormes).

**UI.** Carte modale non bloquante (le jeu continue derrière), 2 choix clairs, un timer visuel avant disparition. Ton humoristique dans le texte.

**Implémentation.** Banque d'événements en config :
```
{ id, text, optionA:{label, effect}, optionB:{label, effect}, weight }
```
Tirage pondéré. Effets = fonctions réutilisant les buffs existants (multiplicateurs temporaires, ajout/retrait de bananes).

---

## B2. Météo bananière

**Concept.** Un « climat » ambiant qui change de temps en temps et module légèrement le jeu. Donne de la variété de session et une ambiance.

**Types de météo.**
| Météo | Effet | Durée | Visuel |
|---|---|---|---|
| ☀️ Ensoleillé | prod +10 % | moyenne | fond plus chaud |
| 🍌 Pluie de bananes | +X bananes / sec bonus | courte | bananes qui tombent en fond |
| 🌵 Sécheresse | idle −20 % (clic non affecté) | courte | fond désaturé |
| 🌙 Nuit | bananes dorées +50 % fréquence | moyenne | fond sombre |
| ⛈️ Orage techno | Frénésie se remplit 2× plus vite | courte | éclairs |

**Fonctionnement.** Une météo active à la fois, change toutes les quelques minutes. La sécheresse pousse à cliquer (le clic n'est pas pénalisé) → varie le style de jeu.

**Chiffres de départ.** Cycle de 3–6 min. Effets modérés (±10–20 %) pour rester un assaisonnement, pas un facteur dominant.

**UI.** Petite icône météo + nom en haut. Effet de fond léger (particules/teinte) cohérent avec l'ère en cours.

**Implémentation.** Enum d'états météo en config, timer de cycle, multiplicateurs appliqués globalement. Effets visuels en CSS (overlay de particules optionnel).

---

## B3. Quêtes journalières

**Concept.** Petits objectifs qui se renouvellent chaque jour. Exploite le `lastSeen` déjà nécessaire pour l'idle hors-ligne. Donne une raison de revenir.

**Exemples.**
- « Fais **500 clics** aujourd'hui. »
- « Achète **10 producteurs**. »
- « Clique **3 bananes dorées**. »
- « Atteins **X bananes/sec**. »
- « Déclenche **2 Frénésies**. »

**Fonctionnement.** 3 quêtes/jour tirées aléatoirement. Complétées → récompense (bananes dorées, boost temporaire, un peu d'ADN). Reset à minuit (heure locale) détecté via comparaison de date avec `lastSeen`. Option **série/streak** : jours consécutifs → bonus croissant.

**Chiffres de départ.** Objectifs calibrés sur ~5–15 min de jeu chacun. Récompense = 1 banane dorée d'effet, ou +1 ADN, ou prod ×2 pendant 5 min.

**UI.** Petit panneau « Quêtes du jour » avec 3 lignes + barres de progression + pastille de récompense. Badge sur l'onglet quand une quête est terminée à réclamer.

**Implémentation.** Stocker `dailyDate`, la liste des 3 quêtes et leur progression dans le save. Au chargement : si la date a changé, régénérer. Compteurs branchés sur les événements de jeu (clic, achat, etc.). Attention : ne pas se faire avoir par un changement d'horloge système (tolérable pour un jeu entre potes).

---

## B4. Banane pourrie (golden cookie « maléfique »)

**Concept.** Comme la banane dorée, mais **maléfique** : il vaut mieux **NE PAS** la cliquer. Crée de la tension risque/récompense et un peu de panique amusante.

**Fonctionnement.**
- Apparaît parfois **à la place** d'une banane dorée, avec un visuel proche mais reconnaissable (marron, mouches, odeur visuelle) → le joueur doit apprendre à distinguer.
- Si cliquée → **malus** aléatoire : prod −50 % 30 s, perte d'un % des bananes, invasion de mouches qui masquent l'UI quelques secondes, etc.
- Si **ignorée** (disparaît seule) → parfois petit **bonus** de « bon réflexe », ou rien.
- Variante maligne : un upgrade « lunettes anti-pourriture » qui la marque clairement → transforme le risque en confort à débloquer.

**Chiffres de départ.** ~15–20 % des spawns de bananes spéciales sont des pourries. Malus borné (jamais punitif au point de dégoûter). Fenêtre de clic identique à la dorée.

**UI.** Sprite distinct (marron tacheté, petites mouches animées). Si cliquée : effet d'écran (filtre verdâtre, mouches) + texte « Beurk ! ». Feedback clair pour apprendre.

**Implémentation.** Même système de spawn que la banane dorée, avec un flag `evil`. Effets négatifs = buffs à coefficient < 1. Réutilise tout l'existant.

---

# C. Narratif & ton

## C1. Lore débloqué par palier (journal du savant fou)

**Concept.** À chaque palier de l'échelle bananière, débloquer une **entrée de journal** absurde, façon carnet d'un savant obsédé par la banane. Récompense narrative gratuite qui donne une personnalité au jeu.

**Fonctionnement.** Franchir un palier → notification « Nouvelle entrée de journal » + l'entrée s'ajoute à un onglet « Journal » consultable. Ton : pseudo-scientifique, mégalo, de plus en plus inquiétant à mesure qu'on approche de la singularité.

**Exemples (ton).**
- Palier 1 (Manger) : « Jour 1. J'ai mangé une banane. Je me sens… puissant. L'expérience continue. »
- Palier 4 (Singes) : « Jour 88. Les singes m'obéissent. Ils ne savent pas encore pourquoi. Moi non plus. »
- Palier 6 (Science) : « Jour 203. J'ai séquencé le génome de la banane. Elle me regarde différemment maintenant. »
- Palier 8 (Cosmos) : « Jour ???. La banane n'est plus un fruit. La banane est un plan. »
- Fin (Nano Banana) : « Il n'y a plus de jours. Il n'y a que la Banane. »

**UI.** Onglet « Journal » avec liste d'entrées débloquées (les futures = « ??? »). Petite anim machine à écrire à l'ouverture d'une nouvelle entrée.

**Implémentation.** Tableau d'entrées en config indexé par palier. Set des entrées débloquées dans le save. Zéro logique complexe.

---

## C2. Noms de prestige évolutifs (titres)

**Concept.** Le rang du joueur affiche un **titre** qui évolue avec le nombre de prestiges (ou l'ADN total). Petite fierté + humour.

**Échelle de titres (départ).**
| Prestiges | Titre |
|---|---|
| 0 | Mangeur de banane |
| 1 | Apprenti Bananier |
| 3 | Maître Cavendish |
| 5 | Baron de la Banane |
| 10 | Banane Suprême |
| 20 | Seigneur du Régime |
| 50 | Dieu Cavendish |
| 100 | L'Entité Bananière |

**Fonctionnement.** Titre affiché près du compteur / dans l'écran de prestige. Se met à jour automatiquement au passage de seuil, avec une petite notif « Nouveau titre débloqué ! ».

**UI.** Texte stylé sous le pseudo/compteur. Option : couleur/effet du titre qui change selon le palier (doré, néon, etc.).

**Implémentation.** Table `[{ threshold, title }]`, on prend le plus haut seuil atteint. Trivial.

---

## C3. Succès cachés rigolos

**Concept.** En plus des succès « normaux » (jalons de progression), des **succès cachés** à conditions absurdes, non annoncés. Récompense la curiosité et fait rire à la découverte.

**Exemples.**
| Succès caché | Condition | Clin d'œil |
|---|---|---|
| « Erreur 404 : banane introuvable » | cliquer exactement 404 fois | — |
| « Composteur professionnel » | laisser pourrir 1000 bananes | lié à la maturité |
| « Patient zéro » | rester 10 min sans cliquer (full idle) | — |
| « TOC bananier » | acheter un producteur jusqu'à un compte rond (ex. 100) | — |
| « Numéro de chance » | cliquer 7, 77, 777 bananes dorées | clin d'œil Cookie Clicker |
| « Insomniaque » | jouer entre 3 h et 4 h du matin (heure locale) | — |
| « Le retour » | revenir après +48 h d'absence | — |
| « Trader de bananes » | utiliser l'export/import du save | — |

**Fonctionnement.** Conditions vérifiées silencieusement. Déblocage → notif surprise « Succès caché ! » + petit bonus symbolique. Restent « ??? » dans la liste tant que non obtenus.

**Chiffres de départ.** Bonus faibles (cosmétique ou +0,5 % prod) — c'est la **découverte** la récompense, pas la puissance.

**UI.** Dans l'onglet Succès, une section « Cachés » avec des cases mystère. Révélées une fois obtenues, avec le texte rigolo.

**Implémentation.** Chaque succès caché = une condition (fonction) évaluée aux moments pertinents (clic, tick, achat, chargement). Set des succès obtenus dans le save. Garder les compteurs nécessaires (clics totaux, bananes pourries, etc.).

---

# Priorisation suggérée

Pour ne pas surcharger la v1 (qui doit déjà avoir une **fin** propre) :

- **À intégrer en v1 si le temps le permet** (gros effet, peu de code) : A1 Frénésie, C1 Lore, C2 Titres, C3 Succès cachés, B4 Banane pourrie. Ils renforcent le fun et le ton sans toucher à l'équilibrage de fond.
- **v1.1 / v2** (plus de design/équilibrage) : A2 Synergies, A3 Arbre de talents, A4 Bananes spéciales, B1 Événements à choix, B2 Météo, B3 Quêtes journalières.

> Tout reste piloté par la **config JSON** : on peut activer/désactiver chaque mécanique par un flag pour tester progressivement.
