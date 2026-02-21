# 🛒 E-Commerce Cart Abandonment Predictor (Big Data Pipeline)

A high-performance Machine Learning pipeline that processes **42+ million rows** of raw clickstream data to predict whether a user will abandon their shopping cart or make a purchase. 

This project demonstrates how to bypass the limitations of standard Pandas by using **Polars** and **Parquet** to achieve a 2,500x speedup in data processing, alongside advanced memory optimization and data leakage prevention.

## 🚀 Key Engineering Achievements

* **Massive Data Processing:** Successfully ingested and transformed a 5.7 GB raw CSV file containing 42 million individual e-commerce events (views, carts, purchases).
* **Parquet Serialization:** Converted row-based CSV data into column-oriented `.parquet` files, reducing initial data scan times from 62 seconds down to 0.02 seconds.
* **Lazy Evaluation & Polars:** Engineered a feature extraction pipeline using Rust-backed Polars to group and aggregate 42 million clicks into 9.2 million unique user sessions in under 3 seconds.
* **Memory Optimization (Downcasting):** Slashed the dataset's RAM footprint by casting default 64-bit integers down to 32-bit and 8-bit integers without data loss.
* **Plugging Data Leakage:** Identified and resolved a critical target-leakage trap where the AI was using basic algebra to deduce the target variable from session totals.
* **Imbalanced Classification:** Handled an extreme 93% to 7% class imbalance using balanced class weights to optimize for **Recall**, successfully identifying 47% of real buyers based purely on their browsing behavior.

## 🛠️ Tech Stack

* **Data Engineering:** Polars, PyArrow (Parquet), Python
* **Machine Learning:** Scikit-Learn (Logistic Regression, Train/Test Split, Classification Metrics)
* **Environment:** Virtual Environments (venv), Jupyter Notebooks

## 🧠 The Architecture

1. **Ingestion & Compression:** Raw CSV is streamed and converted into Parquet format to prevent out-of-memory (OOM) crashes.
2. **Feature Engineering (Sessionization):** Individual time-stamped clicks are aggregated into distinct shopping sessions using fast multi-threaded `groupby` operations. Features include `view_count` and `cart_count`.
3. **Memory Tuning:** Features are downcasted, and heavy string-based UUIDs are stripped from the training matrix.
4. **Predictive Modeling:** A linear model evaluates the ratio of views-to-carts to predict final purchase intent, enabling potential backend triggers for real-time discount pop-ups.

