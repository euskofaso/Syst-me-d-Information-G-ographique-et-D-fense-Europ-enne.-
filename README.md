# Structuration territoriale et analyse spatiale des bénéficiaires du Fonds européen de défense (EDF) à l'échelle NUTS-3

> **Article de recherche individuel** — HEC Montréal  
> Cours de Thierry Warin · Matricule 11231523

---

## Résumé

Ce projet analyse la distribution spatiale des **654 bénéficiaires** du Fonds européen de défense (EDF, 2021–2024) à travers **1 514 régions NUTS-3** européennes. En mobilisant les outils de la science des données spatiales, l'étude teste trois hypothèses :

1. **Autocorrélation spatiale** — La répartition géographique des bénéficiaires suit-elle une logique de concentration spatiale ?
2. **Proximité aux donneurs d'ordre** — La distance aux 17 principaux contractants de défense (SIPRI Top 100) influence-t-elle la participation régionale ?
3. **Densité entrepreneuriale** — Le tissu entrepreneurial local constitue-t-il un déterminant significatif de la participation à l'EDF ?

## Données

| Source | Description |
|--------|-------------|
| [Commission européenne — Financial Transparency System](https://ec.europa.eu/budget/financial-transparency-system/) | Bénéficiaires EDF (2021–2024) |
| [Eurostat — `bd_size_r3`](https://ec.europa.eu/eurostat) | Nombre d'entreprises par région NUTS-3 |
| [Eurostat — `demo_r_pjangrp3`](https://ec.europa.eu/eurostat) | Population régionale |
| [GISCO — NUTS 2021](https://ec.europa.eu/eurostat/web/gisco) | Shapefile des régions NUTS-3 (EPSG:3035) |
| [SIPRI Arms Industry Database](https://www.sipri.org/databases/armsindustry) | Top 100 entreprises de défense (2023) |
| Orbis (Bureau van Dijk) via HEC | Géocodage par lots des sièges sociaux |

> **Note :** Le Royaume-Uni est exclu en raison de sa non-participation à l'EDF post-Brexit.

## Méthodologie

### Traitement géospatial
- **Projection** : EPSG:3035 (ETRS89-LAEA) pour les analyses spatiales, EPSG:4326 pour la cartographie
- **Jointures spatiales** et agrégation des bénéficiaires par région NUTS-3
- **Variable dépendante** : `EDF_per_ca` — nombre de bénéficiaires par million d'habitants
- **Cartographie** : 5 cartes choroplèthes (Jenks natural breaks, 6 classes)

### Analyse exploratoire spatiale (ESDA)
- **Matrice de poids** : Contiguïté Queen d'ordre 1 (`NUTS3_queen.gal`), construite sur `NUTS_ID`
- **Moran's I global** : 0.082 (z = 4.897, p = 0.002) → autocorrélation spatiale positive significative
- **LISA** (indicateurs locaux) : 22 clusters High-High, 55 Low-High, 59 High-Low, 0 Low-Low (999 permutations)

### Modélisation économétrique
- **OLS** (R² = 0.055) :
  - `dist_km` (distance au contractant SIPRI le plus proche) : non significatif
  - `eurostat_e` (nombre d'entreprises) : hautement significatif (p < 0.001)
  - `eurostat_p` (population) : significatif (p = 0.003)
- **Spatial Error Model** (sélectionné via Robust LM-error, p = 0.044) :
  - Lambda = 0.071 (p = 0.110, non significatif)

## Principaux résultats

| Hypothèse | Résultat |
|------------|----------|
| Autocorrélation spatiale des bénéficiaires | ✅ Confirmée |
| Proximité aux donneurs d'ordre SIPRI comme facteur explicatif | ❌ Non soutenue |
| Densité entrepreneuriale locale comme déterminant | ✅ Confirmée |

## Structure des fichiers

```
├── data/
│   ├── EDF_beneficiaries.csv          # Bénéficiaires géocodés
│   ├── NUTS3_2021_3035.*              # Shapefile NUTS-3
│   └── SIPRI_top100_2023.csv          # Contractants de défense
├── analysis/
│   ├── NUTS3_final_GeoDa_v2.shp       # Shapefile final pour GeoDa
│   ├── NUTS3_queen.gal                # Matrice de poids spatiale
│   └── results/                       # Sorties OLS et SEM
├── maps/
│   ├── carte1_beneficiaires.png
│   ├── carte2_edf_per_capita.png
│   ├── carte3_lisa_clusters.png
│   ├── carte4_entreprises.png
│   └── carte5_dist_sipri.png
├── article/
│   └── article_final.docx             # Article complet
├── presentation/
│   └── slides.pptx                    # Présentation (11 diapositives)
└── README.md
```

## Outils

- **QGIS 3.44** — Traitement géospatial, jointures, cartographie choroplèthe
- **GeoDa 1.22** — Poids spatiaux, Moran's I, LISA, régressions OLS et SEM
- **Orbis (Bureau van Dijk)** — Géocodage par lots
- **Canva / PowerPoint** — Présentation

## Limites

- Les bénéficiaires sont géolocalisés au **siège social**, non aux sites de production
- Design **transversal** (cross-sectional) : aucune inférence causale
- L'EDF représente la coopération institutionnalisée de l'UE, non l'ensemble de l'EDTIB
- Fenêtre temporelle limitée (2021–2024)

## Références clés

- Dunning, J. H. (1998). *Location and the Multinational Enterprise: A Neglected Factor?*
- Dupuy, R. (2023). Analyse de la base industrielle et technologique de défense européenne.
- Anselin, L. (1995). Local Indicators of Spatial Association — LISA.
- Moran, P. A. P. (1950). Notes on Continuous Stochastic Phenomena.

## Licence

Projet académique — HEC Montréal. Utilisation à des fins éducatives uniquement.
