# Scenario-Based Questions

[⬅ Back to index](../README.md)

Covers: the "what would you do if…" questions asked in AI/ML interviews: model performance drops, suspiciously good results, imbalanced data, a hallucinating chatbot, poor RAG retrieval, slow or expensive LLM features, prompt injection incidents, choosing between approaches with little data, and explaining decisions to stakeholders.

> How to answer: **(1) clarify and gather data, (2) form hypotheses, (3) check the cheapest/most likely causes first, (4) fix, (5) prevent recurrence with monitoring and tests.**

---

## Classic ML Scenarios

### 1. Your model's accuracy dropped in production last week. How do you investigate?

<details>
<summary>Answer</summary>

1. **Confirm it's real** – is the metric computed correctly? Are labels delayed or incomplete? Did traffic composition change?
2. **Check the data pipeline** – missing or null features, schema changes upstream, a broken join, stale features, a changed unit (cents → dollars), timezone bugs. These cause most sudden drops.
3. **Check for deploys** – new model version, new feature code, library upgrades → roll back if correlated.
4. **Check drift** – compare feature and prediction distributions with training data; find which segments degraded (country, device, new product).
5. **Concept drift** – has the real-world relationship changed (new fraud pattern, seasonality, a competitor promotion)?
6. **Fix** – repair the pipeline, retrain on recent data, add features; then add monitoring and alerts on the root cause.

</details>

### 2. Your model gets 99% accuracy offline. What do you check?

<details>
<summary>Answer</summary>

Suspiciously good results usually mean a problem:
- **Class imbalance** – 99% might be the majority-class rate. Check precision/recall and a confusion matrix.
- **Data leakage** – a feature derived from the label or from the future (timestamps, IDs correlated with the outcome, post-event fields).
- **Train/test contamination** – duplicates or the same users in both sets; random split on time-series data.
- **An easy test set** that doesn't reflect production.

Compare with a simple baseline, inspect feature importances for suspicious features, and validate on a time-based holdout.

</details>

### 3. You need to build a fraud model but only 0.1% of transactions are fraud. What's your approach?

<details>
<summary>Answer</summary>

- **Metric** – PR-AUC, recall at a fixed precision, or expected monetary loss; not accuracy.
- **Data** – gather all positive labels (chargebacks, confirmed reports), consider label delay; time-based split.
- **Modelling** – gradient-boosted trees with class weights or careful down-sampling of negatives (then recalibrate probabilities); strong velocity and behavioural features; anomaly detection to catch new patterns.
- **Decisions** – thresholds based on the cost of false positives (customer friction) vs false negatives (losses); a review queue and step-up authentication for medium risk.
- **Operations** – frequent retraining, drift monitoring, feedback from reviewers.

</details>

### 4. You have only 500 labelled examples for a text classification task. What do you do?

<details>
<summary>Answer</summary>

- Try **zero/few-shot LLM prompting** as a baseline (no training needed).
- **Embeddings + logistic regression** – strong with little data.
- **Fine-tune a small pretrained model** (or use SetFit-style contrastive fine-tuning) with careful cross-validation.
- **Augment labels**: use an LLM to label more data (then spot-check), active learning (label the most uncertain examples next), weak supervision with heuristics.
- Keep a clean test set of real examples; compare approaches on cost, latency and accuracy.

</details>

### 5. Stakeholders want the model to be explainable, but the best model is a black box. What do you do?

<details>
<summary>Answer</summary>

Clarify **why** explanations are needed: regulation (credit decisions require reasons), debugging, or user trust. Options: measure how much accuracy an interpretable model (logistic regression, shallow GBDT with monotonic constraints) actually loses – often little; use SHAP to provide per-decision reasons from the complex model; provide reason codes and counterfactuals to users; document the model (model card). If regulation demands it, interpretable models may be the right choice despite a small accuracy loss.

</details>

### 6. An A/B test shows your new model improves click-through but reduces revenue. What do you do?

<details>
<summary>Answer</summary>

The offline/online objective is misaligned. Investigate segments (which users and items changed), check whether the model promotes cheaper or clickbait items, and look at downstream funnel metrics. Then align the objective: multi-objective ranking (clicks + purchase probability × value), constraints on revenue-critical slots, or a different label (conversions instead of clicks). Decide the launch based on the primary business metric and guardrails agreed **before** the test.

</details>

### 7. Your recommendation system keeps showing the same popular items. How do you fix it?

<details>
<summary>Answer</summary>

It's **popularity bias** amplified by a feedback loop. Fixes: re-ranking for **diversity** and novelty; **exploration** (bandits, reserving slots for fresh or long-tail items); debiasing training data (inverse propensity weighting, down-weighting popular items); personalised candidate sources; and measuring catalogue coverage and long-term engagement, not just CTR.

</details>

---

## LLM & GenAI Scenarios

### 8. Your customer-support chatbot is hallucinating policies. How do you fix it?

