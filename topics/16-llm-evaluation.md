# LLM Evaluation

[⬅ Back to index](../README.md)

Covers: why evaluating LLM features is hard, building evaluation sets, automatic metrics, LLM-as-judge and its biases, human evaluation, public benchmarks and contamination, evaluating RAG and agents, regression testing in CI, online evaluation and monitoring, and safety evaluation.

---

### 1. Why is evaluating LLM applications hard?

<details>
<summary>Answer</summary>

- Outputs are **open-ended**: many different answers can be correct.
- Outputs are **non-deterministic**: the same input can produce different results.
- Quality has many dimensions: correctness, faithfulness, helpfulness, tone, format, safety, latency, cost.
- Small prompt or model changes can fix one case and break others.
- Public benchmarks rarely reflect your specific task.

So teams need **task-specific evaluation sets**, several kinds of metrics, and continuous evaluation – "evals" are the LLM equivalent of unit and integration tests.

</details>

### 2. How do you build an evaluation set?

<details>
<summary>Answer</summary>

1. Start from **real user inputs** (logs, support tickets) plus hand-written cases; begin with 50–200 examples and grow over time.
2. Cover the distribution: common cases, **edge cases**, adversarial inputs, unanswerable questions, multiple languages if relevant.
3. Write **expected outputs or grading criteria** for each (a reference answer, required facts, a rubric, or a check function).
4. Tag examples by category so you can see where quality changes.
5. Every production bug becomes a new test case.
6. Keep it separate from anything used for few-shot examples or fine-tuning.

</details>

### 3. What kinds of automatic metrics are used?

<details>
<summary>Answer</summary>

- **Code-based checks** (cheapest and most reliable): exact match, JSON schema validity, regex, contains required fields, unit tests pass (code generation), SQL returns the right rows, numeric tolerance.
- **Reference-based text metrics**: BLEU/ROUGE (surface overlap), BERTScore (embedding similarity) – limited for open-ended answers.
- **Classifier-based**: toxicity, PII detection, language detection.
- **LLM-as-judge**: a model grades outputs against a rubric or reference.
- **Operational**: latency, tokens, cost, refusal rate, format-failure rate.

Prefer deterministic checks wherever possible; use LLM judges for qualities that need judgement.

</details>

### 4. What is LLM-as-judge, and what are its pitfalls?

<details>
<summary>Answer</summary>

Using an LLM to evaluate outputs: **single-answer grading** (score against a rubric or reference) or **pairwise comparison** (which of two answers is better). It scales human-like judgement cheaply.

