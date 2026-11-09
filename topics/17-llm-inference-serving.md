# LLM Inference, Serving & Optimisation

[⬅ Back to index](../README.md)

Covers: prefill vs decode, latency metrics (TTFT, TPOT, throughput), GPU memory maths, quantisation, KV cache management and PagedAttention, continuous batching, speculative decoding, parallelism (tensor, pipeline, expert), serving frameworks, on-device inference, streaming, cost estimation and reduction, and routing between models.

---

### 1. What are the prefill and decode phases of LLM inference?

<details>
<summary>Answer</summary>

- **Prefill** – the whole prompt is processed **in parallel** in one forward pass, filling the KV cache. It's **compute-bound** and determines **time to first token**.
- **Decode** – output tokens are generated **one at a time**, each step reading all the weights and the KV cache. It's **memory-bandwidth-bound**: the GPU spends most of its time moving weights, not computing.

This asymmetry drives most serving optimisations (batching many decodes together, separating prefill and decode onto different machines – "disaggregated serving").

</details>

### 2. Which latency and throughput metrics matter?

<details>
<summary>Answer</summary>

- **TTFT (time to first token)** – how long until output starts; dominated by queueing + prefill. Key for perceived responsiveness.
- **TPOT / ITL (time per output token / inter-token latency)** – speed of streaming, e.g. 20–50 ms/token.
- **End-to-end latency** ≈ TTFT + TPOT × output tokens.
- **Throughput** – tokens/second (or requests/second) across all users per GPU.
- **Goodput** – throughput of requests that meet latency targets.

There's a trade-off: bigger batches raise throughput and lower cost but increase per-request latency.

</details>

### 3. How much GPU memory does it take to serve a model?

<details>
<summary>Answer</summary>

**Weights** = parameters × bytes per parameter:
- 70B at BF16 (2 bytes) ≈ 140 GB → needs at least two 80 GB GPUs.
- 70B at INT8 ≈ 70 GB; at 4-bit ≈ 35–40 GB.
- 8B at BF16 ≈ 16 GB.

Plus the **KV cache** = `2 × layers × KV heads × head dimension × bytes × tokens in flight`. Example: a 70B-class model with 80 layers, 8 KV heads (GQA), head dimension 128, BF16 → 2 × 80 × 8 × 128 × 2 ≈ **0.33 MB per token**; 32 concurrent requests × 8K tokens ≈ 256K tokens ≈ **84 GB** – as much as the weights. Plus activations and framework overhead.

Being able to do this calculation in an interview is a strong signal.

</details>

### 4. What is quantisation, and what are the trade-offs?

<details>
<summary>Answer</summary>

Storing weights (and sometimes activations and the KV cache) in fewer bits: FP16/BF16 → **FP8 / INT8 / INT4**.
- Benefits: less memory (bigger models or batches per GPU), faster decode (less data to move), cheaper serving, on-device feasibility.
- Costs: some quality loss, larger at 4 bits and below and for small models; some kernels/hardware don't support all formats.

Methods: **post-training quantisation** (GPTQ, AWQ – use calibration data to minimise error; bitsandbytes NF4; GGUF formats for llama.cpp), FP8 on recent GPUs, and **quantisation-aware training** for the best low-bit quality. Always evaluate the quantised model on your tasks.

</details>

### 5. What is PagedAttention (vLLM)?

<details>
<summary>Answer</summary>

Naively, each request reserves contiguous KV-cache memory for its maximum length, wasting most of it (fragmentation, over-reservation). **PagedAttention** stores the KV cache in fixed-size **blocks** allocated on demand, like virtual memory pages, with a block table per request. Results: near-zero waste, many more concurrent requests per GPU, and cheap **prefix sharing** (several requests with the same system prompt share cache blocks). It's the core idea of **vLLM** and is now used across serving engines.

</details>

### 6. What is continuous batching?

<details>
<summary>Answer</summary>

