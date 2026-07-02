Enterprise Customer Churn Feature Pipeline

An end-to-end, hardware-optimized data engineering pipeline designed to ingest raw customer behavior data, resolve structural missingness using an automated decision matrix, neutralize outliers, engineer high-quality features, and generate a production-ready feature store with zero-leakage Point-in-Time retrieval.

The complete pipeline is implemented modularly inside a single Jupyter Notebook:

project1.ipynb
📌 Table of Contents
Overview
Pipeline Architecture
Module 1 – Input Fidelity & Data Resiliency
Module 2 – Processing Engine
Module 3 – Output Engine
Data Schema Evolution
Generated Outputs
Notebook Execution Flow
Technologies Used
🚀 Overview

This project demonstrates how enterprise-scale customer churn datasets can be transformed into production-ready machine learning assets through automated preprocessing and feature engineering.

The pipeline focuses on:

Intelligent missing value handling
Statistical outlier treatment
Feature engineering
Multicollinearity removal
Feature scaling
Enterprise Feature Store simulation
Point-in-Time (Leakage-Free) retrieval
🏗 Pipeline Architecture
Raw Customer Data
        │
        ▼
┌──────────────────────────┐
│ Module 1                │
│ Input Fidelity          │
├──────────────────────────┤
│ • Missing Value Routing │
│ • IQR Winsorization     │
│ • Feature Engineering   │
└──────────────────────────┘
        │
        ▼
┌──────────────────────────┐
│ Module 2                │
│ Process Engine          │
├──────────────────────────┤
│ • One-Hot Encoding      │
│ • Dummy Trap Removal    │
│ • Correlation Pruning   │
│ • Z-Score Scaling       │
└──────────────────────────┘
        │
        ▼
┌──────────────────────────┐
│ Module 3                │
│ Output Engine           │
├──────────────────────────┤
│ • Metadata Registry     │
│ • Feature Views         │
│ • Point-in-Time Join    │
│ • Production Export     │
└──────────────────────────┘
📦 Module 1 – Input Fidelity & Automated Data Resiliency

This module establishes statistical balance by cleaning structural noise and engineering high-impact behavioral features.

1. Dynamic Missing Data Decision Matrix

Instead of applying a single imputation strategy, each feature is automatically routed according to its missing percentage.

Missing Percentage	Strategy	Example
< 5%	Row Deletion (dropna)	avg_order_value
5% – 20%	Median Imputation	engagement_score
> 20%	KNN Imputation (5 Neighbors)	discount_usage_rate
2. Non-Parametric Outlier Winsorization

Extreme values are capped using the Interquartile Range (IQR) method.

Applied to features such as:

avg_order_value
days_since_last_purchase
total_orders

IQR boundaries:

Lower Fence = Q1 − 1.5 × IQR

Upper Fence = Q3 + 1.5 × IQR

This preserves every observation while preventing model instability.

3. Domain-Driven Feature Engineering

Three new behavioral metrics are engineered:

order_value_density

Average customer spending per order.

inactivity_ratio

Normalized customer inactivity relative to account age.

support_friction_index

Behavioral risk score combining:

Customer support tickets
Purchase inactivity
⚙ Module 2 – Process Engine & Vectorized Computation

This module converts cleaned data into a mathematically optimized feature matrix.

1. One-Hot Encoding

Categorical variables are transformed using

pd.get_dummies()

To avoid the Dummy Variable Trap,

drop_first=True

is enabled.

2. Intelligent Multicollinearity Pruning

The pipeline computes an upper-triangle Pearson Correlation Matrix.

If

Correlation > 0.80

the pipeline:

Compares both features against the target label.
Computes their correlation with
churned_Yes
Removes the feature having the weaker predictive relationship.
3. Vectorized Z-Score Standardization

Continuous variables are standardized using

μ = 0
σ = 1

through Pandas vectorized broadcasting.

This eliminates row-wise loops and improves computational efficiency.

🏭 Module 3 – Output Engine & Feature Store Infrastructure

This module simulates an enterprise Feature Store similar to Feast.

1. Entity Alignment

The pipeline restores:

Customer_ID

and injects

event_timestamp

to simulate real production event logs.

2. Logical Feature Views

Features are organized into separate views.

User Demographics View

Contains slow-changing customer information.

Example:

Age
Gender
Region
User Behavioral View

Contains frequently changing metrics such as

Purchase history
Engagement
Spending behavior
3. Point-in-Time Query Engine

A leakage-proof feature retrieval mechanism is implemented using

pd.merge_asof()

This guarantees that training data only accesses customer information that existed on or before the requested historical timestamp.

No future information leaks into model training.

📊 Data Schema Evolution
Stage	Columns	Major Transformations	Output File
Raw Ingestion	16	Original customer dataset	customer_churn.csv
Module 1	18	Missing values resolved, outliers capped, 3 engineered features	processed_module1_output.csv
Module 2	14	Removed ID, dropped highly correlated features, Z-score scaling	processed_module2_output.csv
Module 3	16	Restored ID, added timestamps, Point-in-Time retrieval	final_production_feature_store_output.csv
📁 Generated Outputs
customer_churn.csv

processed_module1_output.csv

processed_module2_output.csv

final_production_feature_store_output.csv
▶ Notebook Execution Flow

Execute the notebook sequentially.

Cells 1–2
Load dataset
Inject artificial missing values
Cells 3–5

Run the automated Missing Value Decision Matrix.

Deletion
      ↓
Median
      ↓
KNN Imputation
Cell 6

Perform IQR-based outlier clipping.

Cell 7

Generate engineered behavioral features.

Save

processed_module1_output.csv
Cell 8

Perform One-Hot Encoding.

Remove Dummy Variable Trap.

Cell 9

Run the Target-Aware Multicollinearity Engine.

Cell 10

Perform vectorized Z-Score Standardization.

Export

processed_module2_output.csv
Cells 11–12
Restore entity IDs
Generate timestamps
Register logical feature views
Cell 13

Execute the Point-in-Time feature retrieval simulation.

Generate

final_production_feature_store_output.csv
🛠 Technologies Used
Python
Pandas
NumPy
Scikit-learn
Jupyter Notebook
🎯 Key Features
Dynamic Missing Value Routing
Automated KNN Imputation
Median Imputation
Statistical Outlier Winsorization
Behavioral Feature Engineering
One-Hot Encoding
Dummy Variable Trap Removal
Target-Aware Multicollinearity Pruning
Vectorized Z-Score Standardization
Feature Store Simulation
Metadata Registry
Point-in-Time Retrieval
Leakage-Free Feature Engineering
Production-Ready Data Assets
📌 Final Output

The final deliverable is a production-ready customer feature store that is:

Clean
Statistically balanced
Scaled
Leakage-free
Chronologically trackable
Ready for Machine Learning model training and deployment.
