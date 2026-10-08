# 🚀 Responsible Customer Churn Prediction

## 📌 Project Overview

This project evaluates whether **Machine Learning is justified for a real customer churn decision** before moving directly into model training.

The project follows a responsible ML workflow:

```text
Business Decision
       ↓
Problem Framing
       ↓
Responsible Data Card
       ↓
Data Quality Checks
       ↓
Non-ML Baseline
       ↓
ML Baseline Models
       ↓
Risk & Cost Analysis
       ↓
Human Review
       ↓
Monitoring
       ↓
Rollback / Fallback
```

The goal is not only to build an accurate model, but to understand whether the model can be used **safely, fairly, and practically**.

---

# 🎯 Business Problem

Customer churn can result in lost revenue and reduced customer lifetime value.

The business decision is:

> **Which customers should be considered for retention outreach because they may be at higher risk of churn?**

The ML system should provide **decision support**, not automatically make irreversible customer decisions.

---

# 🤖 Is Machine Learning Justified?

### Decision: Yes, for further investigation.

Machine Learning can potentially identify relationships between customer characteristics and churn that are difficult to capture using a simple rule.

However, ML should only be adopted if it:

* Performs better than a simple baseline.
* Provides useful predictions.
* Has acceptable false-positive and false-negative costs.
* Does not introduce unacceptable bias.
* Can be monitored.
* Has a safe fallback strategy.

### Important limitation

The supplied dataset contains only **12 customer records**.

Therefore, the current experiment demonstrates the **ML workflow and responsible problem framing**, but it does **not** provide sufficient evidence for production deployment.

---

# 📊 Dataset

The project uses the supplied:

```text
customer-churn-training.csv
```

### Dataset structure

| Feature             | Description               |
| ------------------- | ------------------------- |
| `customer_id`       | Customer identifier       |
| `tenure_months`     | Customer tenure           |
| `support_tickets`   | Number of support tickets |
| `monthly_spend_inr` | Monthly customer spending |
| `last_login_days`   | Days since last login     |
| `plan_type`         | Customer plan category    |
| `churned`           | Target variable           |

`customer_id` is used only as an identifier and is excluded from model training.

---

# 🧩 ML Problem Framing

| Component           | Definition                                        |
| ------------------- | ------------------------------------------------- |
| Business Decision   | Identify customers for possible retention review  |
| Prediction Target   | `churned`                                         |
| Positive Class      | `1` = churned                                     |
| Negative Class      | `0` = not churned                                 |
| Unit of Observation | Individual customer                               |
| Action              | Retention review/outreach                         |
| Non-ML Baseline     | Majority-class prediction                         |
| ML Models           | Logistic Regression, Decision Tree, Random Forest |

---

# 🗂️ Responsible Data Card

## 1. Dataset Purpose

The dataset supports experimentation with **customer churn prediction**.

The intended decision is to identify customers who may benefit from retention review.

The model must **not** be used by itself to:

* Terminate customer services.
* Increase prices.
* Deny services.
* Make irreversible customer decisions.
* Automatically apply punitive actions.

---

## 2. Provenance and Permission

The dataset was supplied for this project as:

```text
customer-churn-training.csv
```

The CSV itself does not establish:

* Original collection methodology
* Customer consent
* Licensing restrictions
* Data ownership
* Representativeness of the population

Therefore, these properties must be verified from the original data provider before real-world deployment or redistribution.

---

# 👥 Population and Representation

The supplied dataset contains:

```text
Total customers: 12
Churned:         5
Not churned:     7
```

Churn distribution:

```text
Not Churned  ███████  7
Churned      █████    5
```

Because the dataset is extremely small, it may not represent the full customer population.

Potential limitations include:

* Small sample size
* Unknown sampling process
* Unknown geographic representation
* Unknown demographic representation
* Unknown time period
* Unknown customer segment coverage

Therefore, additional representative data is required before deployment.

---

# 🔍 Features and Target

### Features

The dataset contains:

```text
customer_id
tenure_months
support_tickets
monthly_spend_inr
last_login_days
plan_type
```

### Target

```text
churned
```

where:

```text
1 → Customer churned
0 → Customer did not churn
```

### Leakage Risk

A feature should only be used if it would have been available **at the exact moment the prediction is made**.

Potential future-derived information must not be included.

Examples of dangerous leakage could include:

```text
Cancellation date
Final account status
Post-churn support activity
Post-churn refunds
```

The current CSV does not contain timestamps, so complete temporal leakage validation cannot be performed from this file alone.

---

# 🔐 Sensitive Attributes and Proxy Risk

The supplied dataset does not provide an explicit protected-attribute audit.

However, variables such as:

* Spending
* Tenure
* Support behavior
* Login behavior
* Plan type

could potentially act as proxies for customer circumstances.