Static batching waits for a batch to fill and for **all** sequences in it to finish, so short requests wait for long ones and GPUs sit idle. **Continuous (in-flight) batching** schedules at the **token-step level**: finished sequences leave the batch immediately and new requests join at the next step. This dramatically increases throughput and lowers average latency. Chunked prefill further mixes prefill work into decode steps to avoid stalls.

</details>

### 7. How does speculative decoding work?

<details>
<summary>Answer</summary>

A small, fast **draft** model (or extra prediction heads like Medusa/EAGLE, or n-gram lookup from the prompt) proposes several tokens ahead; the large **target** model verifies all of them in **one parallel forward pass**, accepting the longest prefix that matches its own distribution (with a rejection-sampling rule that keeps output quality **identical** to the target model). Because decode is memory-bound, verifying k tokens costs about the same as generating one, so you get 2–3× speedups when the draft is usually right.

</details>

### 8. How do you serve a model that doesn't fit on one GPU?

<details>
<summary>Answer</summary>

- **Tensor parallelism** – split each layer's matrices across GPUs (within a node with fast NVLink); every layer needs communication.
- **Pipeline parallelism** – put different layers on different GPUs/nodes; activations flow between stages.
- **Expert parallelism** – for MoE models, place different experts on different GPUs.
- **Data parallelism / replicas** – copies of the whole (sharded) model to handle more traffic.

Or reduce the size: quantisation, a smaller/distilled model.

</details>

### 9. What serving frameworks are commonly used?

<details>
<summary>Answer</summary>

- **vLLM** – PagedAttention, continuous batching, wide model support; very popular open-source choice.
- **SGLang** – fast serving with prefix caching (RadixAttention) and structured generation.
- **TensorRT-LLM** (NVIDIA) – highly optimised kernels; often used with Triton Inference Server or NVIDIA NIM.
- **Hugging Face TGI**, **llama.cpp** / **Ollama** (CPU/Mac/edge, GGUF quantised models), **MLC LLM**, **ExecuTorch** / **LiteRT** / **Core ML** for mobile.
- Managed: provider APIs, cloud model hosting (Bedrock, Vertex AI, Azure AI), and inference platforms.

</details>

### 10. How do you run LLMs on-device (phones, laptops)?

<details>
<summary>Answer</summary>

Use small models (roughly 1–8B parameters), aggressively quantised (4-bit), with runtimes that use the NPU/GPU: Apple's **Foundation Models framework** and Core ML/MLX, Android's **Gemini Nano** via ML Kit GenAI APIs/AICore, LiteRT, ExecuTorch, llama.cpp. Benefits: privacy, offline use, no per-token cost, low network latency. Limits: memory and battery, thermal throttling, lower capability than cloud models, and model download size. Common pattern: on-device for simple/private tasks, cloud fallback for complex ones.

</details>

### 11. Why stream LLM responses, and how?

<details>
<summary>Answer</summary>

Generation takes seconds; streaming tokens as they're produced makes the app feel responsive (users start reading after TTFT). Implement with **Server-Sent Events** or WebSockets from the model server through your backend to the client; handle cancellation (stop generation when the user leaves, to save cost), partial-output rendering (markdown, code blocks), and errors mid-stream. Structured outputs can also be streamed and parsed incrementally.

</details>

### 12. How do you estimate the cost of an LLM feature?

<details>
<summary>Answer</summary>

`cost/request = input tokens × input price + output tokens × output price` (output tokens usually cost several times more than input; cached input is cheaper). Multiply by requests per day.

Example: a support bot with 3,000 input tokens (system prompt + retrieved chunks + history) and 300 output tokens, 1M requests/month. At illustrative prices of $1 per million input tokens and $5 per million output tokens: 3,000 × $1/M + 300 × $5/M = $0.003 + $0.0015 = **$0.0045 per request → ~$4,500/month**. With 70% of input tokens served from prompt cache at a 90% discount, the input cost drops substantially.

For self-hosting, compare GPU cost per hour ÷ achieved tokens per hour at your latency target.

</details>

### 13. What are the main ways to reduce LLM inference cost?

