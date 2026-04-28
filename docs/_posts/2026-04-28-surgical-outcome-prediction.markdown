---
layout: post
title: "Surgical Outcome Prediction with Machine Learning"
date: 2026-04-28 12:00:00 -0400
categories: [machine-learning, healthcare]
description: Using pre-operative patient data to predict surgical outcomes — approach, challenges, and results.
---

Predicting surgical outcomes before a procedure begins is one of the more meaningful problems machine learning can tackle in healthcare. This post walks through the work, the decisions made, and what I learned.

## The Problem

Surgeons and care teams need tools to assess patient risk pre-operatively — not to replace clinical judgment, but to augment it. The goal here was to build a model that, given a set of patient features collected before surgery, estimates the likelihood of a complication or adverse outcome.

## Data

The dataset included pre-operative features such as:

- Patient demographics (age, BMI)
- Comorbidities (diabetes, hypertension, etc.)
- Lab values (hemoglobin, creatinine, etc.)
- Procedure type and surgical complexity

Before modeling, significant time went into cleaning and preprocessing — handling missing values, encoding categoricals, and addressing class imbalance in the outcome labels.

## Approach

I tested several classification algorithms:

- **Logistic Regression** — baseline, interpretable
- **Random Forest** — handles non-linearity, robust to outliers
- **Gradient Boosting (XGBoost)** — best overall performance

Feature importance from the tree-based models revealed that a handful of lab values and comorbidity combinations were the strongest predictors, which aligns with clinical intuition.

## Results

| Model | AUC-ROC | Precision | Recall |
|---|---|---|---|
| Logistic Regression | 0.74 | 0.68 | 0.65 |
| Random Forest | 0.81 | 0.74 | 0.71 |
| XGBoost | **0.85** | **0.78** | **0.76** |

The XGBoost model achieved an AUC-ROC of **0.85**, which is a meaningful improvement over the logistic regression baseline and competitive with published results in similar domains.

## Challenges

- **Class imbalance** — adverse outcomes are relatively rare. SMOTE and class-weight adjustments helped significantly.
- **Missing data** — some lab values were missing for a large fraction of patients. Multiple imputation was used rather than simple mean-fill.
- **Interpretability** — SHAP values were used to explain individual predictions, which is important for clinical trust.

## What's Next

- Incorporate temporal features (trend in lab values leading up to surgery)
- Explore calibration to ensure predicted probabilities are reliable
- Validate on an external dataset

The code and data pipeline are on [GitHub](https://github.com/{{ site.github_username }}).
