# K-Means Clustering — Palmer Penguins Morphology Analysis

## Dataset Overview

**Source:** `penguins.csv` (Palmer Penguins dataset)
**Size:** 344 penguins, 7 columns (342 after dropping 2 rows missing all numeric measurements)
**Species:** Adelie (152), Gentoo (124), Chinstrap (68) — kept aside as a label, never used to fit the model
**Clustering features:** 4 continuous morphology measurements — `bill_length_mm`, `bill_depth_mm`, `flipper_length_mm`, `body_mass_g`

Categorical columns (`island`, `sex`) were excluded from the clustering features so the model discovers structure purely from physical measurements. `species` and `sex` were retained separately for post-hoc interpretation only.

## Preprocessing

1. Dropped rows with missing numeric measurements (2 rows)
2. Applied `StandardScaler` to all four numeric features — essential because K-means uses Euclidean distance, and without scaling, `body_mass_g` (values in the thousands) would dominate the distance calculation over `bill_depth_mm` (values in the tens)

## Choosing k: Elbow Method + Silhouette Score

- **Elbow Method:** SSE vs. k showed a bend in the k=2–4 range but was not sharply conclusive on its own.
- **Silhouette Score:** peaked clearly at **k=2 (score = 0.53)** and declined steadily as k increased — giving an unambiguous, quantitative answer where the elbow plot alone was fuzzy.

**Chosen k = 2**, based on the Silhouette Score.

## Results

| Cluster | Size | Avg. Bill Length | Avg. Bill Depth | Avg. Flipper Length | Avg. Body Mass |
|---|---|---|---|---|---|
| 0 | 219 | 41.9 mm | 18.4 mm | 191.8 mm | 3710.7 g |
| 1 | 123 | 47.5 mm | 15.0 mm | 217.2 mm | 5076.0 g |

**Cluster vs. species:**

| | Adelie | Chinstrap | Gentoo |
|---|---|---|---|
| Cluster 0 | 151 | 68 | 0 |
| Cluster 1 | 0 | 0 | 123 |

## Key Insight

The two-cluster solution isn't a compromise — it's the genuinely strongest structure in the data. **Cluster 1 is essentially pure Gentoo** (123/123, zero Adelie or Chinstrap), separated out by its much larger flipper length and body mass. **Cluster 0 merges Adelie and Chinstrap**, because these two species overlap heavily in bill and body measurements and can't be geometrically separated by K-means using only these four features.

The sex split within each cluster is close to even (107F/107M in Cluster 0, 58F/61M in Cluster 1), showing the clustering is driven by species-level size differences, not by male-vs-female sexual dimorphism — that effect exists in the data but is smaller than the species-level size gap.

## Limitations & Next Steps

- K-means assumes roughly spherical, similarly-sized clusters — Adelie and Chinstrap's genuine morphological overlap means no k using only these 4 features will cleanly split them into 3.
- `island` was deliberately excluded to keep clustering purely morphology-based; including it (Chinstrap and Adelie occupy different islands) would likely recover all 3 species as separate clusters.
- Potential next steps: force k=3 and compare silhouette score against k=2 to quantify the tradeoff; try a Gaussian Mixture Model for non-spherical cluster boundaries; use PCA to visualize all 4 dimensions in a single 2D projection.

## Files

- `kmeans_penguins.ipynb` — full notebook, all cells executed, with Elbow plot, Silhouette plot, cluster scatterplot, and species-comparison scatterplot
- `README.md` — this file
