# Data Files

Due to GitHub's file size limit, only `shared_train_idx.csv` 
is included in this repository. All other data files are 
available in the shared Google Drive folder.

## Files in this repository
- `shared_train_idx.csv` (998 KB) — Shared train row IDs, 
  155,120 rows, 80/20 split stratified on condition_category, 
  set.seed(42)

## Files available on Google Drive only
- `drug_reviews_phase2.csv` (205 MB) — Cleaned reviews + 
  Phase 2 features, 198,308 rows
- `bert_pca_50.csv` (190 MB) — 50 PCA-reduced BERT 
  embeddings, 198,308 rows
- `pca_model.rds` (57.7 MB) — Fitted PCA model object
- `bert_embeddings.npy` (581 MB) — Raw 768-dim BERT 
  embeddings (not required for Phase 3 scripts)
- `drug_reviews_phase2_5.csv` (1.81 GB) — Combined file, 
  avoid due to memory issues

## Setup Instructions
1. Download data files from shared Google Drive
2. Place all files in the /data folder
3. You are ready to run the scripts
