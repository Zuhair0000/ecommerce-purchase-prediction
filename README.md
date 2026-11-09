# 🛒 E-Commerce Purchase Prediction — Big Data Pipeline with Polars

A machine learning pipeline that processes **42.4 million raw e-commerce events** (a 5.7 GB CSV) to predict whether a shopping session will end in a purchase. It uses **Polars** and **Parquet** to handle data far larger than pandas can comfortably fit in memory.

## Dataset
October 2019 clickstream data from a multi-category online store: 👉 [eCommerce behavior data from multi category store (Kaggle)](https://www.kaggle.com/datasets/mkechinov/ecommerce-behavior-data-from-multi-category-store). Each row is one event: a product view, add-to-cart or purchase.

| Metric | Value |
|---|---|
| Raw events | 42,448,764 |
| Unique users | 3,022,290 |
| Shopping sessions | 9,244,422 |
| Sessions with a purchase | 629,560 (6.8%) |

## Pipeline
1. **Lazy loading:** `pl.scan_csv` reads the 5.7 GB file lazily, so nothing loads into memory until it's needed.
2. **CSV to Parquet:** streamed the CSV into a compressed, columnar Parquet file with `sink_parquet` in about 14 seconds, without loading the whole file into memory.
3. **Sessionisation:** grouped 42.4 million events into 9.2 million sessions with multi-threaded Polars aggregations in **under 3 seconds**, counting views, cart additions and purchases per session.
4. **Target:** `made_purchase` = 1 if the session contains at least one purchase.
5. **Avoiding data leakage:** `total_actions` was excluded from the features, because it equals views + carts + purchases, and the model could work out the answer from it. The model uses only `view_count` and `cart_count`.
6. **Model:** logistic regression with **balanced class weights** to handle the 93/7 imbalance, evaluated on a 20% hold-out set.

## Tech stack
Python · Polars · PyArrow (Parquet) · scikit-learn · Jupyter

## Run it locally
1. Download `2019-Oct.csv` from the Kaggle dataset above and place it in the project folder.
2. Create an environment and install dependencies:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   pip install polars pyarrow pandas scikit-learn jupyter
   ```
3. Open `data_prep.ipynb` and run all cells.

## What I'd improve next
- Benchmark Polars against pandas on the same steps to measure the speed-up
- Reduce memory further by dropping the string session IDs before training
- Add richer session features: time spent, number of distinct products, price of carted items
- Try gradient boosting and report precision, recall and PR-AUC
