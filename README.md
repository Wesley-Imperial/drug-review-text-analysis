# Drug Review Text Analysis
### Imperial College London | MSBA | Text Analysis for Business 2026
https://drive.google.com/drive/folders/1JdwkNarffY648h-UwDiEAby9fyDM2TG0?usp=drive_link

Analysing 193,895 patient drug reviews to uncover two hidden signals — what makes a review useful to patients, and what clinical information it contains. Built with R, BERT embeddings, and machine learning.

---

## Project Overview

This project investigates patient drug reviews from Drugs.com (2008–2017) from two complementary perspectives:

- **Patient Perspective (Aspect 1):** What linguistic features make a review useful to other patients?
- **Clinical Perspective (Aspect 2):** Can we predict the medical condition and detect side effects from review text alone?

**Main Research Question:**
> "How do linguistic features in patient drug reviews reflect patient experience, perceived usefulness, and clinically relevant information?"

---

## Team

| Member | Responsibility |
|--------|---------------|
| Pete | Phase 2 — Feature extraction & BERT embeddings |
| Sarah | Phase 1 — Exploratory data analysis |
| Evonne | Aspect 1 — Patient perspective models |
| Wesley | Aspect 2 — Clinical perspective models |
| Ellen | Phase 5 — Cross-aspect comparison & interpretation |

---

## Repository Structure

```
drug-review-text-analysis/
├── data/
│   └── README.md              ← data access instructions
├── feature-extraction/
│   ├── scripts/
│   └── csv-and-npy-files/
│       └── README.md          ← output file descriptions
├── eda/
│   └── scripts                ← exploratory data analysis
├── aspect1-patient/
│   ├── scripts/
│   │   ├── aspect1_benchmark.qmd
│   │   └── aspect1_bert.qmd
│   └── plots/
└── aspect2-clinical/
│   ├── scripts/
│   │   ├── aspect2_benchmark.qmd
│   │   ├── aspect2_bert.qmd
│   │   ├── aspect2_side_effects.qmd
|   └── plots/
```

---

## Data

Data files are not included in this repository due to size constraints. All files are available in the shared Google Drive folder.

| File | Rows | Description |
|------|------|-------------|
| `drug_reviews_phase2.csv` | 193,895 | Cleaned reviews + Phase 2 features |
| `bert_pca_50.csv` | 198,308 | 50 PCA-reduced BERT embeddings |
| `shared_train_idx.csv` | 155,120 | Shared train row IDs (80/20 split) |
| `bert_embeddings.npy` | 198,308 | Raw 768-dim BERT embeddings |

Place all data files in the `/data` folder before running any scripts.

---

## Methods

### Phase 1 — Exploratory Data Analysis
Rating distribution, review length, sentiment analysis, word clouds, and condition frequency analysis.

### Phase 2 — Feature Extraction & BERT Embeddings
Six linguistic features extracted per review: sentiment score, emotional intensity, review length, rating-tone mismatch, TF-IDF specificity, and condition category. BERT embeddings generated using bert-base-uncased, reduced to 50 dimensions via PCA.

### Phase 3 — Benchmark Models
Two benchmark models using Phase 2 linguistic features:
- Aspect 1: Logistic regression predicting review usefulness
- Aspect 2: Multinomial logistic regression predicting condition category

### Phase 4 — BERT Models
Two BERT-based models replacing linguistic features with 50 PCA components:
- Aspect 1: Random forest predicting review usefulness
- Aspect 2: Random forest predicting condition category

### Phase 5 — Cross-Aspect Comparison
Feature importance comparison across all four models to identify shared and divergent linguistic signals between patient usefulness and clinical condition prediction.

---

## Key Results

### Aspect 1 — Patient Perspective (Usefulness)

| Model | Features | Classifier | Macro F1 | Accuracy | Kappa |
|-------|----------|------------|----------|----------|-------|
| Benchmark | 6 linguistic features | Binary logistic regression (GLM) | 0.858 | 75.14% | 0.002 |
| BERT | 50 PCA components | Random forest | 0.858 | 91.94% | 0.763 |

