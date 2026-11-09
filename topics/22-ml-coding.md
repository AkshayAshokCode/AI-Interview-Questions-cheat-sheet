# ML Coding (Implement From Scratch)

[⬅ Back to index](../README.md)

Covers: the from-scratch implementations commonly asked in ML coding rounds, in Python with NumPy (and PyTorch where natural): linear regression with gradient descent, logistic regression, k-means, k-NN, softmax and cross-entropy, evaluation metrics, scaled dot-product attention, multi-head attention, a PyTorch training loop, top-k cosine similarity search, a BPE tokeniser step, a decision-tree split, and sampling with temperature and top-p.

> Tips for the round: clarify input shapes first, write vectorised NumPy (avoid Python loops over samples), handle numerical stability (softmax, log), and test with a tiny example.

---

### 1. Implement linear regression with gradient descent.

<details>
<summary>Answer</summary>

```python
import numpy as np

def linear_regression(X, y, lr=0.01, epochs=1000):
    n, d = X.shape
    w, b = np.zeros(d), 0.0
    for _ in range(epochs):
        y_pred = X @ w + b
        error = y_pred - y                    # (n,)
        grad_w = (2 / n) * X.T @ error        # d(MSE)/dw
        grad_b = (2 / n) * error.sum()
        w -= lr * grad_w
        b -= lr * grad_b
    return w, b
```

Talking points: scale features first for faster convergence; the closed form `np.linalg.lstsq(X, y)` for small d; add L2 by `grad_w += 2 * lam * w`; mini-batches for large n.

</details>

### 2. Implement logistic regression.

<details>
<summary>Answer</summary>

```python
def sigmoid(z):
    return 1 / (1 + np.exp(-z))

def logistic_regression(X, y, lr=0.1, epochs=1000):
    n, d = X.shape
    w, b = np.zeros(d), 0.0
    for _ in range(epochs):
        p = sigmoid(X @ w + b)
        grad_w = X.T @ (p - y) / n            # gradient of binary cross-entropy
        grad_b = (p - y).mean()
        w -= lr * grad_w
        b -= lr * grad_b
    return w, b

def bce_loss(y, p, eps=1e-12):
    p = np.clip(p, eps, 1 - eps)              # avoid log(0)
    return -np.mean(y * np.log(p) + (1 - y) * np.log(1 - p))
```

Note the gradient `Xᵀ(p − y)/n` – the same simple form as linear regression, which interviewers like you to derive.

</details>

### 3. Implement k-means clustering.

<details>
<summary>Answer</summary>

```python
def kmeans(X, k, iters=100, seed=0):
    rng = np.random.default_rng(seed)
    centroids = X[rng.choice(len(X), k, replace=False)]
    for _ in range(iters):
        # distances: (n, k)
        dists = np.linalg.norm(X[:, None, :] - centroids[None, :, :], axis=2)
        labels = dists.argmin(axis=1)
        new = np.array([X[labels == j].mean(axis=0) if np.any(labels == j) else centroids[j]
                        for j in range(k)])
        if np.allclose(new, centroids):
            break
        centroids = new
    return centroids, labels
```

Follow-ups: k-means++ initialisation, empty clusters (handled above by keeping the old centroid), memory of the (n, k, d) broadcast for large data (compute `‖x‖² − 2x·c + ‖c‖²` instead), choosing k.

</details>

### 4. Implement k-nearest neighbours classification.

<details>
<summary>Answer</summary>

```python
def knn_predict(X_train, y_train, X_test, k=5):
    # squared Euclidean distances via the expansion trick: (m, n)
    d2 = (np.sum(X_test**2, axis=1)[:, None]
          - 2 * X_test @ X_train.T
          + np.sum(X_train**2, axis=1)[None, :])
    idx = np.argpartition(d2, k, axis=1)[:, :k]      # k smallest, unordered, O(n)
    neighbour_labels = y_train[idx]
    return np.array([np.bincount(row).argmax() for row in neighbour_labels])
```

Mention `argpartition` vs full sort, scaling features, ties, and ANN indexes for large data.

</details>

### 5. Implement a numerically stable softmax and cross-entropy.

<details>
<summary>Answer</summary>

```python
def softmax(logits, axis=-1):
    z = logits - logits.max(axis=axis, keepdims=True)   # stability: avoid exp overflow
    e = np.exp(z)
    return e / e.sum(axis=axis, keepdims=True)

def cross_entropy(logits, targets):
    # logits: (n, C), targets: (n,) class indices
    z = logits - logits.max(axis=1, keepdims=True)
    log_probs = z - np.log(np.exp(z).sum(axis=1, keepdims=True))   # log-softmax
    return -log_probs[np.arange(len(targets)), targets].mean()
```