If protected attributes are introduced later, the model should be evaluated for group-level differences in:

* False-positive rate
* False-negative rate
* Precision
* Recall
* Calibration

---

# 🧪 Data Quality Checks

The dataset was checked for:

### Missing Values

```text
0 missing values
```

### Duplicate Rows

```text
0 duplicate rows
```

### Dataset Size

```text
12 rows
7 columns
```

### Target Balance

```text
Churned:      5
Not churned: 7
```

The small sample size remains the most important limitation.

---

# 📏 Non-ML Baseline

Before ML, a majority-class baseline is established.

Since the majority class is:

```text
Not Churned = 7 / 12
```

the baseline predicts:

```text
Every customer → Not Churned
```

### Baseline performance

```text
Accuracy = 58.3%
```

However, this baseline has:

```text
Recall = 0
```

for the churn class because it never predicts churn.

---

# 🤖 ML Models

Three baseline models were evaluated:

### 1. Logistic Regression

Provides an interpretable linear baseline.

### 2. Decision Tree

Can model nonlinear relationships and is relatively easy to interpret.

### 3. Random Forest

Uses multiple decision trees to capture more complex patterns.

---

# 📊 Model Results

Using **3-fold stratified cross-validation**:

| Model               | Accuracy | Precision | Recall |    F1 | ROC-AUC |
| ------------------- | -------: | --------: | -----: | ----: | ------: |
| Majority Baseline   |    58.3% |      0.0% |   0.0% |  0.0% |       — |
| Logistic Regression |     100% |      100% |   100% |  100% |   1.000 |
| Decision Tree       |    91.7% |     83.3% |   100% | 90.9% |   0.929 |
| Random Forest       |     100% |      100% |   100% |  100% |   1.000 |

---

# ⚠️ Important Model Interpretation

The 100% cross-validation scores for Logistic Regression and Random Forest may initially look excellent.

However:

> **The dataset contains only 12 observations.**

Therefore, these results should **not** be interpreted as evidence that the model will achieve 100% performance on new customers.

The correct conclusion is:

```text
Strong signal in this small dataset
              +
Extremely limited sample size
              =
Further investigation required
```

A much larger dataset and a genuine future holdout are required before making deployment claims.

---

# 💰 False Positive and False Negative Costs

## False Positive

The model predicts:

```text
Customer → High Churn Risk
```

but the customer would not have churned.

Potential costs:

* Unnecessary retention campaign
* Discount cost
* Employee time
* Customer annoyance

---

## False Negative

The model predicts:

```text
Customer → Low Churn Risk
```

but the customer actually churns.

Potential costs:

* Lost customer
* Lost revenue
* Lost customer lifetime value

Because these costs can differ, the final model threshold should be selected using **business cost**, not accuracy alone.

---

# 🧑‍💼 Human Review

The system should include an abstention/human-review mechanism.

Example:

```text
                 Churn Probability
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Low Risk       Uncertain      High Risk
          │              │              │
          ↓              ↓              ↓
      No Action     Human Review   Retention Review
```

The model should support human decision-making rather than automatically determining customer treatment.

---

# 📈 Monitoring Plan

If the model is eventually deployed, monitor:

## Data

* Missing values
* Feature distributions
* New categories
* Data drift
* Unexpected values

## Model

* Precision
* Recall
* F1
* ROC-AUC
* False-positive rate
* False-negative rate
* Calibration

## Business

* Churn rate
* Retention rate
* Retention campaign cost
* Customer lifetime value
* Retention ROI

---

# 🔄 Rollback Conditions

The ML system should be disabled and returned to the non-ML strategy if:

* Data quality becomes unreliable.
* Significant model degradation occurs.
* Data leakage is discovered.
* Major data drift occurs.
* False-positive costs become unacceptable.
* False-negative costs become unacceptable.
* Significant fairness concerns appear.
* The retention intervention no longer produces business value.

### Fallback

```text
ML System
    ↓
Problem Detected
    ↓
Disable ML Decision Support
    ↓
Return to Non-ML Retention Strategy
    ↓
Investigate + Revalidate
```

---

# ⚠️ Risk Register

| ID  | Risk               | Impact | Mitigation                       |
| --- | ------------------ | ------ | -------------------------------- |
| R1  | Data leakage       | High   | Verify feature availability      |
| R2  | Very small dataset | High   | Collect more representative data |
| R3  | Overfitting        | High   | Use larger future holdout        |
| R4  | Class imbalance    | Medium | Monitor recall/F1/PR-AUC         |
| R5  | False positives    | Medium | Cost-based threshold             |
| R6  | False negatives    | High   | Monitor recall                   |
| R7  | Proxy bias         | High   | Group-level evaluation           |
| R8  | Data drift         | High   | Continuous monitoring            |
| R9  | Poor provenance    | High   | Verify source and permission     |
| R10 | Model misuse       | High   | Human review                     |
| R11 | Model degradation  | High   | Monitoring + rollback            |

