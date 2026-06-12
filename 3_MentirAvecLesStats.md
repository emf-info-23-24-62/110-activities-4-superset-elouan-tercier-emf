# Faire mentir les statistiques

> *"Il y a trois sortes de mensonges : les mensonges, les sacrés mensonges, et les statistiques."*  
> — attribué à Benjamin Disraeli

Les données que vous allez utiliser sont les mêmes qu'à l'activité précédente. Pourtant, selon la façon de les présenter, vous allez pouvoir "prouver" des affirmations contradictoires — toutes basées sur des faits réels, mais toutes trompeuses à leur manière.

L'objectif n'est pas d'apprendre à manipuler, mais de **savoir reconnaître quand on est manipulé**.

## Les techniques classiques

| Technique | Description | Effet |
|-----------|-------------|-------|
| **Axe tronqué** | Faire commencer l'axe Y à une valeur différente de zéro | Amplifie visuellement les variations |
| **Choix de la période** | Sélectionner un intervalle de temps favorable | Montre une hausse ou une baisse selon le point de départ |
| **Absolu vs relatif** | Choisir l'unité qui favorise le message | Change complètement le classement |
| **Corrélation vs causalité** | Présenter deux tendances similaires comme liées | Suggère une relation qui n'existe pas |
| **Agrégation cachée** | Moyenner des groupes de tailles très différentes | Masque les disparités |

---

## Exercice 1 — L'axe tronqué

### La question posée

> "Les émissions du secteur IT sont-elles en train d'exploser ?"

### Graphique A — Version honnête

Créez un Line Chart dans Superset avec :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Group by | `Secteur` |
| Y-Axis Bounds | Min : `0`, Max : *(automatique)* |

👉 Avec tous les secteurs affichés et l'axe Y partant de 0, la courbe IT est **quasiment plate** par rapport à l'Industrie.

Sauvegardez : `[Honnête] Emissions tous secteurs`

### Graphique B — Version trompeuse

Créez un nouveau Line Chart avec :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Filters | `Secteur = IT` |
| Y-Axis Bounds | Min : `2500`, Max : `5500` |

👉 En affichant uniquement IT avec un axe qui commence à 2500, la courbe semble **monter en flèche** — une "explosion" qui justifierait des mesures d'urgence.

Sauvegardez : `[Trompeur] Explosion emissions IT`

### Analyse

**Réponse — En chiffres absolus, de combien les émissions IT ont-elles augmenté entre 2015 et 2024 ?**

    +2 300 tonnes (de 2 800 t en 2015 à 5 100 t en 2024).
    Pour mise en perspective : c'est moins que la *baisse* de l'Industrie sur la même
    période (-2 800 t). Et l'IT à 5 100 t en 2024 ne représente que **7 %** du total
    des émissions des 4 secteurs (73 200 t) — l'Industrie seule en représente 62 %.

**Réponse — En pourcentage, est-ce vraiment "explosif" comparé à l'ensemble des secteurs ?**

    En % de croissance, oui : +82 % sur 10 ans, c'est la plus forte hausse des 4 secteurs.
    MAIS le "pourcentage" est trompeur quand le point de départ est bas. Passer de 2 800
    à 5 100 t n'a pas du tout le même impact que passer de 48 000 à 87 000 t (un même +82 %
    appliqué à l'Industrie). L'IT pourrait quadrupler sans atteindre le niveau actuel de
    l'Industrie. Donc "explosif" en relatif → vrai. Significatif sur le total des émissions
    → non, marginal.

