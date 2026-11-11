# Scenario-Based Questions

[⬅ Back to index](../README.md)

Covers: what to do when a chatbot hallucinates, retrieval is poor, a feature is slow or expensive, prompts are attacked, outputs break after a model upgrade, an agent loops or takes a wrong action, quality differs by language, users over-trust answers, sensitive data appears in prompts, and AI coding causes bugs.

---

### 1. Your customer-support chatbot is hallucinating policies. How do you fix it?

<details>
<summary>Answer</summary>

1. **Collect failing conversations** and categorise: was the right policy document retrieved? Was it ignored? Was there no document?
2. **Ground it** – RAG over the official policy docs, with instructions to answer only from context and cite sources; say "I don't know / let me connect you to an agent" otherwise.
3. **Improve retrieval** if the right document wasn't found (chunking, hybrid search, reranking, query rewriting).
4. **Add guardrails** – groundedness checks before sending; restrict certain topics (refund amounts) to tool calls against real systems.
5. **Evaluate** – build an eval set from these failures; track faithfulness and escalation rates; monitor in production.

</details>
### 2. Your RAG system returns irrelevant documents. How do you improve it?

<details>
<summary>Answer</summary>

- Look at **real failing queries** and their retrieved chunks.
- Fix **parsing and chunking** first (broken tables, chunks without context, too-large chunks).
- Add **hybrid search** (BM25 for exact terms like product codes) and a **reranker**.
- **Query rewriting** for conversational and vague queries; metadata filters (product, version, date).
- Try a better or domain-tuned embedding model.
- Measure recall@k on a labelled set after each change so improvements are real.

</details>
### 3. Your LLM feature is too slow (8 seconds). How do you reduce latency?

<details>
<summary>Answer</summary>

Measure where time goes (queueing, retrieval, TTFT, generation length, tool calls), then:
- **Stream** the response (perceived latency drops to TTFT).
- **Fewer output tokens** – ask for concise answers, cap max tokens.
- **Smaller/faster model** for this task, or routing; disable heavy reasoning modes when not needed.
- **Shorter prompts** and **prompt caching** to cut TTFT.
- **Parallelise** independent retrieval/tool calls; cache results.
- Precompute what can be done ahead of time (batch generation for common content).
- For self-hosted models: quantisation, speculative decoding, better batching settings.

</details>
### 4. The LLM feature costs too much at scale. What do you do?

<details>
<summary>Answer</summary>

Measure cost per request and its components (input vs output tokens, which calls), then: route easy requests to cheaper models; trim prompts and retrieved context; prompt caching; cache frequent answers (exact/semantic, permission-aware); batch APIs for offline work; fine-tune or distil a small model for the high-volume task; avoid LLM calls where rules or classic ML suffice; set per-user rate limits. Always re-run evals to confirm quality holds.

</details>
### 5. Users found a way to make your assistant reveal its system prompt and ignore rules. What do you do?

<details>
<summary>Answer</summary>

- **Immediate** – assess impact (was any sensitive data or capability exposed?), add input/output filters for the known attack, and tighten tool permissions.
- **Design fixes** – never put secrets in prompts; enforce rules in code, not just in instructions; least-privilege tools with confirmation for sensitive actions; separate untrusted content from instructions.
- **Process** – add the attacks to a red-team regression suite, monitor for similar patterns, and accept that prompt-level defences are never complete – limit what a successful attack can do.

</details>
### 6. Should you fine-tune or use RAG for a legal-document Q&A product?

<details>
<summary>Answer</summary>

Mostly **RAG**: the knowledge is large, changes (new cases, amendments), must be **cited**, and access may be restricted per client. Fine-tuning is worth considering later for **style and task behaviour** (legal drafting tone, citation format, structured outputs) or to make a smaller model cheaper – combined with RAG, not replacing it. Build an evaluation set with legal experts first; it decides between options.

</details>
### 7. Your new prompt improved the average score but broke some important cases. What do you do?

<details>
<summary>Answer</summary>

Look at results **by category**, not just the average. Identify which cases regressed and why; add them as required test cases with must-pass status; iterate on the prompt (or split the task by routing different case types to different prompts); consider whether the "important cases" deserve a deterministic path. Ship only when critical categories don't regress, and roll out gradually with monitoring.

</details>
### 8. You're asked to add AI to a product. How do you decide whether it's worth it?

<details>
<summary>Answer</summary>

- Start from a **user problem** and how success will be measured, not from the technology.
- Check feasibility quickly: prototype with a strong model on real examples; build a small eval set.
- Assess the cost of errors (is a wrong answer annoying or dangerous?) and design human oversight accordingly.
- Estimate cost per use at expected volume and latency requirements.
- Consider simpler alternatives (search, rules, templates).
- Launch to a small group, measure, iterate.

</details>
### 9. Your agent sometimes gets stuck in loops or takes wrong actions. How do you improve it?

