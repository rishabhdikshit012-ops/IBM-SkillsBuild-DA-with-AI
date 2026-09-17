# 🛒 Online Retail II — Exploratory Data Analysis

> A comprehensive, end-to-end EDA of a real-world UK wholesale gift retailer dataset covering **1,067,371 transactions** across **December 2009 – December 2011**, with full data cleaning, statistical analysis, RFM customer segmentation, and 15 business questions answered with charts and actionable insights.

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Dataset](#-dataset)
- [Project Structure](#-project-structure)
- [Key Findings](#-key-findings)
- [EDA Pipeline](#-eda-pipeline)
  - [1 · Data Cleaning](#1--data-cleaning)
  - [2 · Statistical Analysis](#2--statistical-analysis)
  - [3 · Business Questions](#3--business-questions)
- [Technologies Used](#-technologies-used)
- [Getting Started](#-getting-started)
- [Risks & Recommendations](#-risks--recommendations)
- [Project Report](#-project-report)
- [License](#-license)

---

## 🔍 Project Overview

This project performs a full **Exploratory Data Analysis (EDA)** on the **Online Retail II** dataset — a publicly available transactional record from a UK-based non-store online retailer specialising in wholesale gift-ware and home décor.

The goal is to extract **business-critical insights** from raw transaction data through a rigorous, reproducible Python notebook pipeline covering:

| Stage | Description |
|-------|-------------|
| **Data Cleaning** | 10-step pipeline: type correction, missing values, deduplication, outlier treatment (IQR Winsorisation), feature engineering |
| **Univariate Analysis** | Distributions of quantity, price, revenue; country frequencies; time patterns; cancellation rate |
| **Bivariate Analysis** | Revenue by country, monthly trends, price–quantity correlation, day-of-week patterns |
| **Multivariate Analysis** | Correlation heatmap, Month×DoW revenue heatmap, pairplot, RFM segmentation, Pareto curve |
| **Business Q&A** | 15 targeted business questions answered with charts and quantified insights |

---

## 📦 Dataset

| Attribute | Value |
|-----------|-------|
| **Name** | Online Retail II UCI |
| **Source** | [Kaggle — Online Retail II UCI](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci?select=online_retail_II.csv) |
| **Original Publisher** | UCI Machine Learning Repository |
| **File** | `online_retail_II.csv` |
| **Raw Size** | 1,067,371 rows × 8 columns |
| **Clean Size** | 1,029,601 rows × 56 columns |
| **Period** | December 2009 – December 2011 |
| **Business Type** | UK-based B2B wholesale gift & home-ware retailer |

### Raw Column Schema

| Column | Type (raw) | Description |
|--------|-----------|-------------|
| `Invoice` | string | Invoice number. Prefix `C` = cancellation |
| `StockCode` | string | Unique 5-digit product (SKU) code |
| `Description` | string | Product name (free text, ~4,382 nulls) |
| `Quantity` | int64 | Units per transaction line (negative on returns) |
| `InvoiceDate` | string | Transaction timestamp (stored as string in raw file) |
| `Price` | float64 | Unit price in GBP (£) |
| `Customer ID` | float64 | Customer identifier (float due to ~243k NaN values) |
| `Country` | string | Customer country (43 unique values) |

---

## 📁 Project Structure

```
📦 Online-Retail-II-EDA/
├── 📓 eda.ipynb                        ← Main EDA notebook (151 cells)
├── 📄 requirements.txt                 ← Python dependencies
├── 📝 Project_Report.docx              ← Full written report with charts
├── 📊 online_retail_II.csv             ← Raw dataset (download from Kaggle link above)
├── 📊 online_retail_II_cleaned.csv     ← Intermediate cleaned dataset (auto-generated)
├── 📊 online_retail_II_final.csv       ← Final clean dataset (auto-generated)
└── 📁 report_images/                   ← Exported chart PNGs (auto-generated)
```

> **Note:** The raw CSV file (`online_retail_II.csv`) is not committed to this repository due to its size (~100 MB). Download it from the [Kaggle link](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci?select=online_retail_II.csv) and place it in the project root before running the notebook.

---

## 🔑 Key Findings

| Finding | Value | Source |
|---------|-------|--------|
| Raw dataset size | 1,067,371 rows × 8 cols | Section 2 |
| Duplicate rows removed | 34,335 | Step 3 |
| Guest transactions (no Customer ID) | 243,007 (22.8%) | Step 2 |
| Cancellation rate | 1.9% (19,104 rows) | A4 |
| UK share of transactions | **91.9%** | A2 |
| UK share of revenue | **~86%** | B1 |
| Pearson r (Quantity vs Price) | **−0.315** | B3 |
| Peak revenue month (all years) | **November** | B2, Q1 |
| Peak trading day / hour | **Thursday / 12:00** | Q11 |
| Top 20% customers → revenue | **~73%** | C6 |
| Top 20% SKUs → revenue | **~70%** | Q15 |
| Repeat buyers → revenue | **~88%** | Q6 |
| Netherlands AOV vs UK AOV | **~2×** | Q4 |
| Compound Monthly Growth Rate | **~2.3% / month** | Q12 |
| Quantity Winsorisation cap | **45 units** (IQR ×3) | Step 6 |
| Price Winsorisation cap | **£12.77** (IQR ×3) | Step 6 |
| Final clean dataset | **1,029,601 rows × 56 cols** | Step 10 |

---

## 🔄 EDA Pipeline

### 1 · Data Cleaning

The raw dataset required a **10-step cleaning pipeline** before analysis:

| Step | Operation | Why |
|------|-----------|-----|
| 1 | **Type correction** — `InvoiceDate` → `datetime64`, `Customer ID` → nullable `Int64` | String dates can't be aggregated; float IDs display as decimals |
| 2 | **Missing values** — Description filled `'UNKNOWN'`; Customer ID nulls → sentinel `0` (guest) | Preserves 243k guest-revenue rows rather than discarding them |
| 3 | **Deduplication** — removed 34,335 exact duplicate rows (+ 2 after column drop) | Duplicates inflate all revenue and count metrics |
| 4 | **String normalisation** — whitespace stripped, Country title-cased, Description uppercased | Silent groupby mismatches from `'United Kingdom'` vs `' United Kingdom'` |
| 5 | **Invalid values removed** — negative Quantity on non-cancellation rows, Price ≤ 0 | Data-entry errors that would corrupt revenue calculations |
| 6 | **IQR Winsorisation (k=3)** — Quantity capped at 45, Price capped at £12.77 | Extreme outliers distort means and charts; k=3 retains legitimate bulk orders |
| 7 | **Feature engineering** — Year, Month, DayOfWeek, Hour, Revenue = Qty × Price; OHE Country (43 dummies) | Enables time-series, seasonality, and country-level analysis |
| 8 | **Rename to snake_case** — all 56 columns normalised | Consistent programmatic access; prevents KeyError from capitalisation mismatches |
| 9 | **Drop `description`** — free-text column with no numeric signal | Reduces memory; dropping it exposes 2 additional duplicates |
| 10 | **Final validation** — 10 pass/fail checks | Guarantees pipeline correctness before analysis begins |

---

### 2 · Statistical Analysis

#### 🔹 Univariate (A1–A5)

| Analysis | Key Result |
|----------|-----------|
| **A1** — Numeric distributions | All 3 variables right-skewed; median qty = 3, median price = £2.10 |
| **A2** — Country frequency | UK = **91.9%** of transactions; Germany 2nd at ~1.6% |
| **A3** — Time distributions | Peak month = **November**; peak day = **Thursday**; peak hour = **12:00** |
| **A4** — Cancellation rate | **1.9%** overall; concentrated in specific countries and SKUs |
| **A5** — Top products by frequency | Dominated by novelty/gift items (T-light holders, cake stands) |

#### 🔹 Bivariate (B1–B6)

| Analysis | Key Result |
|----------|-----------|
| **B1** — Revenue by country | UK = ~**86%** of total revenue; Netherlands highest international AOV |
| **B2** — Monthly revenue trend | Consistent seasonal ramp every year; **November 2011** = single highest month |
| **B3** — Qty vs Price scatter | Pearson r = **−0.315** — higher price items ordered in smaller quantities |
| **B4** — Revenue by day of week | **Thursday** highest; **Sunday** near-zero — confirms B2B wholesale pattern |
| **B5** — Cancellation by country | **France & EIRE** > 3% (vs UK 1.8%); concentrated in key growth markets |
| **B6** — Top products by revenue | Different SKU set from A5 — volume ≠ value |

#### 🔹 Multivariate (C1–C6)

| Analysis | Key Result |
|----------|-----------|
| **C1** — Correlation heatmap | revenue–quantity r = +0.79; time features near-zero correlation with revenue |
| **C2** — Month × DoW heatmap | **November Thursday** = single highest-revenue combination |
| **C3** — Pairplot | Quantity drives revenue more than price; right-skewed marginals |
| **C4** — Country × Year | Germany & Netherlands show strongest international YoY growth |
| **C5** — RFM segmentation | Champions = smallest segment, largest revenue share |
| **C6** — Customer Pareto | Top 20% customers → **73%** of revenue; top 5% → **~45%** |

---

### 3 · Business Questions

15 business questions, each with a chart and quantified answer:

| # | Domain | Question |
|---|--------|----------|
| Q1 | Revenue | Which months consistently generate the highest revenue? |
| Q2 | Revenue | UK vs International split — is the UK share growing or shrinking YoY? |
| Q3 | Product | Top-10 SKUs: do the highest-frequency products overlap with highest-revenue ones? |
| Q4 | Market | Average Order Value by country — which international markets beat the UK? |
| Q5 | Customer | RFM segment revenue share — how much does each segment contribute? |
| Q6 | Customer | Repeat vs one-time buyers — what share of revenue comes from each? |
| Q7 | Customer | Top 20 customers by lifetime revenue — are they still active? |
| Q8 | Customer | Guest vs identified customers — revenue share and basket size difference |
| Q9 | Operations | Cancellation rate vs AOV by country — where is revenue risk highest? |
| Q10 | Operations | Monthly cancellation trend 2009–2011 — rising or stable? |
| Q11 | Operations | Peak trading hours × day of week — when is the business busiest? |
| Q12 | Growth | YoY growth in customers, invoices, and revenue — what is the CMGR? |
| Q13 | Pricing | Price band analysis — how is revenue distributed across tiers? |
| Q14 | Product Quality | Most-cancelled products — are high-return SKUs concentrated? |
| Q15 | Product | Product Pareto — do the top 20% of SKUs generate 80% of revenue? |

---

## 🛠 Technologies Used

| Library | Version | Role |
|---------|---------|------|
| `pandas` | ≥ 3.0.3 | DataFrames, groupby, cleaning, RFM |
| `numpy` | ≥ 2.4.6 | Numeric arrays, IQR computation, correlation |
| `matplotlib` | ≥ 3.11.0 | All charts (bar, scatter, pie, line, heatmap) |
| `seaborn` | ≥ 0.13.2 | Pairplot, heatmap styling |
| `jupyter` | ≥ 1.0.0 | Notebook runtime meta-package |
| `notebook` | ≥ 7.0.0 | Jupyter Notebook server |

---

## 🚀 Getting Started

### Prerequisites
- Python 3.10+
- pip

### 1 · Clone the repository

```bash
git clone https://github.com/<your-username>/online-retail-ii-eda.git
cd online-retail-ii-eda
```

### 2 · Install dependencies

```bash
pip install -r requirements.txt
```

### 3 · Download the dataset

Download `online_retail_II.csv` from Kaggle:

> 🔗 [https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci?select=online_retail_II.csv](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci?select=online_retail_II.csv)

Place the file in the **project root directory** (same folder as `eda.ipynb`).

### 4 · Run the notebook

```bash
jupyter notebook eda.ipynb
```

Then select **Kernel → Restart & Run All** to execute the full pipeline from scratch.

The notebook will automatically generate:
- `online_retail_II_cleaned.csv` — intermediate cleaned dataset
- `online_retail_II_final.csv` — final analysis-ready dataset

> ⏱ **Expected runtime:** ~3–5 minutes on a modern laptop (pairplot is the slowest cell at ~60 seconds).

---

## ⚠️ Risks & Recommendations

Seven data-grounded risks and their recommended responses identified through the EDA:

| # | Risk | Severity | Recommendation |
|---|------|----------|----------------|
| R1 | **Geographic concentration** — UK = 86% of revenue | 🔴 HIGH | International diversification programme; target Germany & Netherlands (highest AOV & growth) |
| R2 | **Customer concentration** — top 5% = ~45% of revenue | 🔴 HIGH | Tiered KAM programme (Platinum/Gold/Silver); monthly RFM alerts; immediate outreach to lapsing top-20 accounts |
| R3 | **Seasonal volatility** — Q4 = 40% of annual revenue, Nov/Jan ratio = 3–4× | 🟠 MEDIUM-HIGH | Seasonal operations calendar; counter-seasonal product line; Q1 wholesale incentive schemes |
| R4 | **Elevated cancellations** — France & EIRE >3% (vs 1.9% average) | 🟡 MEDIUM | SKU audit for >10% cancel-rate products; post-cancellation survey; market-level root-cause analysis |
| R5 | **Portfolio long-tail** — bottom 50% SKUs = <10% of revenue | 🟡 MEDIUM | 3-tier SKU classification; sunset rule for low performers; pricing experiments on high-freq/low-rev SKUs |
| R6 | **Guest data blindspot** — 22.77% of transactions unidentified | 🟡 MEDIUM | Incentivised account registration; email capture at checkout; recurring guest detection |
| R7 | **Single trading window** — 30 hrs/week, peak Thu 12:00 | 🟢 MEDIUM-LOW | Email timing Tue/Thu 08:30; weekend maintenance windows; Thu noon capacity provisioning |

---

## 📄 Project Report

A full written report (`Project_Report.docx`) accompanies this project, containing:

- **Section 1** — Introduction & Dataset Description
- **Section 2** — Problem Statements (8 analytical problems)
- **Section 3** — Raw Dataset Overview (schema, missing values, descriptive stats)
- **Section 4** — Data Cleaning Operations (all 10 steps with rationale)
- **Section 5** — Statistical Analysis (A1–A5, B1–B6, C1–C6) with embedded charts
- **Section 6** — Business Questions & Answers (Q1–Q15) with embedded charts
- **Section 7** — Risks & Recommendations (7 risks, 7 recommendations, priority matrix)

---

## 📜 License

This project is released under the [MIT License](LICENSE).

The **Online Retail II** dataset is published by the UCI Machine Learning Repository and made available on Kaggle under its respective terms:
> 🔗 [https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci?select=online_retail_II.csv](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci?select=online_retail_II.csv)

---

<div align="center">

Made with ❤️ using Python · pandas · matplotlib · seaborn

</div>