**Réponse — Quels indices visuels permettent de détecter qu'un graphique utilise un axe tronqué ?**

    - L'axe Y ne commence **pas à zéro** (regarder la valeur la plus basse de l'axe)
    - Des **variations qui semblent énormes** alors que les chiffres réels sont proches
    - Présence du **symbole de coupure** (// ou zigzag) sur l'axe — c'est honnête mais souvent absent
    - Une seule **courbe affichée hors contexte** (pas de comparaison avec d'autres séries)
    - Échelle Y exprimée dans une unité qui amplifie (ex : t avec plage [2 500 ; 5 500] au lieu
      de Mt avec plage [0 ; 50])
    - Absence de la valeur 0 sur l'axe Y, ou affichage des graduations cachées

---

## Exercice 2 — Le choix de la période

### La question posée

> "L'Industrie réduit-elle ses émissions ?"

Les mêmes données permettent de répondre **oui** et **non** — selon la fenêtre temporelle choisie.

### Graphique A — "L'Industrie est en bonne voie"

Créez un Line Chart :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Filters | `Secteur = Industrie` ET `Annee >= 2019` |
| Y-Axis Bounds | Min : `0` |

👉 De 2019 (pic à 52 000 t) à 2024 (45 200 t) : **baisse de 13%**. Message : l'Industrie fait des efforts.

Sauvegardez : `[Trompeur] Industrie en bonne voie`

### Graphique B — "L'Industrie repart à la hausse"

Créez un Line Chart :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Filters | `Secteur = Industrie` ET `Annee >= 2020` |
| Y-Axis Bounds | Min : `0` |

👉 De 2020 (creux COVID à 38 000 t) à 2024 (45 200 t) : **hausse de 19%**. Message : l'Industrie empire.

Sauvegardez : `[Trompeur] Industrie en hausse`

### Graphique C — La vue complète

Créez un Line Chart sans filtre sur l'année :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Filters | `Secteur = Industrie` |


Sauvegardez : `[Honnête] Industrie vue complète`

### Analyse

**Réponse — Pourquoi l'année de référence choisie change-t-elle complètement le message du graphique ?**

    Parce qu'on peut choisir un point de référence **maximum** ou **minimum** local pour
    inverser le sens de la tendance.

    Chiffres concrets sur l'Industrie :
    - 2019 → 2024 :  52 000 → 45 200  =  **-13 %**  → "L'Industrie réduit ses émissions"
    - 2020 → 2024 :  38 000 → 45 200  =  **+19 %**  → "L'Industrie repart à la hausse"

    2019 = pic historique. 2020 = creux COVID artificiel (arrêt d'activité). Choisir
    l'un ou l'autre comme point de départ permet de "prouver" deux thèses opposées avec
    les mêmes données. C'est du **cherry-picking** de la période de référence.

**Réponse — Quelle question devez-vous toujours poser quand on vous présente une évolution en % ?**

    « **Par rapport à QUELLE année (ou QUEL point de référence) ?** Et pourquoi celui-là
    et pas un autre ? »

    Sous-questions utiles :
    - Le point de départ est-il un pic ou un creux exceptionnel (COVID, crise, etc.) ?
    - Quelle est la tendance sur 10/20/30 ans (vue longue) ?
    - Si on prend l'année juste avant ou juste après, le sens de la variation change-t-il ?
    - Quelle est la valeur absolue derrière le pourcentage (+5 % sur 100 € ≠ +5 % sur 1 M €) ?

---

## Exercice 3 — Absolu vs relatif

### La question posée

> "Quel secteur pollue le plus ?"

La réponse change entièrement selon l'unité de mesure choisie.

### Graphique A — Classement par émissions absolues

Créez un Bar Chart (horizontal) :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Secteur` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Filters | `Annee = 2024` |
| Sort by | Metric descending |
| Chart Orientation | Horizontal |

👉 Classement 2024 : Industrie (45 200) >> Services (12 500) > Commerce (10 400) > IT (5 100)

Sauvegardez : `[Absolu] Emissions par secteur 2024`

### Graphique B — Classement par émissions par employé

Pour ce graphique, vous devez créer une métrique calculée dans le dataset.

Dans **Data → Datasets**, éditez `emissions_co2_secteurs`.

Onglet **Metrics → + Add item** :

```
Metric name : emissions_par_employe
SQL expression : SUM(Emissions_CO2_tonnes) * 1.0 / SUM(Nb_employes)
```

Créez un Bar Chart :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Secteur` |
| Metric | `emissions_par_employe` |
| Filters | `Annee = 2024` |
| Chart Orientation | Horizontal |

👉 Classement par employé 2024 :
- Industrie : 45 200 / 28 500 = **1,59 t/employé**
- Commerce : 10 400 / 21 500 = **0,48 t/employé**
- IT : 5 100 / 8 500 = **0,60 t/employé**
- Services : 12 500 / 48 000 = **0,26 t/employé**

👉 Services devient le secteur le "plus propre", IT n'est plus le meilleur, même s'il a les plus faibles émissions absolues.

Sauvegardez : `[Relatif/employe] Emissions par secteur 2024`

### Graphique C — Classement par émissions par million de CA

Créez une seconde métrique calculée :

```
Metric name : emissions_par_MCHF
SQL expression : SUM(Emissions_CO2_tonnes) * 1.0 / SUM(CA_MCHF)
```

Créez un Bar Chart avec cette métrique.

👉 Classement par MCHF de CA 2024 :
- Industrie : 45 200 / 4 850 = **9,32 t/MCHF**
- Commerce : 10 400 / 2 680 = **3,88 t/MCHF**
- Services : 12 500 / 4 550 = **2,75 t/MCHF**
- IT : 5 100 / 2 100 = **2,43 t/MCHF**

👉 IT devient le secteur le plus efficient économiquement.

Sauvegardez : `[Relatif/CA] Emissions par secteur 2024`

### Analyse

**Réponse — Reconstituez le classement des 4 secteurs selon les 3 métriques :**

| Secteur   | Rang absolu | Rang par employé | Rang par MCHF |
|-----------|:-----------:|:----------------:|:-------------:|
| IT        |      4      |        2         |       4       |
| Industrie |      1      |        1         |       1       |
| Commerce  |      3      |        3         |       2       |
| Services  |      2      |        4         |       3       |

(1 = secteur le plus polluant sur cette métrique, 4 = le moins polluant.)

Valeurs sources 2024 :
- Absolu (t)        : Industrie 45 200 ; Services 12 500 ; Commerce 10 400 ; IT 5 100
- Par employé (t)   : Industrie 1,59 ; IT 0,60 ; Commerce 0,48 ; Services 0,26
- Par MCHF (t/MCHF) : Industrie 9,32 ; Commerce 3,88 ; Services 2,75 ; IT 2,43

**Réponse — Un lobbyiste du secteur IT veut montrer que son secteur est le plus vertueux. Quel graphique choisit-il ? Pourquoi ?**

    Il choisit le **graphique par MCHF de CA** (et secondairement par employé sur l'absolu).
    - Par MCHF : IT = 2,43 t/MCHF → **le plus bas des 4 secteurs**. Message massue :
      "L'IT pollue le moins par franc de richesse créée. C'est le secteur le plus
      écoresponsable de l'économie suisse."
    - En absolu : IT = 5 100 t → également le plus bas. Message : "Notre secteur est de
      loin le plus petit pollueur du pays."

    Il évitera soigneusement le graphique **par employé**, où IT est 2ème (0,60 t/employé,
    nettement au-dessus de Services à 0,26 et de Commerce à 0,48).

**Réponse — Un journaliste veut montrer que l'Industrie pollue "50 fois plus que le secteur IT". Est-ce factuellement correct ? Est-ce honnête ?**

    **NON, factuellement faux.** Le vrai rapport en absolu 2024 est :
    45 200 / 5 100 = **8,86 fois**, soit environ × 9 — pas × 50.

    Même si le journaliste corrigeait à "×9", ce serait toujours **trompeur** :
    - Industrie a 3,4 fois plus d'employés que IT (28 500 vs 8 500)
    - et 2,3 fois plus de CA (4 850 vs 2 100 MCHF)
    - donc comparer les volumes absolus sans rapporter à la taille du secteur n'a pas
      grand sens — c'est comme dire "Total Energies pollue plus qu'un café du coin".

    Pour être honnête, le journaliste devrait préciser la métrique (absolu vs par employé vs
    par CA) et idéalement présenter plusieurs angles. Le facteur × 9 absolu et × 2,6 par
    employé (1,59/0,60) et × 3,8 par MCHF (9,32/2,43) racontent la même réalité avec
    des intensités très différentes.

---

## Exercice 4 — Corrélation vs causalité

### La question posée

> "Les énergies renouvelables augmentent-elles les émissions CO2 ?"

Cette question semble absurde, mais un graphique bien choisi peut la rendre "crédible".

### Graphique A — Le graphique "accusateur"

Créez un Scatter Plot :

| Champ | Valeur |
|-------|--------|
| X-Axis | `AVG(Part_renouvelable_pct)` |
| Y-Axis | `SUM(Emissions_CO2_tonnes)` |
| Dimension | `Annee` |
| Filters | `Secteur = IT` |

👉 Chaque point est une année. La tendance est claire : plus le secteur IT adopte les renouvelables, plus ses émissions augmentent. La droite de tendance monte !

Sauvegardez : `[Trompeur] Renouvelables augmentent emissions IT`

### Graphique B — La vraie explication

Créez un graphique à double axe (ou deux Line Charts côte à côte sur un dashboard) :

**Graphique B1 — Croissance des emplois IT :**

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Nb_employes)` |
| Filters | `Secteur = IT` |

**Graphique B2 — Croissance des émissions ET des renouvelables IT :**

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metrics | `SUM(Emissions_CO2_tonnes)`, `AVG(Part_renouvelable_pct)` |
| Filters | `Secteur = IT` |

👉 Les trois courbes montent ensemble : emplois, émissions, et renouvelables. La vraie cause commune est la **croissance du secteur**. Plus d'entreprises IT = plus d'énergie consommée = plus d'émissions ET plus de renouvelables adoptés.

Sauvegardez : `[Honnête] Croissance IT explique tout`

### Analyse

**Réponse — Quelle est la variable cachée qui explique la corrélation entre renouvelables et émissions dans le secteur IT ?**

    La **croissance du secteur lui-même** (mesurée par le nombre d'employés et le chiffre
    d'affaires). De 2015 à 2024 :
    - Employés     : 4 200 → 8 500  (+102 %, x 2)
    - CA           : 890   → 2 100  (+136 %, x 2,4)
    - Émissions    : 2 800 → 5 100  (+82 %)
    - Renouvelable : 18 %  → 55 %  (+37 points)

    Plus le secteur grossit, plus il :
    a) consomme d'énergie au global → émissions absolues ↑
    b) a les moyens financiers d'investir dans les renouvelables → % renouvelable ↑

    Les deux courbes montent ensemble PARCE QU'elles ont une **cause commune** (croissance),
    pas parce que l'une cause l'autre. C'est la définition d'une **confounding variable**.

**Réponse — Donnez un exemple réel (hors de ce dataset) de corrélation célèbre qui n'est pas une causalité.**

    Le classique : **ventes de glaces ↑ et nombre de noyades ↑** — on en déduirait que
    manger des glaces fait se noyer. La vraie variable cachée est l'**été** (chaleur) :
    quand il fait chaud, on mange plus de glaces ET on se baigne plus, donc plus de
    noyades. Aucun lien causal entre les deux.

    Autres exemples célèbres :
    - Nombre de pirates ↓ et température mondiale ↑ depuis 1800 (Pastafarisme — pirates
      n'ont aucun lien avec le climat, c'est juste la modernité qui a fait baisser les deux
      indépendamment)
    - Consommation de fromage par habitant ↑ et morts par étranglement dans les draps ↑
      (Spurious Correlations de Tyler Vigen — pure coïncidence statistique)
    - Taille du pied chez l'enfant ↑ et qualité de lecture ↑ (variable cachée : l'âge)

**Réponse — Quelle question poser systématiquement face à une corrélation présentée comme une cause ?**

    « **Quelle troisième variable pourrait expliquer les deux à la fois ?** »

    Sous-questions :
    - Existe-t-il une cause commune (confounder) plausible ?
    - L'ordre temporel est-il vérifié (A précède bien B) ?
    - Si on contrôle pour la variable cachée, la corrélation tient-elle encore ?
    - Y a-t-il une **expérience contrôlée** (ou quasi-expérience naturelle) qui établit le lien ?
    - Est-ce une corrélation statistique ou un mécanisme causal documenté ?

---

## Assembler le Dashboard de la manipulation

Créez un nouveau dashboard nommé `Démonstration : faire mentir les stats`.

Organisez-le en 4 lignes, une par technique :

```
┌─────────────────────────────────────────────────────────┐
│  TECHNIQUE 1 : AXE TRONQUÉ                             │
│  [Honnête] tous secteurs   │  [Trompeur] explosion IT  │
├─────────────────────────────────────────────────────────┤
│  TECHNIQUE 2 : CHOIX DE LA PÉRIODE                     │
│  [Honnête] vue complète    │  [Trompeur] en bonne voie │
│                            │  [Trompeur] en hausse     │
├─────────────────────────────────────────────────────────┤
│  TECHNIQUE 3 : ABSOLU VS RELATIF                       │
│  [Absolu]  │  [Relatif/emp]  │  [Relatif/CA]           │
├─────────────────────────────────────────────────────────┤
│  TECHNIQUE 4 : CORRÉLATION VS CAUSALITÉ                │
│  [Trompeur] scatter        │  [Honnête] vraie cause    │
└─────────────────────────────────────────────────────────┘
```

Ajoutez un **Text Box** en haut de chaque section (depuis le menu **Edit → Add text**) avec le nom de la technique et une courte description.

---

## Question de synthèse

Vous venez de créer plusieurs paires de graphiques basés sur **exactement les mêmes données**, qui racontent des histoires opposées.

**Réponse — Parmi les 4 techniques vues, laquelle vous semble la plus difficile à détecter à la lecture rapide d'un graphique ? Justifiez.**

    La **corrélation vs causalité** (technique 4). Pourquoi ?

    Pour les trois autres, il existe un **signal visuel** sur le graphique lui-même :
    - axe tronqué → on peut lire la valeur min de l'axe Y
    - choix de période → on voit les dates affichées
    - absolu/relatif → on lit l'unité (t, t/employé, etc.)

    Avec la corrélation/causalité, le graphique peut être 100 % honnête, les chiffres exacts,
    l'axe à zéro, la période complète… et pourtant la conclusion qu'on en tire ("A cause B")
    est fausse. C'est dans l'**interprétation**, pas dans la représentation, que la
    manipulation se glisse. Aucun indice graphique ne la révèle — il faut connaître le domaine
    et activement chercher des variables cachées.

**Réponse — Proposez 3 questions que vous devriez vous poser systématiquement avant de partager ou de croire un graphique.**

    1. **L'axe Y commence-t-il à zéro, et quelle est la période représentée ?**
       (Et si non, pourquoi ce point de départ ? Que se serait-il passé avec un autre ?)
    2. **La métrique est-elle absolue ou relative ? Relative à quoi ?**
       (Si c'est un % ou un ratio, quel est le dénominateur ? Le total a-t-il changé ?)
    3. **Si deux variables évoluent ensemble, existe-t-il une troisième variable
       (cause commune) qui pourrait expliquer les deux ?**
       (Avant de conclure "A cause B", chercher le confounder.)

    Bonus : qui a produit ce graphique, et quel message a-t-il intérêt à faire passer ?

**Réponse — Dans votre futur métier d'informaticien, dans quels contextes pourriez-vous être confronté à des données présentées de manière trompeuse ?**

    Au quotidien, partout où il y a des dashboards à interpréter ou à présenter :

    - **Dashboards de monitoring** (Grafana/Prometheus comme dans les projets précédents) :
      un axe tronqué sur la latence peut donner l'impression d'un incident critique
      pour une variation de quelques ms ; un seuil d'alerte mal réglé peut "prouver"
      qu'un système est instable.
    - **Rapports de performance** présentés au management ou au client : on a vite fait
      de cacher une baisse en sélectionnant la bonne période ou la bonne métrique
      (réponses en ms vs uptime en %).
    - **Métriques produit / KPIs** (acquisition, rétention, NPS) : passer en % de croissance
      au lieu du chiffre absolu masque un produit qui stagne.
    - **Sécurité / incidents** : "0 incident critique ce mois" peut cacher 50 incidents majeurs
      ou un seul incident catastrophique.
    - **Benchmarks de produits concurrents** : choix des features comparées, échelles,
      conditions de test.
    - **Présentations au comité de direction** où l'enjeu politique pousse à présenter
      les chiffres « sous le bon angle ».
    - **Vie privée** : presse, réseaux sociaux, débats publics — c'est exactement les mêmes
      techniques.

    L'esprit critique sur les graphiques est une compétence transverse, autant utile au
    travail que pour s'informer en tant que citoyen.

---

## Conclusions

Les données ne mentent pas — mais leur présentation peut créer de toutes pièces une illusion de vérité.

Un professionnel de l'informatique manipule des données au quotidien : tableaux de bord de monitoring, rapports de performance, métriques métier. Savoir **lire un graphique de manière critique** est aussi important que savoir le créer.

Les questions à garder en tête :

- L'axe Y commence-t-il à zéro ?
- Quelle est la période couverte ? Pourquoi ce point de départ ?
- La métrique est-elle absolue ou relative ? Relative à quoi ?
- Deux variables qui évoluent ensemble ont-elles vraiment un lien de cause à effet ?
- Qui a produit ce graphique ? Quel message veut-il faire passer ?
