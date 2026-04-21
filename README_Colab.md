# Distributed Machine Learning on Banking Data
### Google Colab Implementation

A distributed machine learning project built on a real-world bank marketing dataset. This notebook-based implementation uses Apache Spark (PySpark), pandas, and scikit-learn to cover five areas: data analysis with SQL, exploratory data analysis, predictive modelling, real-time stream simulation, and data parallelism.

---

## Project Overview

Banks deal with large volumes of customer data daily. This project simulates how a bank's data engineering team would use distributed computing to process, analyse, and extract predictions from customer marketing data — all without needing a physical cluster, by running Spark in local mode on Google Colab.

---

## Dataset

**File:** `bank.csv` — Bank Marketing Dataset (UCI Machine Learning Repository)
**Records:** 4,521 rows | **Features:** 17 columns

| Column | Description |
|---|---|
| age | Client age |
| job | Employment type |
| marital | Marital status |
| education | Education level |
| default | Credit default status |
| balance | Yearly account balance (euros) |
| housing | Has housing loan |
| loan | Has personal loan |
| contact | Communication type |
| day / month | Last contact date |
| duration | Call duration in seconds |
| campaign | Contacts in this campaign |
| pdays | Days since last campaign (-1 = never contacted) |
| previous | Contacts before this campaign |
| poutcome | Previous campaign outcome |
| y | **Target**: subscribed to term deposit? (yes/no) |

---

## How to Run — Step by Step

### Step 1 — Open Google Colab
Go to [colab.research.google.com](https://colab.research.google.com) and click **New notebook**.

### Step 2 — Upload the dataset
In the left sidebar, click the **folder icon** → **upload icon** → select `bank.csv` from your computer. The file will be available at `/content/bank.csv`.

> Note: Colab resets uploads when the session ends. Re-upload if you restart the runtime.

### Step 3 — Install PySpark (run once per session)
```python
!pip install pyspark pandasql -q
```

### Step 4 — Run the parts in order

Each part is self-contained. Run cells top to bottom within each part.

| Part | What it does | Approximate run time |
|---|---|---|
| Part 1 | SQL queries on banking data (Hive simulation)
| Part 2 | Exploratory Data Analysis with Spark
| Part 3 | ML models — Logistic Regression + Random Forest
| Part 4 | Real-time stream simulation
| Part 5 | Data parallelism and partition analysis

### Step 5 — Download output charts
```python
from google.colab import files
for fname in ["eda_correlation.png", "eda_job_rate.png", "eda_duration.png",
              "feature_importances.png", "partition_distribution.png"]:
    files.download(f"/content/{fname}")
```

### Step 6 — Save the notebook
**File → Download → Download .ipynb** — this saves all code and outputs together for submission.

---

## Part Descriptions

### Part 1 — Data Analysis and Management (Hive Simulation)
Replicates what Apache Hive does on top of Hadoop — running SQL-style analytical queries on large datasets. In Colab, this is demonstrated using `pandasql`, which applies SQL syntax to pandas DataFrames. Queries cover subscription rates by job type, average balance by outcome, month-wise campaign performance, and education-level conversion rates.

**Key finding:** Retired clients and students convert at the highest rate. May is the busiest campaign month but not the most efficient.

### Part 2 — Exploratory Data Analysis with Spark
Uses PySpark to distribute data loading and computation, then converts results to pandas for visualisation. Covers null detection, class balance, descriptive statistics, correlation analysis, and distribution plots.

**Key finding:** Call duration (`duration`) has the strongest positive correlation with subscription. The dataset has significant class imbalance — 88% did not subscribe.

### Part 3 — Predictive Modelling with Spark MLlib
Builds a complete ML pipeline using Spark's built-in library. The pipeline handles string indexing, one-hot encoding, feature assembly, and scaling automatically. Two models are trained and compared:

- **Logistic Regression** — fast baseline, good interpretability
- **Random Forest** — better accuracy and handles class imbalance more gracefully

Cross-validation is used to tune the regularisation parameter on the Logistic Regression model.

**Key finding:** Random Forest achieves ~90% accuracy with higher F1 score. Call duration and previous campaign outcome are the most important features.

### Part 4 — Real-Time Stream Processing (Simulated)
Since Google Colab does not support persistent socket connections, Spark Streaming is simulated using micro-batch processing. Random samples from the dataset are treated as incoming stream batches, with windowed aggregations computed on each batch. This mirrors the logic of Spark Structured Streaming's window operations and watermarking.

**Key concepts demonstrated:** Micro-batch processing, windowed aggregation, real-time filtering for high-value contacts.

### Part 5 — Data Parallelism
Explores how Spark distributes data across partitions and how partition strategy affects performance. Demonstrates hash partitioning (by job), range partitioning (by age), a custom UDF for parallel risk scoring, and a timing comparison between Spark and single-threaded pandas.

**Key finding:** For 4,521 rows, pandas is faster — Spark's task scheduling overhead is visible at small scale. The advantage reverses at millions of rows, which is exactly the scale Spark is designed for.

---

## Output Files

| File | Generated in |
|---|---|
| `eda_correlation.png` | Part 2 |
| `eda_job_rate.png` | Part 2 |
| `eda_duration.png` | Part 2 |
| `feature_importances.png` | Part 3 |
| `partition_distribution.png` | Part 5 |

---

## Dependencies

All installed via pip inside Colab — no local setup required.

| Library | Purpose |
|---|---|
| pyspark | Distributed data processing and ML |
| pandas | Data manipulation and plotting prep |
| pandasql | SQL queries on DataFrames (Part 1) |
| matplotlib | Chart generation |
| seaborn | Statistical visualisations |
| scikit-learn | Supplementary ML utilities |
