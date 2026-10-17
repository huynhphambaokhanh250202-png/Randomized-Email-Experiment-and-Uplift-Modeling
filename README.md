# Randomized Email Experiment and Uplift Modeling

A retailer ran a randomized email experiment on **64,000 customers**: one third received an email featuring
men's merchandise, one third an email featuring women's merchandise, and one third no email (control).
Visits, conversions, and spending were tracked for two weeks.

This project analyzes the experiment end to end to answer four marketing questions:

1. **Did the emails work?** Which creative performed better, and are the results statistically reliable?
2. **Who responds?** Which customers are likely to visit after an email (propensity model)?
3. **Who is persuadable?** Which customers visit *because of* the email (uplift model)?
4. **Who should get which email?** If only 10,000 customers can be emailed, who should they be?

👉 **Full analysis, code, and charts: [`Email_Marketing_RCT.ipynb`](Email_Marketing_RCT.ipynb)**

## Key Findings

**1. Both emails worked, and the men's email won the creative test.**

| Comparison | Visit rate | Lift | Conversion rate | Spend per customer |
|---|---|---|---|---|
| No email (control) | 10.6% | | 0.57% | $0.65 |
| Men's email | 18.3% | +7.7 pts | 1.25% | $1.42 |
| Women's email | 15.1% | +4.5 pts | 0.88% | $1.08 |

All email-vs-control differences in visits, conversions, and spend are significant after Holm correction for
multiple testing. The men's email beat the women's email by 3.1 visit points (a 21% relative lift).

**2. The women's email only works on customers who bought women's products before.**
Among past women's-product buyers it lifts visits by about 7 points; among customers who only bought men's
products, by about 1 point. The men's email lifts visits in every segment, and most among customers who bought
both product lines (+13 points). Interaction regressions confirm these differences are significant.

**3. Uplift modeling finds persuadable customers that propensity models miss, when effects vary.**
For the women's email, uplift models ranked persuadable customers far better than a propensity model:

| Ranking method | Qini area (women's email) |
|---|---|
| Uplift decision tree | 58.4 |
| T-learner (gradient boosting) | 46.8 |
| Propensity model | 20.3 |

For the men's email, the effect is similar across customers, so uplift models had little to exploit.

**4. Simple targeting rules capture most of the value.**
Sending the men's email to everyone captures nearly all available gains (+77 visits per 1,000 customers).
Choosing the best email per customer, or targeting a 10,000-customer budget by model scores, did not
reliably beat these simpler strategies (bootstrap confidence intervals include zero). The clear
recommendations are to **send the men's email broadly** and **send the women's email only to past
women's-product buyers**.

**5. Conversions need much larger tests.**
The experiment could detect an 8% relative lift in visits, but detecting a 10% lift in conversions would require
about 286,000 customers per group.

## Methods

| Area | Techniques |
|---|---|
| Data cleaning | Missing values, category fixes, logical consistency checks, range validation |
| Experiment validity | Sample ratio mismatch test (chi-square), covariate balance (standardized mean differences) |
| A/B and creative testing | Two-proportion z-tests, Welch t-tests, bootstrap confidence intervals, Holm correction |
| Experiment design | Power analysis and minimum detectable effect |
| Segmentation tests | Segment-level lifts, linear probability model with treatment × segment interactions |
| Propensity models | Logistic regression, decision tree (cross-validated), random forest, gradient boosting, neural network (MLP) |
| Uplift modeling | T-learner, transformed-outcome uplift decision tree, uplift deciles, Qini curves |
| Targeting policy | Inverse-propensity-weighted policy evaluation with bootstrap confidence intervals |

**Propensity model results (test set):** logistic regression performed as well as more complex models
(ROC AUC 0.62 vs. 0.59–0.62), so the simpler, more interpretable model is preferred.

## Data

Kevin Hillstrom, *MineThatData E-Mail Analytics and Data Mining Challenge* (2008). 64,000 customers who
purchased in the prior 12 months, with recency, past-year spend, product categories purchased, channel,
location, and new-customer status, plus visits, conversions, and spend in the two weeks after the campaign.
[Dataset description](https://blog.minethatdata.com/2008/03/minethatdata-e-mail-analytics-and-data.html)

## Repository Contents

| File | Description |
|---|---|
| `Email_Marketing_RCT.ipynb` | Full analysis notebook |
| `ab_test_results.csv` | A/B test results for all comparisons and outcomes |
| `segment_lifts.csv` | Email effects by customer segment |
| `uplift_segments_womens_email.csv` | Segments from the uplift decision tree with measured lifts |
| `policy_comparison.csv` | Value of each targeting strategy |
| `customer_recommendations.csv` | Customer-level predicted uplift and recommended email |

## How to Run

```bash
pip install pandas numpy scipy statsmodels scikit-learn matplotlib jupyter
```

Place the dataset CSV in the project folder, update `DATA_PATH` in the setup cell if needed, then open the
notebook and choose **Kernel → Restart & Run All**.

## Tools

Python · pandas · NumPy · SciPy · statsmodels · scikit-learn · Matplotlib · Jupyter
