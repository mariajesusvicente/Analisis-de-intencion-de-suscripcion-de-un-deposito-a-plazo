# 🏦 Bank Term Deposit Subscription Prediction

Predicting whether a bank client will subscribe to a term deposit **before the call is made**, using statistical analysis and machine learning on real direct-marketing campaign data.

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikitlearn&logoColor=white)
![LightGBM](https://img.shields.io/badge/LightGBM-2C8EBB)
![XGBoost](https://img.shields.io/badge/XGBoost-EB5E28)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?logo=jupyter&logoColor=white)

---

## 📌 Overview

A Portuguese bank runs phone campaigns to sell term deposits, but only **11.7%** of the clients it contacts end up subscribing. Calling everyone costs time and money.

This project answers one question: **which clients are worth calling?**

The project covers the full machine learning workflow, from raw data to an interpretable model:

- **Exploratory analysis**: understand who subscribes and why, backed by statistical tests
- **Missing-data diagnosis**: formally classify the "Unknown" values (MCAR vs MAR vs MNAR)
- **Leakage control**: use only information available *before* the client is contacted
- **Model comparison**: benchmark 6 classifiers with nested cross-validation
- **Interpretation**: find which client traits drive subscription and choose an operating threshold

---

## 📊 Dataset

**Bank Marketing (bank-full)** from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/222/bank+marketing)

| | |
|---|---|
| Records | 45,211 clients |
| Features | 17 (socio-demographic, financial and campaign-related) |
| Target | `deposito_a_plazo`: did the client subscribe? (Yes / No) |
| Class balance | 39,922 No · 5,289 Yes (**11.7% positive**) |

> Variables were translated and recoded into Spanish (e.g. age grouped into life-stage segments).

---

## 🔬 Methodology

```
Raw data (UCI bank-full)
        │
        ▼
┌──────────────────────────┐
│  EDA & statistical tests │  ← Chi-square, t-test, Mann-Whitney, Cohen's d
│                          │     Outliers, correlations, client profiling
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────────┐
│  Missing-data analysis   │  ← "Unknown" values tested as MAR / MNAR
│                          │     → kept as their own category
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────────┐
│  Leakage removal         │  ← Drop call duration & contact-date features
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────────┐
│  scikit-learn Pipeline   │  ← Imputation + RobustScaler + OneHotEncoder
│                          │     (fitted inside each fold)
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────────┐
│  Nested Cross-Validation │  ← Outer: 5-fold · Inner: 3-fold GridSearch
│                          │     Metric: AUC-PR (average precision)
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────────┐
│  Interpretation          │  ← Feature importance + F1-optimal threshold
└──────────────────────────┘
```

### 1. Exploratory Data Analysis
- Univariate, bivariate and multivariate analysis of every variable
- Association with the target measured with **Chi-square** (categorical) and **t-test / Mann-Whitney U + Cohen's d** (numerical)
- Correlation matrices for numerical and categorical variables (Cramér's V)
- Socio-demographic profile of the clients who subscribe

### 2. Missing data: MAR vs MNAR
Several variables contain an "Unknown" category (`ocupacion`, `educacion`, `contacto`, `resultado_campaña_anterior`). Chi-square tests showed that the share of "Unknown" values changes with the target and with age, which **rules out MCAR**. Using balance as a socio-economic proxy also pointed to **MNAR** for education and occupation. So "Unknown" was kept as its own category rather than imputed.

### 3. Avoiding data leakage
`duracion_llamada` (call duration) is the strongest predictor in the EDA (Cohen's d = 1.34), but it is **only known after the call ends**. It was dropped along with the contact date and channel, so the model can be used to decide *who to call*.

### 4. Nested cross-validation
Hyperparameters are tuned in the inner loop and performance is measured in the outer loop, so the reported scores are **unbiased estimates** of real-world performance. Models are optimized for **AUC-PR**, because with an 11.7% positive class, accuracy is misleading (a model that always says "No" scores 88%).

---

## 🏆 Results

**Nested CV, AUC-PR (5 outer folds)**

| Rank | Model | AUC-PR (mean ± SD) | 95% CI |
|:---:|---|:---:|:---:|
| 🥇 | **LightGBM** | **0.371 ± 0.019** | [0.348, 0.395] |
| 🥈 | Logistic Regression | 0.366 ± 0.019 | [0.343, 0.389] |
| 🥉 | K-Nearest Neighbors | 0.328 ± 0.016 | [0.308, 0.348] |
| 4 | Decision Tree | 0.315 ± 0.020 | [0.289, 0.340] |
| — | *Random baseline* | *0.117* | — |

Naive Bayes and XGBoost were also evaluated in an earlier round (XGBoost reached an AUC-ROC of 0.74, the same as LightGBM).

**Key takeaways**

- ✅ The best model is about **3× better than random** at finding subscribers, using only pre-call information
- ⚖️ LightGBM and Logistic Regression perform **almost the same** (their confidence intervals overlap), so Logistic Regression is a strong choice when interpretability matters
- 🎯 At the **F1-optimal threshold (0.18)**, Logistic Regression reaches Precision 0.38 · Recall 0.40 · F1 0.39

**Top drivers of subscription (Logistic Regression, grouped importance)**

1. **Age segment**: young adults and retirees subscribe much more
2. **Outcome of the previous campaign**: a past success is the single strongest signal
3. **Occupation**
4. **Contact in a previous campaign**
5. **Mortgage** (clients with a mortgage are less likely to subscribe)

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Data handling | pandas, NumPy |
| Statistics | SciPy, statsmodels |
| Machine Learning | scikit-learn, LightGBM, XGBoost |
| Visualization | Matplotlib, Seaborn |
| Environment | Jupyter / Google Colab |

---

## 🚀 Getting Started

```bash
git clone https://github.com/rosa-vazquez/bank-term-deposit-prediction.git
cd bank-term-deposit-prediction
pip install -r requirements.txt
jupyter notebook
```

Then open `Analisis-de-intencion-de-suscripcion-de-un-deposito-a-plazo.ipynb` and run all cells. The dataset `bank-full-recodificada.csv` is already included in the repository, in the same folder as the notebook.

> 💡 Tip: the notebook runs directly in **Google Colab** without any setup. Nested CV takes about 30 minutes (KNN is the slowest model).

---

## 📁 Project Structure

<pre>
├── Analisis-de-intencion-de-suscripcion-de-un-deposito-a-plazo.ipynb   # Full analysis
├── bank-full-recodificada.csv                                         # Recoded UCI dataset
├── requirements.txt                                                   # Python dependencies
├── .gitignore                                                         # Files excluded from Git
├── LICENSE                                                            # MIT License
└── README.md
</pre>

---

## 👩‍💻 Authors

- **Rosa Vázquez Sánchez**
- **María Jesús Vicente Ledesma**

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
 