Subtracting the max doesn't change the result (softmax is shift-invariant) but prevents overflow. The gradient of cross-entropy w.r.t. logits is simply `softmax(logits) − one_hot(targets)`.

</details>

### 6. Implement precision, recall, F1 and ROC-AUC.

<details>
<summary>Answer</summary>

```python
def precision_recall_f1(y_true, y_pred):
    tp = np.sum((y_pred == 1) & (y_true == 1))
    fp = np.sum((y_pred == 1) & (y_true == 0))
    fn = np.sum((y_pred == 0) & (y_true == 1))
    precision = tp / (tp + fp) if tp + fp else 0.0
    recall = tp / (tp + fn) if tp + fn else 0.0
    f1 = 2 * precision * recall / (precision + recall) if precision + recall else 0.0
    return precision, recall, f1

def roc_auc(y_true, scores):
    # probability a random positive outranks a random negative (rank-based, handles ties)
    order = np.argsort(scores)
    ranks = np.empty(len(scores)); ranks[order] = np.arange(1, len(scores) + 1)
    for s in np.unique(scores):                      # average ranks for ties
        m = scores == s; ranks[m] = ranks[m].mean()
    n_pos = y_true.sum(); n_neg = len(y_true) - n_pos
    return (ranks[y_true == 1].sum() - n_pos * (n_pos + 1) / 2) / (n_pos * n_neg)
```

</details>

### 7. Implement scaled dot-product attention (with an optional causal mask).

<details>
<summary>Answer</summary>

```python
def attention(Q, K, V, causal=False):
    # Q: (n_q, d_k), K: (n_k, d_k), V: (n_k, d_v)
    d_k = Q.shape[-1]
    scores = Q @ K.T / np.sqrt(d_k)                  # (n_q, n_k)
    if causal:
        n = scores.shape[0]
        mask = np.triu(np.ones((n, n), dtype=bool), k=1)
        scores = np.where(mask, -np.inf, scores)      # block future positions
    weights = softmax(scores, axis=-1)
    return weights @ V, weights
```

Be ready to explain each line (why √d_k, what the mask does) and the O(n²) cost.

</details>

### 8. Implement multi-head self-attention in PyTorch.

<details>
<summary>Answer</summary>

```python
import torch, torch.nn as nn, torch.nn.functional as F

class MultiHeadSelfAttention(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        assert d_model % n_heads == 0
        self.h, self.d_head = n_heads, d_model // n_heads
        self.qkv = nn.Linear(d_model, 3 * d_model)
        self.out = nn.Linear(d_model, d_model)

    def forward(self, x, causal=True):
        B, T, C = x.shape
        q, k, v = self.qkv(x).split(C, dim=-1)
        # (B, T, C) -> (B, h, T, d_head)
        q, k, v = (t.view(B, T, self.h, self.d_head).transpose(1, 2) for t in (q, k, v))
        att = (q @ k.transpose(-2, -1)) / self.d_head ** 0.5          # (B, h, T, T)
        if causal:
            mask = torch.triu(torch.ones(T, T, dtype=torch.bool, device=x.device), 1)
            att = att.masked_fill(mask, float("-inf"))
        y = F.softmax(att, dim=-1) @ v                                # (B, h, T, d_head)
        y = y.transpose(1, 2).contiguous().view(B, T, C)              # concat heads
        return self.out(y)
```

In production you'd call `F.scaled_dot_product_attention`, which uses FlashAttention kernels.

</details>

### 9. Write a PyTorch training loop.

<details>
<summary>Answer</summary>

```python
def train(model, train_loader, val_loader, epochs=10, lr=3e-4, device="cuda"):
    model.to(device)
    opt = torch.optim.AdamW(model.parameters(), lr=lr, weight_decay=0.01)
    loss_fn = nn.CrossEntropyLoss()
    for epoch in range(epochs):
        model.train()
        for xb, yb in train_loader:
            xb, yb = xb.to(device), yb.to(device)
            opt.zero_grad()
            loss = loss_fn(model(xb), yb)
            loss.backward()
            torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
            opt.step()
        model.eval()
        correct = total = 0
        with torch.no_grad():
            for xb, yb in val_loader:
                preds = model(xb.to(device)).argmax(dim=1)
                correct += (preds == yb.to(device)).sum().item(); total += len(yb)
        print(f"epoch {epoch}: val acc {correct / total:.3f}")
```

