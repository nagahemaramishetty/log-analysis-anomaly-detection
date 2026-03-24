# Large-Scale Log Analysis & Anomaly Detection

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![PySpark](https://img.shields.io/badge/PySpark-3.x-orange?logo=apachespark)
![Sklearn](https://img.shields.io/badge/Scikit--Learn-IsolationForest-green?logo=scikit-learn)
![ARIMA](https://img.shields.io/badge/Statsmodels-ARIMA-purple)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Project Overview

End-to-end log analysis and anomaly detection pipeline built on **1.88 million real-world 
NASA HTTP server log records**. The pipeline ingests raw unstructured server logs, parses 
them using Regex into structured event tables, performs exploratory data analysis to 
establish traffic baselines, and applies a **hybrid anomaly detection system** combining 
Isolation Forest (point anomalies) and ARIMA (temporal pattern deviations) — simulating 
production-scale observability pipelines used at AWS, Google SRE, and Datadog.

---

## Dashboard Preview

![Anomaly Detection Dashboard](dashboard_preview.png)

---

## Business Problem

Legacy monitoring systems rely on **static threshold-based alerting** — firing alerts 
whenever a metric exceeds a fixed value. This approach generates high false alert rates, 
causes alert fatigue, and misses contextual anomalies that are unusual relative to time-of-day 
patterns but do not breach absolute thresholds.

This project replaces static thresholds with a hybrid ML-based detection system that reduces 
false alert rate by **39%** while maintaining high precision on real anomaly events.

---

## Technical Architecture
```
Raw NASA HTTP Logs (.gz)
        |
        v
[Step 1] Data Ingestion — wget + gunzip + Python file I/O
        |
        v
[Step 2] Regex Parsing — Named capture groups, CLF format structurization
        |
        v
[Step 3] EDA — Status distribution, endpoint analysis, temporal baselines
        |
        v
[Step 4] Isolation Forest — Point anomaly detection on hourly windows
        |
        v
[Step 5] ARIMA(2,1,2) — Temporal pattern deviation on daily windows
        |
        v
[Step 6] Unified Anomaly Report — Model fusion + MTTD benchmarking
        |
        v
[Step 7] Multi-Panel Monitoring Dashboard — Production-style visualization
```

---

## Key Results

| Metric | Value |
|---|---|
| Total log records processed | 1,888,739 |
| Parse accuracy (Regex CLF) | 99.84% |
| Hourly windows analyzed | 662 |
| Isolation Forest anomalies detected | 14 (2.11% rate) |
| ARIMA anomalous days flagged | 1 |
| Static threshold alerts fired | 23 |
| False alert reduction vs threshold | 39% |
| Top anomaly — Jul 13 09:00 | 14,925 requests/hour |
| ARIMA model AIC | 628.974 |
| ARIMA model BIC | 635.453 |

---

## Anomaly Detection Methodology

### Isolation Forest (Point Anomaly Detection)
- Algorithm builds an ensemble of **100 random isolation trees**
- Each data point scored by number of splits required to isolate it
- Points isolated in fewer splits receive higher anomaly scores
- **Contamination parameter:** 0.02 (2% expected anomaly rate)
- **Feature matrix:** request_count, hour_of_day, day_of_week, day_of_month
- Temporal context features prevent false positives on normal peak-hour traffic

### ARIMA(2,1,2) (Temporal Pattern Deviation Detection)
- **Augmented Dickey-Fuller test** confirmed non-stationarity (p=0.9842) — d=1 differencing applied
- AR order p=2 — past 2 days used as autoregressive predictors
- MA order q=2 — past 2 forecast errors incorporated
- Anomaly threshold: residuals exceeding **2 standard deviations** from mean residual
- Both AR coefficients statistically significant (p<0.001)

### Why Hybrid Approach
Isolation Forest detects **hourly point anomalies** — sudden spikes in a single time window.
ARIMA detects **daily pattern deviations** — days that break the expected autocorrelated trend.
These are fundamentally different anomaly types requiring different detection strategies.
The July 13 traffic surge was confirmed by both models independently, 
validating the hybrid architecture.

---

## Tech Stack

| Category | Tools |
|---|---|
| Data Ingestion | Python, wget, gunzip |
| Parsing | Python (Regex, Pandas) |
| Big Data Processing | PySpark |
| Statistical Testing | Statsmodels (ADF test) |
| Anomaly Detection | Scikit-learn (Isolation Forest) |
| Time Series Modeling | Statsmodels (ARIMA) |
| Visualization | Matplotlib, Seaborn |
| Environment | Google Colab |
| Version Control | Git, GitHub |

---

## Project Structure
```
log-analysis-anomaly-detection/
│
├── Log_Analysis_Anomaly_Detection.ipynb   # Main notebook — all 7 steps
├── dashboard_preview.png                  # Final monitoring dashboard
└── README.md                              # Project documentation
```

---

## How to Run

1. Open `Log_Analysis_Anomaly_Detection.ipynb` in Google Colab
2. Click **Runtime → Run All**
3. All data is downloaded automatically — no local files required
4. Total runtime: approximately 3-5 minutes

---

## Interview Talking Points

- **Why Isolation Forest over DBSCAN or LOF?**
Isolation Forest scales to large datasets with O(n log n) complexity and does not require 
distance computations, making it suitable for 1M+ record log pipelines.

- **Why ARIMA over Prophet for this dataset?**
The dataset spans only 28 days — insufficient for Prophet's seasonal decomposition. 
ARIMA(2,1,2) is more appropriate for short time series with strong autocorrelation.

- **Why is the contamination parameter set to 0.02?**
Server log anomaly rates in production environments typically range 1-5%. 
0.02 represents a conservative estimate that balances precision and recall 
without over-flagging normal traffic variation.

- **What does the ADF test result tell us?**
p-value of 0.9842 confirms the daily series is non-stationary — meaning the mean 
and variance change over time. This mandates d=1 differencing in ARIMA to achieve 
stationarity before modeling.

---

## Connect

[![LinkedIn](https://img.shields.io/badge/LinkedIn-nagaramishetty-blue?logo=linkedin)](https://linkedin.com/in/nagaramishetty)
[![Email](https://img.shields.io/badge/Email-nagahemaramishetty2@gmail.com-red?logo=gmail)](mailto:nagahemaramishetty2@gmail.com)
```
