# Classical ML Algorithms

[⬅ Back to index](../README.md)

Covers: linear and logistic regression, decision trees, random forests, gradient boosting (XGBoost, LightGBM), SVMs, k-nearest neighbours, Naive Bayes, k-means and other clustering, PCA and t-SNE/UMAP, anomaly detection, recommender basics (collaborative filtering, matrix factorisation), time-series basics, and how to choose an algorithm.

---

### 1. How does linear regression work, and what are its assumptions?

<details>
<summary>Answer</summary>

It models `y = wᵀx + b` and minimises **mean squared error**. Solutions: the closed form (normal equation `w = (XᵀX)⁻¹Xᵀy`, expensive for many features) or gradient descent.

Assumptions: a linear relationship, independent errors, constant error variance (homoscedasticity), normally distributed errors (for inference), and little multicollinearity. Coefficients are interpretable (the effect of a one-unit change, holding others fixed). Regularised versions: ridge (L2), lasso (L1).

</details>

### 2. How does logistic regression work? Why not use MSE for it?

<details>
<summary>Answer</summary>

It passes a linear score through the **sigmoid** `σ(z) = 1 / (1 + e^(−z))` to get a probability `P(y=1|x)`, and trains by minimising **log loss (binary cross-entropy)**. The decision boundary is linear; the coefficients are log-odds.

Why not MSE: combined with the sigmoid, MSE gives a **non-convex** loss with weak gradients when predictions are confidently wrong; log loss is convex and penalises confident mistakes heavily. Extends to multiple classes via **softmax** (multinomial logistic regression). It's a strong, fast, interpretable baseline.

</details>

### 3. How does a decision tree decide where to split?

<details>
<summary>Answer</summary>

At each node it tries candidate splits on each feature and picks the one that most reduces **impurity**:
- Classification: **Gini impurity** `1 − Σpᵢ²` or **entropy** (information gain).
- Regression: variance (MSE) reduction.

It grows greedily until a stopping rule (max depth, min samples per leaf), then may be **pruned**. Pros: interpretable, handles mixed feature types and non-linearity, no scaling needed. Cons: a single tree **overfits** easily and is unstable (small data changes produce different trees).

</details>

### 4. How does a random forest work, and why is it better than one tree?

<details>
<summary>Answer</summary>

It trains many decision trees, each on a **bootstrap sample** of the rows and considering a **random subset of features** at each split, then averages (regression) or votes (classification). The randomness **decorrelates** the trees, so averaging reduces variance substantially without increasing bias much. Extras: out-of-bag error as a free validation estimate, feature importance. Robust and needs little tuning – a great tabular baseline.

</details>

### 5. How does gradient boosting work? Why is XGBoost/LightGBM so popular?

<details>
<summary>Answer</summary>

Boosting builds trees **sequentially**: each new (shallow) tree is fit to the **negative gradient of the loss** (the residuals, for MSE) of the current ensemble, and added with a small learning rate. Over hundreds of trees, the model corrects its errors step by step.

XGBoost, LightGBM and CatBoost add: second-order gradients, regularisation on tree complexity, histogram-based fast split finding, native missing-value handling, efficient categorical handling (CatBoost), and parallel/GPU training. On **tabular data** they are usually the strongest models, often beating deep learning. Key hyperparameters: learning rate, number of trees (with early stopping), tree depth/leaves, subsampling.

</details>

### 6. Random forest vs gradient boosting?

<details>
<summary>Answer</summary>

| Random forest | Gradient boosting |
|---|---|
| Trees built independently (parallel), deep trees | Trees built sequentially, shallow trees |
| Reduces **variance** | Reduces **bias** |
| Robust, little tuning, hard to overfit by adding trees | Usually more accurate, but needs tuning and can overfit (use early stopping) |
| Good quick baseline | Best-in-class for tabular competitions and production |

</details>

### 7. How does an SVM work? What is the kernel trick?

<details>
<summary>Answer</summary>

A **Support Vector Machine** finds the hyperplane that separates classes with the **maximum margin**; only the closest points (support vectors) define it. A **soft margin** (parameter C) allows some misclassification for noisy data.

The **kernel trick** computes dot products in a high-dimensional feature space without constructing it explicitly (RBF, polynomial kernels), letting SVMs learn non-linear boundaries. SVMs work well on small-to-medium, high-dimensional data (e.g. text), but scale poorly to millions of examples and don't output calibrated probabilities natively.

</details>

### 8. How does k-nearest neighbours work, and what are its weaknesses?

<details>
<summary>Answer</summary>

To predict, find the k closest training points (by Euclidean or cosine distance) and take a majority vote or average. There's no training phase ("lazy learning"). Choose k by validation (small k → high variance, large k → high bias).

Weaknesses: slow predictions on large data (needs indexing such as KD-trees or **approximate nearest neighbour** search), sensitive to feature scaling and irrelevant features, and suffers from the curse of dimensionality. The same nearest-neighbour idea powers embedding-based retrieval in modern AI systems.

</details>

