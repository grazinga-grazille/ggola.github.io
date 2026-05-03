---
layout: single
title: "Forecasting Rental Bike Demand with a Production-Ready Kedro Pipeline"
date: 2026-04-29
categories: machine-learning time-series
tags: [kedro, catboost, forecasting, time-series, plotly-dash, docker]
author_profile: true
read_time: true
show_date: true
excerpt: "A modular, production-ready ML pipeline that predicts next-hour rental bike demand using CatBoost, lag features, and a live Plotly Dash dashboard — built with Kedro for reproducibility."
---

## Overview

**Forecasting Rental Bike Count** is a production-ready machine learning pipeline that predicts the number of rental bikes needed in the next hour. Built with [Kedro](https://kedro.org/) for reproducible, modular ML workflows, it goes from raw data to a live interactive dashboard.

---

## The Problem

Bike-sharing systems need to balance supply and demand in real time. Too few bikes at a station means lost trips; too many means wasted redistribution effort. Accurate short-term demand forecasting lets operators pre-position bikes before demand spikes.

The goal: **predict next-hour bike rental count** from historical usage enriched with weather and time features.

---

## Pipeline Design

The project is structured as two Kedro pipelines that chain together cleanly:

### 1. Feature Engineering

| Step | What it does |
|---|---|
| Rename columns | Maps raw names to readable ones (`hr` → `hour`, `cnt` → `bike_count`) |
| Lag features | Captures temporal dependencies across multiple windows |

**Lag windows per feature:**

| Feature | Lags (hours) |
|---|---|
| `bike_count` | 1, 2, 22, 23 |
| `hour` | 1, 2, 3 |
| `temperature` | 1, 2, 3 |
| `humidity` | 1, 2, 3 |

The 22 and 23-hour lags on bike count capture the same-time-yesterday pattern — a strong seasonal signal for hourly demand.

### 2. Training

1. **Make target** — shifts `bike_count` by 1 period to create a next-hour forecast target
2. **Split** — chronological 80/20 train-test split (no data leakage)
3. **Train** — fits selected regression model
4. **Predict & evaluate** — MAE, RMSE, and MAPE on the held-out test set
5. **Persist** — saves model to `data/06_models/`

---

## Models

| Model | Notes |
|---|---|
| **CatBoost** (default) | Gradient boosting; handles categoricals natively; saved as `.cbm` |
| Random Forest | Ensemble baseline; saved as `.pkl` |
| Linear Regression | Simple baseline for comparison |

CatBoost is the primary model with:
- Learning rate: `0.2`
- Max depth: `6`
- L2 regularisation: `1`
- Loss: `RMSE`

---

## Live Dashboard

A **Plotly Dash** app runs a simulated inference pipeline, replaying historical data at 2-second intervals and updating predictions in real time — showing how the model would perform in a live deployment.

**[View Live Demo →](http://67.205.148.207:8050)**

---

## Tech Stack

| Layer | Tools |
|---|---|
| Pipeline orchestration | [Kedro](https://kedro.org/) |
| Modelling | CatBoost, scikit-learn |
| Data | pandas, NumPy |
| Visualisation | Plotly Dash |
| Pipeline viz | Kedro-Viz |
| Containerisation | Docker, docker-compose |
| Packaging | uv, pyproject.toml |
| Linting | ruff |

---

## Repo

Source code and setup instructions: [github.com/grazinga-grazille/forecasting-rental-bike-count](https://github.com/grazinga-grazille/forecasting-rental-bike-count)
