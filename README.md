
Capstone Project
Shadow API Detection Using Machine Learning
Final Report
- Define the Problem Statement:
- Organizations increasingly rely on micro-services and third-party integrations through Application Programming Interfaces (APIs) to operate their business. APIs are leveraged to support, manage or run business processes and capabilities. Over time, these APIs can become mismanaged, forgotten, or otherwise not officially managed by organizational resources and accumulate as “shadow APIs”.
- Shadow APIs often lack the proper security mechanisms such as strong authentication, data protections and monitoring controls resulting in potential entry points for attackers to access sensitive data or compromise systems. Additionally, current discovery mechanisms for shadow APIs can be slow, manual and error prone. This project aims to build a machine learning powered pipeline that automatically detects shadow API calls as well as risk rank to facilitate more effective triage and remediation.

- Model Outcomes or Predictions:
- Both supervised and unsupervised anomaly detector models could be helpful for this use case. For supervised modeling, a model trained on API traffic that has already been labeled as shadow (bad) or normal (good) will provide a probabilistic output for whether any new API call is shadow or normal based on the patterns the model learns from the labeled training data. Conversely, unsupervised learning models don’t rely on labeled training data. They’re able to determine the statistical footprint of normal API traffic and extrapolate anomalies by measuring how much a new API call differs from the statistical norm.
- For this project, classification models and unsupervised anomaly detection are a natural fit because the target variable is categorical, each API call is either shadow or normal. Therefore, the expected output will be a predicted classification (for classifier models) or anomaly scores that measure deviation from learned “normal” behaviors (for detectors). There is opportunity to introduce regression models if I wanted to estimate the financial impact of a potential shadow-API breach, forecast API response times or throughput latency from traffic trends, or predict daily shadow call volumes for proactive capacity planning.

- Data Acquisition:
- This project uses two distinct datasets: a real-world behavioral dataset sourced from Kaggle with labeled API access behavioral data that is designed to reflect realistic class imbalance, with fewer labeled outlier cases than normal, majority class cases. Additionally, this project leveraged a custom generated synthetic dataset created using the Faker Python package with a 20% shadow/80% normal class split.