---

# 🧠 Responsible AI Decision

## Final Decision

### ✅ ML is justified for further investigation.

### ❌ ML is not justified for production deployment using this dataset alone.

The current dataset demonstrates that ML models can identify strong patterns in the supplied sample.

However, responsible deployment requires:

1. A much larger dataset.
2. Clearly documented data provenance.
3. A defined prediction horizon.
4. Time-aware validation.
5. A future holdout dataset.
6. Business cost estimates.
7. Fairness evaluation.
8. Human review.
9. Monitoring.
10. Rollback procedures.

---

# 📁 Project Structure

```text
responsible-customer-churn/
│
├── README.md
│
├── requirements.txt
│
├── data/
│   └── customer-churn-training.csv
│
├── notebooks/
│   └── customer_churn_baseline.ipynb
│
└── docs/
    ├── ml_problem_framing_memo.md
    ├── responsible_data_card.md
    └── risk_register.md
```

---

# 🛠️ Technologies Used

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Jupyter Notebook

---

# 🚀 How to Run

Clone the repository:

```bash
git clone <your-github-repository-url>
cd responsible-customer-churn
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Start Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
notebooks/customer_churn_baseline.ipynb
```

Run all cells.

---

# 📋 Project Deliverables

This project satisfies the requested proof requirements:

### ✅ 1. ML Problem-Framing Memo

Documents:

* Business decision
* Prediction target
* Unit of observation
* Action window
* Non-ML baseline
* ML justification

### ✅ 2. Responsible Data Card

Documents:

* Dataset purpose
* Provenance
* Permission/consent considerations
* Population and representation
* Features and target
* Leakage risks
* Data quality
* Risks and safeguards
* Evaluation plan

### ✅ 3. Baseline Notebook

Includes:

* Data loading
* Data validation
* Target analysis
* Majority baseline
* Logistic Regression
* Decision Tree
* Random Forest
* Cross-validation
* Precision
* Recall
* F1
* ROC-AUC
* Confusion matrices

### ✅ 4. Risk Register

Documents:

* Data risks
* Model risks
* Fairness risks
* Business risks
* Mitigation
* Monitoring
* Rollback

---

# 🎓 Key Learning

This project demonstrates that responsible ML begins **before model training**.

Instead of asking only:

> **"Can I build a model?"**

the project asks:

> **"Should ML be used for this decision?"**

> **"What could go wrong?"**

> **"What are the costs of incorrect predictions?"**

> **"When should the model abstain?"**

> **"How will humans review uncertain cases?"**

> **"What happens if the model fails?"**

---

# 💡 Responsible AI Principle

> **The model predicts churn risk; it does not decide the customer's fate.**

---

# 👨‍💻 Author

**Aditya Patil**

B.Sc. Computer Science | AI/ML & Data Science Fresher

### Skills

* Python
* Machine Learning
* Data Science
* Generative AI
* NLP
* Agentic AI
* Responsible AI
* SQL
* Pandas
* Scikit-learn

---

# ⭐ Project Status

**Completed — ML Problem Framing & Responsible Data Card**

This project demonstrates practical experience in:

**Problem Framing → Responsible Data Analysis → Baseline Modeling → Risk Assessment → Evaluation → Human Review → Monitoring → Rollback**
# 🚀 Responsible Customer Churn Prediction

## 📌 Project Overview

This project evaluates whether **Machine Learning is justified for a real customer churn decision** before moving directly into model training.

The project follows a responsible ML workflow:

```text
Business Decision
       ↓
Problem Framing
       ↓
Responsible Data Card
       ↓
Data Quality Checks
       ↓
Non-ML Baseline
       ↓
ML Baseline Models
       ↓
Risk & Cost Analysis
       ↓
Human Review
       ↓
Monitoring
       ↓
Rollback / Fallback
```

The goal is not only to build an accurate model, but to understand whether the model can be used **safely, fairly, and practically**.

---

# 🎯 Business Problem

Customer churn can result in lost revenue and reduced customer lifetime value.

The business decision is:

> **Which customers should be considered for retention outreach because they may be at higher risk of churn?**

The ML system should provide **decision support**, not automatically make irreversible customer decisions.

---

# 🤖 Is Machine Learning Justified?

### Decision: Yes, for further investigation.

Machine Learning can potentially identify relationships between customer characteristics and churn that are difficult to capture using a simple rule.

However, ML should only be adopted if it:

* Performs better than a simple baseline.
* Provides useful predictions.
* Has acceptable false-positive and false-negative costs.
* Does not introduce unacceptable bias.
* Can be monitored.
* Has a safe fallback strategy.

### Important limitation