Points interviewers check: `zero_grad`, `train()`/`eval()`, `no_grad()` for validation, device handling, gradient clipping, and where you'd add a scheduler, mixed precision (`torch.autocast`) and checkpointing.

</details>

### 10. Return the top-k most similar vectors to a query (cosine similarity).

<details>
<summary>Answer</summary>

```python
def top_k_cosine(query, matrix, k=5):
    # query: (d,), matrix: (n, d)
    q = query / np.linalg.norm(query)
    M = matrix / np.linalg.norm(matrix, axis=1, keepdims=True)
    sims = M @ q                                      # (n,)
    idx = np.argpartition(-sims, k)[:k]               # unordered top k in O(n)
    idx = idx[np.argsort(-sims[idx])]                 # sort only those k
    return idx, sims[idx]
```

Follow-ups: precompute normalised vectors, batch many queries as a matrix multiply, and switch to an ANN index (FAISS/HNSW) for millions of vectors.

</details>

### 11. Implement one training step of a BPE tokeniser.

<details>
<summary>Answer</summary>

```python
from collections import Counter

def most_frequent_pair(words):
    # words: dict mapping tuple of symbols -> frequency, e.g. ('l','o','w','</w>'): 5
    pairs = Counter()
    for symbols, freq in words.items():
        for a, b in zip(symbols, symbols[1:]):
            pairs[(a, b)] += freq
    return pairs.most_common(1)[0][0] if pairs else None

def merge_pair(words, pair):
    merged = {}
    for symbols, freq in words.items():
        out, i = [], 0
        while i < len(symbols):
            if i < len(symbols) - 1 and (symbols[i], symbols[i + 1]) == pair:
                out.append(symbols[i] + symbols[i + 1]); i += 2
            else:
                out.append(symbols[i]); i += 1
        merged[tuple(out)] = merged.get(tuple(out), 0) + freq
    return merged

# training: repeat num_merges times: pair = most_frequent_pair(words); words = merge_pair(words, pair)
```

</details>

### 12. Find the best split for a decision tree (Gini impurity).

<details>
<summary>Answer</summary>

```python
def gini(y):
    _, counts = np.unique(y, return_counts=True)
    p = counts / counts.sum()
    return 1 - np.sum(p ** 2)

def best_split(X, y):
    best = (None, None, gini(y))                     # (feature, threshold, weighted impurity)
    n = len(y)
    for f in range(X.shape[1]):
        for t in np.unique(X[:, f]):
            left = X[:, f] <= t
            if left.all() or (~left).all():
                continue
            imp = (left.sum() * gini(y[left]) + (~left).sum() * gini(y[~left])) / n
            if imp < best[2]:
                best = (f, t, imp)
    return best
```

Mention complexity (sorting each feature allows an O(n log n) scan with running counts) and how recursion builds the tree.

</details>

### 13. Sample the next token with temperature and top-p.

<details>
<summary>Answer</summary>

```python
def sample_next_token(logits, temperature=1.0, top_p=0.9, rng=np.random.default_rng()):
    if temperature == 0:
        return int(np.argmax(logits))                 # greedy
    probs = softmax(logits / temperature)
    order = np.argsort(-probs)
    cumulative = np.cumsum(probs[order])
    cutoff = np.searchsorted(cumulative, top_p) + 1   # smallest set with mass >= top_p
    keep = order[:cutoff]
    p = probs[keep] / probs[keep].sum()
    return int(rng.choice(keep, p=p))
```

</details>

### 14. Implement a simple batched data loader / mini-batch generator.

<details>
<summary>Answer</summary>

```python
def minibatches(X, y, batch_size=32, shuffle=True, seed=0):
    idx = np.arange(len(X))
    if shuffle:
        np.random.default_rng(seed).shuffle(idx)
    for start in range(0, len(X), batch_size):
        b = idx[start:start + batch_size]
        yield X[b], y[b]
```

A generator avoids loading copies of the whole dataset; in PyTorch, the equivalent is a `Dataset` with `__len__`/`__getitem__` plus a `DataLoader` (shuffling, batching, parallel workers, pinned memory).

</details>

---

[⬅ Previous: Responsible AI, Safety & Security](21-responsible-ai-safety.md) · [⬅ Back to index](../README.md) · [Next: Scenario-Based Questions ➡](23-scenario-based.md)