<details>
<summary>Answer</summary>

1. **Collect failing conversations** and categorise: was the right policy document retrieved? Was it ignored? Was there no document?
2. **Ground it** – RAG over the official policy docs, with instructions to answer only from context and cite sources; say "I don't know / let me connect you to an agent" otherwise.
3. **Improve retrieval** if the right document wasn't found (chunking, hybrid search, reranking, query rewriting).
4. **Add guardrails** – groundedness checks before sending; restrict certain topics (refund amounts) to tool calls against real systems.
5. **Evaluate** – build an eval set from these failures; track faithfulness and escalation rates; monitor in production.

</details>

### 9. Your RAG system returns irrelevant documents. How do you improve it?

<details>
<summary>Answer</summary>

- Look at **real failing queries** and their retrieved chunks.
- Fix **parsing and chunking** first (broken tables, chunks without context, too-large chunks).
- Add **hybrid search** (BM25 for exact terms like product codes) and a **reranker**.
- **Query rewriting** for conversational and vague queries; metadata filters (product, version, date).
- Try a better or domain-tuned embedding model.
- Measure recall@k on a labelled set after each change so improvements are real.

</details>

### 10. Your LLM feature is too slow (8 seconds). How do you reduce latency?

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

### 11. The LLM feature costs too much at scale. What do you do?

<details>
<summary>Answer</summary>

Measure cost per request and its components (input vs output tokens, which calls), then: route easy requests to cheaper models; trim prompts and retrieved context; prompt caching; cache frequent answers (exact/semantic, permission-aware); batch APIs for offline work; fine-tune or distil a small model for the high-volume task; avoid LLM calls where rules or classic ML suffice; set per-user rate limits. Always re-run evals to confirm quality holds.

</details>

### 12. Users found a way to make your assistant reveal its system prompt and ignore rules. What do you do?

<details>
<summary>Answer</summary>

- **Immediate** – assess impact (was any sensitive data or capability exposed?), add input/output filters for the known attack, and tighten tool permissions.
- **Design fixes** – never put secrets in prompts; enforce rules in code, not just in instructions; least-privilege tools with confirmation for sensitive actions; separate untrusted content from instructions.
- **Process** – add the attacks to a red-team regression suite, monitor for similar patterns, and accept that prompt-level defences are never complete – limit what a successful attack can do.

</details>

### 13. Should you fine-tune or use RAG for a legal-document Q&A product?

<details>
<summary>Answer</summary>

Mostly **RAG**: the knowledge is large, changes (new cases, amendments), must be **cited**, and access may be restricted per client. Fine-tuning is worth considering later for **style and task behaviour** (legal drafting tone, citation format, structured outputs) or to make a smaller model cheaper – combined with RAG, not replacing it. Build an evaluation set with legal experts first; it decides between options.

</details>

### 14. Your new prompt improved the average score but broke some important cases. What do you do?

<details>
<summary>Answer</summary>

Look at results **by category**, not just the average. Identify which cases regressed and why; add them as required test cases with must-pass status; iterate on the prompt (or split the task by routing different case types to different prompts); consider whether the "important cases" deserve a deterministic path. Ship only when critical categories don't regress, and roll out gradually with monitoring.

</details>

### 15. You're asked to add AI to a product. How do you decide whether it's worth it?

<details>
<summary>Answer</summary>

- Start from a **user problem** and how success will be measured, not from the technology.
- Check feasibility quickly: prototype with a strong model on real examples; build a small eval set.
- Assess the cost of errors (is a wrong answer annoying or dangerous?) and design human oversight accordingly.
- Estimate cost per use at expected volume and latency requirements.
- Consider simpler alternatives (search, rules, templates).
- Launch to a small group, measure, iterate.

</details>

### 16. Your agent sometimes gets stuck in loops or takes wrong actions. How do you improve it?

<details>
<summary>Answer</summary>

Trace failing runs step by step. Typical fixes: clearer tool descriptions and fewer overlapping tools; better error messages from tools; step/budget limits and loop detection; moving predictable parts into deterministic code; adding a planning or verification step; managing context (summarise long tool outputs); stronger model for decision points; human approval before irreversible actions. Build an agent eval set with success checks and track the pass rate over multiple runs.

</details>

### 17. A model performs well overall but badly for one user group. What do you do?

<details>
<summary>Answer</summary>

Measure performance **by slice** to confirm. Investigate causes: under-representation in training data, label quality differences, features that behave differently for that group, or distribution shift. Fixes: collect more representative data, re-weight, group-aware evaluation and thresholds (where lawful), separate models or features if behaviour truly differs. Add slice-level metrics to monitoring and launch criteria so it doesn't regress.

</details>

---

[⬅ Previous: ML Coding](22-ml-coding.md) · [⬅ Back to index](../README.md) · [Next: Company-Wise Questions & What's New ➡](24-company-wise-whats-new.md)
