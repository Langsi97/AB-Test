# 📊 A/B Test Analysis: Landing Page Conversion Experiment
### Should we ship the new landing page? A decision backed by five independent statistical methods

![Python](https://img.shields.io/badge/Python-3.11-blue)
![statsmodels](https://img.shields.io/badge/statsmodels-Inference-4B8BBE)
![SciPy](https://img.shields.io/badge/SciPy-Hypothesis%20Testing-8CAAE6)
![Decision](https://img.shields.io/badge/Recommendation-Do%20Not%20Ship-red)

---

## 📌 TL;DR

An e-commerce company tested a redesigned landing page against its current page with **288,541 users** (about 144,000 per group) from a public A/B test dataset. I cleaned the experiment data, checked that randomisation worked, and tested the result five ways.

- **Result:** the new page converted at **11.87%** against **12.03%** for the old page. That's **−0.16 percentage points** (−1.3% relative).
- **Significance:** not statistically significant (Z-test p = 0.195, χ² p = 0.197, odds ratio 0.985 with a 95% CI that includes 1).
- **Best case:** the 95% confidence interval for the lift is **−0.39 to +0.08 pp**. Even at the top of that range, the new page adds less than 0.1 pp of conversion (about 0.6% relative).
- **Power:** the test had more than **8 times** the sample needed to detect a 1 pp lift, and could detect an effect as small as about **0.34 pp**. The null result is a real finding, not a sign of too little data.

**Recommendation: do not ship the new page.** Keep the current page, and spend the design budget on a bolder change.

---

## 💼 Business Problem

Landing page changes look cheap, but shipping the wrong one is expensive:

- **Risk of shipping a loser:** a page that quietly converts worse costs revenue on every visit until someone notices.
- **Risk of a false win:** declaring victory on noise leads to roadmap decisions built on an effect that doesn't exist.
- **Opportunity cost:** engineering, design and QA time spent rolling out a change that adds no value.

The question for the product team: **Does the new landing page increase the share of visitors who convert, enough to justify rolling it out?**

## ✅ Solution

A structured experiment analysis that covers what a quick notebook usually skips:

```
Data acquisition (kagglehub) → Integrity checks → Randomisation check → EDA
→ Hypothesis tests (Z-test, χ²) → Bootstrap CI → Logistic regression
→ Power & minimum detectable effect → Business recommendation
```

| Step | Why it matters for the decision |
|---|---|
| Integrity checks | Duplicate users and users who saw the wrong page bias the result before any test is run |
| Randomisation check | Confirms the two groups are comparable, so any difference can be attributed to the page |
| Five independent methods | A conclusion that holds under different assumptions is safer to act on |
| Power analysis | Separates "no effect" from "not enough data", the most common mistake in A/B readouts |

---

## 🧹 Data & Integrity Checks

| Column | Description |
|---|---|
| `user_id` | Unique visitor identifier |
| `timestamp` | Visit time |
| `group` | Assigned group (control / treatment) |
| `landing_page` | Page actually shown (old_page / new_page) |
| `converted` | Whether the user converted (0 / 1) |

Issues found and fixed before analysis:

1. **Experiment contamination:** removed users whose assigned group didn't match the page they saw. For example, control users who were shown the new page.
2. **Duplicate users:** removed repeat records so each user counts once.
3. **Types and missing values:** checked every column.

**Clean sample:** 288,541 users, split about 50/50 (about 144,000 per group).

---

## 📊 Results

### Conversion by group

| Group | Page | Conversion rate |
|---|---|---|
| Control | Old | **12.029%** |
| Treatment | New | **11.873%** |
| **Difference** | | **−0.156 pp (−1.30% relative)** |

### Five methods, one conclusion

| Method | Result | Reading |
|---|---|---|
| Z-test for two proportions | Z = −1.29, p = 0.195 | Fail to reject H₀ |
| Chi-square test of independence | p = 0.197 | Conversion is independent of page |
| Bootstrap 95% CI (difference) | [−0.395 pp, +0.078 pp] | Includes zero |
| Logistic regression | Odds ratio = 0.985, 95% CI includes 1 | No significant effect |
| Power analysis | 17,198 users per group needed for a 1 pp lift at 80% power | Sample is more than 8 times that |

---

## 🔍 Key Findings

1. **The new page does not improve conversion.** The observed difference is small, slightly negative and not significant under any of the methods.
2. **The upside is too small to matter.** The bootstrap CI puts the best plausible lift at +0.08 pp, about 0.6% relative. Even if the true effect sits at the top of the interval, it is unlikely to repay the cost of a rollout.
3. **The null result is trustworthy.** With about 144,000 users per group, the test had 80% power to detect a change of about 0.34 pp (about 2.8% relative). If the new page had a meaningful effect, this experiment would almost certainly have found it.
4. **Data cleaning was a precondition, not a formality.** Users who saw the wrong page for their group would have mixed the two groups and weakened the comparison. Removing them is what makes the result valid.
5. **Agreement across methods builds confidence.** Parametric tests, a distribution-free bootstrap and a regression model all reach the same conclusion, so the decision doesn't depend on any one method's assumptions.

## 💡 Business Recommendations

- **Do not ship the new landing page.** Keep the current page as the default.
- **Stop iterating on this design.** More traffic won't rescue it. The experiment was already big enough to detect small effects.
- **Test bolder changes next.** Small cosmetic changes rarely move conversion by more than a fraction of a point. Prioritise ideas with a plausible effect of 1 pp or more, such as offer, pricing display or checkout friction.
- **Set the success criteria before launch.** Agree on the primary metric, minimum detectable effect, sample size and run time in advance. That prevents early stopping and "peeking" at p-values.
- **Segment carefully.** The dataset includes country data (`countries.csv`). A country-level analysis can show whether the page works for a specific market, but it needs a multiple-testing correction to avoid false positives.

---

## ⚠️ Limitations

- **One metric:** the analysis covers conversion only. Revenue per visitor, bounce rate and downstream retention were not available.
- **Short-term effects:** novelty or change-aversion effects can't be fully separated from the long-run impact without a longer run.
- **Public dataset:** the business context is illustrative. The methodology transfers directly to production experiments.

## 🚀 Next Steps

- Country-level heterogeneity analysis with Bonferroni or Benjamini–Hochberg correction
- A Bayesian A/B analysis (Beta–Binomial) to report the *probability* the new page is better, which is often easier for stakeholders to act on
- A reusable experiment-readout template (integrity → power → tests → decision) for future tests

---

## 🛠️ Tech Stack

| Purpose | Tools |
|---|---|
| Data | pandas, NumPy, kagglehub |
| Statistics | SciPy (Z-test, χ²), statsmodels (proportions test, power analysis, logistic regression) |
| Visualisation | Matplotlib, Seaborn |

## ▶️ How to Run

```bash
git clone https://github.com/Langsi97/AB-Test.git
cd AB-Test
pip install pandas numpy matplotlib seaborn scipy statsmodels kagglehub
jupyter notebook AB_test.ipynb
```

The notebook downloads the dataset automatically through `kagglehub`.

---

## 👤 Author

**Langsi Ambe Revelation** · Data Scientist · MSc Statistics & Data Science (Hasselt University)
[LinkedIn](https://www.linkedin.com/in/langsi-ambe97) · [GitHub](https://github.com/Langsi97)
