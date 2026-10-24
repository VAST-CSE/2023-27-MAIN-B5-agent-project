 Autonomous Data Scientist with DataSentinel AI

An autonomous multi-agent data science pipeline with a built-in data quality and anomaly engine.

The system takes a raw CSV dataset and runs the full workflow without manual steps: it inspects the data, audits its quality, cleans it, runs exploratory analysis, selects features, trains and compares ML models, and explains the results in plain English. A central **Orchestrator Agent** coordinates every phase. **DataSentinel AI**, the quality and anomaly engine, produces a **Quality Score out of 100** and decides what needs fixing before any model is trained.

> Project status:The web interface (login, dashboard, inspector, quality workspace, report builder) is implemented.

Final-year B.Tech CSE project, Vidya Academy of Science and Technology, Thrissur (APJ Abdul Kalam Technological University).

 Table of contents

1. [Features](#features)
2. [How it works](#how-it-works)
3. [System architecture](#system-architecture)
4. [Project structure](#project-structure)
5. [Getting started](#getting-started)
6. [Configuration](#configuration)
7. [Output and metric reference](#output-and-metric-reference)
8. [Sample results](#sample-results)
9. [Design decisions and known limitations](#design-decisions-and-known-limitations)
10. [Troubleshooting](#troubleshooting)
11. [Roadmap](#roadmap)
12. [Ethical use and data privacy](#ethical-use-and-data-privacy)
13. [Team](#team)

Features

- **Schema-free ingestion:** upload any CSV through the web interface. The Inspector Agent validates the file type, size and path, reads it with no prior schema, profiles rows, columns and data types, and proposes likely target columns.
- **DataSentinel AI quality engine:** detects missing values, duplicate rows, constant columns, impossible range values, outliers and class imbalance. Outliers are found with three independent detectors (IQR, Z-score and Isolation Forest).
- **One transparent Quality Score:** `100 - weighted penalties`, with every penalty shown, so the score can be explained and audited.
- **Ground-truth benchmarking:** DataSentinel is tested against a dataset with deliberately injected errors and reports precision and recall for each error type.
- **Automated cleaning with an audit log:** imputation, encoding, scaling, deduplication and outlier capping, with every transformation recorded.
- **Automated EDA:** descriptive statistics, histograms, box plots, count plots, correlation matrix, class distribution and auto-generated text observations.
- **Feature engineering:** correlation filtering, SelectKBest, mutual information and tree-based importance.
- **AutoML agent:** detects classification or regression and trains several candidate models with cross-validation to avoid overfitting.
- **LLM explanations and reporting:** metrics and quality reports are turned into plain-English findings, with a model leaderboard, alerts and a downloadable report.
- **Web dashboard:** login, dashboard overview, dataset inspector, DataSentinel quality workspace and executive report builder.

How it works

Every phase receives plain data from the previous one, and the Orchestrator Agent controls execution order and state.

| Phase | Agent | Input | Output |
|------|-------|-------|--------|
| 1 | Dataset Upload and Inspector | Raw CSV uploaded via the web interface | Schema summary, column types, dimensions, target options |
| 2 | DataSentinel AI | Profiled dataset | Quality score, anomaly list, severity alerts |
| 3 | Preprocessing and Cleaning | Raw dataset and DataSentinel issue report | Cleaned dataset and transformation audit log |
| 4 | Automated EDA | Cleaned dataset | Statistical summaries, charts, textual insights |
| 5 | Feature Engineering | Cleaned dataset and target variable | Refined subset of predictive features |
| 6 | AutoML Agent | Engineered features and target | Trained models, hyperparameters, raw performance metrics |
| 7 | Evaluation, LLM Explanation and Reporting | Metrics, quality reports, training summaries | Model leaderboard, AI explanations, alerts, downloadable report |

Phase details

1. **Inspector:** validates file type, size and path; profiles rows, columns and dtypes; detects target candidates.
2. **DataSentinel AI:** checks missing, duplicate and constant columns; detects outliers (IQR, Z-score, Isolation Forest); runs range, consistency and imbalance checks; computes the Quality Score.
3. **Preprocessing:** imputes numeric values (median/mean) and categorical values (mode); one-hot or ordinal encoding and scaling; drops duplicates; caps or removes outliers; logs every step.
4. **EDA:** descriptive statistics for all columns; histograms, box plots, count plots; correlation matrix and class distribution; auto-generated observations.
5. **Feature engineering:** correlation filtering and SelectKBest; mutual information scores; tree-based feature importance; drops redundant and constant columns.
6. **AutoML:** detects classification or regression; trains Logistic Regression, Decision Tree, Random Forest, SVM and Gradient Boosting (or the regression equivalents); uses cross-validation.
7. **Evaluation and reporting:** classification metrics (accuracy, precision, recall, F1, ROC-AUC, confusion matrix) or regression metrics (MAE, MSE, RMSE, R2); the LLM turns the findings into plain English.

System architecture

```
User / Client Interface    (Tailwind CSS dashboard)
            |  HTTP / REST API
FastAPI Backend Server     (request handler and session controller)
            |
Orchestrator Agent         (workflow state machine and agent execution controller)
            |
Multi-Agent Pipeline Engine
  1. Dataset Inspector -> 2. DataSentinel AI -> 3. Preprocessing
                                                      |
  4. EDA Agent  <-  5. Feature Engineering  <-  6. ML Agent
        |
7. Evaluation and Report Agent  ->  Database and Storage
```

Project structure

> Replace this with your real folder layout.

```
.
├── README.md
├── requirements.txt
├── screenshots/
│   ├── 1_login_page.png
│   ├── 2_dashboard_agent_pipeline.png
│   ├── 3_datasentinel_quality.png
│   └── 4_executive_report.png
├── login.html
├── dashboard.html
├── dashboard2.html
├── inspector.html
└── [backend files, for example main.py, agents/, data/]
```

Getting started

 Prerequisites

- Python [3.9 or newer]
- A modern web browser (Chrome, Edge or Firefox)
- [LLM API key, if the explanation phase uses one]
- [PostgreSQL / MongoDB, if used]

1. Clone the repository

```bash
git clone https://github.com/[your-username]/[repo-name].git
cd [repo-name]
```

2. Create a virtual environment and install dependencies

```bash
python -m venv .venv

# Linux / macOS
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

pip install -r requirements.txt
```

3. Start the backend

```bash
uvicorn main:app --reload
```

4. Open the web interface

Open `login.html` in your browser (or visit `http://localhost:[port]` if the backend serves the pages).

**Demo login (for local demonstration only):**

| Field | Value |
|-------|-------|
| Email | `demo@datasentinel.ai` |
| Password | `sentinel2026` |

Stop the backend with `Ctrl+C`.

Configuration

Detection thresholds and model settings used by the project:

| Setting | Value | Used by |
|---------|-------|---------|
| IQR multiplier | 1.5x | IQR Bounds Checker |
| Z-score threshold | `abs(z) > 3.0` | Z-Score Normal Validator |
| Isolation Forest contamination | 0.05 | Isolation Forest Anomaly Engine |
| Cross-validation folds | 5 | AutoML Agent |
| Numeric imputation | median / mean | Preprocessing |
| Categorical imputation | mode | Preprocessing |

[Add where these live, for example a `config.yaml` or constants in `config.py`.]

**Secrets:** never commit API keys, database passwords or tokens. Keep them in a `.env` file and make sure `.env` is listed in `.gitignore`.

Output and metric reference

Data quality score

```
Quality Score = 100 - Penalties [Missingness, Duplicates, Range Errors, Outliers]
```

| Penalty | What it measures |
|---------|------------------|
| Missingness | Share of missing values across the dataset |
| Duplicates | Exact duplicate rows |
| Range errors | Impossible values (for example negative values or percentages over 100) |
| Outliers | Extreme values flagged by the anomaly detectors |

Example from the demo dataset: `100 - [Missingness 17, Duplicates 12, Range Errors 8, Outliers 5] = 58 / 100 (Moderate Risk)`.

Anomaly detectors

| Detector | Method | Setting |
|----------|--------|---------|
| Isolation Forest | Multi-dimensional isolation-tree density estimation | contamination 0.05 |
| IQR Bounds Checker | Interquartile-distance outlier bounds | 1.5x threshold |
| Z-Score Validator | Standard deviations from the mean | abs(z) > 3.0 |

AutoML models

| Task | Models |
|------|--------|
| Classification | Logistic Regression, Decision Tree, Random Forest, SVM, Gradient Boosting |
| Regression | Linear Regression, Decision Tree, Random Forest, Gradient Boosting |

Evaluation metrics

| Task | Metrics |
|------|---------|
| Classification | Accuracy, Precision, Recall, F1, ROC-AUC, Confusion Matrix |
| Regression | MAE, MSE, RMSE, R2 |

Executive report

The report builder produces a printable or PDF report with the dataset name, quality score, quality inspection summary, AutoML training results (best model and metrics) and a verification signature.

Sample results

Tested on a deliberately corrupted *Student Performance* dataset (1,000 rows) with injected errors:

| Injected issue | Injected ground truth | DataSentinel detected | Precision / Recall | Remediation |
|----------------|----------------------|-----------------------|--------------------|-------------|
| Impossible range errors | 6 negative / over-100 values | 6 flags | 100% | Capped to valid range |
| Exact row duplicates | 12 duplicate copies | 12 rows identified | 100% | Deduplicated |
| Synthetic outliers | 9 extreme spikes | 9 IQR outliers | 100% | Winsorized |

- **Data Quality Score:** 58 / 100 (Moderate Risk)
- **Best model:** Random Forest Classifier, evaluated with 5-fold cross-validation
- **Performance:** Accuracy 88.5%, F1-score 0.892, Precision 0.887, Recall 0.897

Design decisions and known limitations

- **Transparent scoring.** The Quality Score is a simple weighted-penalty formula rather than a black box, so every point lost can be traced to a specific issue.
- **Multiple outlier detectors.** IQR, Z-score and Isolation Forest are combined because each catches different kinds of anomalies.
- **Benchmarked on injected errors.** The 100% precision and recall figures come from a dataset where the errors were added on purpose. Real-world data will not give such clean results.
- **Tabular CSV only.** Images, text corpora and time-series-specific workflows are not supported.
- **Target detection is a suggestion.** The Inspector proposes likely target columns, and a human should confirm them for important work.
- **LLM explanations depend on the model used.** Always check generated explanations against the numeric metrics.
- [Add your own limitations, for example dataset size limits, supported file encodings, or missing database persistence.]

Troubleshooting

| Symptom | Likely cause and fix |
|---------|----------------------|
| `ModuleNotFoundError` | The virtual environment is not active or dependencies are not installed. Run `pip install -r requirements.txt`. |
| Login page opens but nothing loads after sign-in | The backend is not running, or the API URL in the frontend does not match the backend port. |
| CSV upload is rejected | Check the file type (`.csv`), file size and encoding (UTF-8 is safest). |
| Charts are blank | Open the browser console for errors, and check that any CDN scripts (for example chart libraries) can load. |
| LLM explanation step fails | The API key is missing or invalid. Check your `.env` file. |

Roadmap

- [x] Web interface: login, dashboard, dataset inspector, quality workspace, report builder
- [x] DataSentinel quality score with IQR, Z-score and Isolation Forest detectors
- [x] AutoML model comparison with cross-validation
- [x] Executive report with PDF export
- [ ] [Persistent storage of runs and reports in a database]
- [ ] [Data drift monitoring and alert log]
- [ ] [Support for larger datasets and more file formats]
- [ ] [Automated unit tests and CI]

Ethical use and data privacy

Only upload datasets you own or are permitted to analyse. Datasets may contain personal or sensitive information, so remove or anonymise identifiers before uploading. If an external LLM API is used for explanations, summaries of your data may be sent to that provider, so avoid uploading confidential data. The demo credentials in this repository are for local demonstration only and must not be used in any real deployment.

 Team

| Name |
|------|
| [Team member] | 
| [Team member] | 
| [Team member] | 
| [Team member] |

**Guide:** Asst. Prof. Aysha, Department of Computer Science and Engineering, Vidya Academy of Science and Technology, Thrissur
