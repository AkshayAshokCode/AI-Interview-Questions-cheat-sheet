# Efficiency: Cost, Latency & Scale

[⬅ Back to index](../README.md)

Covers: token economics, estimating cost, prompt caching and its rules, batch processing, choosing effort and model size, routing, output length, latency metrics and levers, streaming, agent cost control, rate limits, and monitoring spend.

---

### 1. What drives the cost of an LLM feature?

<details>
<summary>Answer</summary>

- **Input tokens** – system prompt, tool definitions, retrieved context, conversation history (re-sent every turn).
- **Output tokens** – usually several times more expensive per token than input, including thinking tokens.
- **Model choice** – prices differ by an order of magnitude or more between small and large models.
- **Number of calls** – agents and chains multiply calls per user task.
- **Discounts** – cached input and batch processing are much cheaper.

The useful unit is **cost per completed task**, not per request – a cheap request that needs retries or extra turns isn't cheap.

</details>

### 2. How do you estimate the cost of an LLM feature?

<details>
<summary>Answer</summary>

`cost/request = input tokens × input price + output tokens × output price` (output tokens usually cost several times more than input; cached input is cheaper). Multiply by requests per day.

Example: a support bot with 3,000 input tokens (system prompt + retrieved chunks + history) and 300 output tokens, 1M requests/month. At illustrative prices of $1 per million input tokens and $5 per million output tokens: 3,000 × $1/M + 300 × $5/M = $0.003 + $0.0015 = **$0.0045 per request → ~$4,500/month**. With 70% of input tokens served from prompt cache at a 90% discount, the input cost drops substantially.

For self-hosting, compare GPU cost per hour ÷ achieved tokens per hour at your latency target.

</details>
### 3. How does prompt caching work, and what are its rules?

<details>
<summary>Answer</summary>

The provider stores the processed state of a **prompt prefix** so later requests that start with exactly the same content skip reprocessing it – cached reads cost a small fraction of normal input (roughly 5–10% of the input price on Claude, depending on the model) and reduce time to first token; writing to the cache costs a little more than normal input.

Rules to remember (Claude):
- It's a **prefix match** in the order tools → system → messages. Any change in the prefix invalidates everything after it.
- You mark cache breakpoints (up to 4) or use automatic caching; there's a **minimum prefix length** (a few hundred to a few thousand tokens depending on the model) – shorter prefixes silently don't cache.
- Caches expire after a short time without use (5 minutes by default; a longer TTL is available).
- Silent invalidators: timestamps or request IDs in the system prompt, non-deterministic JSON key order, changing the tool list, switching models.
- Verify with the cache-read token counts in the response usage.

</details>

### 4. Which workloads benefit most from prompt caching?

<details>
<summary>Answer</summary>

Anything that re-sends a large, stable prefix: chatbots with long system prompts and growing conversations, document Q&A over the same long document, agents re-sending tool definitions and history every step, few-shot prompts with many examples, and coding assistants re-sending repository context. Savings of 50–90% on input cost and noticeably faster responses are common in these cases.

</details>

### 5. How do you reduce output tokens without hurting quality?

<details>
<summary>Answer</summary>

Ask for the format you need ("answer in 3 bullets", "return only the JSON"), avoid asking for explanations you won't show, set a sensible max tokens, use lower effort for simple routes, and for structured tasks use schemas so the model doesn't add prose. Measure quality with evals after each change.

</details>

### 6. What are the main ways to reduce LLM inference cost?

<details>
<summary>Answer</summary>

- **Right-size the model** – a smaller or fine-tuned/distilled model for simple tasks; **routing** between cheap and expensive models by difficulty.
- **Fewer tokens** – shorter prompts, fewer/better retrieved chunks, concise outputs, summarised history.
- **Caching** – prompt (prefix) caching, exact and semantic response caching.
- **Batch APIs** for offline work (often ~50% cheaper).
- Self-hosting optimisations: quantisation, continuous batching, speculative decoding, high GPU utilisation.
- Avoid unnecessary calls (deterministic code where possible, rules before LLMs).

</details>
### 7. What is model routing / cascading?

<details>
<summary>Answer</summary>

- **Routing** – a classifier (or small LLM) decides which model should handle a request based on difficulty, domain or required features; easy requests go to cheap fast models.
- **Cascading** – try the cheap model first; if confidence is low or a check fails, escalate to the stronger model.

Both cut cost and latency substantially when most traffic is easy, but need good evals to make sure quality holds for routed requests.

</details>
### 8. How do you choose an effort level or model size for a route?

<details>
<summary>Answer</summary>

Measure on your eval set. Typical findings: coding and long agentic tasks benefit from higher effort and stronger models; chat, classification, extraction and high-volume routes often perform just as well at low effort or on a small model. Before building a cascade of models, try the **most capable model at a lower effort** – it's often good enough and keeps one cache and one behaviour profile. Tune per route, not globally.

</details>

### 9. Which latency and throughput metrics matter?

<details>
<summary>Answer</summary>

- **TTFT (time to first token)** – how long until output starts; dominated by queueing + prefill. Key for perceived responsiveness.
- **TPOT / ITL (time per output token / inter-token latency)** – speed of streaming, e.g. 20–50 ms/token.
- **End-to-end latency** ≈ TTFT + TPOT × output tokens.
- **Throughput** – tokens/second (or requests/second) across all users per GPU.
- **Goodput** – throughput of requests that meet latency targets.

There's a trade-off: bigger batches raise throughput and lower cost but increase per-request latency.

</details>
### 10. What are the main levers for reducing latency?

<details>
<summary>Answer</summary>

- **Stream** responses (perceived latency drops to time to first token).
- **Fewer output tokens** (generation is the slowest part).
- **Prompt caching** (faster time to first token on long prompts).
- **Smaller/faster model or lower effort** for the route.
- **Parallelise** independent calls and tool executions.
- **Fewer round trips** – combine steps, programmatic tool calling for many tool calls.
- Precompute what you can offline; cache frequent answers.
- Faster serving options where offered (e.g. a premium fast mode on some models).

</details>

### 11. How do you handle rate limits and failures from LLM APIs?

<details>
<summary>Answer</summary>

- Retries with **exponential backoff and jitter** on 429/5xx; respect `Retry-After`.
- Client-side **rate limiting/queues** sized to your quota (requests and tokens per minute).
- **Fallback** to another model or provider (an LLM gateway helps).
- Timeouts, and circuit breakers when a provider degrades.
- Idempotent handling so retries don't duplicate side effects in agent workflows.
- Graceful degradation in the UI ("this feature is busy, try again").

</details>
### 12. How do you monitor and control AI spend in production?

<details>
<summary>Answer</summary>

Log tokens (input, cached, output) and cost **per request, feature, customer and model**; build dashboards and alerts on cost per task and unusual spikes; set spend limits per API key/workspace; cap per-user usage (rate limits, quotas); review the biggest cost drivers monthly (often a long system prompt, an uncached prefix, or an agent loop taking too many steps); and include cost in your eval reports when comparing prompts and models.

</details>

---

[⬅ Previous: Guardrails, Safety & Security](14-guardrails-safety.md) · [⬅ Back to index](../README.md) · [Next: Choosing Models & Approaches ➡](16-choosing-models.md)
