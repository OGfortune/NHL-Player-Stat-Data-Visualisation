# Data Analytics in R — Disease Burden, Predictive Modelling & S3 Programming

An end-to-end R project covering three areas of applied data analytics: exploratory analysis of
European disease burden data, binary classification with the `caret` package, and object-oriented
programming using R's S3 class system.

Built as a final project for the R Programming module, MSc Computer Science, University College Dublin.

---

## Overview

The project is organised into three self-contained parts, each using a different dataset and
demonstrating a different set of skills.

| Part | Focus | Dataset |
|------|-------|---------|
| 1 | Exploratory data analysis & visualisation | Global Burden of Disease (Western Europe, 2015–2019) |
| 2 | Predictive modelling with `caret` | Heart Failure Prediction (Kaggle) |
| 3 | S3 classes, generic methods & interactive plotting | NHL season goals (TidyTuesday) |

---

## Part 1 — Disease Burden Analysis

Analysis of mortality and Disability Adjusted Life Years (DALYs) across Western European countries
using data from the IHME Global Burden of Disease Study 2019. The working dataset covers 2,944
observations across 10 variables, filtered to Western Europe for the period 2015–2019.

**Analyses performed**

- Top 10 causes of death aggregated across all countries
- Total DALYs by country
- Absolute deaths vs. population-adjusted death rate per 100,000
- Death rate segmented by sex, age group and cause
- Percentage change in mortality between 2015 and 2019

**Selected findings**

- Ischaemic heart disease was the leading cause of death across the region, with roughly 2.88m
  deaths — nearly double the next highest cause, stroke, at approximately 1.51m. Cardiovascular
  disease dominates the regional burden.
- Germany recorded both the highest absolute deaths and the highest total DALYs, but adjusting to a
  rate per 100,000 changed the ranking entirely: Greece and Monaco showed the highest death rates,
  demonstrating why crude counts alone are misleading when population size varies.
- DALYs increased with age across both sexes, with men consistently losing more years to disability
  and the gap widening in older age groups.
- The regional trend in death rate was downward overall, with Greece the notable exception at a ~10%
  increase — more than three times the next-highest country.

---

## Part 2 — Predictive Modelling with `caret`

A binary classification task predicting the presence of heart disease from clinical risk factors,
built to demonstrate the `caret` package's workflow for data splitting, pre-processing, model
tuning and performance evaluation.

**Pipeline**

1. Categorical encoding — binary features one-hot encoded, multi-class features expanded to dummy
   variables via `fastDummies`
2. Train/test split using `caret`'s hold-out partitioning
3. Model training: logistic regression and random forest
4. Evaluation via confusion matrix, accuracy with confidence intervals, Kappa, sensitivity,
   specificity and McNemar's test
5. K-fold cross-validation
6. Feature importance ranking and re-training on the reduced feature set

**Results**

| Model | Method | Test accuracy | Notes |
|-------|--------|---------------|-------|
| Logistic regression | Hold-out | 84% (95% CI: 79–88%) | 86% on training data |
| Random forest | Hold-out | 85% (95% CI: 80–90%) | Optimal `mtry` = 2 (accuracy 0.878, Kappa 0.751) |
| Random forest | 10-fold CV | 89% | Best performing configuration |
| Random forest | Important features only | 82% | Feature reduction did not improve performance |

Both models achieved substantial Kappa values and balanced sensitivity and specificity, with
McNemar's test indicating no significant imbalance between false positives and false negatives.
Cross-validation improved random forest generalisation from 85% to 89%, illustrating its value on
smaller datasets. Restricting to the top-ranked features reduced accuracy, which suggests the
excluded variables still carried signal despite lower individual importance scores.

---

## Part 3 — S3 Classes and Generic Methods

An implementation of a custom S3 class, `hockeyStats`, wrapping analysis of NHL player statistics
across 4,810 records.

**Components**

- **Constructor** — `hockeyStats()` returns a structured list holding data, analysis results and plot
- **Analysis function** — `analyzeHockeyData()` validates that mandatory columns are present, then
  computes average goals or assists per player alongside seasons played; the `stat` argument accepts
  one metric at a time
- **`print` method** — reports record count and dataset details
- **`summary` method** — descriptive statistics (mean, median, interquartile range) for the selected
  statistic and seasons played
- **`plot` method** — interactive Plotly scatter of average statistic against seasons played, with
  player names surfaced on hover

Input validation is handled explicitly: the function checks for required columns and fails
informatively when they are missing.

---

## Tech Stack

- **Language:** R
- **Modelling:** `caret`, `randomForest`
- **Data manipulation:** `tidyverse`, `rio`, `fastDummies`
- **Visualisation:** `ggplot2`, `plotly`
- **Reporting:** Quarto

---

## Running the Project

```r
install.packages(c("caret", "randomForest", "rio", "tidyverse",
                   "ggplot2", "fastDummies", "plotly"))
```

Render the full report from the project root:

```bash
quarto render Data-Analytics.qmd
```

The rendered HTML includes all code, output tables and interactive plots.

---

## Repository Structure

```
.
├── Data-Analytics.qmd        # Source Quarto document
├── Data-Analytics.html       # Rendered report
├── BurdenOfDisease.csv       # GBD extract, Western Europe 2015–2019
├── heart.csv                 # Heart Failure Prediction dataset
├── season_goals.csv          # NHL season goals dataset
└── README.md
```

---

## Data Sources

- **Global Burden of Disease Collaborative Network.** Global Burden of Disease Study 2019 (GBD 2019)
  Results. Seattle: Institute for Health Metrics and Evaluation (IHME), 2020.
  https://vizhub.healthdata.org/gbd-results/
- **Heart Failure Prediction Dataset** by fedesoriano, via Kaggle.
- **NHL Season Goals**, via the TidyTuesday project.

## Reference

Kuhn, M. (2008). Building Predictive Models in R Using the caret Package. *Journal of Statistical
Software*, 28(5), 1–26. https://doi.org/10.18637/jss.v028.i05

---

## Author

**Oghenemalu Fortune Ighoiye**
[GitHub](https://github.com/OGfortune) · [LinkedIn](https://www.linkedin.com/in/oghenemalu-fortune-ighoiye-3bb916186/)