The supplied dataset contains only **12 customer records**.

Therefore, the current experiment demonstrates the **ML workflow and responsible problem framing**, but it does **not** provide sufficient evidence for production deployment.

---

# 📊 Dataset

The project uses the supplied:

```text
customer-churn-training.csv
```

### Dataset structure

| Feature             | Description               |
| ------------------- | ------------------------- |
| `customer_id`       | Customer identifier       |
| `tenure_months`     | Customer tenure           |
| `support_tickets`   | Number of support tickets |
| `monthly_spend_inr` | Monthly customer spending |
| `last_login_days`   | Days since last login     |
| `plan_type`         | Customer plan category    |
| `churned`           | Target variable           |

`customer_id` is used only as an identifier and is excluded from model training.

---

# 🧩 ML Problem Framing

| Component           | Definition                                        |
| ------------------- | ------------------------------------------------- |
| Business Decision   | Identify customers for possible retention review  |
| Prediction Target   | `churned`                                         |
| Positive Class      | `1` = churned                                     |
| Negative Class      | `0` = not churned                                 |
| Unit of Observation | Individual customer                               |
| Action              | Retention review/outreach                         |
| Non-ML Baseline     | Majority-class prediction                         |
| ML Models           | Logistic Regression, Decision Tree, Random Forest |

---

# 🗂️ Responsible Data Card

## 1. Dataset Purpose

The dataset supports experimentation with **customer churn prediction**.

The intended decision is to identify customers who may benefit from retention review.

The model must **not** be used by itself to:

* Terminate customer services.
* Increase prices.
* Deny services.
* Make irreversible customer decisions.
* Automatically apply punitive actions.

---

## 2. Provenance and Permission

The dataset was supplied for this project as:

```text
customer-churn-training.csv
```

The CSV itself does not establish:

* Original collection methodology
* Customer consent
* Licensing restrictions
* Data ownership
* Representativeness of the population

Therefore, these properties must be verified from the original data provider before real-world deployment or redistribution.

---

# 👥 Population and Representation

The supplied dataset contains:

```text
Total customers: 12
Churned:         5
Not churned:     7
```

Churn distribution:

```text
Not Churned  ███████  7
Churned      █████    5
```

Because the dataset is extremely small, it may not represent the full customer population.

Potential limitations include:

* Small sample size
* Unknown sampling process
* Unknown geographic representation
* Unknown demographic representation
* Unknown time period
* Unknown customer segment coverage

Therefore, additional representative data is required before deployment.

---

# 🔍 Features and Target

### Features

The dataset contains:

```text
customer_id
tenure_months
support_tickets
monthly_spend_inr
last_login_days
plan_type
```

### Target

```text
churned
```

where:

```text
1 → Customer churned
0 → Customer did not churn
```

### Leakage Risk

A feature should only be used if it would have been available **at the exact moment the prediction is made**.

Potential future-derived information must not be included.

Examples of dangerous leakage could include:

```text
Cancellation date
Final account status
Post-churn support activity
Post-churn refunds
```

The current CSV does not contain timestamps, so complete temporal leakage validation cannot be performed from this file alone.

---

# 🔐 Sensitive Attributes and Proxy Risk

The supplied dataset does not provide an explicit protected-attribute audit.

However, variables such as:

* Spending
* Tenure
* Support behavior
* Login behavior
* Plan type

could potentially act as proxies for customer circumstances.

If protected attributes are introduced later, the model should be evaluated for group-level differences in:

* False-positive rate
* False-negative rate
* Precision
* Recall
* Calibration

---

# 🧪 Data Quality Checks

The dataset was checked for:

### Missing Values

```text
0 missing values
```

### Duplicate Rows

```text
0 duplicate rows
```

### Dataset Size

```text
12 rows
7 columns
```

### Target Balance

```text
Churned:      5
Not churned: 7
```

The small sample size remains the most important limitation.

---

# 📏 Non-ML Baseline

Before ML, a majority-class baseline is established.

Since the majority class is:

```text
Not Churned = 7 / 12
```

the baseline predicts:

```text
Every customer → Not Churned
```

### Baseline performance

```text
Accuracy = 58.3%
```

However, this baseline has:

```text
Recall = 0
```

for the churn class because it never predicts churn.

---

# 🤖 ML Models

Three baseline models were evaluated:

### 1. Logistic Regression

Provides an interpretable linear baseline.

### 2. Decision Tree

Can model nonlinear relationships and is relatively easy to interpret.

### 3. Random Forest

Uses multiple decision trees to capture more complex patterns.

---

# 📊 Model Results

Using **3-fold stratified cross-validation**:

| Model               | Accuracy | Precision | Recall |    F1 | ROC-AUC |
| ------------------- | -------: | --------: | -----: | ----: | ------: |
| Majority Baseline   |    58.3% |      0.0% |   0.0% |  0.0% |       — |
| Logistic Regression |     100% |      100% |   100% |  100% |   1.000 |
| Decision Tree       |    91.7% |     83.3% |   100% | 90.9% |   0.929 |
| Random Forest       |     100% |      100% |   100% |  100% |   1.000 |

---

# ⚠️ Important Model Interpretation

The 100% cross-validation scores for Logistic Regression and Random Forest may initially look excellent.

However:

> **The dataset contains only 12 observations.**

Therefore, these results should **not** be interpreted as evidence that the model will achieve 100% performance on new customers.

The correct conclusion is:

```text
Strong signal in this small dataset
              +
Extremely limited sample size
              =
Further investigation required
```

A much larger dataset and a genuine future holdout are required before making deployment claims.

---

# 💰 False Positive and False Negative Costs

## False Positive

The model predicts:

```text
Customer → High Churn Risk
```

but the customer would not have churned.

Potential costs:

* Unnecessary retention campaign
* Discount cost
* Employee time
* Customer annoyance

---

## False Negative

The model predicts:

```text
Customer → Low Churn Risk
```

but the customer actually churns.

Potential costs:

* Lost customer
* Lost revenue
* Lost customer lifetime value

Because these costs can differ, the final model threshold should be selected using **business cost**, not accuracy alone.

---

# 🧑‍💼 Human Review

The system should include an abstention/human-review mechanism.

Example:

```text
                 Churn Probability
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Low Risk       Uncertain      High Risk
          │              │              │
          ↓              ↓              ↓
      No Action     Human Review   Retention Review
```

The model should support human decision-making rather than automatically determining customer treatment.

---

# 📈 Monitoring Plan

If the model is eventually deployed, monitor:

## Data

* Missing values
* Feature distributions
* New categories
* Data drift
* Unexpected values

## Model

* Precision
* Recall
* F1
* ROC-AUC
* False-positive rate
* False-negative rate
* Calibration

## Business

* Churn rate
* Retention rate
* Retention campaign cost
* Customer lifetime value
* Retention ROI

---

# 🔄 Rollback Conditions

The ML system should be disabled and returned to the non-ML strategy if:

* Data quality becomes unreliable.
* Significant model degradation occurs.
* Data leakage is discovered.
* Major data drift occurs.
* False-positive costs become unacceptable.
* False-negative costs become unacceptable.
* Significant fairness concerns appear.
* The retention intervention no longer produces business value.

### Fallback

```text
ML System
    ↓
Problem Detected
    ↓
Disable ML Decision Support
    ↓
Return to Non-ML Retention Strategy
    ↓
Investigate + Revalidate
```

---

# ⚠️ Risk Register

| ID  | Risk               | Impact | Mitigation                       |
| --- | ------------------ | ------ | -------------------------------- |
| R1  | Data leakage       | High   | Verify feature availability      |
| R2  | Very small dataset | High   | Collect more representative data |
| R3  | Overfitting        | High   | Use larger future holdout        |
| R4  | Class imbalance    | Medium | Monitor recall/F1/PR-AUC         |
| R5  | False positives    | Medium | Cost-based threshold             |
| R6  | False negatives    | High   | Monitor recall                   |
| R7  | Proxy bias         | High   | Group-level evaluation           |
| R8  | Data drift         | High   | Continuous monitoring            |
| R9  | Poor provenance    | High   | Verify source and permission     |
| R10 | Model misuse       | High   | Human review                     |
| R11 | Model degradation  | High   | Monitoring + rollback            |

---

# 🧠 Responsible AI Decision

## Final Decision

### ✅ ML is justified for further investigation.

### ❌ ML is not justified for production deployment using this dataset alone.

The current dataset demonstrates that ML models can identify strong patterns in the supplied sample.

However, responsible deployment requires:

1. A much larger dataset.
2. Clearly documented data provenance.
3. A defined prediction horizon.
4. Time-aware validation.
5. A future holdout dataset.
6. Business cost estimates.
7. Fairness evaluation.
8. Human review.
9. Monitoring.
10. Rollback procedures.

---

# 📁 Project Structure

```text
responsible-customer-churn/
│
├── README.md
│
├── requirements.txt
│
├── data/
│   └── customer-churn-training.csv
│
├── notebooks/
│   └── customer_churn_baseline.ipynb
│
└── docs/
    ├── ml_problem_framing_memo.md
    ├── responsible_data_card.md
    └── risk_register.md
```

---

# 🛠️ Technologies Used

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Jupyter Notebook

---

# 🚀 How to Run

Clone the repository:

```bash
git clone <your-github-repository-url>
cd responsible-customer-churn
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Start Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
notebooks/customer_churn_baseline.ipynb
```

Run all cells.

---

# 📋 Project Deliverables

