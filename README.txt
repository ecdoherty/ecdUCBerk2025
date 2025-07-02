# **Shadow API Detection Using Machine Learning** #

# Executive summary
This capstone project investigates the application of machine learning techniques to detect and risk-rank shadow API calls, which pose significant cybersecurity threats due to their unmonitored and unmanaged nature. The project combines both real-world and synthetic datasets to assess supervised and unsupervised models for their ability to identify anomalous behavior indicative of shadow APIs.

# Rationale  
Organizations increasingly rely on APIs, but managing their lifecycle has become difficult, often resulting in undocumented or rogue endpoints that increase the attack surface. Traditional discovery methods are insufficient, necessitating a machine-learning-based approach that can proactively detect and prioritize shadow API risks.

# Research Question
Can machine-learning models reliably detect and risk-rank shadow-API calls?

# Data Sources
## API Centric Traffic Data:
- API Access Behavior Anomaly: https://www.kaggle.com/datasets/tangodelta/api-access-behaviour-anomaly-dataset  
    This is a dataset of API calls that has qualified user access behaviors as numerical features labeled as normal or outlier
- Synthetic Data:
    Used Faker Python package to generate synthetic shadow and non-shadow API calls 

# Methodology
A combination of supervised classifiers (Logistic Regression, Random Forest, Gradient Boosting), anomaly detection models (One-Class SVM, Isolation Forest, Local Outlier Factor), AutoEncoders, and distance/density-based methods (KNN, DBSCAN) were applied and models were evaluated using precision, recall, F1-score, and accuracy metrics for both the Kaggle and synthetic datasets.

# Results

Supervised models achieved near-perfect classification, indicating strong separability in both datasets. AutoEncoders demonstrated high reconstruction-based anomaly detection performance after threshold tuning. One-Class SVM and Isolation Forest performed well on structured data, while DBSCAN and KNN revealed clusters and outliers with moderate precision and recall. Overall, multiple approaches validated the feasibility of machine learning for shadow API detection.

# Next steps
Future work includes testing models on real-time streaming API logs, integrating risk scoring into monitoring systems, and expanding datasets to include broader threat patterns and attack scenarios.

# Outline of project
-	Link to notebook; https://github.com/ecdoherty/ecdUCBerk2025 