Known biases: **position bias** (prefers the first or second answer), **verbosity bias** (prefers longer answers), **self-preference** (prefers its own model family's style), and sensitivity to the judge prompt. Mitigations: precise rubrics with examples, binary or small scales, ask for reasoning before the verdict, swap positions and average, use a strong judge model, and **validate the judge against human labels** (measure agreement) before trusting it.

</details>

### 5. When do you need human evaluation?

<details>
<summary>Answer</summary>

For subjective qualities (tone, helpfulness, creativity), high-stakes domains (medical, legal, financial), calibrating LLM judges, and final checks before major launches. Make it reliable with clear guidelines, trained raters, multiple raters per item and inter-rater agreement measurement, and side-by-side comparisons (easier and more consistent than absolute scores).

</details>

### 6. What are the well-known public benchmarks, and how much should you trust them?

<details>
<summary>Answer</summary>

Examples: **MMLU / MMLU-Pro** (knowledge across subjects), **GPQA** (hard science questions), **GSM8K / MATH / AIME** (maths), **HumanEval / LiveCodeBench / SWE-bench** (coding), **Chatbot Arena** (crowdsourced pairwise preferences), long-context and tool-use benchmarks, and newer very hard sets (e.g. Humanity's Last Exam).

Limitations: **contamination** (test questions leaked into training data), saturation (top models all score near the ceiling), and mismatch with your use case. Treat them as rough signals; decide using your own evals.

</details>

### 7. What is benchmark contamination, and how do you detect or avoid it?

<details>
<summary>Answer</summary>

When benchmark questions (or close paraphrases) appear in a model's training data, scores measure memorisation rather than ability. Detection: n-gram overlap checks against training data, comparing performance on the original vs rephrased or newly written questions, and time-based splits (questions created after the model's cutoff, as in LiveCodeBench). For your own evals: keep them private, don't paste them into tools that may train on them, and refresh them regularly.

</details>

### 8. How do you evaluate a RAG system specifically?

<details>
<summary>Answer</summary>

Separate components:
- **Retrieval** – recall@k, precision@k, MRR on questions labelled with relevant chunks.
- **Context relevance** – how much retrieved text is actually useful.
- **Faithfulness / groundedness** – every claim in the answer is supported by retrieved context (LLM judge or NLI model checks claims one by one).
- **Answer correctness and relevance** – against references or a rubric.
- **Citation accuracy** – cited sources really support the claims.
- **Abstention** – says "I don't know" on unanswerable questions.

See [RAG](13-rag.md).

</details>

### 9. How do you run evaluations in a CI/CD pipeline?

<details>
<summary>Answer</summary>

- Store eval sets and grading code in the repository.
- On every change to prompts, models, retrieval settings or tools, run the eval suite (or a fast subset on each commit and the full set nightly).
- Compare against the current production baseline per category; **block the release on regressions** beyond a threshold.
- Run multiple samples per example for non-deterministic outputs and look at pass rates, not single runs.
- Track cost and latency alongside quality.
- Keep results and traces to inspect failures.

</details>

### 10. How do you evaluate LLM features in production (online)?

<details>
<summary>Answer</summary>

- **User signals** – thumbs up/down, edits to generated text, copy/accept rates, retries, abandonment, escalation to a human.
- **A/B tests** for prompt or model changes on business metrics.
- **Sampling + automatic grading** – run LLM judges or classifiers on a sample of live traffic (faithfulness, toxicity, format).
- **Monitoring** – latency, error rates, token usage and cost, refusal rates, drift in input topics.
- **Human review queues** for flagged conversations.

Feed failures back into the offline eval set.

</details>

### 11. How do you evaluate safety?

<details>
<summary>Answer</summary>

- **Red-teaming** – humans and automated attackers try jailbreaks, prompt injection, harmful requests, PII extraction, and bias-eliciting prompts.
- **Safety eval sets** – harmful-request refusal rates and **over-refusal** rates on benign requests (both matter).
- Bias and fairness checks across demographic variations of the same prompt.
- For agents, tests of unsafe or unauthorised actions in sandboxes.
- Re-run after every model or prompt change.

See [Responsible AI & Safety](21-responsible-ai-safety.md).

</details>

### 12. What is "vibe checking", and why is it not enough?

<details>
<summary>Answer</summary>

Trying a handful of prompts by hand and judging whether outputs "feel right". It's useful early for exploration, but it misses regressions, covers few cases, and is biased by recent examples. Mature teams move quickly to a systematic eval set, tracked metrics and automated runs – while still reading real outputs regularly, because metrics can miss issues.

</details>

### 13. How do you compare two models or prompts fairly?

<details>
<summary>Answer</summary>

Use the same eval set and grading; run multiple samples per input; report metrics per category with **confidence intervals** (small eval sets have large error bars); include cost and latency; use pairwise comparisons with position swapping for subjective quality; and check statistical significance before declaring a winner. Look at examples where they disagree – it's often more informative than the aggregate score.

</details>

### 14. How do you measure hallucination rate?

<details>
<summary>Answer</summary>

- For grounded tasks (RAG, summarisation): split answers into **atomic claims** and check each against the source documents (LLM judge or NLI model) – hallucination rate = unsupported claims ÷ total claims.
- For factual QA: questions with verified answers, scoring correct / incorrect / abstained separately (an "I don't know" is better than a wrong answer).
- Track citation validity.
- Sample human verification to calibrate the automated checks.

</details>

---

[⬅ Previous: Agents, Tool Use & MCP](15-agents-tool-use.md) · [⬅ Back to index](../README.md) · [Next: LLM Inference, Serving & Optimisation ➡](17-llm-inference-serving.md)