This project satisfies the requested proof requirements:

### ✅ 1. ML Problem-Framing Memo

Documents:

* Business decision
* Prediction target
* Unit of observation
* Action window
* Non-ML baseline
* ML justification

### ✅ 2. Responsible Data Card

Documents:

* Dataset purpose
* Provenance
* Permission/consent considerations
* Population and representation
* Features and target
* Leakage risks
* Data quality
* Risks and safeguards
* Evaluation plan

### ✅ 3. Baseline Notebook

Includes:

* Data loading
* Data validation
* Target analysis
* Majority baseline
* Logistic Regression
* Decision Tree
* Random Forest
* Cross-validation
* Precision
* Recall
* F1
* ROC-AUC
* Confusion matrices

### ✅ 4. Risk Register

Documents:

* Data risks
* Model risks
* Fairness risks
* Business risks
* Mitigation
* Monitoring
* Rollback

---

# 🎓 Key Learning

This project demonstrates that responsible ML begins **before model training**.

Instead of asking only:

> **"Can I build a model?"**

the project asks:

> **"Should ML be used for this decision?"**

> **"What could go wrong?"**

> **"What are the costs of incorrect predictions?"**

> **"When should the model abstain?"**

> **"How will humans review uncertain cases?"**

> **"What happens if the model fails?"**

---

# 💡 Responsible AI Principle

> **The model predicts churn risk; it does not decide the customer's fate.**

---

# 👨‍💻 Author

**Aditya Patil**

B.Sc. Computer Science | AI/ML & Data Science Fresher

### Skills

* Python
* Machine Learning
* Data Science
* Generative AI
* NLP
* Agentic AI
* Responsible AI
* SQL
* Pandas
* Scikit-learn

---

# ⭐ Project Status

**Completed — ML Problem Framing & Responsible Data Card**

This project demonstrates practical experience in:

**Problem Framing → Responsible Data Analysis → Baseline Modeling → Risk Assessment → Evaluation → Human Review → Monitoring → Rollback**
# 🚀 Responsible Customer Churn Prediction

## 📌 Project Overview

This project evaluates whether **Machine Learning is justified for a real customer churn decision** before moving directly into model training.

The project follows a responsible ML workflow:

```text
Business Decision
       ↓
Problem Framing
       ↓
Responsible Data Card
       ↓
Data Quality Checks
       ↓
Non-ML Baseline
       ↓
ML Baseline Models
       ↓
Risk & Cost Analysis
       ↓
Human Review
       ↓
Monitoring
       ↓
Rollback / Fallback
```

The goal is not only to build an accurate model, but to understand whether the model can be used **safely, fairly, and practically**.

---

# 🎯 Business Problem

Customer churn can result in lost revenue and reduced customer lifetime value.

The business decision is:

> **Which customers should be considered for retention outreach because they may be at higher risk of churn?**

The ML system should provide **decision support**, not automatically make irreversible customer decisions.

---

# 🤖 Is Machine Learning Justified?

### Decision: Yes, for further investigation.

Machine Learning can potentially identify relationships between customer characteristics and churn that are difficult to capture using a simple rule.

However, ML should only be adopted if it:

* Performs better than a simple baseline.
* Provides useful predictions.
* Has acceptable false-positive and false-negative costs.
* Does not introduce unacceptable bias.
* Can be monitored.
* Has a safe fallback strategy.

### Important limitation

The supplied dataset contains only **12 customer records**.

Therefore, the current experiment demonstrates the **ML workflow and responsible problem framing**, but it does **not** provide sufficient evidence for production deployment.

---

# 📊 Dataset

The project uses the supplied:

```text
customer-churn-training.csv
```

### Dataset structure

| Feature             | Description               |
| ------------------- | ------------------------- |
| `customer_id`       | Customer identifier       |
| `tenure_months`     | Customer tenure           |
| `support_tickets`   | Number of support tickets |
| `monthly_spend_inr` | Monthly customer spending |
| `last_login_days`   | Days since last login     |
| `plan_type`         | Customer plan category    |
| `churned`           | Target variable           |

`customer_id` is used only as an identifier and is excluded from model training.

---

# 🧩 ML Problem Framing

| Component           | Definition                                        |
| ------------------- | ------------------------------------------------- |
| Business Decision   | Identify customers for possible retention review  |
| Prediction Target   | `churned`                                         |
| Positive Class      | `1` = churned                                     |
| Negative Class      | `0` = not churned                                 |
| Unit of Observation | Individual customer                               |
| Action              | Retention review/outreach                         |
| Non-ML Baseline     | Majority-class prediction                         |
| ML Models           | Logistic Regression, Decision Tree, Random Forest |

---

# 🗂️ Responsible Data Card

## 1. Dataset Purpose

The dataset supports experimentation with **customer churn prediction**.

