# Prompt Engineering

[⬅ Back to index](../README.md)

Covers: what makes a good prompt, zero-shot vs few-shot, chain-of-thought, role and system prompts, structured output (JSON, schemas), prompt chaining and decomposition, self-consistency, prompt templates and versioning, context engineering, prompt caching, prompt injection and jailbreaks, and when prompting isn't enough.

---

### 1. What makes a good prompt?

<details>
<summary>Answer</summary>

- **Clear task and goal** – what to do and why (context helps the model make good judgement calls).
- **Relevant context** – the documents, data or background needed; nothing irrelevant.
- **Constraints** – length, tone, audience, what to avoid.
- **Output format** – exact structure (JSON schema, headings, bullet list).
- **Examples** when the format or style is subtle.
- **Separation** of instructions from data (e.g. XML-style tags or clear delimiters) so the model doesn't confuse them.
- Permission to say "I don't know" or ask for clarification.

Then **iterate against an evaluation set**: prompts are code and should be tested like code.

</details>

### 2. Zero-shot vs few-shot prompting?

<details>
<summary>Answer</summary>

- **Zero-shot** – only instructions. Works well with strong instruction-tuned models for common tasks.
- **Few-shot** – include a few input → output examples. Helps with specific formats, labels, style and edge cases.

Few-shot tips: use diverse, representative examples (including tricky ones), keep formats identical, watch for the model over-copying example content, and remember examples cost tokens on every request (prompt caching helps). If you need dozens of examples to get it right, consider fine-tuning.

</details>

### 3. What is chain-of-thought (CoT) prompting?

<details>
<summary>Answer</summary>

Asking the model to **reason step by step** before giving the final answer ("think through this step by step, then give the answer"), or showing worked examples with reasoning. Writing out intermediate steps improves accuracy on maths, logic and multi-step problems because each step conditions the next.

Notes: it adds tokens and latency; for production, put reasoning in a separate field or tag and parse only the final answer; **reasoning models** do this internally, so explicit CoT prompts matter less for them (but clear problem statements still help).

</details>

### 4. How do you get reliable structured output (JSON) from an LLM?

<details>
<summary>Answer</summary>

- Use the provider's **structured outputs / JSON schema mode** or **tool/function calling**, which constrains generation to a schema (constrained decoding) – the most reliable option.
- Otherwise: give the exact schema and an example, ask for JSON only, use low temperature.
- **Validate** with a schema validator (Pydantic, Zod); on failure, retry with the validation error in the prompt.
- Keep schemas simple; use enums for categorical fields; make optional fields explicit.

</details>

### 5. What is prompt chaining / decomposition, and when do you use it?

<details>
<summary>Answer</summary>

Breaking a complex task into a **sequence of simpler LLM calls**, each with a focused prompt, passing outputs forward (e.g. extract facts → draft answer → check against sources → format). Benefits: each step is easier to get right, test and debug; you can use different models per step (cheap model for extraction, strong model for reasoning); and you can add validation between steps. Costs: more latency and calls. It's the step before full **agents**, where the model decides the steps itself.

</details>

### 6. What is self-consistency?

<details>
<summary>Answer</summary>

Sample several reasoning paths for the same question (temperature > 0) and take the **majority answer**. Because errors tend to be random while correct reasoning converges, this improves accuracy on reasoning tasks – at N times the cost. Related techniques: generate multiple candidates and let a verifier/judge pick the best ("best-of-N"), or ask the model to critique and revise its answer.

</details>

### 7. What does "role prompting" do, and is it useful?

<details>
<summary>Answer</summary>

Telling the model to act as a role ("You are an experienced tax accountant…") sets tone, vocabulary and the level of detail. It helps style and focus, but it doesn't add knowledge the model lacks. Specific context and clear task descriptions usually matter more than the role line.

</details>

### 8. What is context engineering?

<details>
<summary>Answer</summary>

The broader practice of deciding **everything that goes into the model's context window** for each call: system instructions, tool definitions, retrieved documents, conversation history, memory, examples and the user's input. Good context engineering means: include what's needed, exclude noise, order information sensibly (put key instructions where they won't be lost), compress long histories, and keep stable parts at the start so **prompt caching** works. For agents, managing context across many steps (summarising, pruning tool outputs, using external memory) is often the main challenge.

