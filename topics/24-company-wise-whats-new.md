# Company-Wise Questions & What's New

[⬅ Back to index](../README.md)

Covers: how AI/ML interviews work at AI labs (OpenAI, Anthropic, Google DeepMind), big tech (Google, Meta, Amazon, Microsoft, Apple, Nvidia), AI startups and Indian companies; the questions each commonly asks; and the trends interviewers expect you to know (reasoning models, agents and MCP, small and on-device models, multimodal and voice, open-weight models, AI coding tools, evaluation and regulation).

> These lists come from commonly reported interview experiences. Companies change their loops often, so use them to practise each company's **style**, not as a guaranteed list. AI moves fast – check the latest model releases before your interview.

---

## What Companies Ask

### 1. What are interviews at AI labs (OpenAI, Anthropic, Google DeepMind) like?

<details>
<summary>Answer</summary>

**Commonly reported format** – strong **practical coding** (often realistic engineering problems rather than puzzle-style DSA – e.g. implement a component, debug or extend existing code, sometimes in multiple timed stages), **system design** (often LLM-serving or infrastructure flavoured for engineering roles), ML depth for research/applied roles (implementing papers, transformers from scratch, training dynamics), a **project deep dive**, and **values/mission** conversations – AI safety and responsible deployment are taken seriously, so expect questions on why you want to work on AI and how you think about its risks.

**Typical topics** – transformer internals ([09](09-transformers.md)), training and inference efficiency ([17](17-llm-inference-serving.md)), evaluation ([16](16-llm-evaluation.md)), alignment and safety ([14](14-fine-tuning-alignment.md), [21](21-responsible-ai-safety.md)), distributed systems for GPU fleets.

**Tip** – read the company's recent research and product posts; have thoughtful, honest opinions on safety and on the trade-offs of their products.

</details>

### 2. What do Google and Meta ask in ML/AI engineering interviews?

<details>
<summary>Answer</summary>

- **Google** – general DSA coding rounds, an **ML domain** round (fundamentals, deep learning, metrics), **ML system design** for senior roles (e.g. YouTube recommendations, search ranking, spam detection), plus "Googleyness and leadership". Gemini-related teams ask about LLMs, evaluation and serving.
- **Meta** – fast-paced DSA coding, **ML system design** (feed ranking, ads CTR prediction, integrity/harmful content detection, friend recommendations, Reels recommendations), and behavioral. GenAI teams (LLaMA, Meta AI) go deeper on LLM training, fine-tuning and inference.

See [ML & GenAI System Design](20-ml-genai-system-design.md).

</details>

### 3. What do Amazon, Microsoft, Apple and Nvidia ask?

<details>
<summary>Answer</summary>

- **Amazon** – Leadership Principles in every round, DSA coding, ML breadth, and ML design around **recommendations, search, forecasting, fraud, Alexa/LLM features**; Applied Scientist loops include ML depth and a "science breadth" round. AWS AI roles add Bedrock/SageMaker-style system design.
- **Microsoft** – team-dependent: coding, ML/LLM fundamentals, and designs around Copilot-style assistants, RAG over enterprise data (Microsoft 365), and Azure AI services.
- **Apple** – team-based loops; strong focus on **on-device ML**, privacy-preserving techniques, efficiency (quantisation, Core ML), and product quality. See [Inference](17-llm-inference-serving.md), [Responsible AI](21-responsible-ai-safety.md).
- **Nvidia** – GPU/systems depth: CUDA concepts, kernels and memory hierarchy, parallelism strategies, inference optimisation (TensorRT-LLM), distributed training.

</details>

### 4. What do AI startups and AI-application companies ask for "AI Engineer" roles?

<details>
<summary>Answer</summary>