**Key findings:**
- Despite identical Macro F1, the two models are fundamentally different — Kappa jumps from 0.002 to 0.763
- Benchmark collapses to majority class — only 25 useful predictions total, missing ~9,800 actual useful reviews
- BERT correctly identifies ~6,958 useful reviews with far fewer missed cases (~2,906 vs ~9,800)
- Rating dominates benchmark feature importance (score 100); all other features negligible
- BERT importance distributed across multiple PCA components — captures nuanced linguistic signals

---

### Aspect 2 — Clinical Perspective (Condition + Side Effects)

| Model | Features | Classifier | Macro F1 | Accuracy | Kappa |
|-------|----------|------------|----------|----------|-------|
| Benchmark | 6 linguistic features | Multinomial logistic regression | 0.244 | 32.65% | 0.091 |
| BERT | 50 PCA components | Random forest | 0.793 | 83.04% | 0.791 |

**Key findings:**
- BERT embeddings improve Macro F1 by +225% over simple linguistic features
- Benchmark collapses to 2 dominant classes (Women's Health, Mental Health) — 10 classes get zero predictions
- BERT recovers all 14 classes including Cancer (sensitivity 0.64) and Cardiovascular (0.50)
- Side effect language appears in 71.6% of all reviews, averaging 1.9 mentions per review
- Pain & Inflammation has the highest side effect mention rate (85.9%); Respiratory the lowest (38.0%)
- Side effect mentions peak at rating 4, not rating 1 — clinical detail is deliberate, not purely emotional

---

### Cross-Aspect Comparison

| | Usefulness (Aspect 1) | Clinical Signal (Aspect 2) |
|--|--|--|
| What drives it? | Rating, tone, experience | Specific vocabulary, semantics |
| Model difficulty | Easy — proxy-based, surface-level | Hard — context-dependent |
| BERT improvement | Minimal (same F1, better Kappa) | Massive (+0.549 F1) |

**Key insight:** Usefulness is a socially interpretable signal easily captured by surface features. Clinical information is a semantically embedded signal that requires deep language understanding. These are not just different tasks — they are fundamentally different types of language.

---

## Requirements

### R Packages
```r
install.packages(c(
  "tidyverse",
  "caret",
  "nnet",
  "randomForest",
  "MLmetrics",
  "tidytext",
  "ggplot2",
  "doParallel"
))
```

### Python Packages

transformers
torch
numpy
pandas
scikit-learn

---

## How to Run

1. Clone this repository
2. Open `data/README.md` for the Google Drive link and download all data files
3. Place all downloaded files into the `/data` folder at the root of the repository
4. Run scripts in order:
   - `feature-extraction/scripts/phase0_phase2.Rmd` ← data cleaning & feature extraction
   - `feature-extraction/scripts/phase2_5_bert.ipynb` ← BERT embedding generation
   - Then run aspect scripts:
     - Create a `/data` folder inside `aspect1-patient/` and `aspect2-clinical/`
     - Run `aspect1-patient/scripts/aspect1_benchmark.qmd` and `aspect1_bert.qmd`
     - Run `aspect2-clinical/scripts/aspect2_benchmark.qmd`, `aspect2_bert.qmd`, and `aspect2_side_effects.qmd`

> **Important:** All scripts use `set.seed(42)` and load the shared train/test split 
> from `shared_train_idx.csv`. Never regenerate the split independently.

> **Tip:** Pre-trained models are saved as `.rds` files. To skip re-running the models 
> (which can take a long time), load them directly:
> ```r
> model_bm <- readRDS("data/model_benchmark.rds")
> model_bert <- readRDS("data/model_bert_100.rds")
> ```

---

## Project Conventions

- `set.seed(42)` at the top of every R script
- Train/test split loaded from `shared_train_idx.csv` — never regenerated
- `row_id` added with `mutate(row_id = row_number())` immediately after loading CSV
- Primary metric: Macro F1 across all models
- Never modify Pete's original output files

---

## Acknowledgements

Dataset: Suruchi Fialoke, Drug Review Dataset, Kaggle (originally from UCI ML Repository, drugs.com 2008–2017)