### 9. What is Naive Bayes and why does it work despite the "naive" assumption?

<details>
<summary>Answer</summary>

It applies Bayes' theorem assuming features are **conditionally independent given the class**: `P(y|x) ∝ P(y) Π P(xᵢ|y)`. The assumption is usually false, but the classifier only needs the **ranking** of class probabilities to be right, which often holds. It's extremely fast, works with little data, and is a strong baseline for text classification (spam filtering). Use **Laplace smoothing** to avoid zero probabilities for unseen words.

</details>

### 10. How does k-means clustering work, and how do you choose k?

<details>
<summary>Answer</summary>

1. Initialise k centroids (**k-means++** spreads them out for better results).
2. Assign each point to the nearest centroid.
3. Move each centroid to the mean of its points.
4. Repeat until assignments stop changing.

It minimises within-cluster sum of squares, converges to a local optimum (run several initialisations), and assumes roughly spherical, similar-sized clusters. Choose k with the **elbow method**, **silhouette score**, or business needs. Alternatives: **DBSCAN** (density-based, finds arbitrary shapes and outliers, no k needed), hierarchical clustering, Gaussian mixture models (soft assignments).

</details>

### 11. What is PCA, and when would you use t-SNE or UMAP instead?

<details>
<summary>Answer</summary>

**PCA** projects data onto the orthogonal directions of maximum variance (top eigenvectors of the covariance matrix). It's linear, fast, deterministic, and good for compression, noise reduction and speeding up models. Standardise features first; choose the number of components by explained variance (e.g. 95%).

**t-SNE** and **UMAP** are non-linear methods that preserve **local neighbourhoods**, great for **visualising** clusters of embeddings in 2D. t-SNE distorts global distances and is slow; UMAP is faster and preserves more global structure. Neither is ideal as a preprocessing step for models.

</details>

### 12. How do you detect anomalies?

<details>
<summary>Answer</summary>

- **Statistical** – z-scores, IQR rules, seasonal decomposition for time series.
- **Isolation Forest** – anomalies are isolated by fewer random splits.
- **One-class SVM**, **Local Outlier Factor** (density-based).
- **Autoencoders** – high reconstruction error indicates an anomaly.
- **Supervised** classification if labelled anomalies exist (fraud with chargebacks).

Challenges: few labels, changing definitions of "normal", and the cost of false alarms; usually combined with rules and human review.

</details>

### 13. How do collaborative filtering and matrix factorisation work for recommendations?

<details>
<summary>Answer</summary>

- **Collaborative filtering** – recommend based on similar users ("users like you liked…") or similar items ("people who bought X bought Y"), using the user–item interaction matrix.
- **Matrix factorisation** – approximate the sparse user–item matrix as the product of user and item **embedding** matrices (`R ≈ UVᵀ`), learned with ALS or SGD; the score is the dot product of user and item vectors. Popularised by the Netflix Prize.

Problems: **cold start** (new users/items have no interactions – use content features), popularity bias, and implicit feedback (clicks, not ratings). Modern systems use **two-tower** neural models and multi-stage ranking – see [ML & GenAI System Design](20-ml-genai-system-design.md).

</details>

### 14. What are the basics of time-series forecasting?

<details>
<summary>Answer</summary>

- Components: **trend, seasonality, cycles, noise**; check **stationarity** (constant mean/variance) and difference the series if needed.
- Classical models: moving averages, exponential smoothing (Holt-Winters), **ARIMA/SARIMA**, Prophet.
- ML approaches: gradient boosting on lag and calendar features; deep models (LSTMs, temporal convolution, transformer-based forecasters, and pretrained time-series foundation models).
- Always validate with **time-based splits** (walk-forward validation); never shuffle.

</details>

### 15. How do you choose an algorithm for a problem?

<details>
<summary>Answer</summary>

- **Tabular data** → gradient-boosted trees (XGBoost/LightGBM); logistic regression as a baseline and when interpretability is required.
- **Images, audio, video** → deep learning (CNNs/vision transformers), usually fine-tuning a pretrained model.
- **Text** → pretrained transformers (embeddings + a classifier, fine-tuning, or LLM prompting).
- **Small data** → simpler models, strong regularisation, transfer learning.
- **Need for explanations** → linear models, shallow trees, or SHAP on top of complex models.
- **Latency/cost constraints** → smaller models, distillation.

Always start with a simple baseline and let validation metrics decide.

</details>

### 16. What is the difference between bagging and dropout?

<details>
<summary>Answer</summary>

Both reduce variance by averaging many models. **Bagging** explicitly trains separate models on bootstrap samples and averages them. **Dropout** randomly drops neurons during each training step, so the network effectively trains an exponential number of thinned sub-networks that share weights; at inference, using the full network approximates averaging them. Dropout is "cheap bagging" for neural networks.

</details>

---

[⬅ Previous: ML Fundamentals](03-ml-fundamentals.md) · [⬅ Back to index](../README.md) · [Next: Evaluation Metrics ➡](05-evaluation-metrics.md)
