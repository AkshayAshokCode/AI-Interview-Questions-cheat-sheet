# AI Interview Questions – Cheat Sheet

A revision guide for AI/ML and GenAI interviews: **360 questions with interview-ready answers**, split into 24 topics. It covers ML fundamentals, deep learning, transformers, LLMs, prompting, RAG, fine-tuning, agents and MCP, evaluation, inference and serving, MLOps, ML/GenAI system design, responsible AI, from-scratch ML coding, scenario questions, and what AI labs, big tech and Indian companies ask.

Every answer is hidden in a collapsible block, so you can **test yourself first** and then click to check.

> **📈 [Open the interactive study tracker](https://akshayashokcode.github.io/AI-Interview-Questions-cheat-sheet/)**: mark questions ✓ Done or ↻ Revise, see your progress per topic, search every question, listen to answers read aloud, and continue where you left off.
>
> **No account needed.** Your progress saves automatically on your device. Want to keep it safe and in sync across your devices? Sign in (optional).

Also see: **[Android](https://github.com/AkshayAshokCode/Android-Interview-Questions-cheat-sheet)** · **[iOS](https://github.com/AkshayAshokCode/iOS-Interview-Questions-cheat-sheet)** · **[System Design](https://github.com/AkshayAshokCode/System-Design-Interview-Questions-cheat-sheet)**

---

## How to use this repo

1. **Know which role you're interviewing for** ([topic 01](topics/01-ai-interviews-overview.md)): ML engineer, AI/LLM engineer, data scientist and research roles weight topics differently.
2. **Read the question, answer it out loud**, then expand **Answer** to compare.
3. For LLM roles, prioritise topics 09–17 and 20; for classic ML roles, 02–06, 19 and 20.
4. Practise the [ML coding](topics/22-ml-coding.md) implementations without looking, and the [scenarios](topics/23-scenario-based.md) out loud.

> Tip: AI interviewers care about **trade-offs and evaluation**. Whatever you propose (a model, a prompt, RAG, fine-tuning), say how you'd measure whether it works.

---

## Topics

### 🧭 Getting Started

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 01 | [AI Interviews: Roles & Rounds](topics/01-ai-interviews-overview.md) | 10 | ML vs AI/LLM engineer vs data scientist, interview rounds, project deep dives, preparation plan |
| 02 | [Math & Statistics for ML](topics/02-math-statistics.md) | 19 | Dot products, eigenvectors, SVD, gradients, Bayes, MLE vs MAP, A/B tests, entropy and KL divergence |

### 📊 Classic Machine Learning

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 03 | [ML Fundamentals](topics/03-ml-fundamentals.md) | 20 | Bias-variance, overfitting, L1 vs L2, cross-validation, data leakage, imbalance, drift |
| 04 | [Classical ML Algorithms](topics/04-classical-algorithms.md) | 16 | Linear/logistic regression, trees, random forests, gradient boosting, SVM, k-means, PCA |
| 05 | [Evaluation Metrics](topics/05-evaluation-metrics.md) | 14 | Precision/recall/F1, ROC-AUC vs PR-AUC, calibration, NDCG/MRR, offline vs online metrics |

### 🧠 Deep Learning

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 06 | [Neural Network Fundamentals](topics/06-neural-networks.md) | 18 | Backprop, activations, Adam/AdamW, normalisation, dropout, residuals, debugging training |
| 07 | [Computer Vision](topics/07-computer-vision.md) | 13 | CNNs, ResNet, ViT, detection (YOLO, DETR), segmentation, CLIP, vision-language models |
| 08 | [NLP & Sequence Models](topics/08-nlp-sequence-models.md) | 12 | TF-IDF, word2vec, BPE tokenisation, LSTMs, attention origins, BERT vs GPT |
| 09 | [Transformers Deep Dive](topics/09-transformers.md) | 16 | Self-attention, √d_k, multi-head, RoPE, KV cache, FlashAttention, GQA, mixture of experts |

### 🤖 LLMs & Generative AI

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 10 | [LLM Fundamentals](topics/10-llm-fundamentals.md) | 18 | Training pipeline, scaling laws, decoding, hallucinations, reasoning models, open vs closed |
| 11 | [Prompt Engineering](topics/11-prompt-engineering.md) | 15 | Few-shot, chain-of-thought, structured output, context engineering, caching, prompt injection |
| 12 | [Embeddings & Vector Search](topics/12-embeddings-vector-search.md) | 15 | Embedding models, HNSW, IVF-PQ, vector databases, hybrid search, rerankers |
| 13 | [Retrieval-Augmented Generation (RAG)](topics/13-rag.md) | 15 | Pipeline, chunking, retrieval quality, citations, permissions, evaluation, GraphRAG, RAG vs fine-tuning |
| 14 | [Fine-Tuning & Alignment](topics/14-fine-tuning-alignment.md) | 15 | When to fine-tune, LoRA/QLoRA, SFT data, RLHF, DPO, RL with verifiable rewards, distillation |
| 15 | [Agents, Tool Use & MCP](topics/15-agents-tool-use.md) | 15 | Tool calling, ReAct, planning, memory, multi-agent, MCP, computer use, agent security |
| 16 | [LLM Evaluation](topics/16-llm-evaluation.md) | 14 | Eval sets, LLM-as-judge, benchmarks and contamination, RAG/agent evals, CI, production monitoring |
| 17 | [LLM Inference, Serving & Optimisation](topics/17-llm-inference-serving.md) | 16 | TTFT, GPU memory maths, quantisation, PagedAttention, batching, speculative decoding, cost |
| 18 | [Generative Models](topics/18-generative-models.md) | 11 | VAEs, GANs, diffusion, latent diffusion, guidance, ControlNet/LoRA, evaluation |

### ⚙️ Production & Design

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 19 | [MLOps & Production ML](topics/19-mlops-production.md) | 15 | Feature stores, training-serving skew, deployment, monitoring, drift, distributed training, LLMOps |
| 20 | [ML & GenAI System Design](topics/20-ml-genai-system-design.md) | 14 | Recommendations, search ranking, ads CTR, fraud, moderation, RAG assistant, ChatGPT, coding assistant |
| 21 | [Responsible AI, Safety & Security](topics/21-responsible-ai-safety.md) | 14 | Bias and fairness, SHAP, privacy, OWASP LLM Top 10, guardrails, red-teaming, EU AI Act |

### ⭐ Interview Rounds

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 22 | [ML Coding (Implement From Scratch)](topics/22-ml-coding.md) | 14 | Linear/logistic regression, k-means, k-NN, softmax, metrics, attention, training loop, BPE, sampling |
| 23 | [Scenario-Based Questions](topics/23-scenario-based.md) | 17 | Accuracy drop, leakage, imbalance, hallucinating chatbot, bad retrieval, slow/expensive LLM features |
| 24 | [Company-Wise Questions & What's New](topics/24-company-wise-whats-new.md) | 14 | AI labs, Google, Meta, Amazon, Microsoft, Apple, Nvidia, startups, Indian companies; latest trends |

---

## 🗓 Suggested revision plans

**2-week plan (≈1–2 hours/day)**

| Day | Topics |
|---|---|
| 1 | 01 Roles & Rounds + 02 Math & Statistics |
| 2 | 03 ML Fundamentals |
| 3 | 04 Classical Algorithms + 05 Metrics |
| 4 | 06 Neural Networks |
| 5 | 07 Computer Vision + 08 NLP |
| 6 | 09 Transformers (implement attention yourself) |
| 7 | 10 LLM Fundamentals + 11 Prompt Engineering |
| 8 | 12 Embeddings + 13 RAG |
| 9 | 14 Fine-Tuning + 15 Agents & MCP |
| 10 | 16 Evaluation + 17 Inference & Serving |
| 11 | 18 Generative Models + 19 MLOps |
| 12 | 20 ML & GenAI System Design (practise two designs out loud) |
| 13 | 21 Responsible AI + 22 ML Coding |
| 14 | 23 Scenarios + 24 Company-Wise + Last-Minute Revision |

**2 days before:** bias-variance (03), metrics (05), transformers (09), LLM fundamentals (10), RAG (13), fine-tuning (14), inference (17), one system design (20), scenarios (23) and the list below.

---

## ⚡ Last-Minute Revision (one-liners)

<details>
<summary>Open the quick revision list</summary>

**ML fundamentals**
- Bias = too simple (underfit); variance = too sensitive (overfit). More data and regularisation fight variance; bigger models fight bias.
- L1 → sparse weights (feature selection); L2 → small weights; L2 ≈ Gaussian prior, L1 ≈ Laplace prior.
- Leakage = information unavailable at prediction time; fit preprocessing on training data only; split by time/user.
- Imbalanced data: PR-AUC, precision/recall, class weights, threshold tuning – never accuracy.
- Gradient boosting usually wins on tabular data; deep learning on images, audio, text.

**Metrics**
- Precision = TP/(TP+FP); recall = TP/(TP+FN); F1 = harmonic mean.
- ROC-AUC = P(random positive scored above random negative); PR-AUC is better for rare positives.
- Calibrated probabilities matter when they drive decisions (ads, risk).
- Ranking: NDCG for graded relevance, MRR for the first relevant result, recall@k for retrieval.

**Deep learning**
- Backprop = chain rule from the loss backwards; stores activations (training memory ≫ inference).
- AdamW is the default for transformers; warmup + cosine decay.
- Vanishing gradients → ReLU family, residuals, normalisation; exploding → gradient clipping.
- BatchNorm normalises across the batch (CNNs); LayerNorm/RMSNorm per example (transformers).

**Transformers & LLMs**
- Attention = softmax(QKᵀ/√d_k)·V; scaling keeps softmax out of saturation.
- Causal mask blocks future tokens; RoPE encodes relative positions; attention cost O(n²).
- KV cache stores past keys/values; memory = 2 × layers × KV heads × head dim × bytes × tokens. GQA shrinks it.
- MoE: many experts, router activates a few per token → big capacity, small compute per token.
- LLM pipeline: pretraining → SFT → preference tuning (RLHF/DPO) → RL on verifiable rewards for reasoning.
- Temperature sharpens/flattens; top-p samples from the smallest set covering p probability mass.
- Hallucinations: ground with RAG + citations, allow "I don't know", use tools, verify.

**RAG, fine-tuning & agents**
- RAG for knowledge (private, fresh, citable); fine-tuning for behaviour, format and smaller models.
- Retrieval quality drives RAG quality: parsing, chunking, hybrid search, reranking, query rewriting.
- Evaluate retrieval (recall@k) and generation (faithfulness, correctness) separately.
- LoRA: W + BA with low rank r; QLoRA = LoRA on a 4-bit base.
- DPO trains on preference pairs with no reward model; RLHF uses a reward model + PPO + KL penalty.
- Agents: model chooses tools in a loop; start with workflows; limit steps; human approval for risky actions.
- MCP standardises connecting AI apps to tools and data; watch for prompt injection.

**Serving & production**
- Prefill is compute-bound (TTFT); decode is memory-bandwidth-bound (tokens/s).
- 7B params × 2 bytes ≈ 14 GB in BF16; ≈ 4 GB at 4-bit.
- Throughput boosters: continuous batching, PagedAttention, quantisation, speculative decoding, prefix caching.
- Cost levers: smaller models + routing, fewer tokens, caching, batch APIs.
- Production ML: monitor data quality, drift and performance; avoid training-serving skew; shadow → canary → A/B.
- Security: prompt injection, excessive agency, improper output handling; enforce rules in code, not prompts.

</details>
