# Neural Network Fundamentals

[⬅ Back to index](../README.md)

Covers: neurons and layers, why non-linearity, activation functions, forward pass and backpropagation, loss functions, weight initialisation, optimisers (SGD, momentum, Adam, AdamW), learning rate schedules, batch size, vanishing and exploding gradients, batch norm vs layer norm, dropout, residual connections, mixed precision, and debugging training.

---

### 1. What is a neural network, and why do we need non-linear activations?

<details>
<summary>Answer</summary>

A neural network stacks layers that compute `h = f(Wx + b)`: a linear transform followed by a non-linear **activation** f. Without non-linearity, any stack of linear layers collapses into **one** linear transform, so depth would add nothing. Non-linear activations let networks approximate complex functions (the **universal approximation theorem**: one hidden layer with enough units can approximate any continuous function; depth makes it far more efficient in practice).

</details>

### 2. Compare common activation functions.

<details>
<summary>Answer</summary>

| Activation | Formula / shape | Notes |
|---|---|---|
| **Sigmoid** | `1/(1+e^-x)`, output (0, 1) | Saturates → vanishing gradients; not zero-centred. Used for binary outputs and gates. |
| **Tanh** | output (−1, 1) | Zero-centred, still saturates. Used in RNN gates. |
| **ReLU** | `max(0, x)` | Cheap, no saturation for x > 0, sparse; "dying ReLU" units stuck at 0. Default for CNNs. |
| **Leaky ReLU / PReLU** | small slope for x < 0 | Avoids dead units. |
| **GELU** | smooth ReLU-like | Used in BERT, GPT-2/3. |
| **SwiGLU** | gated, SiLU-based | Used in the feed-forward layers of most modern LLMs (LLaMA family and others). |
| **Softmax** | turns scores into a probability distribution | Output layer for multi-class and next-token prediction. |

</details>

### 3. Explain backpropagation.

<details>
<summary>Answer</summary>

1. **Forward pass** – compute each layer's outputs and the loss, storing intermediate activations.
2. **Backward pass** – starting from `∂L/∂output`, apply the **chain rule** layer by layer to compute the gradient of the loss with respect to every activation and weight, reusing results from the layer above.
3. **Update** – an optimiser uses the gradients to adjust weights.

Frameworks (PyTorch autograd) build a computational graph and do this automatically. Memory cost: activations must be stored for the backward pass, which is why training needs far more memory than inference (and why **gradient checkpointing** trades compute for memory by recomputing activations).

</details>

### 4. Which loss functions are used for which tasks?

<details>
<summary>Answer</summary>

- **Regression** – MSE (penalises large errors), MAE (robust), Huber (a mix).
- **Binary classification** – binary cross-entropy (with a sigmoid; use the combined "with logits" version for numerical stability).
- **Multi-class** – categorical cross-entropy with softmax (LLMs use this over the vocabulary for each next token).
- **Imbalanced classification / detection** – **focal loss** down-weights easy examples.
- **Embeddings / similarity** – contrastive loss, triplet loss, InfoNCE (CLIP, sentence embeddings).
- **Ranking** – pairwise (RankNet) or listwise losses.

</details>

### 5. Why does weight initialisation matter? What are Xavier and He initialisation?

<details>
<summary>Answer</summary>

Bad initialisation makes activations and gradients shrink or blow up as they pass through layers, so training stalls or diverges. All-zero weights are worse: every neuron learns the same thing (symmetry is never broken).

- **Xavier/Glorot** – variance `2/(fan_in + fan_out)`; designed for tanh/sigmoid.
- **He/Kaiming** – variance `2/fan_in`; designed for ReLU (which zeroes half the inputs).

Goal: keep the variance of activations roughly constant across layers. Normalisation layers and residual connections make deep networks much less sensitive to this.

</details>

### 6. Compare SGD, momentum, RMSProp, Adam and AdamW.

<details>
<summary>Answer</summary>

- **SGD** – `w ← w − η·g` on mini-batches; noisy but generalises well.
- **Momentum** – accumulates a velocity of past gradients; speeds up along consistent directions and damps oscillation.
- **RMSProp** – divides by a running average of squared gradients: **per-parameter adaptive learning rates**.
- **Adam** – momentum + RMSProp with bias correction; fast, robust default for most deep learning.
- **AdamW** – Adam with **decoupled weight decay** (applied directly to weights rather than through the gradient), which regularises correctly with adaptive methods. The standard for training transformers and LLMs.

