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

    (votre réponse ici)

**Réponse — En pourcentage, est-ce vraiment "explosif" comparé à l'ensemble des secteurs ?**

    (votre réponse ici)

**Réponse — Quels indices visuels permettent de détecter qu'un graphique utilise un axe tronqué ?**

    (votre réponse ici)

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

    (votre réponse ici)

**Réponse — Quelle question devez-vous toujours poser quand on vous présente une évolution en % ?**

    (votre réponse ici)

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

| Secteur | Rang absolu | Rang par employé | Rang par MCHF |
|---------|------------|-----------------|---------------|
| IT | | | |
| Industrie | | | |
| Commerce | | | |
| Services | | | |

**Réponse — Un lobbyiste du secteur IT veut montrer que son secteur est le plus vertueux. Quel graphique choisit-il ? Pourquoi ?**

    (votre réponse ici)

**Réponse — Un journaliste veut montrer que l'Industrie pollue "50 fois plus que le secteur IT". Est-ce factuellement correct ? Est-ce honnête ?**

    (votre réponse ici)

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

    (votre réponse ici)

**Réponse — Donnez un exemple réel (hors de ce dataset) de corrélation célèbre qui n'est pas une causalité.**

    (votre réponse ici)

**Réponse — Quelle question poser systématiquement face à une corrélation présentée comme une cause ?**

    (votre réponse ici)

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

    (votre réponse ici)

**Réponse — Proposez 3 questions que vous devriez vous poser systématiquement avant de partager ou de croire un graphique.**

    1. (votre réponse ici)
    2. (votre réponse ici)
    3. (votre réponse ici)

**Réponse — Dans votre futur métier d'informaticien, dans quels contextes pourriez-vous être confronté à des données présentées de manière trompeuse ?**

    (votre réponse ici)

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
