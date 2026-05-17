# IndabaX Nigeria 2026 Emission Forecasting Solution

Predicting atmospheric emission levels using spatiotemporal feature engineering, ensemble gradient boosting, and seasonal calibration.

---

## Overview

This repository contains my solution for the IndabaX Nigeria 2026 Emission Forecasting competition.

The objective of the competition was to predict emission levels from spatial and temporal environmental data. The challenge involved a difficult temporal distribution shift:

* Training data covered **January–September**
* Test data covered **September–December**

This created a major seasonal extrapolation problem, especially for regions with strong winter emission patterns such as East Asia and South Asia.

The final solution combines:

* Ensemble gradient boosting models
* Extensive spatiotemporal feature engineering
* Region-aware seasonal calibration

---

# Solution Architecture

The final pipeline consists of two stages:

## Stage 1 — Ensemble Model

An ensemble of:

* LightGBM
* XGBoost

trained using:

* 5-fold cross-validation
* log1p-transformed target variable
* extensive feature engineering

This stage generates the raw model predictions:

```text
P_RAW
```

---

## Stage 2 — Seasonal Calibration

Because the model never observed full winter months during training, it systematically under-predicted emissions for October–December.

To correct this:

* region-specific mirror months were used
* Jan–Feb statistics were mapped to Oct–Dec
* predictions were calibrated upward for winter-heavy regions

This stage generates:

```text
P_CAL
```

---

## Final Prediction

The final submission is a weighted blend:

```text
Final Prediction = 0.5 × P_RAW + 0.5 × P_CAL
```

followed by clipping to valid ranges.

---

# Feature Engineering

The solution relies heavily on structured feature engineering(52 new features were created)

## Temporal Features

* Cyclical hour encoding
* Cyclical month encoding
* Day-of-week encoding
* Day-of-year encoding
* Weekend indicators
* Rush-hour indicators

## Seasonal Features

* Hemisphere-aware winter/summer signals
* Cold-season intensity
* Seasonal interaction terms

## Spatial Features

* Latitude and longitude transformations
* Absolute coordinate features
* Latitude × longitude interactions

## Region Features

Custom geographic regions were defined using latitude and longitude rules:

* East Asia
* South Asia
* Europe
* North America
* Southern Hemisphere
* Other

## Interaction Features

The most important features were region-season interaction terms such as:

* East Asia × winter
* South Asia × winter
* Latitude × cold season

These features helped the model capture geographically-specific seasonal emission behavior.

---

# Model Training

## Cross-Validation

* 5-Fold KFold Cross Validation
* Fixed random seeds
* Out-of-fold evaluation

## Models

### LightGBM

Used for:

* fast gradient boosting
* non-linear feature interactions
* robust tabular learning

### XGBoost

Used for:

* complementary boosting behavior
* improved ensemble diversity
* stronger extrapolation robustness

The final model prediction is the average of both models.

---

# Environment

Recommended environment:

* Python 3.10+
* NumPy
* pandas
* scikit-learn
* LightGBM
* XGBoost

Install dependencies:

```bash
pip install -r requirements.txt
```

---

# Repository Structure

```text
.
├── README.md
├── requirements.txt
├── emi_indaba.ipynb
├── submission_18.csv
└── data/
```

---

# Output

The final submission file is:

```text
submission_v18.csv
```

---

# Key Insight

The central insight behind this solution was recognizing that the competition was primarily a:

> temporal extrapolation problem rather than a spatial memorization problem

The model needed to generalize from:

* non-winter training months
  to:
* winter-heavy unseen test months

This made seasonal calibration and region-aware feature engineering critical to performance.

---


