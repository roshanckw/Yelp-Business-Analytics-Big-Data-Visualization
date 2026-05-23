# Yelp Business Analytics — Big Data & Visualisation

An end-to-end big data analytics project analysing 6.9 million+ Yelp reviews and 150,000+ businesses to identify the key drivers of business success and customer foot traffic, using distributed computing, machine learning, real-time streaming simulation, and interactive Tableau dashboards.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Dataset](#dataset)
- [Tech Stack](#tech-stack)
- [Project Pipeline](#project-pipeline)
- [Data Processing](#data-processing)
- [Machine Learning Models](#machine-learning-models)
- [Real-Time Streaming Simulation](#real-time-streaming-simulation)
- [Tableau Dashboards](#tableau-dashboards)
- [Key Findings](#key-findings)
- [Project Structure](#project-structure)
- [How to Run](#how-to-run)

---

## Project Overview

Businesses on Yelp generate enormous amounts of customer activity data, but struggle to identify which factors actually drive success and customer engagement. This project integrates Yelp business attributes and customer check-in data to answer two core questions:

1. **What drives business success?** (ratings, categories, location, review count, operational status)
2. **What predicts customer foot traffic?** (temporal check-in patterns, business attributes)

The project covers the full data science workflow — ingestion, preprocessing, exploratory analysis, predictive modelling, streaming simulation, and interactive visualisation.

---

## Dataset

**Source:** [Yelp Open Dataset](https://www.kaggle.com/datasets/adamamer2001/yelp-complete-open-dataset2024) (Kaggle)

| Metric | Value |
|--------|-------|
| Reviews | 6,990,280 |
| Businesses | 150,346 |
| Users | 1,987,897 |
| Tips | 908,915 |
| Metro areas | 11 |
| Business attributes | 1.2 million+ |
| Check-in businesses | 131,930 |

The dataset consists of 5 JSON files:

| File | Description | Key Variables |
|------|-------------|---------------|
| `business.json` | Business attributes and location | business_id, name, stars, categories, review_count, location |
| `review.json` | Customer reviews | review_id, user_id, business_id, stars, text, date |
| `user.json` | User profiles | user_id, review_count, average_stars, friends |
| `checkin.json` | Customer visit logs | business_id, checkin_time |
| `tip.json` | Short customer recommendations | user_id, business_id, text, date |

> **Note:** Due to dataset size, raw JSON files are not included in this repo. Download them directly from the Kaggle link above.

---

## Tech Stack

| Stage | Tools |
|-------|-------|
| Big Data Processing | PySpark (Apache Spark) |
| Cloud Compute | Google Colab |
| Storage | Google Drive |
| Analysis | PySpark, Pandas, SQL |
| Machine Learning | PySpark MLlib |
| Streaming Simulation | Python (Kafka-like producer-consumer) |
| Visualisation | Tableau Desktop |
| Supporting Libraries | Matplotlib, Seaborn |
| Language | Python 3 |

---

## Project Pipeline

```
Raw JSON Data (Google Drive)
        ↓
  Data Ingestion (PySpark)
        ↓
  Data Preprocessing (PySpark)
  - Null handling
  - Feature engineering
  - Normalisation
        ↓
  Descriptive Analysis (PySpark + Pandas + SQL)
        ↓
  Predictive Modelling (PySpark MLlib)
  - Business success classification
  - Foot traffic classification
        ↓
  Streaming Simulation (Kafka-like)
        ↓
  Export cleaned CSVs → Tableau
        ↓
  Interactive Dashboards (Tableau)
```

---

## Data Processing

### Ingestion
- Datasets mounted from Google Drive and loaded into PySpark DataFrames
- Schema inspection and row count validation performed on both business and check-in datasets

### Preprocessing
- Handled missing/null values across both datasets
- Normalised city and state fields for consistency
- Engineered temporal features from check-in timestamps (year, day of week)
- Counted check-ins per business to derive foot traffic metrics
- Created binary labels: **business success** (high vs low rating) and **foot traffic** (high vs low check-ins)

### Descriptive Analysis
Key analyses performed using PySpark, Pandas, and SQL:
- Rating distribution across all businesses
- Most popular business categories
- Correlation between stars and review count
- Check-in patterns by year and day of week
- Business distribution by state and city
- Open vs closed business counts and average ratings
- Average stars by foot traffic level

---

## Machine Learning Models

Two classification tasks were modelled using **PySpark MLlib**.

### Task 1 — Business Success Prediction

Predicts whether a business will achieve high or low ratings based on its attributes.

| Model | AUC |
|-------|-----|
| Logistic Regression | 0.57 |
| Random Forest | 0.62 |

**Key features:** `city_index` (location) and `review_count` were the strongest predictors, highlighting that location and customer visibility are critical to business success.

### Task 2 — Foot Traffic Classification

Predicts whether a business will experience high or low customer foot traffic based on check-in patterns.

| Model | AUC |
|-------|-----|
| Random Forest | ~0.99 |
| Gradient Boosted Trees (GBT) | 0.99 |

Temporal check-in patterns proved highly predictive of customer engagement, making real-time traffic forecasting feasible with this approach.

**Pipeline used:** StringIndexer → VectorAssembler → Model (standard MLlib pipeline)

---

## Real-Time Streaming Simulation

A **Kafka-like producer-consumer simulation** was implemented in Python to demonstrate how real-time data processing would work in a live business context.

- **Producer:** generates synthetic check-in events
- **Consumer:** applies the pre-trained foot traffic model to classify each event as high or low traffic in real time

This simulation shows how businesses could use live check-in data for immediate operational decisions — staff allocation, marketing triggers, and managing peak-hour overcrowding.

---

## Tableau Dashboards

Three interactive dashboards were built in Tableau Desktop after exporting cleaned data from PySpark as CSV files. Business and check-in datasets were related via `business_id` using Tableau's relationship model (avoiding data duplication).

### Dashboard 1 — Business Success Dashboard
- Business rating distribution
- Top business categories
- Open vs closed business comparison
- Geographic map of businesses (colour = rating, size = review count)
- KPI indicators: total businesses, average rating

### Dashboard 2 — Business Performance Dashboard
- Ratings, reviews, and foot traffic compared across categories and states
- Average rating by category
- Review count vs state

### Dashboard 3 — Foot Traffic Insights Dashboard
- Average check-ins by category
- Check-in trends by day of week and year
- Foot traffic vs rating relationship
- High rating vs high traffic matrix
- KPI indicators: average foot traffic, average business rating

All dashboards include filters for **state**, **category**, and **day of week**.

---

## Key Findings

- Business ratings are skewed toward higher values, clustering between **3.5–4.5 stars**, indicating generally positive customer experiences on Yelp
- **Restaurants, Food, and Shopping** dominate the platform — category selection significantly impacts business visibility
- Geographic concentration is strongest in **Pennsylvania and Florida**, with Philadelphia hosting the highest number of businesses
- **Weekends (especially Sundays)** show the highest customer foot traffic
- High-traffic businesses achieve an average rating of **3.88** vs **3.59** for low-traffic businesses, confirming a strong engagement-quality relationship
- **Location (city)** and **review count** are the strongest predictors of business success
- Temporal check-in patterns alone achieve **AUC of 0.99** for foot traffic prediction

---

## Project Structure

```
yelp-business-analytics/
│
├── yelp_analysis.ipynb          # Main Google Colab notebook (PySpark pipeline)
├── yelp_dashboard.twbx          # Tableau workbook
├── README.md
│
└── appendices/                  # Supporting screenshots (optional)
```

---

## How to Run

1. Download the Yelp dataset from [Kaggle](https://www.kaggle.com/datasets/adamamer2001/yelp-complete-open-dataset2024) and upload to Google Drive
2. Open `yelp_analysis.ipynb` in Google Colab
3. Mount your Google Drive and update the file paths in the notebook
4. Run all cells in order — the notebook handles installation of PySpark automatically
5. Export the cleaned CSV outputs and load into Tableau using `yelp_dashboard.twbx`

---
 