The intended decision is to identify customers who may benefit from retention review.

The model must **not** be used by itself to:

* Terminate customer services.
* Increase prices.
* Deny services.
* Make irreversible customer decisions.
* Automatically apply punitive actions.

---

## 2. Provenance and Permission

The dataset was supplied for this project as:

```text
customer-churn-training.csv
```

The CSV itself does not establish:

* Original collection methodology
* Customer consent
* Licensing restrictions
* Data ownership
* Representativeness of the population

Therefore, these properties must be verified from the original data provider before real-world deployment or redistribution.

---

# 👥 Population and Representation

The supplied dataset contains:

```text
Total customers: 12
Churned:         5
Not churned:     7
```

Churn distribution:

```text
Not Churned  ███████  7
Churned      █████    5
```

Because the dataset is extremely small, it may not represent the full customer population.

Potential limitations include:

* Small sample size
* Unknown sampling process
* Unknown geographic representation
* Unknown demographic representation
* Unknown time period
* Unknown customer segment coverage

Therefore, additional representative data is required before deployment.

---

# 🔍 Features and Target

### Features

The dataset contains:

```text
customer_id
tenure_months
support_tickets
monthly_spend_inr
last_login_days
plan_type
```

### Target

```text
churned
```

where:

```text
1 → Customer churned
0 → Customer did not churn
```

### Leakage Risk

A feature should only be used if it would have been available **at the exact moment the prediction is made**.

Potential future-derived information must not be included.

Examples of dangerous leakage could include:

```text
Cancellation date
Final account status
Post-churn support activity
Post-churn refunds
```

The current CSV does not contain timestamps, so complete temporal leakage validation cannot be performed from this file alone.

---

# 🔐 Sensitive Attributes and Proxy Risk

The supplied dataset does not provide an explicit protected-attribute audit.

However, variables such as:

* Spending
* Tenure
* Support behavior
* Login behavior
* Plan type

could potentially act as proxies for customer circumstances.

If protected attributes are introduced later, the model should be evaluated for group-level differences in:

* False-positive rate
* False-negative rate
* Precision
* Recall
* Calibration

---

# 🧪 Data Quality Checks

The dataset was checked for:

### Missing Values

```text
0 missing values
```

### Duplicate Rows

```text
0 duplicate rows
```

### Dataset Size

```text
12 rows
7 columns
```

### Target Balance

```text
Churned:      5
Not churned: 7
```

The small sample size remains the most important limitation.

---

# 📏 Non-ML Baseline

Before ML, a majority-class baseline is established.

Since the majority class is:

```text
Not Churned = 7 / 12
```

the baseline predicts:

```text
Every customer → Not Churned
```

### Baseline performance

```text
Accuracy = 58.3%
```

However, this baseline has:

```text
Recall = 0
```

for the churn class because it never predicts churn.

---

# 🤖 ML Models

Three baseline models were evaluated:

### 1. Logistic Regression

Provides an interpretable linear baseline.

### 2. Decision Tree

Can model nonlinear relationships and is relatively easy to interpret.

### 3. Random Forest

Uses multiple decision trees to capture more complex patterns.

---

# 📊 Model Results

Using **3-fold stratified cross-validation**:

| Model               | Accuracy | Precision | Recall |    F1 | ROC-AUC |
| ------------------- | -------: | --------: | -----: | ----: | ------: |
| Majority Baseline   |    58.3% |      0.0% |   0.0% |  0.0% |       — |
| Logistic Regression |     100% |      100% |   100% |  100% |   1.000 |
| Decision Tree       |    91.7% |     83.3% |   100% | 90.9% |   0.929 |
| Random Forest       |     100% |      100% |   100% |  100% |   1.000 |

---

# ⚠️ Important Model Interpretation

The 100% cross-validation scores for Logistic Regression and Random Forest may initially look excellent.

However:

> **The dataset contains only 12 observations.**

Therefore, these results should **not** be interpreted as evidence that the model will achieve 100% performance on new customers.

The correct conclusion is:

```text
Strong signal in this small dataset
              +
Extremely limited sample size
              =
Further investigation required
```

A much larger dataset and a genuine future holdout are required before making deployment claims.

---

# 💰 False Positive and False Negative Costs

## False Positive

The model predicts:

```text
Customer → High Churn Risk
```

but the customer would not have churned.

Potential costs:

* Unnecessary retention campaign
* Discount cost
* Employee time
* Customer annoyance

---

## False Negative

The model predicts:

```text
Customer → Low Churn Risk
```

but the customer actually churns.

Potential costs:

* Lost customer
* Lost revenue
* Lost customer lifetime value

Because these costs can differ, the final model threshold should be selected using **business cost**, not accuracy alone.

---

# 🧑‍💼 Human Review

The system should include an abstention/human-review mechanism.

