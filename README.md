# Aadhaar-Enrolment-Update-Analysis-UIDAI-Hackathon-2026
# 🪪 Aadhaar Enrolment & Update Pattern Analysis (UIDAI Data Hackathon 2026)
A complete end-to-end data analytics project uncovering lifecycle-driven operational stress, regional concentration patterns, and evidence-based insights to support Aadhaar infrastructure planning.

📊 **Core Methods:** SARIMAX · K-Means Clustering · Pareto Analysis  
🏆 **Custom KPI:** Biometric Update Ratio (BUR)  
📁 **Dataset Size:** ~4.9 Million Records  
🗓️ **Data Range:** March 2025 – January 2026

---

## 📌 Problem Statement

Aadhaar has achieved near-universal coverage. The hard part now isn't enrolling people — it's **maintaining** their identity over time.

The goal of this project is to classify and quantify how Aadhaar's operational load has shifted from new enrolments to post-enrolment updates, and to surface **where, why, and how** infrastructure stress is concentrated — using:

- 📝 Enrolment records (age-wise, PIN-code level, daily frequency)
- 📊 Demographic and biometric update records
- 🗺️ State and district-level spatial data

### ⚠️ Challenge:
- Infrastructure designed for **enrolment** now runs on **maintenance-dominated** demand
- Extreme **regional concentration** — few states drive the majority of the national load
- **Hidden stress zones** completely invisible through raw volume metrics alone
- Sharp **lifecycle asymmetry** between age cohorts (0–5 vs 18+)
- Evaluation: **Policy impact and operational insight**, not just model performance

---

## 📂 Dataset

> ⚠️ Raw datasets are not included in this repository — they are the property of UIDAI, provided exclusively for hackathon participants.

| Dataset | Records | CSV Segments | Key Columns |
|---|---|---|---|
| Enrolment | 1,006,029 | 3 | `age_0_5`, `age_5_17`, `age_18_greater` |
| Demographic Updates | 2,071,700 | 5 | `demo_age_5_17`, `demo_age_17_` |
| Biometric Updates | 1,861,108 | 4 | `bio_age_5_17`, `bio_age_17_` |
| **Total** | **~4.9 Million** | **12** | |

---

## ⚙️ Tech Stack

- Python 🐍
- `pandas`, `numpy`
- `scikit-learn` — K-Means Clustering
- `statsmodels` — SARIMAX time-series forecasting
- `matplotlib`, `seaborn`
- `geopandas`, `shapely`, `fiona` — choropleth mapping

---

## 🔍 Key Steps

### 1. 📦 Data Loading & Cleaning

- Concatenated **12 CSV segments** into 3 unified dataframes using `pd.concat()`
- Age columns cast to `int64` via `pd.to_numeric(errors='coerce')`
- Dates standardized using `pd.to_datetime(dayfirst=True)`
- State and district names stripped of whitespace inconsistencies
- Junk numeric rows filtered using `~str.isnumeric()`
- **Zero missing values** confirmed post-integration — no imputation required
- Anomalies retained with flags rather than dropped (e.g. J&K zero-update, September 2025 data outage)

---

### 2. 🧹 Feature Engineering

#### 📐 Derived Metrics
- `enrolment_total` = `age_0_5 + age_5_17 + age_18_greater`
- `biometric_total` = `bio_age_5_17 + bio_age_17_`
- `demographic_total` = `demo_age_5_17 + demo_age_17_`
- `total_updates` = `demographic_total + biometric_total`

#### 🎯 Custom KPIs
- **Biometric Update Ratio (BUR):** `total_bio_updates ÷ total_enrolment` — surfaces hidden stress zones invisible in volume metrics
- **Update-to-Enrolment Ratio:** monthly, state-level, used for heatmap analysis

#### 🕒 Temporal Features
- `year_month` period column for monthly aggregation
- **3-month Rolling Mean** to smooth daily volatility in trend plots

---

### 3. 📊 Exploratory Data Analysis (EDA)

