# Premiers graphiques avec Superset

Superset est installé et connecté à votre Google Sheet. L'objectif de cette partie est de créer vos premiers graphiques et de les assembler dans un dashboard.

## L'interface Explore

Superset propose un outil central pour créer des graphiques : **Explore**. Il fonctionne sans écrire de code : vous sélectionnez un dataset, un type de graphique, et vous configurez les dimensions et métriques visuellement.

```mermaid
flowchart LR
    DS["Dataset<br/>co2_emissions"] --> EX["Explore<br/>(configuration)"]
    EX --> CH["Chart<br/>(graphique)"]
    CH --> DB["Dashboard"]

    %% Styles
    style DS fill:#d9e1f2,stroke:#6d9eeb,stroke-width:2px,color:#000
    style EX fill:#fce5cd,stroke:#f6b26b,stroke-width:2px,color:#000
    style CH fill:#d9f2e1,stroke:#6bda9a,stroke-width:2px,color:#000
    style DB fill:#d9f2e1,stroke:#6bda9a,stroke-width:2px,color:#000
```

### Vocabulaire Superset

| Terme | Signification |
|-------|---------------|
| **Dataset** | La source de données (table ou CSV) |
| **Metric** | Ce que vous mesurez (somme, moyenne, comptage…) |
| **Dimension** | Ce par quoi vous regroupez (secteur, année…) |
| **Filter** | Condition pour restreindre les données affichées |
| **Chart** | Un graphique sauvegardé |
| **Dashboard** | Un ensemble de charts assemblés |

## Accéder à Explore

Dans le menu de gauche :  
👉 **Charts → + Chart**

Sélectionnez le dataset `co2_emissions`, puis choisissez un type de visualisation.

## Graphique 1 — Evolution des émissions par secteur

**Type de visualisation :** Line Chart

Dans la configuration du graphique :

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Dimension | `Secteur` |

Cliquez sur **Update Chart**.

Sauvegardez le graphique :  
👉 Bouton **Save** → Nom : `Emissions CO2 par secteur (2015-2024)`

**Réponse — Quel secteur domine largement en termes d'émissions absolues ?**

    L'**Industrie** domine très largement avec 45 200 t en 2024 — soit 62 % du total
    de toutes les émissions cumulées (73 200 t). Les 3 autres secteurs réunis
    (IT + Commerce + Services = 28 000 t) ne représentent même pas la moitié de l'Industrie.

**Réponse — Quel secteur a la croissance la plus forte sur la période ?**

    L'**IT** : +82 % (de 2 800 t en 2015 à 5 100 t en 2024).
    Classement de la croissance 2015→2024 :
    - IT        : +82 % (+2 300 t)
    - Commerce  : +22 % (+1 900 t)
    - Services  : +19 % (+2 000 t)
    - Industrie : -6  % (-2 800 t)  ← seul secteur en baisse

---

## Graphique 2 — Comparaison des émissions en 2024

**Type de visualisation :** Bar Chart

| Champ | Valeur |
|-------|--------|
| X-Axis | `Secteur` |
| Metric | `SUM(Emissions_CO2_tonnes)` |
| Filters | `Annee = 2024` |

Sauvegardez : `Emissions CO2 par secteur en 2024`