Example:

```text
                 Churn Probability
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Low Risk       Uncertain      High Risk
          │              │              │
          ↓              ↓              ↓
      No Action     Human Review   Retention Review
```

The model should support human decision-making rather than automatically determining customer treatment.

---

# 📈 Monitoring Plan

If the model is eventually deployed, monitor:

## Data

* Missing values
* Feature distributions
* New categories
* Data drift
* Unexpected values

## Model

* Precision
* Recall
* F1
* ROC-AUC
* False-positive rate
* False-negative rate
* Calibration

## Business

* Churn rate
* Retention rate
* Retention campaign cost
* Customer lifetime value
* Retention ROI

---

# 🔄 Rollback Conditions

The ML system should be disabled and returned to the non-ML strategy if:

* Data quality becomes unreliable.
* Significant model degradation occurs.
* Data leakage is discovered.
* Major data drift occurs.
* False-positive costs become unacceptable.
* False-negative costs become unacceptable.
* Significant fairness concerns appear.
* The retention intervention no longer produces business value.

### Fallback

```text
ML System
    ↓
Problem Detected
    ↓
Disable ML Decision Support
    ↓
Return to Non-ML Retention Strategy
    ↓
Investigate + Revalidate
```

---

# ⚠️ Risk Register

| ID  | Risk               | Impact | Mitigation                       |
| --- | ------------------ | ------ | -------------------------------- |
| R1  | Data leakage       | High   | Verify feature availability      |
| R2  | Very small dataset | High   | Collect more representative data |
| R3  | Overfitting        | High   | Use larger future holdout        |
| R4  | Class imbalance    | Medium | Monitor recall/F1/PR-AUC         |
| R5  | False positives    | Medium | Cost-based threshold             |
| R6  | False negatives    | High   | Monitor recall                   |
| R7  | Proxy bias         | High   | Group-level evaluation           |
| R8  | Data drift         | High   | Continuous monitoring            |
| R9  | Poor provenance    | High   | Verify source and permission     |
| R10 | Model misuse       | High   | Human review                     |
| R11 | Model degradation  | High   | Monitoring + rollback            |

---

# 🧠 Responsible AI Decision

## Final Decision

### ✅ ML is justified for further investigation.

### ❌ ML is not justified for production deployment using this dataset alone.

The current dataset demonstrates that ML models can identify strong patterns in the supplied sample.

However, responsible deployment requires:

1. A much larger dataset.
2. Clearly documented data provenance.
3. A defined prediction horizon.
4. Time-aware validation.
5. A future holdout dataset.
6. Business cost estimates.
7. Fairness evaluation.
8. Human review.
9. Monitoring.
10. Rollback procedures.

---

# 📁 Project Structure

```text
responsible-customer-churn/
│
├── README.md
│
├── requirements.txt
│
├── data/
│   └── customer-churn-training.csv
│
├── notebooks/
│   └── customer_churn_baseline.ipynb
│
└── docs/
    ├── ml_problem_framing_memo.md
    ├── responsible_data_card.md
    └── risk_register.md
```

---

# 🛠️ Technologies Used

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Jupyter Notebook

---

# 🚀 How to Run

Clone the repository:

```bash
git clone <your-github-repository-url>
cd responsible-customer-churn
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Start Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
notebooks/customer_churn_baseline.ipynb
```

Run all cells.

---

# 📋 Project Deliverables

This project satisfies the requested proof requirements:

### ✅ 1. ML Problem-Framing Memo

Documents:

* Business decision
* Prediction target
* Unit of observation
* Action window
* Non-ML baseline
* ML justification

### ✅ 2. Responsible Data Card

Documents:

* Dataset purpose
* Provenance
* Permission/consent considerations
* Population and representation
* Features and target
* Leakage risks
* Data quality
* Risks and safeguards
* Evaluation plan

### ✅ 3. Baseline Notebook

Includes:

* Data loading
* Data validation
* Target analysis
* Majority baseline
* Logistic Regression
* Decision Tree
* Random Forest
* Cross-validation
* Precision
* Recall
* F1
* ROC-AUC
* Confusion matrices

### ✅ 4. Risk Register

Documents:

* Data risks
* Model risks
* Fairness risks
* Business risks
* Mitigation
* Monitoring
* Rollback

---

# 🎓 Key Learning

This project demonstrates that responsible ML begins **before model training**.

Instead of asking only:

> **"Can I build a model?"**

the project asks:

> **"Should ML be used for this decision?"**

> **"What could go wrong?"**

> **"What are the costs of incorrect predictions?"**

> **"When should the model abstain?"**

> **"How will humans review uncertain cases?"**

> **"What happens if the model fails?"**

---

# 💡 Responsible AI Principle

> **The model predicts churn risk; it does not decide the customer's fate.**

---
