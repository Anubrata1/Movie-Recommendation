# Unsupervised Movie Recommendation with Nearest Neighbors

This project is a content-based movie recommendation system built with `sklearn.neighbors.NearestNeighbors`. Given a movie title, it returns the most similar movies using metadata—not a predicted rating or artificial target variable.

## What the model uses

- Text: genres, keywords, overview, and tagline using separately weighted TF-IDF blocks
- Categorical: original language
- Numeric: runtime, popularity, vote average, vote count, and release year
- Missing-value indicators for optional features
- TruncatedSVD for the selected compact representation
- Cosine distance with brute-force nearest-neighbor search

Identifiers, titles, URLs, and image paths are not similarity features. Titles and IDs are used only for lookup and duplicate control.

## Data retention

| Stage | Rows | Retained |
|---|---:|---:|
| Original dataset | 1,482,601 | 100.00% |
| Required identifier/title check | 1,482,578 | 100.00% |
| Duplicate removal | 1,458,577 | 98.38% |
| Final recommendation matrix | 1,458,577 | 98.38% |

Optional missing fields are imputed feature-by-feature rather than removing entire rows. Same-title movies from different known release years are retained as possible remakes.

## Final configuration

- Algorithm: unsupervised `NearestNeighbors`
- Features: all useful metadata groups
- Weights: genres 3.0, keywords 3.0, overview 1.5, tagline 0.5, numeric 0.25, language 0.5
- Representation: TF-IDF followed by 100-component TruncatedSVD
- Distance metric: cosine
- Recommendations: K=10
- Final catalog coverage: 1,458,577 movies

The notebook compares multiple K values, cosine/Euclidean/Manhattan distances, 12 feature ablations, four weighting configurations, and direct TF-IDF against six SVD sizes.

## Evaluation

Because there are no user labels or interaction histories, the project does not claim classification accuracy, R², RMSE, or F1. It reports intrinsic recommendation metrics:

- Average neighbor similarity
- Genre and keyword overlap at K
- Diversity at K
- Catalog coverage over evaluation queries
- Recommendation coverage
- Neighbor-distance statistics

These metrics describe relationships in the engineered feature space. They do not prove that an individual user will like a recommendation.

## Run the notebook

The full dataset and generated model are intentionally excluded from Git because they exceed GitHub's file-size limit. Before running the notebook, place the source dataset at `data/movies.csv`. Running all cells recreates `models/final_nearest_neighbors.joblib` and the result reports.

```powershell
cd C:\Users\anubr\PROJECT1_ML\Movie-Recommendation
.\.venv\Scripts\Activate.ps1
jupyter lab movie_knn_recommender.ipynb
```

Select `Python (Movie Recommendation)`, restart the kernel, and run all cells. A complete run rebuilds the reports and the approximately 1 GB final index.

Use the final function in the notebook:

```python
recommend_movies("Interstellar", k=10)
```

It returns rank, title, cosine similarity, genres, and release year while excluding the query movie and obvious duplicates.

## Project structure

```text
Movie-Recommendation/
|-- movie_knn_recommender.ipynb
|-- README.md
|-- requirements.txt
|-- .gitignore
|-- .python-version
|-- data/
|   `-- movies.csv
|-- models/
|   `-- final_nearest_neighbors.joblib
`-- results/
    |-- data_retention_report.csv
    |-- missing_data_report.csv
    |-- nearest_neighbor_experiments.csv
    |-- k_experiments.csv
    |-- distance_metric_experiments.csv
    |-- feature_weight_experiments.csv
    `-- svd_experiments.csv
```

## Extension path

If ratings, clicks, watch history, likes, or completion data become available, this content-based system can be extended into a collaborative or hybrid recommender. Until then, it remains an unsupervised movie-to-movie similarity system.