**Réponse — L'Industrie émet combien de fois plus que le secteur IT en 2024 ?**

    45 200 / 5 100 = **8,86 fois**, soit environ **× 9**.
    (Attention : c'est un rapport en valeur absolue ; quand on rapporte au CA ou
    aux emplois, l'écart change complètement — voir exercice 3 de la partie 3.)

---

## Graphique 3 — Part des énergies renouvelables

**Type de visualisation :** Line Chart

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `AVG(Part_renouvelable_pct)` |
| Dimension | `Secteur` |

Sauvegardez : `Part renouvelable par secteur`

**Réponse — Quel secteur adopte le plus rapidement les énergies renouvelables ?**

    L'**IT**, et de loin : 18 % en 2015 → **55 %** en 2024, soit **+37 points** en 10 ans.
    Classement de la progression :
    - IT        : +37 points  (18 → 55 %)
    - Services  : +22 points  (14 → 36 %)
    - Commerce  : +21 points  (12 → 33 %)
    - Industrie : +13 points  (8  → 21 %)

---

## Graphique 4 — Nombre d'employés par secteur

**Type de visualisation :** Bar Chart (empilé)

| Champ | Valeur |
|-------|--------|
| X-Axis | `Annee` |
| Metric | `SUM(Nb_employes)` |
| Dimension | `Secteur` |
| Bar Stack | activé |

Sauvegardez : `Emplois par secteur`

**Réponse — Quel secteur emploie le plus de personnes en 2024 ?**

    Les **Services** : 48 000 employés en 2024.
    Ranking 2024 : Services (48 000) > Industrie (28 500) > Commerce (21 500) > IT (8 500).
    À noter : IT a la plus petite masse salariale mais a doublé ses effectifs depuis 2015
    (4 200 → 8 500, +102 %).

---

## Graphique 5 — Tableau récapitulatif 2024

**Type de visualisation :** Table

| Champ | Valeur |
|-------|--------|
| Columns | `Secteur` |
| Metrics | `SUM(Emissions_CO2_tonnes)`, `AVG(Part_renouvelable_pct)`, `SUM(Nb_employes)`, `SUM(CA_MCHF)` |
| Filters | `Annee = 2024` |
| Row Limit | 10 |

Sauvegardez : `Tableau récapitulatif 2024`

---

## Utiliser SQL Lab

Superset intègre un éditeur SQL qui permet d'aller plus loin que l'interface visuelle. Cela est utile pour créer des métriques calculées.

Dans le menu :  
👉 **SQL Lab → SQL Editor**

Sélectionnez la base de données `main`.

### Requête 1 — Emissions par employé

```sql
SELECT
    "Annee",
    "Secteur",
    "Emissions_CO2_tonnes",
    "Nb_employes",
    ROUND((CAST("Emissions_CO2_tonnes" AS NUMERIC) / "Nb_employes"), 2) AS emissions_par_employe
FROM public.co2_emissions
ORDER BY "Annee", "Secteur";
```

Exécutez la requête et observez les résultats.

**Réponse — Quel secteur a les émissions les plus élevées par employé en 2024 ?**

    L'**Industrie** avec **1,59 t CO2 / employé** en 2024 — très loin devant les autres :
    - Industrie : 1,586 t/employé   (45 200 / 28 500)
    - IT       : 0,600 t/employé   (5 100 / 8 500)
    - Commerce : 0,484 t/employé   (10 400 / 21 500)
    - Services : 0,260 t/employé   (12 500 / 48 000)

### Requête 2 — Emissions par million de CHF de CA

```sql
SELECT
    "Annee",
    "Secteur",
    ROUND(CAST("Emissions_CO2_tonnes" AS NUMERIC) / "CA_MCHF", 2) AS emissions_par_MCHF
FROM public.co2_emissions
WHERE "Annee" = 2024
ORDER BY emissions_par_MCHF DESC;
```

**Réponse — Le classement des secteurs change-t-il selon la métrique utilisée (absolu, par employé, par MCHF de CA) ?**

    OUI, et c'est très éclairant. Récapitulatif des classements 2024 (1 = plus polluant) :

    | Secteur   | Absolu | Par employé | Par MCHF |
    |-----------|--------|-------------|----------|
    | Industrie |   1    |     1       |     1    |  ← toujours premier
    | Services  |   2    |     4       |     3    |  ← bon en "par employé"
    | Commerce  |   3    |     3       |     2    |
    | IT        |   4    |     2       |     4    |  ← le plus efficient au CA

    Seule l'Industrie reste première sur les 3 axes (volume + intensité par tête + par MCHF).
    Les 3 autres secteurs voient leur classement bouger : IT passe de "le moins polluant en absolu"
    à "2ème par employé" (parce qu'il a peu de monde) ; Services dégringole du 2ème au 4ème
    en absolu vs par employé (parce qu'il a énormément d'employés). Cela illustre la nécessité de
    toujours préciser **quelle unité** on utilise quand on parle de pollution.

---

## Créer un Dashboard

Un dashboard regroupe plusieurs graphiques pour raconter une histoire cohérente.

### 1. Créer un nouveau dashboard

Dans le menu :  
👉 **Dashboards → + Dashboard**

Donnez-lui le nom : `Analyse Emissions CO2 Suisse`

### 2. Ajouter les graphiques

Cliquez sur **Edit dashboard**, puis faites glisser vos graphiques depuis le panneau de droite vers la zone centrale :

1. `Emissions CO2 par secteur (2015-2024)` — ligne du dessus, pleine largeur
2. `Emissions CO2 par secteur en 2024` et `Part renouvelable par secteur` — côte à côte
3. `Emplois par secteur` — dessous
4. `Tableau récapitulatif 2024` — en bas

### 3. Ajouter un filtre global

Cliquez sur **Filters** puis **+ Add/Edit Filters** :

| Champ | Valeur |
|-------|--------|
| Filter type | `Value` |
| Dataset | `emissions_co2_secteurs` |
| Column | `Secteur` |
| Default value | (vide = tout afficher) |
| Filter label | `Filtrer par secteur` |

Ce filtre permet de cliquer sur un secteur et de mettre à jour tous les graphiques simultanément.

### 4. Sauvegarder

Cliquez sur **Save** puis **Save and go to dashboard**.

**Réponse — En filtrant uniquement le secteur IT, quelle tendance ressort clairement ?**

    Le secteur IT est en **pleine explosion** sur tous les axes simultanément :
    - Emplois  : 4 200 → 8 500   (+102 %, doublement)
    - CA       : 890 → 2 100 MCHF (+136 %)
    - Émissions: 2 800 → 5 100 t (+82 %)
    - Part renouvelable : 18 → 55 % (+37 points)

    Lecture : c'est un secteur en hyper-croissance économique, qui malgré une transition
    énergétique massive (la part de renouvelable triple presque) voit quand même ses
    émissions absolues augmenter — la croissance du volume d'activité l'emporte sur les
    gains d'efficacité. On verra dans la partie 3 que ce schéma "croissance + renouvelables
    + émissions qui montent" peut être utilisé pour faire mentir les statistiques.

---

## Résultat

Vous savez maintenant :
- créer différents types de graphiques avec Explore
- écrire des requêtes SQL dans SQL Lab
- assembler un dashboard interactif avec filtres

Dans la partie suivante, vous allez utiliser ces mêmes données pour créer des graphiques qui racontent des histoires contradictoires — et comprendre pourquoi les statistiques peuvent induire en erreur.
