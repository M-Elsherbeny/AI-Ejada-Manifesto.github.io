# 🧪 AI Component Development Tracker – Template

> Use this document to continuously capture progress for components still in development.  
> Update iteratively as work progresses across data, modeling, and code.

---

## 🔹 Component Overview

| Field            | Example                        |
|------------------|--------------------------------|
| Component Name   | Personalization Engine         |
| Project          | Robo Advisor                   |
| Status           | In Development                 |
| Target Version   | v0.1.0-alpha                   |
| Last Updated     | 2025-06-01                     |
| Owner / Lead     | Jane Doe (ML Engineer)         |

---

## 📊 Data Preparation (Ongoing)

> Track how data is collected, cleaned, and analyzed during initial exploration.

### 1. **Data Collection**
- [x] Source: Internal User Feedback Logs (CSV)
- [x] Collected: April 2025
- [x] Volume: ~15,000 user logs
- [x] Quality Metrics: 
   - [x] Quality: 80% of data is complete and relevant
   - [x] Diversity: 100% of data is from the US

### 2. Data Augmentation
- [x] Quality: 80% of data is complete and relevant
- [x] Method: 10% of data is augmented with synthetic data

### 3. **Exploratory Data Analysis (EDA)**
> Document EDA notebooks, statistical summaries, and any initial insights.

| Insight # | Summary | Chart |
|-----------|---------|-------|
| EDA-1     | 65% of users rate over 3 risk level | Histogram of user ratings |
| EDA-2     | Drop-off in asset engagement after 10 options shown | Line chart of asset engagement |

- EDA Notebook: `/notebooks/personalization_eda.ipynb`

### 4. **Preprocessing**
> Detail any transformations, filters, or encodings applied. Include script logs.

| Preprocessing steps                     | Reason                                           | Columns               |
|--------------------------|--------------------------------------------------|-----------------------|
| 1. Remove duplicates | To ensure each entry is unique and prevent skewing results | user_id               |
| 2. Remove outliers | To maintain the integrity of the data and avoid distortion of analysis | asset_value           |
| 3. Remove missing values | To ensure a complete dataset for accurate modeling | user_rating           |
| 4. Encode categorical variables | To convert categorical data into a numerical format suitable for modeling | user_category         |
| 5. Scale numerical variables | To standardize the range of numerical features for better model performance | engagement_score      |
| 6. Feature engineering | To create new features that enhance model predictive power | new_feature_1, new_feature_2 |
| 7. Log transformation | To reduce skewness and stabilize variance in the data | asset_value           |


---

## 🧪 Experimentation & Modeling (Ongoing)

> Continuously record modeling activity, hypotheses, results, and decisions.

### 1. **Experiments Summary Table**

| Exp ID | Model              | Hypothesis                          | Outcome         | MLFlow Link                                   | Comments                                                                                     |
|--------|--------------------|-------------------------------------|------------------|------------------------------------------------|----------------------------------------------------------------------------------------------|    
| EXP-001| Logistic Regression | Simpler model is sufficient for ranking | Underfit        | `http://41.33.183.2:4049/#/experiments/exp_001` | Initial results indicate that the model struggles with complex patterns; consider adding interaction terms. |
| EXP-002| Random Forest       | Nonlinear patterns matter           | Better precision  | `http://41.33.183.2:4049/#/experiments/exp_002` | Achieved a 10% increase in precision; further tuning of hyperparameters is recommended for optimal performance. |

#### 1.1 **Experiment Details < should included in the mlflow >**

| Parameter                |                                                                               |
|--------------------------|-------------------------------------------------------------------------------|
| Model                    |                                                                               |
| Hyperparameters           |                                                                               |
| Metrics                  |                                                                               |
| Training Time            |                                                                               |
| Model Size               |                                                                               |
| Data Pointer to DVC      |                                                                               |
| Code Pointer to Git      |                                                                               |
                                                                             



---

## 🛠 Code Development (Ongoing)

### 1. **Versioning**
- Git Repo: `github.com/robo-advisor/personalization-engine`
- Branching Strategy: `main`, `dev`, `experiment/*`

## 2. 🧱 Architectural Decisions (ADR Format)

> Use Architecture Decision Records (ADRs) to document and track key technical decisions.  
> Each ADR should clearly communicate the reasoning, the trade-offs considered, and the resulting consequences.

