---
layout: single
title: "Surgery Duration Predictor: Reducing OR Scheduling Costs with ML"
date: 2026-04-28
categories: machine-learning healthcare
tags: [random-forest, nlp, tfidf, svd, healthcare, operations-research]
author_profile: true
read_time: true
show_date: true
excerpt: "A Random Forest model trained on ~35,000 surgical cases from a Canadian teaching hospital that outperforms surgeon-reported booked durations — saving $363,898 in OR scheduling costs and creating capacity for 176 additional procedures."
---

## Overview

**Surgery Duration Predictor** is a machine learning application developed as part of research conducted in the Department of Management Sciences at the **University of Waterloo**.

Inaccurate surgery time estimates lead to OR scheduling inefficiencies, patient wait times, and unnecessary costs. Most hospitals rely on surgeon-reported booked durations or historical averages — which are often systematically biased. This project replaces that with a data-driven prediction model.

---

## The Problem

Operating room time is one of the most expensive and constrained resources in a hospital. When a surgery runs over its booked slot, it creates overtime costs and cascading delays. When it finishes early, that recovered time is often wasted because the next case isn't ready.

The root cause: **booked durations are inaccurate**. Surgeons estimate optimistically, and static historical averages ignore case-specific context.

---

## Approach

A **Random Forest regression** model was trained on ~35,000 historical surgical cases from a Canadian teaching hospital.

### Feature Engineering

| Feature type | Details |
|---|---|
| Surgical priority | Numeric urgency score (1–5) |
| Patient / room / specialty | One-hot encoded categorical features |
| Procedure description | TF-IDF bigrams compressed to 150 SVD components |

### Target Variable

Actual OR time in minutes, **log-transformed** during training to reduce right-skew. Predictions are exponentiated back to interpretable minutes.

---

## Key Findings

- The model **outperforms booked duration benchmarks** on test data (lower RMSE and MAE)
- Most outlier cases are **under-predicted** — surgeries ran longer than expected
- **Procedure description text** (SVD embeddings from TF-IDF bigrams) is the strongest predictor group — more signal than categorical features alone

---

## Business Impact

Applied to **7,804 surgeries across 12 specialties** (Mar 2024 – Mar 2025):

> Replacing booked durations with model predictions reduced the net OR scheduling cost by **$363,898** — from $8.1M to $7.8M.

Recovered schedule time creates capacity for **176 additional high-volume procedures** without extending OR hours.

### Cost Model

| Scenario | Rate |
|---|---|
| Surgery ran over (overtime) | $35 × 1.5 = **$52.50/min** |
| Surgery ran under (opportunity cost) | **$35/min** |

Net cost is always positive — the model is evaluated by how much it reduces total deviation from scheduled time.

---

## Results Summary

| Metric | Model | Booked Duration |
|---|---|---|
| RMSE | Lower ✓ | Baseline |
| MAE | Lower ✓ | Baseline |
| Net scheduling cost | $7.8M | $8.1M |
| Additional procedures possible | +176 | — |

---

## Try It

The full Streamlit app with live predictions, model performance metrics, and business analysis is available at [surgery-duration-predictor.streamlit.app](https://surgery-duration-predictor.streamlit.app/).
