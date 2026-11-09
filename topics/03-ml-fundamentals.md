# ML Fundamentals

[⬅ Back to index](../README.md)

Covers: types of learning, the ML workflow, bias-variance trade-off, overfitting and underfitting, regularisation, train/validation/test splits, cross-validation, data leakage, feature engineering, feature scaling, missing values, categorical encoding, class imbalance, the curse of dimensionality, parametric vs non-parametric models, generative vs discriminative models.

---

### 1. What are supervised, unsupervised, self-supervised and reinforcement learning?

<details>
<summary>Answer</summary>

- **Supervised** – learn from labelled pairs (x, y): classification, regression. Example: spam detection.
- **Unsupervised** – find structure without labels: clustering, dimensionality reduction, anomaly detection.
- **Semi-supervised** – a few labels plus many unlabelled examples.
- **Self-supervised** – create labels from the data itself: predict the next token (GPT), masked words (BERT), or matching image-text pairs (CLIP). This is how foundation models are pretrained.
- **Reinforcement learning** – an agent learns actions by maximising reward through interaction: games, robotics, and **RLHF** for aligning LLMs.

</details>

### 2. What is the bias-variance trade-off?

<details>
<summary>Answer</summary>

Expected error = **bias² + variance + irreducible noise**.
- **Bias** – error from wrong assumptions; the model is too simple (underfits). High training and test error.
- **Variance** – error from sensitivity to the particular training set; the model is too complex (overfits). Low training error, high test error.

Increasing model complexity lowers bias but raises variance. Fixes:
- High bias → more features, bigger model, less regularisation, train longer.
- High variance → more data, regularisation, simpler model, dropout, early stopping, ensembling (bagging).

Note: very large deep networks can show **double descent**, where test error falls again past the interpolation point; classical intuition still guides most practical decisions.

</details>

### 3. How do you detect and prevent overfitting?

<details>
<summary>Answer</summary>

**Detect** – a large gap between training and validation performance; validation loss starts rising while training loss keeps falling (learning curves).

**Prevent**
- More (or augmented) training data.
- Regularisation: L1/L2, dropout, weight decay.
- Simpler model / fewer features.
- Early stopping on validation loss.
- Cross-validation for model selection.
- Ensembles (bagging, random forests).

</details>

### 4. L1 vs L2 regularisation?

<details>
<summary>Answer</summary>

- **L2 (ridge / weight decay)** adds `λΣw²`: shrinks all weights smoothly towards zero; handles correlated features well; rarely makes weights exactly zero.
- **L1 (lasso)** adds `λΣ|w|`: drives many weights **exactly to zero**, giving **sparse** models and built-in feature selection. Geometrically, the L1 constraint region is a diamond whose corners lie on the axes, so the optimum often hits a corner.
- **Elastic net** combines both.

λ controls strength: too high underfits, too low overfits; tune it with validation.

</details>

### 5. Why do we need train, validation and test sets?

<details>
<summary>Answer</summary>

- **Training set** – fits the model's parameters.
- **Validation set** – tunes hyperparameters and selects models; you look at it many times, so it slowly gets "used up".
- **Test set** – touched **once** at the end for an unbiased estimate of real-world performance.

Split by the right unit: by **time** for forecasting and anything with temporal drift (train on the past, test on the future), by **user/group** when the same user appears in many rows, so the model can't memorise them.

</details>

### 6. What is k-fold cross-validation and when do you use it?

<details>
<summary>Answer</summary>

