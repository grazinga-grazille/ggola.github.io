---
layout: single
title: "Flight Delay Prediction: Tackling Class Imbalance in Carrier Delay Classification"
date: 2026-05-03
categories: machine-learning classification
tags: [xgboost, classification, imbalanced-data, oversampling, adasyn, feature-engineering]
author_profile: true
read_time: true
show_date: true
excerpt: "A binary classification project predicting severe carrier flight delays (60+ min) using XGBoost on 484K flights — and why accuracy alone is meaningless when only 7.7% of flights are delayed."
---

## Overview

This project tackles a real-world classification problem: predicting whether a flight will suffer a **carrier delay of 60 minutes or more**. With nearly half a million flights and heavy class imbalance, the challenge isn't building a model — it's building one that actually catches delays.

---

## The Problem

Airlines and airports lose significant revenue and passenger trust from severe delays. If we can predict which flights are likely to be badly delayed, operations teams can intervene early — reassigning crew, swapping aircraft, or proactively communicating with passengers.

**Target:** `is_delayed_60+`
- `1` → Carrier delay ≥ 60 minutes
- `0` → Carrier delay < 60 minutes

---

## Dataset

Starting from `flights.csv`, the modeling table is built from:

| Feature | Notes |
|---|---|
| `DayOfWeek` | Day of the week |
| `Date` | Expanded into `month` and `day` |
| `UniqueCarrier` | Airline code |
| `FlightNum` | Flight number |
| `TailNum` | Aircraft tail number |
| `Origin` / `Dest` | Airport codes |

All categorical columns are one-hot encoded (`drop_first=True`), producing a wide feature matrix:

- **Rows:** 484,551
- **Columns:** 4,073 (after encoding)

---

## The Real Challenge: Class Imbalance

The dataset is heavily skewed:

| Class | Count | Share |
|---|---|---|
| Not delayed (`0`) | 313,141 | ~92.3% |
| Delayed 60+ min (`1`) | 26,044 | ~7.7% |

This matters enormously for evaluation. A model that predicts *no flight is ever delayed* achieves **92.3% accuracy** — but is completely useless. The baseline XGBoost model demonstrates this exactly:

```
Confusion Matrix:
[[134,209    10]
 [ 11,141     6]]

Accuracy:  0.923
ROC-AUC:   0.674
F1 Score:  0.001
Precision: 0.375
```

High accuracy, near-zero recall on the positive class. The model learned to predict the majority class almost exclusively.

---

## Imbalance Handling

The notebook experiments with three resampling strategies to improve minority class recall:

| Method | Approach |
|---|---|
| `RandomOverSampler` | Duplicates minority class samples at random |
| `RandomUnderSampler` | Reduces majority class samples at random |
| `ADASYN` | Generates synthetic minority samples in harder-to-classify regions |

Each strategy involves a trade-off: improving recall for delayed flights typically comes at the cost of more false positives (flagging on-time flights as delayed).

---

## Key Takeaways

**Accuracy is a trap for imbalanced problems.** At 7.7% positive rate, always predicting "not delayed" scores 92.3% accuracy. The right metrics here are **F1, precision-recall AUC, and recall on the minority class**.

**Wide one-hot encoding has costs.** 4,073 features from encoding alone creates a memory-heavy matrix. Dimensionality reduction or feature selection would help.

**Resampling helps recall but isn't free.** Oversampling and ADASYN improve the model's ability to catch delays, but can degrade overall precision. Threshold tuning on the predicted probabilities is often more surgical than resampling alone.

---

## What's Next

- Threshold tuning using precision-recall curves on validation data
- `scale_pos_weight` in XGBoost as an alternative to resampling
- Hyperparameter search with Optuna
- PR-AUC as the primary evaluation metric (more informative than ROC-AUC for imbalanced data)
- Leakage-safe feature engineering (avoiding features only available post-departure)

---

## Repo

[github.com/grazinga-grazille/flight-delay-prediction](https://github.com/grazinga-grazille/flight-delay-prediction)