Usually very practical:
- A **take-home or live build** – e.g. build a small RAG or agent app, with an evaluation set, in a few hours.
- **LLM application design** – how you'd build their product's core feature; prompting, retrieval, tools, evals, cost and latency.
- **Debugging scenarios** – hallucinations, bad retrieval, slow responses ([Scenarios](23-scenario-based.md)).
- Strong general software engineering (APIs, async Python/TypeScript, testing, deployment).
- Product sense – what to build, how to measure it.

Show shipped projects: a working demo with evaluation results is very persuasive.

</details>

### 5. What do Indian AI and product companies ask?

<details>
<summary>Answer</summary>

| Company type | Commonly reported focus |
|---|---|
| **Indian AI labs (Sarvam AI, Krutrim and others)** | LLM training and fine-tuning for **Indian languages** (tokenisation for Indic scripts, data scarcity, speech models), inference efficiency, evaluation in multiple languages |
| **Flipkart / Meesho / Myntra** | Search ranking, recommendations, query understanding for multilingual/transliterated queries, pricing, demand forecasting, GenAI shopping assistants |
| **Swiggy / Zomato / Zepto** | Delivery time (ETA) prediction, demand forecasting, recommendations, search, catalogue enrichment with LLMs, support automation |
| **PhonePe / Paytm / Razorpay / CRED** | Fraud and risk models, credit scoring, anomaly detection, conversational support bots |
| **Analytics/consulting AI firms** | ML fundamentals, statistics, SQL, case studies, client-facing communication |

Many run an ML case-study round ("design delivery-time prediction") plus coding and ML fundamentals.

</details>

### 6. Which questions come up most across AI interviews?

<details>
<summary>Answer</summary>

1. Bias-variance trade-off, overfitting and regularisation → [03](03-ml-fundamentals.md)
2. Precision vs recall, ROC-AUC vs PR-AUC, imbalanced data → [05](05-evaluation-metrics.md)
3. How gradient boosting works; random forest vs boosting → [04](04-classical-algorithms.md)
4. Backpropagation, vanishing gradients, BatchNorm vs LayerNorm, Adam → [06](06-neural-networks.md)
5. Explain self-attention and the transformer; why √d_k; KV cache → [09](09-transformers.md)
6. How LLMs are trained (pretraining → SFT → RLHF/DPO) → [10](10-llm-fundamentals.md), [14](14-fine-tuning-alignment.md)
7. Temperature/top-p; hallucinations and how to reduce them → [10](10-llm-fundamentals.md)
8. Design a RAG system; chunking; hybrid search; reranking; evaluation → [13](13-rag.md)
9. RAG vs fine-tuning vs long context → [13](13-rag.md)
10. LoRA/QLoRA → [14](14-fine-tuning-alignment.md)
11. Agents, tool calling, MCP, agent failure modes → [15](15-agents-tool-use.md)
12. How to evaluate LLM outputs; LLM-as-judge → [16](16-llm-evaluation.md)
13. Inference optimisation: quantisation, batching, speculative decoding, memory maths → [17](17-llm-inference-serving.md)
14. Data leakage, drift, monitoring, training-serving skew → [19](19-mlops-production.md)
15. Prompt injection and guardrails → [21](21-responsible-ai-safety.md)
16. Implement attention / logistic regression / k-means from scratch → [22](22-ml-coding.md)

</details>

### 7. How should you prepare in the last week before an AI interview?

<details>
<summary>Answer</summary>

