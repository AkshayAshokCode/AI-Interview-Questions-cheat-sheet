# Choosing Models & Approaches

[⬅ Back to index](../README.md)

Covers: model tiers and trade-offs, choosing a model for a feature, open-weight vs API models, small and on-device models, prompting vs RAG vs fine-tuning, comparing vendors, upgrading models safely, and benchmarks vs your own evals.

---

### 1. How do model tiers differ (e.g. Claude Opus, Sonnet, Haiku)?

<details>
<summary>Answer</summary>

Providers offer families of models trading capability for speed and cost. For Claude:
- **Opus** – the most capable general model tier: complex reasoning, coding, long agentic tasks.
- **Sonnet** – balanced speed and capability; strong for everyday coding, agents and enterprise workloads.
- **Haiku** – fastest and cheapest: high-volume classification, extraction, routing, sub-agents, real-time chat.

(There may also be specialised frontier tiers above these.) Pick per task using your evals; many products use several – a strong model for hard steps, a fast one for simple or high-volume steps.

</details>

### 2. How do you choose the right model for a feature?

<details>
<summary>Answer</summary>

1. Define the task and **build a small evaluation set** first.
2. Start with a strong model to establish what's achievable.
3. Try **smaller/cheaper** models and measure the quality drop.
4. Consider latency (time to first token, tokens/second), cost per request at expected volume, context length, multimodality, tool-use ability, privacy and data residency.
5. Consider routing: a cheap model for easy requests, an expensive one for hard ones.

Choose by evaluation results on your data, not by public leaderboards alone.

</details>
### 3. Open-weight vs closed (API) models – how do you choose?

<details>
<summary>Answer</summary>

| Closed APIs (OpenAI, Anthropic, Google) | Open-weight (LLaMA, Mistral, Qwen, DeepSeek, Gemma) |
|---|---|
| Usually the most capable models | Strong and improving quickly; full control |
| No infrastructure to run; pay per token | You host it: GPUs, scaling, ops (or use a hosting provider) |
| Data leaves your environment (with contractual protections) | Data can stay on-premise / on-device |
| Limited customisation (prompting, some fine-tuning) | Full fine-tuning, quantisation, custom serving |
| Model versions can change or be retired | You choose when to upgrade |

Decide on: quality needed for the task, cost at your volume, latency, privacy/compliance, customisation needs and team capacity. Many companies use both: an API model for hard tasks, a small open model for high-volume simple ones.

</details>
### 4. What are small language models (SLMs) and when should you use them?

<details>
<summary>Answer</summary>

Models with roughly 0.5–15B parameters (e.g. Phi, Gemma, small LLaMA/Qwen/Mistral variants, on-device models like Gemini Nano and Apple's on-device foundation model). Good for: high-volume simple tasks (classification, extraction, routing), low latency, **on-device/offline** use, privacy, and cost. Often **fine-tuned or distilled** from bigger models to reach good quality on a narrow task. Weaker at broad knowledge and complex reasoning.

</details>
### 5. How do you run LLMs on-device (phones, laptops)?

<details>
<summary>Answer</summary>

Use small models (roughly 1–8B parameters), aggressively quantised (4-bit), with runtimes that use the NPU/GPU: Apple's **Foundation Models framework** and Core ML/MLX, Android's **Gemini Nano** via ML Kit GenAI APIs/AICore, LiteRT, ExecuTorch, llama.cpp. Benefits: privacy, offline use, no per-token cost, low network latency. Limits: memory and battery, thermal throttling, lower capability than cloud models, and model download size. Common pattern: on-device for simple/private tasks, cloud fallback for complex ones.

</details>
### 6. Prompting vs RAG vs fine-tuning – how do you decide?

<details>
<summary>Answer</summary>

| Need | Start with |
|---|---|
| Better instructions, format, tone | **Prompting** (examples, structure, system prompt) |
| Knowledge the model lacks (private, recent, large) with citations | **RAG / tools** |
| Actions or live data | **Tool use** |
| Consistent specialised behaviour that prompting can't reach, or a cheaper smaller model for a narrow high-volume task | **Fine-tuning** (where offered) |

Order of attempts: prompt → add context/RAG/tools → workflows → fine-tuning. Fine-tuning is expensive to maintain and a poor way to add facts.

</details>

### 7. When should you fine-tune an LLM instead of prompting or using RAG?

<details>
<summary>Answer</summary>

Fine-tune when you need:
- A consistent **format, style, tone or behaviour** that prompting can't reliably achieve.
- A **narrow task** at high volume where a **smaller fine-tuned model** can match a large model's quality at a fraction of the cost and latency.
- Domain-specific skills or language (medical notes, legal drafting, a niche programming language).
- Shorter prompts (behaviour baked in rather than long instructions every call).

Don't fine-tune mainly to add **facts** – use RAG (facts change, and fine-tuned facts can still be hallucinated). Always try prompting (and RAG) first and build an evaluation set: it tells you whether fine-tuning actually helps.

</details>
### 8. How do you choose between several LLM vendors for a production feature?

<details>
<summary>Answer</summary>

1. Build a task-specific **evaluation set** and grading method first.
2. Compare quality, **latency** (TTFT and tokens/s at your prompt sizes), **cost** at projected volume (including caching and batch discounts), context length, tool-use and structured-output reliability.
3. Check **non-functional needs**: data privacy terms and retention, regional hosting/data residency, compliance certifications, rate limits and enterprise SLAs, model deprecation policies.
4. Avoid lock-in: an abstraction layer or LLM gateway, prompts kept portable, evals that can be re-run on any model.
5. Re-evaluate periodically – the landscape changes every few months.

</details>
### 9. How do you test whether a new model version is safe to switch to?

<details>
<summary>Answer</summary>

Run your full eval suite on old and new models with the same prompts; compare quality per category, refusals, format failures, cost and latency; read the migration notes (parameters and defaults can change); adjust prompts written for older models (newer models often need less emphatic or less prescriptive instructions); then shadow test or A/B test on real traffic before switching fully, keeping the ability to roll back.

</details>

### 10. Why aren't public benchmarks enough to choose a model?

<details>
<summary>Answer</summary>

Benchmarks measure general abilities on fixed tasks that may not resemble yours, can be contaminated or saturated, and ignore your constraints (cost, latency, format reliability, your domain's language). Use them to shortlist, then decide with **your own evals** on real inputs.

</details>

---

[⬅ Previous: Efficiency: Cost, Latency & Scale](15-efficiency-cost-latency.md) · [⬅ Back to index](../README.md) · [Next: Multimodal Inputs & Model Features ➡](17-multimodal-features.md)