<details>
<summary>Answer</summary>

Trace failing runs step by step. Typical fixes: clearer tool descriptions and fewer overlapping tools; better error messages from tools; step/budget limits and loop detection; moving predictable parts into deterministic code; adding a planning or verification step; managing context (summarise long tool outputs); stronger model for decision points; human approval before irreversible actions. Build an agent eval set with success checks and track the pass rate over multiple runs.

</details>
### 10. Your LLM feature works well in English but poorly in Hindi and other Indian languages. What do you do?

<details>
<summary>Answer</summary>

- **Measure** – build evaluation sets per language (including code-mixed Hinglish and transliterated text), since English results don't transfer.
- **Model choice** – compare models on those sets; some models (including Indic-focused ones) handle Indian languages and scripts much better, and tokenisation efficiency differs (more tokens per sentence means higher cost and latency).
- **Retrieval** – multilingual embedding models; index content in the languages users search in; query translation as a fallback.
- **Prompting** – instruct the response language explicitly; provide examples in the target language.
- **Fine-tuning** – on high-quality native data if prompting isn't enough.
- **Speech** – if voice is involved, ASR quality for accents and code-switching is often the bottleneck.

</details>
### 11. Your prompt worked in testing but fails often in production. What do you do?

<details>
<summary>Answer</summary>

Your test set didn't match reality. Sample production failures and categorise them (unexpected input types, languages, lengths, ambiguous requests, adversarial use); add them to the eval set; fix the prompt or add routing/validation for those categories; and set up ongoing sampling of production traffic with automated grading so new failure types are caught early.

</details>

### 12. After upgrading to a newer model, outputs changed and some integrations broke. What now?

<details>
<summary>Answer</summary>

Roll back (or route affected traffic to the old model) if users are impacted. Then compare outputs on your eval suite; check migration notes for changed or removed parameters; inspect format differences (JSON escaping, verbosity, tool-call behaviour) and make parsing robust (schemas, proper JSON parsing); adjust prompts written for the old model; re-run evals and roll out gradually. Prevent it next time with pinned versions and pre-upgrade eval runs.

</details>

### 13. Your agent deleted files it shouldn't have. How do you prevent this?

<details>
<summary>Answer</summary>

Restore from version control/backups, then fix the setup: least-privilege tools (read-only by default, scoped paths), sandboxes, **approval required for destructive commands**, deterministic hooks or policies that block dangerous operations, clear instructions and stopping conditions, and logging to see why it happened (often an ambiguous instruction or prompt injection from content it read).

</details>

### 14. Users trust the assistant's answers even when they're wrong. What do you change?

<details>
<summary>Answer</summary>

Show sources/citations and make verification easy; express uncertainty when evidence is weak; allow "I don't know"; improve grounding (RAG quality, quote-first answers); add review steps for high-stakes answers; adjust UI copy so it doesn't oversell accuracy; and measure correctness on real questions, not just satisfaction ratings.

</details>

### 15. You discover customers are pasting sensitive personal data into your AI feature. What do you do?

<details>
<summary>Answer</summary>

Check your data handling: provider retention and training terms, where conversations and logs are stored, who can access them. Add PII detection and redaction before sending and before logging, warnings in the UI, retention limits, and deletion support. Update privacy notices and involve legal/security if regulated data (health, financial) is involved.

</details>

### 16. Your team adopted an AI coding assistant and bug rates went up. What do you do?

<details>
<summary>Answer</summary>

Look at where bugs come from (unreviewed AI code, missing tests, unfamiliar areas). Then: require tests and normal code review for AI-generated changes, use plan-first workflows for non-trivial changes, improve project instruction files with conventions and gotchas, add CI checks (linters, type checks, security scanning), share good practices, and measure defect rates alongside speed.

</details>

### 17. Stakeholders want to "add an AI agent" to a process. How do you respond?

<details>
<summary>Answer</summary>

Clarify the goal and the cost of errors, map the current process, and identify the steps where AI adds value. Often a **workflow** (a few LLM calls with fixed steps and human review) delivers most of the value with far less risk than an autonomous agent. Prototype the simplest version, measure it with an eval set and a pilot, and increase autonomy only as reliability is demonstrated.

</details>

### 18. Your RAG assistant gives outdated answers. Why, and how do you fix it?

<details>
<summary>Answer</summary>

Typical causes: the index isn't re-synced when documents change, old versions of documents remain indexed, retrieval doesn't prefer recent content, or the model answers from its training knowledge instead of the documents. Fix with change-driven re-indexing and deletion of stale versions, recency metadata and filters, "answer only from these documents" instructions with citations, and monitoring of document freshness.

</details>

---

[⬅ Previous: AI System Design](20-ai-system-design.md) · [⬅ Back to index](../README.md) · [Next: Company-Wise Questions & What's New ➡](22-company-wise-whats-new.md)
