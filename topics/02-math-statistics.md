# Math & Statistics for ML

[⬅ Back to index](../README.md)

Covers: vectors and matrices, dot product and cosine similarity, eigenvalues and SVD, derivatives and the chain rule, gradients, convexity, probability rules, Bayes' theorem, common distributions, expectation and variance, MLE vs MAP, the central limit theorem, hypothesis testing, p-values, confidence intervals, A/B test statistics, entropy, cross-entropy and KL divergence.

---

## Linear Algebra

### 1. What is a dot product, and what is cosine similarity?

<details>
<summary>Answer</summary>

- **Dot product** `a · b = Σ aᵢbᵢ = |a||b| cos θ`. It measures how aligned two vectors are, scaled by their lengths.
- **Cosine similarity** `cos θ = (a · b) / (|a||b|)` removes the length, so it ranges from −1 (opposite) to 1 (same direction).

In ML: cosine similarity compares embeddings (semantic search, recommendations); dot products are at the heart of neural network layers and attention scores. If vectors are **normalised to unit length**, dot product = cosine similarity, which is why many vector databases normalise embeddings.

</details>

### 2. What is matrix multiplication doing in a neural network?

<details>
<summary>Answer</summary>

A dense layer computes `y = Wx + b`: each output is a weighted sum (dot product) of all inputs. Stacking a batch of inputs as a matrix `X` (batch × features) turns the whole layer into one matrix multiplication `XWᵀ`, which GPUs execute extremely fast. Shapes matter: `(n × d) · (d × k) → (n × k)`. Getting shapes right (and explaining them) is a common whiteboard check.

</details>

### 3. What are eigenvalues and eigenvectors, and where are they used?

<details>
<summary>Answer</summary>

For a square matrix `A`, an eigenvector `v` only gets scaled, not rotated: `Av = λv`, where `λ` is the eigenvalue. For a **covariance matrix**, eigenvectors are the directions of maximum variance and eigenvalues are how much variance lies along each.

Uses: **PCA** (principal components are the top eigenvectors of the covariance matrix), spectral clustering, understanding stability of iterative methods, PageRank (the dominant eigenvector of the link matrix).

</details>

### 4. What is SVD and why is it useful?

<details>
<summary>Answer</summary>

**Singular Value Decomposition** factors any matrix `A = UΣVᵀ`: orthogonal `U` and `V`, and diagonal `Σ` of singular values (sorted). Keeping only the top k singular values gives the **best rank-k approximation** of `A`.

Uses: PCA (computed via SVD for numerical stability), dimensionality reduction, latent semantic analysis, matrix factorisation for recommendations, compressing weight matrices, and the intuition behind **LoRA** (weight updates are approximately low rank).

</details>

### 5. What does it mean for vectors to be linearly independent, and what is the rank of a matrix?

<details>
<summary>Answer</summary>

Vectors are **linearly independent** if none can be written as a combination of the others. The **rank** of a matrix is the number of linearly independent rows (or columns) – the dimension of the space it spans. In ML: perfectly correlated features make the design matrix rank-deficient, so linear regression has no unique solution (**multicollinearity**); regularisation (ridge) fixes this.

</details>

---

## Calculus & Optimisation

### 6. What is a gradient, and why does gradient descent work?

<details>
<summary>Answer</summary>

The **gradient** ∇L is the vector of partial derivatives of the loss with respect to each parameter; it points in the direction of **steepest increase**. Gradient descent steps the other way: `θ ← θ − η ∇L(θ)`, where `η` is the learning rate. For small enough steps, each step reduces the loss (first-order Taylor approximation). Too large a learning rate overshoots or diverges; too small converges slowly.

</details>

### 7. What is the chain rule, and why does it matter for deep learning?

<details>
<summary>Answer</summary>

For composed functions `f(g(x))`, `df/dx = f'(g(x)) · g'(x)`. A neural network is a long composition of layers, so the gradient of the loss with respect to an early weight is a **product of local derivatives** along the path. **Backpropagation** applies the chain rule efficiently from the output backwards, reusing intermediate results. Long products of small derivatives cause **vanishing gradients**; large ones cause **exploding gradients**.

</details>

### 8. What is a convex function, and why do we care?

<details>
<summary>Answer</summary>

A function is **convex** if the line segment between any two points on its graph lies above the graph (bowl-shaped). Any local minimum is the **global minimum**, so gradient descent reliably finds it. Linear regression (MSE), logistic regression (log loss) and SVMs have convex losses. Neural networks are **non-convex** (many local minima and saddle points), yet in practice SGD finds good solutions, partly because high-dimensional landscapes have many roughly equally good minima.

</details>

---

## Probability

### 9. What is Bayes' theorem? Give an example.

<details>
<summary>Answer</summary>

`P(A|B) = P(B|A) · P(A) / P(B)` – update the **prior** P(A) with evidence B to get the **posterior**.

Classic example: a disease affects 1% of people; a test is 99% sensitive and has a 5% false-positive rate. If you test positive:
`P(disease|+) = 0.99 × 0.01 / (0.99 × 0.01 + 0.05 × 0.99) ≈ 0.0099 / 0.0594 ≈ 16.7%`.
Most positives are false because the disease is rare (**base rate**). This intuition matters for fraud and anomaly detection, where positives are rare.

</details>

### 10. Which probability distributions should you know?

<details>
<summary>Answer</summary>