- **Lifecycle Analysis** — Age-group breakdown of enrolment vs demographic vs biometric activity (stacked bar)
- **State-Level Rankings** — Separate Top 10 rankings for enrolment, demographic updates, and biometric updates
- **Activity Intensity Heatmap** — All 3 activity types mapped across all states using `YlOrRd` palette
- **Choropleth Map** — State-wise biometric volumes overlaid on Indian GeoJSON via `geopandas`
- **Quadrant Analysis** — Demographic vs Biometric scatter with mean-based quadrant lines and top-state annotations
- **Stress Scatter** — Enrolment volume vs BUR to surface high-maintenance, low-enrolment anomaly states
- **Temporal Trend** — Monthly national updates with 3-month rolling mean overlay
- **Update-to-Enrolment Heatmap** — Monthly ratio pivoted by state, plotted with `YlGnBu`

---

## 🤖 Methods Used

| Method | Purpose | Key Output |
|---|---|---|
| **K-Means Clustering (k=3)** | State segmentation by operational load | 3-tier governance model |
| **Elbow Method (k=2–7)** | Optimal cluster count selection | k=3 confirmed |
| **SARIMAX (1,1,1)×(1,1,1,12)** | National biometric demand forecasting | 6-month projection |
| **Pareto Analysis** | Workload concentration mapping | Top 6–7 states = ~75% of load |
| **Correlation Matrix** | Inter-service lifecycle linkage | Correlations: 0.76–0.95 |
| **ADF Test** | Stationarity check before SARIMAX | Stationarity validated |

---

## 🗺️ Best Output: K-Means Cluster Model

### Why clustering worked here:
- States operate under fundamentally **different operational realities** — one model can't serve all
- Manual z-score normalization ensured no single feature dominated distance calculations
- Elbow method cleanly identified **k=3** as the governance-optimal split

| Cluster | States | Biometric Load | What They Need |
|---|---|---|---|
| 🔴 **Engines** | Maharashtra, Uttar Pradesh | ~10–11M each | Surge capacity, automated queues, extended hours |
| 🟠 **Core** | Bihar, WB, MP, Rajasthan, TN, Gujarat | 3–5M each | Steady-state high-volume processing |
| 🟡 **Periphery** | All remaining states & UTs | Low | Geographic access assurance, not capacity scaling |

---

## 📈 Key Insights

- 📌 **0–5 age group** = 65.25% of enrolments but generates **zero** biometric or demographic updates
- 📌 **18+ adults** = just 3.1% of enrolments but drive **66.6%** of all biometric updates — the real operational load
- 📌 **Maharashtra** tops biometric updates at **11.4M** — Uttar Pradesh follows at **6.7M**
- 📌 **Andhra Pradesh BUR: 29.09** — 29 biometric updates processed per 1 new enrolment (highest stress ratio nationally)
- 📌 Enrolment and demographic updates are nearly perfectly correlated (**r = 0.95**) — updates are a continuation of enrolment, not a separate process
- 📌 **SARIMAX forecast:** Monthly load set to rise from ~8.8M → **14–15M updates by mid-2026** — a ~50% surge in baseline demand

---

## ⚠️ Challenges

- Lifecycle inversion (enrolment-heavy early years, update-heavy adulthood) not surfaced by standard enrolment-centric metrics
- Hidden stress zones in smaller states completely masked by national-level aggregation
- September 2025 near-zero data gap required flagging and careful handling during SARIMAX fitting — treated as outlier, not a demand drop
- GeoJSON state names required manual mapping to match UIDAI naming conventions (e.g. `"Orissa"` → `"Odisha"`)
- NaN values emerging after z-score scaling (zero-variance columns) had to be caught and filled before KMeans would run
- Validation-to-forecast performance gap due to limited historical data points for seasonal SARIMAX estimation

---

## 🚀 Future Improvements

- 🔥 District-level clustering using raw PIN-code granularity
- 🔗 Integration with census projections for multi-year demand forecasting
- 🎯 Real-time BUR monitoring dashboard for operational teams
- 🔁 Automated anomaly detection pipeline using rolling z-scores
- 🏛️ Deep-dive on J&K zero-update anomaly — reporting failure vs genuine service inaccessibility

---

*Submitted for UIDAI Data Hackathon 2026 — Data-Driven Innovation for Strengthening the Aadhaar Ecosystem.*
