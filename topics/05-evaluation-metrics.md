# Evaluation Metrics

[⬅ Back to index](../README.md)

Covers: the confusion matrix, accuracy, precision, recall, F1, choosing thresholds, ROC-AUC vs PR-AUC, log loss and calibration, regression metrics, ranking metrics (precision@k, recall@k, MAP, MRR, NDCG), clustering metrics, offline vs online metrics, and choosing the right metric for the business.

---

### 1. What is a confusion matrix?

<details>
<summary>Answer</summary>

A table of predictions vs truth for a classifier:

| | Predicted positive | Predicted negative |
|---|---|---|
| **Actually positive** | True positive (TP) | False negative (FN) |
| **Actually negative** | False positive (FP) | True negative (TN) |

All the common classification metrics are computed from these four numbers. Always look at it before trusting a single summary metric.

</details>

### 2. Precision vs recall – when do you prioritise each?

<details>
<summary>Answer</summary>

- **Precision** = TP / (TP + FP): of the items you flagged, how many were right.
- **Recall** = TP / (TP + FN): of the actual positives, how many you found.

Prioritise:
- **Precision** when false positives are costly: spam filtering (don't hide real email), recommending content, auto-blocking payments.
- **Recall** when misses are costly: cancer screening, fraud detection for review queues, safety filters.

There's a trade-off controlled by the decision threshold.

</details>

### 3. What is the F1 score, and when is it misleading?

<details>
<summary>Answer</summary>

F1 = **harmonic mean** of precision and recall = `2PR / (P + R)`. It's high only when both are high, which suits imbalanced problems better than accuracy. Limitations: it weights precision and recall equally (use **F-beta**, e.g. F2 to favour recall), ignores true negatives, and depends on the threshold. For multi-class: **macro-F1** (average per class, treats classes equally) vs **micro-F1** (pools all predictions, dominated by frequent classes) vs weighted.

</details>

### 4. Why is accuracy a bad metric for imbalanced data?

<details>
<summary>Answer</summary>

With 99% negatives, a model that always predicts "negative" gets 99% accuracy and catches nothing. Use precision, recall, F1, PR-AUC, or a cost-based metric that reflects the business impact of each error type.

</details>

### 5. How do you choose a classification threshold?

<details>
<summary>Answer</summary>

The default 0.5 is rarely optimal. Choose based on:
- The **cost of each error** (e.g. a missed fraud costs $500, a false alarm costs $5 of review time) → pick the threshold minimising expected cost on validation data.
- A **capacity constraint** (reviewers can check 1,000 cases a day → take the top 1,000 scores).
- A **target** ("at least 95% precision") → the threshold that meets it with the best recall.

Use precision-recall curves on validation data; revisit thresholds when data drifts.

</details>

### 6. What is ROC-AUC, and how is it different from PR-AUC?

<details>
<summary>Answer</summary>

- **ROC curve** – true positive rate (recall) vs false positive rate across all thresholds. **ROC-AUC** = probability that a random positive is scored above a random negative. 0.5 = random, 1.0 = perfect. Threshold-independent.
- **PR curve** – precision vs recall across thresholds; **PR-AUC** (average precision) summarises it.

With heavy class imbalance, ROC-AUC can look great because the false positive rate stays tiny (huge number of negatives), while precision is actually poor. **PR-AUC focuses on the positive class** and is more informative for rare-event problems like fraud.

</details>

### 7. What is log loss, and what is calibration?

<details>
<summary>Answer</summary>

- **Log loss** (cross-entropy) rewards correct, **confident** probabilities and heavily penalises confident wrong ones. It evaluates probability quality, not just ranking.
- **Calibration** – predicted probabilities match observed frequencies (of all cases predicted at 0.8, about 80% are positive). Check with a **reliability diagram** or expected calibration error.

Calibration matters when probabilities drive decisions (ad bidding uses predicted CTR × bid, risk scores, medical triage). Fix with **Platt scaling**, **isotonic regression** or **temperature scaling**. Boosted trees and deep networks are often miscalibrated; also note that resampling for imbalance distorts probabilities.

</details>

### 8. Which regression metrics should you know?

<details>
<summary>Answer</summary>

- **MAE** – average absolute error; robust to outliers; in the target's units.
- **MSE / RMSE** – penalise large errors more; RMSE is in the target's units.
- **R²** – fraction of variance explained relative to predicting the mean; can be negative for bad models.
- **MAPE** – percentage error; intuitive for business but breaks near zero and is asymmetric.
- **Quantile (pinball) loss** – for predicting intervals (p10/p90 demand forecasts).

Choose based on the cost of errors: if a 10-unit error is more than twice as bad as a 5-unit one, prefer RMSE.

</details>

### 9. What ranking metrics are used for search and recommendations?

<details>
<summary>Answer</summary>

- **Precision@k / Recall@k** – relevant items in the top k / fraction of all relevant items found in the top k.
- **MRR (mean reciprocal rank)** – 1/rank of the first relevant result, averaged; good when one right answer matters (question answering, navigational search).
- **MAP (mean average precision)** – averages precision at each relevant item's position.
- **NDCG@k** – uses **graded relevance** and discounts lower positions logarithmically (`gain / log2(rank + 1)`), normalised by the ideal ordering; the standard for ranking.
- **Hit rate@k** – whether any relevant item appears in the top k (common for RAG retrieval).

Online, ranking quality shows up as CTR, conversion, dwell time and long-term retention.

</details>

### 10. How do you evaluate clustering without labels?

<details>
<summary>Answer</summary>

- **Silhouette score** – how close points are to their own cluster vs the nearest other cluster (−1 to 1).
- **Davies–Bouldin index** (lower is better), **Calinski–Harabasz** (higher is better).
- **Inertia / within-cluster sum of squares** (elbow method).
- With some labels: adjusted Rand index, normalised mutual information.

Ultimately, clusters must be useful for the business purpose (are segments actionable?), so qualitative review matters.

</details>

### 11. Offline metrics vs online metrics – why can they disagree?

<details>
<summary>Answer</summary>

Offline metrics (AUC, NDCG on historical data) are proxies. Online metrics (CTR, revenue, retention in an A/B test) are what the business cares about. They disagree because of:
- **Feedback loops / selection bias** – historical data only contains items the old model showed.
- The offline metric not matching the true objective (optimising clicks causes clickbait).
- **Training-serving skew** (features computed differently in production).
- Latency – a better but slower model can lose engagement.
- Novelty effects and user behaviour changes.

So: validate offline, then confirm with online experiments; track guardrail metrics.

</details>

### 12. How do you choose the right metric for a business problem?

<details>
<summary>Answer</summary>

Start from the decision the model supports and the cost of each kind of mistake:
- Fraud blocking → precision at a fixed recall, or expected monetary loss.
- Medical screening → recall (sensitivity) with acceptable specificity.
- Search → NDCG@10 offline, click-through and success rate online.
- Ads → log loss and calibration (probabilities drive bids), plus revenue online.
- Demand forecasting → weighted MAPE or quantile loss tied to stock-out vs overstock cost.

State a primary metric, guardrail metrics, and how offline metrics map to the online goal.

</details>

### 13. What is the difference between macro and micro averaging?

<details>
<summary>Answer</summary>

For multi-class problems:
- **Macro average** – compute the metric per class, then average equally. Rare classes count as much as common ones; good when all classes matter equally.
- **Micro average** – pool all TP/FP/FN across classes, then compute. Dominated by frequent classes (equals accuracy for single-label classification).
- **Weighted average** – per-class metrics weighted by class frequency.

</details>

### 14. What are specificity and sensitivity?

<details>
<summary>Answer</summary>

Terms common in medicine:
- **Sensitivity** = recall = true positive rate = TP / (TP + FN).
- **Specificity** = true negative rate = TN / (TN + FP).
The false positive rate on the ROC curve is `1 − specificity`.

</details>

---

[⬅ Previous: Classical ML Algorithms](04-classical-algorithms.md) · [⬅ Back to index](../README.md) · [Next: Neural Network Fundamentals ➡](06-neural-networks.md)