SGD with momentum is still popular in computer vision because it can generalise slightly better; newer optimisers (e.g. Lion, Muon, Adafactor for memory savings) appear in large-scale training.

</details>

### 7. What are learning rate schedules and warmup?

<details>
<summary>Answer</summary>

The learning rate is the most important hyperparameter. Schedules change it during training:
- **Warmup** – start small and increase linearly for the first steps; prevents early instability, especially with Adam and large batches (standard for transformers).
- **Step decay**, **cosine decay** (smoothly to near zero), **linear decay**, **one-cycle**.
- **Learning rate finder** – sweep rates on a short run and pick one before the loss explodes.

A common LLM recipe: linear warmup then cosine decay.

</details>

### 8. How does batch size affect training?

<details>
<summary>Answer</summary>

- **Small batches** – noisy gradients (a regularising effect), less memory, more update steps; can generalise well.
- **Large batches** – smoother gradients, better hardware utilisation, faster wall-clock training; may need a scaled-up learning rate (linear scaling rule) with warmup, and can generalise slightly worse.
- **Gradient accumulation** simulates a large batch on limited memory by summing gradients over several mini-batches before updating.

</details>

### 9. What are vanishing and exploding gradients, and how are they fixed?

<details>
<summary>Answer</summary>

In deep or recurrent networks, gradients are products of many per-layer derivatives:
- If they're < 1, gradients **vanish** – early layers stop learning.
- If they're > 1, gradients **explode** – updates become huge, the loss becomes NaN.

Fixes: ReLU-family activations, careful initialisation, **residual connections**, **normalisation** layers, **gradient clipping** (cap the gradient norm, standard in RNN and transformer training), gated architectures (LSTM/GRU), and smaller learning rates.

</details>

### 10. Batch normalisation vs layer normalisation?

<details>
<summary>Answer</summary>

Both normalise activations to zero mean and unit variance, then apply a learned scale and shift, which stabilises and speeds up training.
- **BatchNorm** – normalises each feature **across the batch**. Great for CNNs; depends on batch statistics (problems with small batches, and different behaviour in train vs eval mode).
- **LayerNorm** – normalises across the **features of each example**, independent of batch size. Works for variable-length sequences and is used in **transformers**. **RMSNorm** (only rescales by the root mean square, no mean subtraction) is a cheaper variant used in most modern LLMs.

Transformers mostly use **pre-norm** (normalise before each sub-layer), which trains more stably than post-norm.

</details>

### 11. How does dropout work?

<details>
<summary>Answer</summary>

During training, each unit is zeroed with probability p, and survivors are scaled by `1/(1 − p)` (inverted dropout) so expected activations match. This prevents co-adaptation of neurons and acts like averaging many sub-networks. At inference dropout is turned off (`model.eval()` in PyTorch – forgetting this is a classic bug). Large LLM pretraining often uses little or no dropout because there's so much data that overfitting isn't the main problem.

</details>

### 12. What are residual (skip) connections and why do they help?

<details>
<summary>Answer</summary>

A residual block outputs `x + F(x)` instead of `F(x)`. The layer only needs to learn the **change** to its input, and the identity path gives gradients a direct route backwards, so very deep networks (ResNet-152, transformers with 100+ layers) train without vanishing gradients. Every transformer layer wraps attention and the feed-forward network in residual connections.

</details>

### 13. What is mixed-precision training?

<details>
<summary>Answer</summary>

Training with lower-precision numbers (**FP16 or BF16**) for most operations while keeping a FP32 master copy of weights for updates. It halves memory for activations and uses fast tensor cores, often speeding training up 2× or more. **BF16** has the same exponent range as FP32, so it rarely needs loss scaling and is the standard for LLM training; FP16 needs **loss scaling** to avoid gradient underflow. FP8 training is increasingly used on the newest GPUs.

</details>

### 14. Your model's training loss isn't decreasing. How do you debug it?

<details>
<summary>Answer</summary>

