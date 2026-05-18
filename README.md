

```markdown
# ⚖️ COMPAS Recidivism & Fairness Audit

**A machine learning case study in algorithmic bias, built from the data that shook the criminal justice world.**

> *“Black defendants were often predicted to be at a higher risk of recidivism than they actually were. Our analysis found that black defendants who did not recidivate over a two-year period were nearly twice as likely to be misclassified as higher risk compared to their white counterparts.”*  
> — ProPublica, Machine Bias (2016)

---

## 🔍 Project Overview

In 2016, ProPublica published a landmark investigation revealing that **COMPAS** — a widely used commercial algorithm for predicting criminal recidivism — was **racially biased**. Black defendants were consistently rated higher risk than they actually were, while white defendants were under-estimated.

This project recreates that investigation from scratch. It:
- **Builds a machine learning model** (logistic regression) to predict two-year recidivism using the original COMPAS dataset.
- **Audits the model for fairness** across racial groups, exposing the exact disparities ProPublica documented.
- **Demonstrates why “race-blind” models are not enough** to guarantee fairness.

The goal is purely **educational** — to understand how bias can creep into risk assessment tools and what it looks like in practice. **This model must never be used for real criminal justice decisions.**

---

## 📊 The Dataset

**Source:** [ProPublica COMPAS Analysis](https://github.com/propublica/compas-analysis)  
**File used:** `compas-scores-two-years.csv`  
**Size:** 7,214 defendants (filtered down to 5,278 after applying ProPublica’s cleaning criteria)

### Features used
- `age`
- `sex`
- `priors_count`
- `c_charge_degree` (felony or misdemeanor)
- `juv_fel_count`
- `juv_misd_count`
- `juv_other_count`

### Target
- `two_year_recid`: Whether the defendant was rearrested within two years.

### Sensitive attribute
- `race`: Simplified to **Black** (African-American) and **White** (Caucasian) to match the original investigation.

---

## 🧪 Methodology

1. **Data Cleaning:** Replicated ProPublica’s filters — removed cases with missing recidivism info, traffic offenses, and incomplete risk scores.
2. **Feature Engineering:** One-hot encoded categorical features. Race was intentionally **excluded** from the training data to test “race-blind” modeling.
3. **Model Training:** Logistic regression (`scikit-learn`) on 70% of the data.
4. **Fairness Audit:** Used `fairlearn` to compute key metrics — **false positive rate**, **false negative rate**, and **selection rate** — disaggregated by race.
5. **Equity Metrics:** Calculated **Equalized Odds Difference** and **Demographic Parity Difference** to quantify unfairness.

---

## 🔥 Key Findings

| Metric | Black Defendants | White Defendants | Disparity |
|--------|-----------------|------------------|-----------|
| **False Positive Rate** | 28.5% | 15.7% | Black defendants **1.8× more likely** to be incorrectly labeled high risk |
| **False Negative Rate** | 37.4% | 58.4% | White defendants **1.6× more likely** to be incorrectly labeled low risk |
| **Selection Rate** (% predicted high risk) | 45.9% | 25.9% | Black defendants flagged high risk at **nearly twice the rate** |
| **Accuracy** | 67.0% | 67.5% | Nearly identical, masking the underlying injustice |

**Fairness metrics:**
- **Equalized Odds Difference:** 0.210 (far from 0 — strong evidence of unfairness)
- **Demographic Parity Difference:** 0.200 (similarly large gap)

**The model is unfair.** Even though race was never used in training, the model learned to discriminate through proxy variables like prior arrests and charge degree — which are themselves the product of a biased criminal justice system.

---

## 🚀 How to Run

### 1. Clone the repo
```bash
git clone https://github.com/yourusername/compas-fairness-audit.git
cd compas-fairness-audit
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Download the dataset
Place `compas-scores-two-years.csv` in the project root. You can get it from:
- [ProPublica’s GitHub](https://raw.githubusercontent.com/propublica/compas-analysis/master/compas-scores-two-years.csv) (right-click → Save As)
- Or the [COMPAS Recidivism Racial Bias Kaggle dataset](https://www.kaggle.com/danofer/compass)

### 4. Run the analysis
```bash
jupyter notebook fairness_audit.ipynb
```
Or run the Python script directly:
```bash
python audit.py
```

---

## 📦 Dependencies

- Python 3.8+
- pandas
- numpy
- scikit-learn
- fairlearn
- matplotlib
- seaborn
- jupyter (optional, for notebook)

---

## 📁 Project Structure

```
.
├── audit.py                  # Full pipeline: data cleaning, training, fairness audit
├── fairness_audit.ipynb      # Step-by-step notebook version
├── requirements.txt
├── README.md
└── data/                     # (place compas-scores-two-years.csv here)
```

---

## ⚠️ Ethical Disclaimer

**This project is for educational and research purposes only.** The model we built is biased by design and replicates the exact injustices found in the COMPAS algorithm. It must **never** be deployed in any real-world criminal justice context. The analysis demonstrates the dangers of blindly trusting machine learning predictions when historical data encodes systemic racism.

If you use this code or data for any purpose, you must read and understand ProPublica’s full methodology and the extensive ethical debates surrounding risk assessment instruments.

---

## 📚 References

- ProPublica, [Machine Bias](https://www.propublica.org/article/machine-bias-risk-assessments-in-criminal-sentencing) (2016)
- ProPublica, [How We Analyzed the COMPAS Recidivism Algorithm](https://www.propublica.org/article/how-we-analyzed-the-compas-recidivism-algorithm) (2016)
- Julia Angwin et al., *Machine Bias* data & analysis: [GitHub](https://github.com/propublica/compas-analysis)
- Fairlearn documentation: [https://fairlearn.org](https://fairlearn.org)

---

## 🙌 Acknowledgments

Thanks to ProPublica for their courageous investigative work and for making this data public, and to the open-source communities behind `fairlearn` and `scikit-learn` for providing the tools to measure algorithmic harm.

---

> **Remember:** When a model is 67% accurate for both groups but wrong in opposite, devastating ways, accuracy is a lie.
```
