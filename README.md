# Cardiovascular Risk Fairness Audit

## Research Question

Do cardiovascular risk prediction models demonstrate gender based performance differences, and if so, do these differences arise from model ranking ability or from classification threshold effects?

---

## Overview

This project evaluates gender-based performance differences in a logistic regression model predicting 10-year coronary heart disease (CHD) using the Framingham Heart Study dataset.

Rather than optimising for accuracy, the focus of this analysis is on:

- Sensitivity (recall)
- False negative rates
- AUC (discriminative ability)
- Threshold behaviour
- Data completeness differences

The goal is to understand how modelling decisions interact with structural dataset characteristics in a healthcare setting.

> ⚠️ This is an analytical audit of model behaviour and is not intended for clinical use.

---

## Dataset

**Source:** Framingham Heart Study (publicly available)

Target variable: `TenYearCHD` (1 = CHD within 10 years)

### Class Distribution

- Overall CHD prevalence: ~15%
- Women: ~12.4%
- Men: ~18.9%

The dataset is moderately imbalanced, making accuracy a weak standalone metric.

---

## Data Completeness Audit

Several key predictors contained missing values:

- Glucose
- BMI
- Total cholesterol
- Blood pressure medication status
- Education
- Cigarettes per day
- Heart rate

### Missingness by Gender

Women exhibited higher missingness in several clinically relevant predictors:

- Glucose
- BMI
- Total cholesterol
- Blood pressure medication

This indicates structural differences in data completeness across gender.

---

## Handling Missing Data

To preserve distributional differences:

- Missingness indicator flags were added for each variable.
- Numerical features were imputed using gender-specific medians.
- Education was imputed using gender-specific mode.
- Missing BP medication values were set to 0.

This approach avoids collapsing group-specific distributions while maintaining modelling feasibility.

---

## Baseline Model

- **Model:** Logistic Regression  
- **Class weighting:** Balanced  
- **Train/test split:** 80/20 (stratified)  
- **Feature scaling:** StandardScaler  

### Overall Performance

- Accuracy: 66%
- AUC: 0.69

The model demonstrates moderate discriminative ability.

---

## Gender-Based Performance Analysis

### At Default Threshold (0.5)

**Recall (Sensitivity)**

- Women: 0.44
- Men: 0.70

**False Negative Rate**

- Women: 0.56
- Men: 0.30

Despite similar ranking performance, a substantially higher proportion of at-risk women were missed by the model.

---

## Discriminative Ability (AUC)

- Women AUC: 0.67
- Men AUC: 0.67

The model separates CHD and non-CHD cases similarly within both genders.

The observed disparity does not arise from poor ranking performance for women.

---

## Threshold Analysis

To test whether disparity was driven by the classification threshold, the decision threshold was reduced from 0.5 to 0.4.

### At Threshold = 0.4

- Women recall: 0.69
- Men recall: 0.80
- Women FNR: 0.31
- Men FNR: 0.19

The recall gap reduced substantially (from 26 percentage points to 11).

This demonstrates that much of the observed gender disparity was driven by the use of a uniform classification threshold.

---

## Key Findings

1. The model exhibits gender-based differences in sensitivity at the default threshold.
2. Discriminative ability (AUC) is similar across genders.
3. The primary driver of disparity is threshold behaviour rather than ranking ability.
4. Women had higher missingness in key predictors, which may contribute to downstream differences.
5. Accuracy alone would not have revealed these performance disparities.

---

## Discussion

This analysis illustrates several important considerations in healthcare machine learning:

- Uniform classification thresholds can produce unequal sensitivity across groups.
- Evaluating AUC alone is insufficient when auditing fairness.
- Group-specific performance metrics are essential in clinical risk modelling.
- Structural differences in data completeness may influence downstream outcomes.

The findings do not imply intentional bias but demonstrate how modelling decisions and data characteristics interact to produce differential outcomes.

---

## Limitations

- Single dataset
- No external validation
- No temporal modelling
- Causal links between missingness and performance disparity not formally tested
- No subgroup-specific threshold optimisation

---

## Tech Stack

- Python
- pandas
- NumPy
- scikit-learn
- matplotlib
- seaborn