Primary Dataset: API Access Behavior Anomaly (Kaggle)
- Source: https://www.kaggle.com/datasets/tangodelta/api-access-behaviour-anomaly-dataset
- Volume: 1,699 rows × 12 columns
- Target Feature: classification column, containing two classes:
- 0: normal (expected API call behavior)
- 1: outlier (suspicious or anomalous behavior)
- Features:
- inter_api_access_duration(sec): time between consecutive API calls
- api_access_uniqueness: uniqueness of API call patterns
- sequence_length(count): number of API calls in a session
- vsession_duration(min): virtual session duration in minutes
- num_sessions, num_users, num_unique_apis: activity-level stats
- Encoded categorical columns: ip_type, source (via Label Encoding

Secondary Dataset: Custom Generated Dataset (via Faker)
- Created using the Faker Python library to simulate normal and shadow API call behavior. A shadow_ratio parameter controlled the proportion of anomalous samples (typically 20% shadow, 80% normal).
- Volume: 10,000 rows × 6 columns
- Target Feature: is_shadow field (renamed to classification for compatibility)
- 0: normal API call
- 1: synthetic shadow API call
- Features:
- timestamp: datetime of the API call
- api_endpoint_encoded: encoded version of the API path (via LabelEncoder)
- user_agent_encoded: encoded version of the caller's user agent
- response_time: simulated latency in milliseconds
- status_code: response type (e.g., 200, 403), one-hot encoded into:
- status_200, status_401, status_403, status_500, etc.

- These datasets provide complementary perspectives on detecting shadow API activity with varying sizes, class imbalance and features.

- Data Preprocessing/Preparation:
Both datasets received similar preprocessing in that categorical features were transformed using LabelEncoder or OneHotEncoders and numerical features were standardized using StandardScalar to ensure model convergence and prevent features with larger scales from dominating others. Additionally, further preprocessing included handling missing values, dropping irrelevant or non-informative columns and setting up the train/test split using stratification to preserve the minority class.

- Modeling:
- Supervised:
- Logistic Regression
- Random Forest
- Gradient Boosted Trees
- Support Vector Classifier
- Unsupervised and Anomaly Detection:
- Autoencoder
- One‑Class SVM
- Isolation Forest
- Local Outlier Factor (LOF)
- K‑Nearest Neighbors
- DBSCAN.

- Model Evaluation:
- Evaluation Metrics used:
- Precision, Recall, F1, F2 (weighted against false negatives as they’re considered riskier), ROC‑AUC
- Top performers by F1:
- Logistic Regression F1=1.0
- LOF F1= 0.942
- Autoencoder F1= 0.989
These three models form the ensemble that drives final risk ranking.
- Supervised Techniques, Results & Interpretation:
All 4 classification techniques tested performed with near perfect precision for both datasets. To verify there was no leakage, I ran several tests. I removed highly correlated or suspicious columns in the Kaggle dataset and reran on both the Random Forest and Logistic Regression models to see if there were any impacts to evaluation metrics. There was no meaningful change. I further validated that there was no hidden leakage with a shuffle test and permutation tests. I leveraged gridsearchCV and cross-validation loops (repeated loops) all performing consistently well, indicating the scores were, in fact, coming from genuinely informative features.
In summary, all the testing and validations done prove that despite its high performance, the classes are linearly separable, and no one feature can be identified as the main contributor for the model performance. Therefore, across the supervised models tested, I recommend using the simplest supervised model, Logistic Regression, since it’s the fastest to train/retrain, has better explainability and is less computationally expensive. Before moving on to Unsupervised modeling, I calibrated the probability scores on the Logistic Regression model for later risk-ranking.

- Unsupervised Techniques, Results & Interpretation:
Next, I evaluated several unsupervised techniques because a perfectly performing model from labeled, pre-trained data does not guarantee perfect performance forever, especially with unclean/unlabeled future data. All 3 models performed well, varying by 1/10th of a point or less across all metrics. For good measure, I even averaged the metrics over 3-fold stratified cross-validation (normal only training, mixed testing).
The best performing unsupervised anomaly detection model is LOF. Since missing a potentially risky Shadow-API call can result high risks of data loss, system availability issues as well as reputational and compliance impacts, recall should be prioritized. The LOF model demonstrates perfect recall plus the highest F2 and ROC‑AUC, making it the safest detector. Again, these results indicate this scenario is a great use case for machine learning as its relatively easy for these models to detect shadow-APIs. However, even though I didn't use labels for our unsupervised learning models, it’s important to note that this clean and curated dataset could still be contributing to inflated performance.
When evaluating the results of the supervised and unsupervised modeling together, there are some interesting and practical recommendations that can be done next. I can deploy LOF and Logistic Regression in parallel with threshold tuning and established alerts. For example, based on the cumulative distribution of anomaly scores, I could set a threshold to trigger alerts for scores above .04 as low. Anything above this number would trigger an alert but would be considered low risk. A high-severity alert could be tuned for anomaly scores above .12 when both the LOF and Logistic Regression models agree and a medium-severity alert if only one of the models triggers an alert above this threshold.

- Autoencoder Techniques, Results & Interpretation
I implemented an Autoencoder as an unsupervised anomaly detection technique to identify shadow APIs by reconstructing input features and comparing reconstruction error to a threshold. I used the same preprocessor pipeline that fed the supervised model evaluations above, converting feature types to avoid errors. Then I worked to tune the threshold for balanced precision and recall using the precision-recall curve to find the left-most threshold that still reaches near perfect recall while maximizing precision. This resulted in an f1 score of 0.989.

- Overall Summary & Interpretation
When interpreting all results across the Supervised and Unsupervised techniques tested, one practical application could be to create a 3-layerd anomaly detection framework to create a more robust model with better risk-sloping for alerts. The Logistic Regression model is excellent at catching known shadow API patterns as it was trained with labels. The outputs of this model have well-calibrated probabilities for risk ranking. Meanwhile, the Autoencoder model detects the novel or unseen anomalies by learning what the “normal” patterns and measuring reconstruction error. Lastly, LOF Flags the data points that are in sparse regions of feature space, complementing the Autoencoder.