Split the data into k folds; train on k−1 and validate on the remaining one, rotating k times; average the scores. It uses data efficiently and gives a variance estimate, which is useful for **small datasets** and model selection. Variants: **stratified** k-fold (keeps class ratios), **group** k-fold (keeps a user's rows together), **time-series split** (always validate on later data). It's often too expensive for large deep learning models, where a single held-out validation set is used.

</details>

### 7. What is data leakage? Give examples.

<details>
<summary>Answer</summary>

Leakage is when information that won't be available at prediction time sneaks into training, producing great offline metrics that collapse in production. Examples:
- A feature computed **after** the outcome (e.g. "number of support calls" when predicting churn, where calls happen after the churn decision).
- **Scaling or imputing on the full dataset** before splitting (the test set's statistics leak into training).
- Duplicates or the same user in both train and test.
- Random splits on time-series data.
- Target encoding computed without out-of-fold methods.

Prevention: fit all preprocessing on training data only (pipelines), split by time or group, and ask "would I know this value at prediction time?" for every feature.

</details>

### 8. What is feature engineering, and what are common techniques?

<details>
<summary>Answer</summary>

Turning raw data into inputs a model can use well:
- **Numerical** – scaling, log transforms for skewed data, binning, ratios, interactions.
- **Categorical** – one-hot, target encoding, embeddings for high cardinality.
- **Time** – hour of day, day of week (cyclical sin/cos encoding), time since last event, rolling aggregates.
- **Text** – TF-IDF, embeddings.
- **Aggregations** – user-level counts and averages over windows (e.g. "transactions in the last hour" for fraud).

Deep learning learns many features automatically, but feature engineering still matters a lot for tabular data.

</details>

### 9. When and why do you scale features?

<details>
<summary>Answer</summary>

- **Standardisation** (z-score: mean 0, std 1) or **min-max scaling** (to [0, 1]).
- Needed for models that use **distances or gradients**: k-NN, k-means, SVMs, PCA, linear/logistic regression with regularisation, neural networks (faster, more stable training).
- **Not needed** for tree-based models (splits are scale-invariant).
- Fit the scaler on training data only, then apply it to validation and test data.

</details>

### 10. How do you handle missing values?

<details>
<summary>Answer</summary>

First ask **why** values are missing (random, or meaningful – "no previous loan").
- Drop rows/columns if few and random.
- Impute with mean/median/mode, or a model-based imputer (k-NN, iterative).
- Add a **"was missing" indicator** feature – missingness is often predictive.
- Use models that handle missing values natively (XGBoost, LightGBM).
- For time series: forward-fill or interpolation.

</details>

### 11. How do you encode categorical variables?

<details>
<summary>Answer</summary>

- **One-hot encoding** – a binary column per category; fine for low cardinality; explodes for many categories.
- **Ordinal/label encoding** – integers; only for ordered categories (or for tree models).
- **Target (mean) encoding** – replace each category with the average target; powerful for high cardinality but **leaks** unless computed out-of-fold with smoothing.
- **Hashing trick** – hash categories into a fixed number of buckets.
- **Learned embeddings** – dense vectors trained with the model; standard for IDs (users, items) in deep recommenders.

</details>

### 12. How do you handle imbalanced classes (e.g. 1% fraud)?

<details>
<summary>Answer</summary>

- **Pick the right metric** – precision, recall, F1, **PR-AUC**; never plain accuracy (99% accuracy by predicting "not fraud" always).
- **Resampling** – undersample the majority, oversample the minority, or **SMOTE** (synthetic minority examples); only on training data.
- **Class weights** in the loss so minority errors cost more.
- **Threshold tuning** – choose the decision threshold based on the business cost of false positives vs false negatives.
- Anomaly detection framing when positives are extremely rare.
- Collect more positive examples where possible.

</details>

### 13. What is the curse of dimensionality?

<details>
<summary>Answer</summary>

As the number of features grows, data becomes **sparse** in the space: the volume grows exponentially, distances between points become similar (nearest neighbours lose meaning), and you need exponentially more data to cover the space. Effects: distance-based methods (k-NN, clustering) degrade and models overfit. Remedies: feature selection, dimensionality reduction (PCA, embeddings), regularisation, and more data.

</details>

### 14. Parametric vs non-parametric models?

<details>
<summary>Answer</summary>

- **Parametric** – a fixed number of parameters regardless of data size (linear/logistic regression, a fixed neural network). Fast, need less data, but strong assumptions.
- **Non-parametric** – complexity grows with data (k-NN, decision trees, kernel SVMs, Gaussian processes). Flexible, fewer assumptions, but need more data and can be slower at prediction time.

</details>

### 15. Generative vs discriminative models?

<details>
<summary>Answer</summary>

- **Discriminative** models learn `P(y|x)` or the decision boundary directly: logistic regression, SVMs, most classifiers. Usually better for pure classification.
- **Generative** models learn `P(x, y)` or `P(x)` – how the data is generated: Naive Bayes, Gaussian mixture models, VAEs, GANs, diffusion models, and autoregressive LLMs (`P(next token | context)`). They can **generate new samples**, handle missing data, and work with less labelled data.

</details>

### 16. What is the difference between a parameter and a hyperparameter? How do you tune hyperparameters?

<details>
<summary>Answer</summary>

**Parameters** are learned from data (weights). **Hyperparameters** are set before training (learning rate, regularisation strength, tree depth, number of layers, batch size).

Tuning methods: grid search (exhaustive, expensive), **random search** (usually better than grid for the same budget), **Bayesian optimisation** (Optuna, Hyperopt), early-stopping-based methods (Hyperband/ASHA). Always tune on validation data, not the test set.

</details>

### 17. What is ensembling? Bagging vs boosting vs stacking?

<details>
<summary>Answer</summary>

Combining several models usually beats any single one.
- **Bagging** – train models in parallel on bootstrap samples and average them; **reduces variance** (random forests).
- **Boosting** – train models sequentially, each fixing the previous ones' errors; **reduces bias** (AdaBoost, gradient boosting, XGBoost, LightGBM, CatBoost).
- **Stacking** – a meta-model learns how to combine the predictions of diverse base models.

</details>

### 18. What is transfer learning?

<details>
<summary>Answer</summary>

Reusing a model trained on a large dataset as the starting point for a related task with less data: e.g. an ImageNet-pretrained CNN fine-tuned to detect defects, or a pretrained language model fine-tuned for sentiment. Options: use it as a **frozen feature extractor**, fine-tune only the top layers, or fine-tune everything with a small learning rate. Foundation models (LLMs, CLIP) take this to the extreme: one pretrained model adapted to many tasks via prompting, retrieval or light fine-tuning.

</details>

### 19. What is concept drift vs data drift?

<details>
<summary>Answer</summary>

- **Data (covariate) drift** – the input distribution `P(x)` changes (new user demographics, a new device type).
- **Concept drift** – the relationship `P(y|x)` changes (fraudsters change tactics; what counts as spam evolves).
- **Label drift** – the class balance `P(y)` changes.

Detect with monitoring (feature distribution statistics like PSI, KS tests; performance on fresh labelled data) and respond with retraining or model updates. See [MLOps](19-mlops-production.md).

</details>

### 20. What is the "no free lunch" theorem, and what's the practical lesson?

<details>
<summary>Answer</summary>

Averaged over all possible problems, no learning algorithm beats any other. The practical lesson: there's no universally best model – choose based on the data and problem (gradient-boosted trees usually win on tabular data; deep learning on images, audio and text), **start with a simple baseline**, and compare empirically.

</details>

### 21. How do you handle outliers?

<details>
<summary>Answer</summary>

First decide whether they're **errors** (sensor glitches, unit mistakes → fix or remove) or **real but rare** values (big transactions → keep; they may be the most important cases).
- Detect: z-scores, IQR rule, isolation forest, domain rules, visualisation.
- Handle: cap/winsorise extreme values, log-transform skewed features, use robust models and losses (tree models, Huber/MAE loss, median instead of mean), or model them separately.
- Never remove outliers from the test set just to improve metrics – production will contain them.

</details>

### 22. What feature selection methods do you know?

<details>
<summary>Answer</summary>

- **Filter methods** – rank features by statistics independent of the model: correlation, mutual information, chi-squared, variance threshold. Fast, may miss interactions.
- **Wrapper methods** – evaluate subsets with the model: forward selection, backward elimination, recursive feature elimination. Accurate but expensive.
- **Embedded methods** – selection happens during training: L1 (lasso) zeroing weights, tree-based feature importance.
- **Permutation importance / SHAP** – measure how much performance drops when a feature is shuffled.

Reasons to select features: less overfitting, faster training and serving, easier explanation, and fewer data dependencies to maintain. Do selection inside cross-validation to avoid leakage.

</details>

### 23. How do you deal with noisy labels?

<details>
<summary>Answer</summary>

- **Measure** label quality: have several annotators label a sample and compute agreement (Cohen's/Fleiss' kappa); review disagreements.
- **Clean** – find likely mislabelled examples (high loss on held-out folds, confident-learning methods) and relabel them.
- **Improve guidelines** and annotator training; use gold questions to monitor annotators.
- **Robust training** – label smoothing, robust losses, early stopping (models memorise noise late in training), down-weighting low-agreement examples.
- Make sure the **test set** is especially clean – noisy test labels make all evaluation misleading.

</details>

### 24. What is active learning?

<details>
<summary>Answer</summary>

When labelling is expensive, let the model choose **which examples to label next**: train on a small labelled set, then ask annotators to label the examples the model is most **uncertain** about (lowest confidence, highest entropy, disagreement within an ensemble) or the most **diverse/representative** ones, retrain, and repeat. It reaches a target accuracy with far fewer labels than random sampling. Watch out for sampling bias (the labelled set no longer matches the real distribution – keep a random test set).

</details>

### 25. Multi-class vs multi-label classification?

<details>
<summary>Answer</summary>

- **Multi-class** – each example has exactly **one** of K classes: a softmax output with categorical cross-entropy; prediction = argmax.
- **Multi-label** – each example can have **several** labels (a photo with "beach", "dog", "sunset"): K independent sigmoid outputs with binary cross-entropy per label; a threshold per label (tuned separately).

Metrics also differ: multi-label uses per-label precision/recall, micro/macro F1, Hamming loss, or subset accuracy.

</details>

---

[⬅ Previous: Math & Statistics for ML](02-math-statistics.md) · [⬅ Back to index](../README.md) · [Next: Classical ML Algorithms ➡](04-classical-algorithms.md)