- **Days 1–2** – revise the most-asked list above and the README one-liners.
- **Day 3** – ML coding: implement attention, logistic regression and k-means from memory; one DSA session.
- **Day 4** – one ML/GenAI system design mock out loud (from the company's domain).
- **Day 5** – LLM depth: RAG, fine-tuning, evaluation, inference; read the company's latest model/product announcements.
- **Day 6** – project deep dive and behavioral stories (impact, failures, trade-offs).
- **Day 7** – light review and rest.

</details>

---

## What's New & Trending

### 8. What are reasoning models, and why do interviewers ask about them?

<details>
<summary>Answer</summary>

Models trained (largely with reinforcement learning on verifiable rewards) to produce long internal chains of thought before answering – e.g. OpenAI's o-series and later GPT models with reasoning, DeepSeek-R1, and Claude's and Gemini's extended-thinking modes. They're much better at maths, coding and multi-step planning, at the cost of more tokens and latency, and most APIs let you control the thinking budget. Interview angles: when to use them vs fast models, cost/latency trade-offs, how RL with verifiable rewards works, and evaluating them. See [LLM Fundamentals](10-llm-fundamentals.md).

</details>

### 9. Why are agents and MCP such a big trend?

<details>
<summary>Answer</summary>

Models became reliable enough at tool use to complete multi-step tasks: coding agents that edit repositories and run tests, research agents, browser/computer-use agents, and customer-support agents that take actions. **MCP** standardised how agents connect to tools and data, so integrations are reusable across applications. Interview angles: workflows vs agents, reliability and evaluation of agents, security (prompt injection, permissions), cost control. See [Agents](15-agents-tool-use.md).

</details>

### 10. What's happening with small and on-device models?

<details>
<summary>Answer</summary>

Small models (a few billion parameters) have improved rapidly through distillation and better data, enabling **on-device AI** (Apple's on-device foundation model and Foundation Models framework, Gemini Nano on Android, Copilot+ PCs) for private, offline, low-latency features. In the cloud, small models handle high-volume tasks cheaply, often with routing to larger models for hard cases. Interview angles: quantisation, distillation, routing, privacy trade-offs.

</details>

### 11. What are the trends in multimodal and voice AI?

<details>
<summary>Answer</summary>

Frontier models are natively multimodal – understanding images, documents, audio and video, and generating images and speech. **Real-time voice** models handle speech in and speech out with low latency (natural interruptions, emotion), enabling voice agents. Video generation has improved quickly. Engineering angles: latency budgets for voice (streaming in both directions), the token cost of images and video, document understanding replacing OCR pipelines, and provenance/watermarking for generated media.

</details>

### 12. How have open-weight models changed the landscape?

<details>
<summary>Answer</summary>

Open-weight families (LLaMA, Mistral, Qwen, DeepSeek, Gemma and others) have narrowed the gap to closed frontier models, with efficient **MoE** architectures and strong reasoning variants, often under permissive licences. This makes self-hosting, fine-tuning and on-premise/private deployment practical, and pushes API prices down. Interview angles: open vs closed trade-offs, licence terms, serving costs, fine-tuning open models with LoRA. See [LLM Fundamentals](10-llm-fundamentals.md).

</details>

### 13. How are AI coding tools changing software engineering, and how should you talk about them?

<details>
<summary>Answer</summary>

AI coding assistants and agents (in IDEs, terminals and code review) now write substantial amounts of code, run tests and propose changes. Interviewers increasingly ask how you use them. Good answers: you use them to speed up boilerplate, exploration, tests and refactors; you **review and test everything** they produce; you give them good context (clear specs, project instructions); you stay aware of security issues and licence concerns; and you still understand the code you ship. Some companies now allow or even expect AI tools in coding interviews – ask beforehand.

</details>

### 14. What other trends are worth knowing?

<details>
<summary>Answer</summary>

- **Long context** (hundreds of thousands to millions of tokens) changing when RAG is needed, plus **prompt caching** making long prompts cheaper.
- **Evaluation as a discipline** – eval-driven development, LLM judges, agent benchmarks.
- **Inference efficiency** – FP8/FP4, speculative decoding, disaggregated prefill/decode serving, MoE serving.
- **Synthetic data and distillation** for training smaller models.
- **AI regulation** (EU AI Act obligations phasing in) and responsible-AI requirements in enterprise deals.
- **State-space and hybrid architectures** as alternatives to pure attention for long sequences.
- **Memory and personalisation** in assistants.

</details>

---

[⬅ Previous: Scenario-Based Questions](23-scenario-based.md) · [⬅ Back to index](../README.md)