- **Bernoulli / Binomial** – one yes/no trial / number of successes in n trials (clicks out of impressions).
- **Categorical / Multinomial** – one of K outcomes (next-token prediction is a categorical distribution over the vocabulary).
- **Poisson** – counts of events in an interval (requests per minute).
- **Normal (Gaussian)** – sums of many effects; measurement noise; the CLT.
- **Exponential** – time between Poisson events.
- **Uniform** – random initialisation, sampling.
- **Beta** – a distribution over probabilities (Bayesian A/B testing, Thompson sampling).

</details>

### 11. What are expectation, variance and covariance?

<details>
<summary>Answer</summary>

- **Expectation** `E[X]` – the probability-weighted average.
- **Variance** `Var(X) = E[(X − E[X])²]` – spread; the standard deviation is its square root.
- **Covariance** `Cov(X, Y) = E[(X − E[X])(Y − E[Y])]` – how two variables move together; **correlation** normalises it to [−1, 1].

Useful facts: `E[aX + b] = aE[X] + b`; `Var(aX) = a²Var(X)`; for independent variables, variances add. Correlation measures only **linear** relationships and doesn't imply causation.

</details>

### 12. What is maximum likelihood estimation (MLE), and how does it relate to loss functions?

<details>
<summary>Answer</summary>

MLE picks parameters that make the observed data most probable: `θ* = argmax Π P(xᵢ|θ)`, usually by maximising the **log-likelihood** (sums are easier than products).

Common losses are negative log-likelihoods:
- Assuming Gaussian noise → maximising likelihood = minimising **mean squared error**.
- Assuming Bernoulli/categorical outputs → minimising **cross-entropy / log loss**.
LLM pretraining is MLE: maximise the probability of each next token given the previous ones.

</details>

### 13. MLE vs MAP estimation?

<details>
<summary>Answer</summary>

**MAP (maximum a posteriori)** adds a prior: `θ* = argmax P(data|θ) · P(θ)`. Taking logs, the prior becomes a penalty term:
- A **Gaussian prior** on weights → **L2 regularisation** (ridge).
- A **Laplace prior** → **L1 regularisation** (lasso).
So regularisation can be seen as encoding a belief that weights should be small. With lots of data, MAP and MLE converge.

</details>

---

## Statistics & Experimentation

### 14. What is the central limit theorem and why does it matter?

<details>
<summary>Answer</summary>

The distribution of the **sample mean** of many independent samples approaches a **normal distribution** as the sample size grows, whatever the original distribution (with finite variance). Its standard error is `σ/√n`. This is why we can build confidence intervals and run z/t-tests on averages (conversion rates, latency means) in A/B tests, even when the underlying data isn't normal.

</details>

### 15. What is a p-value, and what are Type I and Type II errors?

<details>
<summary>Answer</summary>

- **p-value** – the probability of seeing a result at least as extreme as observed **if the null hypothesis were true**. It is *not* the probability that the null is true.
- **Type I error (false positive)** – rejecting a true null; its rate is α (often 0.05).
- **Type II error (false negative)** – failing to reject a false null; its rate is β. **Power** = 1 − β (often targeted at 0.8).

Pitfalls: p-hacking, peeking at results repeatedly, testing many metrics without correction (multiple comparisons).

</details>

### 16. How do you design and analyse an A/B test for a model change?

<details>
<summary>Answer</summary>

1. Define the **primary metric** (e.g. click-through, conversion) and **guardrail metrics** (latency, complaints, revenue).
2. Compute the **sample size** from the baseline rate, the minimum detectable effect, α and power.
3. **Randomise** at the right unit (user, not request, to avoid contamination) and check sample ratio mismatch.
4. Run for full weekly cycles; don't stop early on a good-looking p-value (or use sequential testing).
5. Analyse with a two-proportion z-test or t-test, confidence intervals, and segment checks.
6. Watch for novelty effects, network effects (marketplaces, social) and long-term effects.

</details>

### 17. What is a confidence interval?

<details>
<summary>Answer</summary>

A range computed from data that, under repeated sampling, contains the true parameter a stated fraction of the time (e.g. 95%). For a mean: `x̄ ± 1.96 · s/√n`. Interpretation: "the procedure captures the true value 95% of the time", not "there's a 95% probability the true value is in this specific interval" (that's the Bayesian credible interval). Reporting intervals for model metrics (e.g. accuracy ± error) shows statistical maturity.

</details>

### 18. What are entropy, cross-entropy and KL divergence?

<details>
<summary>Answer</summary>

- **Entropy** `H(p) = −Σ p(x) log p(x)` – the average uncertainty of a distribution.
- **Cross-entropy** `H(p, q) = −Σ p(x) log q(x)` – the average cost of encoding data from p using model q. With one-hot labels, it's just `−log q(correct class)`: the classification loss.
- **KL divergence** `KL(p‖q) = H(p, q) − H(p)` – how much q differs from p; not symmetric, always ≥ 0.

In ML: cross-entropy is the loss for classifiers and LLMs; **perplexity** = exp(cross-entropy) per token; KL appears in VAEs, knowledge distillation, and RLHF/DPO (keeping the fine-tuned model close to the reference model).

</details>

### 19. What is the difference between correlation and causation, and how do you establish causation?

<details>
<summary>Answer</summary>

Correlation can come from confounders (ice cream sales and drownings both rise in summer), reverse causation or chance. To establish causation: **randomised controlled experiments** (A/B tests) are the gold standard. When experiments aren't possible, use causal-inference methods: difference-in-differences, regression discontinuity, instrumental variables, propensity score matching – each with assumptions you must state.

</details>

---

[⬅ Previous: AI Interviews: Roles & Rounds](01-ai-interviews-overview.md) · [⬅ Back to index](../README.md) · [Next: ML Fundamentals ➡](03-ml-fundamentals.md)