<details>
<summary>Answer</summary>

- **Right-size the model** – a smaller or fine-tuned/distilled model for simple tasks; **routing** between cheap and expensive models by difficulty.
- **Fewer tokens** – shorter prompts, fewer/better retrieved chunks, concise outputs, summarised history.
- **Caching** – prompt (prefix) caching, exact and semantic response caching.
- **Batch APIs** for offline work (often ~50% cheaper).
- Self-hosting optimisations: quantisation, continuous batching, speculative decoding, high GPU utilisation.
- Avoid unnecessary calls (deterministic code where possible, rules before LLMs).

</details>

### 14. What is model routing / cascading?

<details>
<summary>Answer</summary>

- **Routing** – a classifier (or small LLM) decides which model should handle a request based on difficulty, domain or required features; easy requests go to cheap fast models.
- **Cascading** – try the cheap model first; if confidence is low or a check fails, escalate to the stronger model.

Both cut cost and latency substantially when most traffic is easy, but need good evals to make sure quality holds for routed requests.

</details>

### 15. How do you handle rate limits and failures from LLM APIs?

<details>
<summary>Answer</summary>

- Retries with **exponential backoff and jitter** on 429/5xx; respect `Retry-After`.
- Client-side **rate limiting/queues** sized to your quota (requests and tokens per minute).
- **Fallback** to another model or provider (an LLM gateway helps).
- Timeouts, and circuit breakers when a provider degrades.
- Idempotent handling so retries don't duplicate side effects in agent workflows.
- Graceful degradation in the UI ("this feature is busy, try again").

</details>

### 16. What GPU concepts should an AI engineer know?

<details>
<summary>Answer</summary>

- **HBM memory capacity** (e.g. 80–192 GB on data-centre GPUs) limits model + KV cache size.
- **Memory bandwidth** (TB/s) limits decode speed: tokens/s per sequence ≈ bandwidth ÷ bytes read per token (roughly the model size in bytes for dense models).
- **FLOPs** limit prefill and training.
- **Interconnects** (NVLink within a node, InfiniBand between nodes) matter for multi-GPU parallelism.
- Utilisation metrics (MFU – model FLOPs utilisation) for training efficiency.

Example: a 16 GB (BF16 8B) model on a GPU with ~3 TB/s bandwidth has a theoretical single-stream ceiling of ~190 tokens/s; batching shares each weight read across many sequences.

</details>

### 17. What are pruning and other model compression techniques besides quantisation?

<details>
<summary>Answer</summary>

- **Pruning** – remove weights or structures that contribute little. **Unstructured** pruning zeroes individual weights (high sparsity but needs sparse kernels to speed up); **structured** pruning removes whole neurons, attention heads, channels or layers (real speedups on normal hardware, more accuracy loss). Usually followed by fine-tuning to recover accuracy.
- **Knowledge distillation** – train a smaller student to mimic a larger model.
- **Low-rank factorisation** – replace big weight matrices with products of smaller ones.
- **Efficient architectures** – MoE, GQA, smaller vocabularies.

Combine techniques (e.g. distil → prune → quantise) and always measure accuracy and real latency on the target hardware.

</details>

### 18. How do you load-test an LLM endpoint?

<details>
<summary>Answer</summary>

- Generate **realistic traffic**: a distribution of prompt and output lengths from production logs (latency depends heavily on both), realistic concurrency and arrival patterns.
- Measure TTFT, inter-token latency and end-to-end latency at p50/p95/p99, throughput (tokens/s), error and rate-limit rates, and GPU utilisation/memory.
- Increase load until latency SLOs break to find the **maximum sustainable concurrency** per GPU/replica; that drives capacity planning and autoscaling thresholds.
- Test long-context and burst scenarios separately, and include cache effects (prefix caching makes repeated prompts look faster than reality).

</details>

---

[⬅ Previous: LLM Evaluation](16-llm-evaluation.md) · [⬅ Back to index](../README.md) · [Next: Generative Models ➡](18-generative-models.md)
