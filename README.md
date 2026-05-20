# 📊 A/B Test Analysis: Landing Page Conversion Experiment

This project performs a **rigorous, end-to-end statistical analysis** of a real A/B test to determine whether a **new landing page** improves user conversion compared to the **existing page**.

Unlike typical A/B notebooks that jump directly into hypothesis testing, this analysis follows **production-grade experimental validation**:

- Strict data integrity checks (duplicates, contamination, type validation)
- Reproducible data acquisition from source
- Exploratory analysis before formal testing
- Multiple independent statistical methods for cross-validation
- Statistical power analysis to validate the credibility of the result
- Logistic regression for interpretability and extensibility
- A clear, evidence-based business recommendation

This repository demonstrates **how A/B testing should be conducted in real data science practice**.

---

## 🎯 Objective

Evaluate whether the **new landing page** increases conversion rate.

- **Control group** → old page
- **Treatment group** → new page
- **Metric** → `converted` (0 / 1)

---

## 🧪 Dataset Description

| Column        | Description                                  |
|--------------|----------------------------------------------|
| user_id      | Unique visitor identifier                     |
| timestamp    | Visit time                                   |
| group        | Assigned group (control / treatment)         |
| landing_page | Page actually shown (old / new)              |
| converted    | Whether the user converted (0 / 1)           |

A second file (`countries.csv`) exists but is not required for the core analysis.

---

## 🧹 Data Integrity Checks (Critical Step)

Before any statistics were run, the following issues were identified and corrected:

1. **Duplicate users removed**  
   Prevents inflated sample size and biased conversion counts.

2. **Group / Page mismatches removed (experiment contamination)**  
   Ensures control users saw the old page and treatment users saw the new page.

3. **Missing values and data types verified**

After cleaning:

- **Total users analyzed:** 288,541  
- **Balanced groups:** ~144,000 users per group

---

## 📈 Exploratory Data Analysis

EDA verifies:

- Proper 50/50 randomisation
- Similar group sizes after cleaning
- Very small visible difference in conversion rates

| Group      | Conversion Rate |
|------------|------------------|
| Control    | **12.029%**      |
| Treatment  | **11.873%**      |

- **Absolute lift:** −0.156 percentage points  
- **Relative lift:** −1.30%

The new page performs slightly worse.

---

## 🧮 Statistical Methods Used (Triangulation)

This analysis does **not** rely on a single statistical test.

| Method                     | Purpose |
|---------------------------|---------|
| Bootstrap simulation      | Distribution-free CI for difference in means |
| Z-test for proportions    | Primary hypothesis test for binary outcomes |
| Chi-square test           | Cross-validation of Z-test result |
| Confidence interval plots | Visual interpretability |
| Statistical power analysis| Validates credibility of the result |
| Logistic regression       | Odds ratio interpretation & extensibility |

All methods independently reach the same conclusion.

---

## 📊 Results

### Z-Test for Proportions

- Z = −1.29  
- p = 0.195  
- **Fail to reject H₀**

### Bootstrap 95% Confidence Interval
```[-0.3948%, +0.0779%]```
The interval contains zero → difference may be noise.

### Chi-Square Test

- p = 0.197  
- Conversion is independent of group.

### Logistic Regression

- Odds Ratio = **0.985**
- 95% CI crosses 1 → not significant

---

## ⚡ Statistical Power Analysis (Key Insight)

To ensure the null result is meaningful, power was evaluated.

- Required sample to detect **1pp lift with 80% power**: **17,198 users per group**
- Actual sample per group: **144,000+ users**

👉 The experiment had **more than 8× the required data**.

**Conclusion:** The null result is real, not due to insufficient data.

---

## ✅ Final Conclusion

All evidence converges:

- The new landing page **does not improve conversion**
- The experiment had more than enough data to detect meaningful improvements
- Multiple statistical methods confirm the result

> **Business Recommendation:** Do **not** ship the new page.

---

## 🧠 Why This Repository Matters

This project demonstrates:

- Correct A/B data cleaning before analysis
- The importance of EDA before hypothesis testing
- Why power analysis is mandatory
- How to present statistical findings clearly to stakeholders
- How to make conclusions robust using multiple methods

This serves as a **reference implementation** for rigorous A/B test analysis.

---

## 🛠️ Tech Stack

- Python
- pandas, numpy
- matplotlib, seaborn
- scipy.stats
- statsmodels
- kagglehub

---

## ▶️ How to Run

Install dependencies:

```
pip install pandas numpy matplotlib seaborn scipy statsmodels kagglehub
```
Run the notebook:

```AB_test.ipynb```

📌 Key Takeaway

A/B testing is not about obtaining a p-value.

It is about ensuring:

Clean data
Valid experimental design
Adequate statistical power
Trustworthy conclusions

This notebook shows exactly how to achieve that.