1. **Overfit a tiny batch** (a few examples) – if it can't reach near-zero loss, there's a bug.
2. Check the **data pipeline**: labels aligned with inputs, normalisation, augmentation not destroying the signal, shuffling.
3. Check the **loss**: correct function, logits vs probabilities (double softmax), class indices, reduction.
4. **Learning rate** – too high (loss explodes/NaN) or too low (flat); try a learning rate finder.
5. Check gradients: are they flowing (not zero, not NaN)? Frozen layers by mistake? `optimizer.zero_grad()` called?
6. Initialisation and normalisation; `model.train()` vs `model.eval()`.
7. Start from a known-good architecture and change one thing at a time.

</details>

### 15. Training loss goes down but validation loss goes up. What do you do?

<details>
<summary>Answer</summary>

That's **overfitting**. Options: more or augmented data, stronger regularisation (weight decay, dropout), smaller model, early stopping, fine-tune fewer layers of a pretrained model, and check for issues like duplicated training data. Also verify the validation set isn't from a different distribution (that would be a **distribution shift** problem, not overfitting).

</details>

### 16. What is the difference between an epoch, a batch and an iteration?

<details>
<summary>Answer</summary>

- **Batch** – the subset of examples processed in one forward/backward pass.
- **Iteration (step)** – one parameter update (one batch, or several with gradient accumulation).
- **Epoch** – one full pass over the training dataset.

Example: 10,000 examples, batch size 100 → 100 iterations per epoch. LLM pretraining is usually described in **tokens** and steps rather than epochs, often seeing most data only about once.

</details>

### 17. What is gradient clipping?

<details>
<summary>Answer</summary>

Capping gradients before the update: usually **clip by global norm** (if the total gradient norm exceeds a threshold, e.g. 1.0, scale all gradients down proportionally). It prevents single bad batches from producing huge updates and destabilising training; standard in RNN and transformer training.

</details>

### 18. What are autoencoders used for?

<details>
<summary>Answer</summary>

An autoencoder compresses inputs into a low-dimensional **latent code** (encoder) and reconstructs them (decoder), trained on reconstruction loss. Uses: dimensionality reduction, denoising (train to reconstruct clean inputs from noisy ones), **anomaly detection** (high reconstruction error), and learning representations. **Variational autoencoders (VAEs)** make the latent space probabilistic so you can sample new data; their encoders are used in latent diffusion models. See [Generative Models](18-generative-models.md).

</details>

### 19. What is label smoothing?

<details>
<summary>Answer</summary>

Instead of training on hard one-hot targets (1 for the true class, 0 elsewhere), use soft targets: `1 − ε` for the true class and `ε/(K − 1)` for the others (e.g. ε = 0.1). It stops the model from pushing logits towards infinity to become 100% confident, which improves **calibration** and generalisation and adds robustness to label noise. Widely used in image classification and in the original transformer. Side effect: it can hurt knowledge distillation from that model (less information in the teacher's probabilities).

</details>

### 20. What is an embedding layer?

<details>
<summary>Answer</summary>

A trainable lookup table mapping each discrete ID (word/token, user, product, category) to a dense vector: a matrix of shape (number of IDs × dimension), where row i is the vector for ID i. It's mathematically equivalent to multiplying a one-hot vector by a weight matrix, but implemented as a fast index lookup. The vectors are learned with the rest of the model, so similar IDs end up close together. Embedding tables can be huge in recommender systems (billions of IDs) – handled with hashing, sharding across machines and mixed-dimension embeddings.

</details>

### 21. What are graph neural networks (GNNs)?

<details>
<summary>Answer</summary>

Neural networks that operate on **graphs** (nodes and edges): each layer updates a node's representation by **aggregating messages from its neighbours** (message passing), so after k layers a node's embedding reflects its k-hop neighbourhood. Variants: GCN (normalised neighbour averaging), GraphSAGE (sampled neighbours – scales to huge graphs), GAT (attention over neighbours).

Uses: fraud rings (accounts sharing devices/cards), recommendations (user–item graphs, e.g. PinSage at Pinterest), social networks, molecules and drug discovery, traffic/ETA prediction on road graphs, knowledge graphs. Challenges: scaling to billions of edges (sampling, partitioning) and over-smoothing with many layers.

</details>

---

[⬅ Previous: Evaluation Metrics](05-evaluation-metrics.md) · [⬅ Back to index](../README.md) · [Next: Computer Vision ➡](07-computer-vision.md)