> **ADR Format:**
- `<ID>. <Title>`
- **Date**: When the decision was made.
- **Status**: Proposed / Accepted / Deprecated / Superseded
- **Context**: Objective summary of the problem or need.
- **Alternatives**: Other options considered, with pros/cons.
- **Decision**: The choice made, starting with “We will…” or “We have agreed to…”
- **Consequences**: Immediate or long-term effects of the decision.

---

### ADR-101. Use Sklearn Pipelines for Modular Experimentation

- **Date**: 2025-05-10  
- **Status**: Accepted

**Context**:  
We need a flexible and reproducible framework for applying data preprocessing and modeling in a consistent manner across various experiments.

**Alternatives**:
1. **Manual application of each step in notebooks**  
   - ✅ Easy to test in isolation  
   - ❌ Hard to maintain or share logic; error-prone  
2. **Use of third-party orchestration libraries (e.g., Kedro, Metaflow)**  
   - ✅ Rich feature set for production use  
   - ❌ Overkill for current scope, learning curve

**Decision**:  
We have agreed to use `sklearn.pipeline.Pipeline` as the central framework for chaining preprocessing and modeling steps. It provides a simple, built-in interface that supports encapsulation and consistency.

**Consequences**:  
- Improved reproducibility and modularity  
- Requires all future model implementations to conform to `fit/transform` interface  

---

### ADR-102. Separate Data Transformation into a Dedicated Module

- **Date**: 2025-05-13  
- **Status**: Proposed

**Context**:  
Preprocessing logic is duplicated across notebooks, training code, and inference paths, leading to inconsistencies.

**Alternatives**:
1. **Keep logic inline in each notebook/script**  
   - ✅ Simple to start  
   - ❌ Poor maintainability, high risk of drift  
2. **Use sklearn ColumnTransformer or FeatureUnion**  
   - ✅ Integrated with sklearn  
   - ❌ Too rigid for our custom transformations

**Decision**:  
We will move preprocessing logic into a reusable module: `data_transformer.py`, organized into individual, testable functions.

**Consequences**:  
- Streamlines testing and reuse  
- Requires restructuring existing notebooks and pipeline scripts  

---

### ADR-103. Add Cold Start Segment Detection to Pipeline

- **Date**: 2025-05-25  
- **Status**: Proposed

**Context**:  
The model performs poorly for users with no history (cold start). These users need to be routed differently.

**Alternatives**:
1. **Ignore cold start and use default model predictions**  
   - ✅ Simpler pipeline  
   - ❌ Leads to low engagement for new users  
2. **Train a separate model for cold start users**  
   - ✅ More accurate for those users  
   - ❌ Data volume may be insufficient for a robust model

**Decision**:  
We will introduce a segmentation step in the pipeline that flags cold start users and routes them to a fallback scoring strategy (e.g., top-rated assets).

**Consequences**:  
- Adds branching logic to pipeline  
- Requires clear definition of "cold start" threshold  
- Enables improved personalization from day one for new users  

---

> ADRs should be saved under `/docs/adr/` with filename format:  
> `NNN-title-of-decision.md`



### 3. **Decision Log**
> Use for major changes, blockers, trade-offs.

| Date       | Decision                | Outcome                                           |
|------------|-----------------------------------|--------------------------------------------------|
| 2025-05-20 | XGBoost vs Random Forest          | Random Forest chosen due to lower latency on test API |

### 4. **Diagram**
> Use for visualizing the current state of the architecture, data flow, components and decisions.

- Component Diagram: `/diagrams/component_diagram.png`
- Sequence Diagram: `/diagrams/sequence_diagram.png`
- Data Flow Diagram: `/diagrams/data_flow_diagram.png`

---

## 📌 Next Steps & TODOs

- [ ] Finalize preprocessing module and unit tests
- [ ] Run EXP-003 with XGBoost on Segment B
- [ ] Prepare minimal API for offline model scoring

---

## 👥 Contributors

| Name         | Role             |
|--------------|------------------|
| Jane Doe     | TL  |
| Ahmed Ali   | ML Engineer    |
| Priya Rao  | ML Engineer  |
| Chris Wang | ML Engineer |

---

> _Reminder: Keep updating this log per sprint or major commit. Helps ensure transparency, continuity, and handovers._