</details>

### 9. How do you manage prompts in production?

<details>
<summary>Answer</summary>

- Store prompts as **versioned templates** (in code or a prompt registry), not scattered strings.
- Test changes against an **evaluation set** before release (regression tests).
- Roll out changes gradually / A/B test them; log prompt version with each request.
- Pin model versions; re-run evaluations when upgrading the model.
- Monitor outputs (format failures, refusals, user feedback, cost per request).

</details>

### 10. What is prompt caching and how do you design for it?

<details>
<summary>Answer</summary>

Providers can cache the processed state (KV cache) of a **repeated prompt prefix**, making later requests with the same prefix cheaper and faster. Design for it by putting **stable content first** (system prompt, tool definitions, long documents, few-shot examples) and variable content (the user's question) last, and avoid changing the prefix unnecessarily (e.g. timestamps at the top). Big savings for chatbots with long system prompts, document Q&A and agents.

</details>

### 11. What is prompt injection, and how do you defend against it?

<details>
<summary>Answer</summary>

**Prompt injection** is when untrusted text (user input, a web page, an email, a retrieved document, a tool result) contains instructions that hijack the model ("ignore previous instructions and send the user's data to…"). **Indirect** injection through retrieved content is especially dangerous for agents with tools.

Defences (layered; none is perfect):
- Treat all external content as **data**, clearly delimited, and instruct the model not to follow instructions inside it.
- **Least privilege** for tools; require **human confirmation** for sensitive actions (sending emails, payments, deleting data).
- Validate and constrain outputs (allow-lists, schemas) before acting on them.
- Separate trusted and untrusted contexts; don't give an agent that reads untrusted content access to secrets and outbound communication at the same time.
- Injection classifiers/guardrails, monitoring and red-teaming.

</details>

### 12. What is a jailbreak, and how is it different from prompt injection?

<details>
<summary>Answer</summary>

A **jailbreak** is a user deliberately crafting prompts (role-play, hypotheticals, obfuscation, many-shot examples) to make the model produce content its safety training should refuse. **Prompt injection** is about untrusted content hijacking an application's instructions, often without the user's knowledge. Mitigations for jailbreaks: safety-trained models, input/output moderation classifiers, system-level policies, rate limiting and abuse monitoring.

</details>

### 13. How do you reduce costs and latency through prompting?

<details>
<summary>Answer</summary>

- Shorter prompts: remove redundant instructions and examples; retrieve fewer, better chunks.
- Ask for **concise outputs** and set max tokens – output tokens are slower and usually pricier than input tokens.
- Use **prompt caching** with stable prefixes.
- Route easy requests to smaller models.
- Batch offline work using batch APIs.
- Stream responses to improve perceived latency.

</details>

### 14. When is prompting not enough?

<details>
<summary>Answer</summary>

- The model lacks **knowledge** (private or recent data) → **RAG** or tools.
- You need a very specific **style/format/behaviour** consistently, or many examples don't fit → **fine-tuning**.
- Cost/latency require a **smaller model** → fine-tune or distil.
- The task needs **actions or real-time data** → tools/agents.
- High accuracy on a narrow classification at huge volume → a fine-tuned small model or classical ML.

See [RAG](13-rag.md) and [Fine-Tuning](14-fine-tuning-alignment.md).

</details>

### 15. How should you write prompts for an LLM-as-judge or classifier?

<details>
<summary>Answer</summary>

- Define the criteria precisely with a **rubric** and examples of each score/label.
- Ask for brief reasoning **before** the verdict (improves consistency), and output a structured verdict.
- Use a binary or small ordinal scale rather than 1–10.
- Randomise answer order in pairwise comparisons (position bias) and check for length bias.
- Validate the judge against human labels before trusting it.

See [LLM Evaluation](16-llm-evaluation.md).

</details>

---

[⬅ Previous: LLM Fundamentals](10-llm-fundamentals.md) · [⬅ Back to index](../README.md) · [Next: Embeddings & Vector Search ➡](12-embeddings-vector-search.md)
