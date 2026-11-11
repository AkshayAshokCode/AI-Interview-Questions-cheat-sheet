# Designing AI Products & UX

[⬅ Back to index](../README.md)

Covers: choosing where AI adds value, setting expectations, streaming and progress UX, showing sources, editing and control, feedback loops, failure and fallback states, human handoff, AI disclosure, personalisation and memory UX, cost-aware design, and measuring success.

---

### 1. How do you decide where AI belongs in a product?

<details>
<summary>Answer</summary>

Start from **user problems with clear value**: tasks that are slow, repetitive or require synthesising lots of information, where outputs can be verified and errors are tolerable or catchable. Prefer AI that **assists** users inside their workflow (drafts, suggestions, summaries) before fully autonomous features. Avoid "AI for its own sake" – a chatbot bolted onto a product rarely beats an AI capability embedded where the work happens.

</details>

### 2. How do you set the right user expectations for an AI feature?

<details>
<summary>Answer</summary>

Be clear about what it does and doesn't do, show example prompts, label it as AI, mention that it can make mistakes where relevant, and avoid marketing language that implies perfect accuracy. Onboarding with good starting examples improves both quality and trust.

</details>

### 3. What UX patterns handle AI latency?

<details>
<summary>Answer</summary>

Stream text as it's generated; show **progress for agents** (current step, tools being used); let users cancel; use optimistic UI for quick actions; precompute likely outputs (e.g. summaries generated in the background); and for long tasks, run asynchronously with notifications when done.

</details>

### 4. Why show sources and let users edit outputs?

<details>
<summary>Answer</summary>

**Sources/citations** let users verify claims and build appropriate trust; **editing** turns AI output into a draft the user controls rather than a final answer. Both reduce the harm of errors and increase adoption. Track edits as quality signals – heavily edited outputs point to prompt or retrieval problems.

</details>

### 5. How should you collect and use user feedback?

<details>
<summary>Answer</summary>

Lightweight in-context feedback (thumbs up/down with optional reasons, "regenerate", corrections) plus **implicit signals** (copy, accept, edit distance, abandonment, follow-up questions). Review negative feedback regularly, add failing cases to the eval set, and close the loop by measuring whether fixes improved the signals. Respect privacy when reviewing conversations.

</details>

### 6. How do you design failure states and fallbacks?

<details>
<summary>Answer</summary>

Plan for: the model refusing, timing out, returning invalid output, low-confidence answers, or the provider being down. Show helpful messages, offer retry or an alternative path (search results instead of an answer, a form, a human), keep the user's input, and never fail silently. For critical flows, have a non-AI fallback.

</details>

### 7. How do you design handoff from AI to a human (e.g. support)?

<details>
<summary>Answer</summary>

Hand off when the user asks, confidence is low, the topic is sensitive or high-value, or the AI fails repeatedly. Pass a **summary of the conversation and collected details** so the user doesn't repeat themselves, set expectations about wait times, and let agents see what the AI said. Measure resolution rates both ways.

</details>

### 8. How do you design memory and personalisation responsibly?

<details>
<summary>Answer</summary>

Make memory **visible and controllable**: users can see what's remembered, edit or delete it, and turn it off; don't remember sensitive information by default; explain how it's used; and avoid surprising users with information from unrelated past contexts. Keep project or workspace memories separate.

</details>

### 9. How do you measure whether an AI feature is successful?

<details>
<summary>Answer</summary>

Combine **quality** (eval scores, error and hallucination rates, edit rates), **usage and outcomes** (adoption, task completion, time saved, conversion, resolution rate), **user sentiment** (feedback, CSAT), **cost** (per task, per user) and **safety** (incidents, policy violations, refusals). Run A/B tests where possible and define success metrics before launch.

</details>

### 10. How does cost affect product design for AI features?

<details>
<summary>Answer</summary>

Cost scales with usage, so design for it: choose model size per feature, cache aggressively, limit context and output length, set per-user quotas or tiered plans for heavy features, precompute in batch where real-time isn't needed, and monitor cost per active user against revenue. Expensive features (long agent runs) may need explicit user triggers rather than running automatically.

</details>

---

[⬅ Previous: Multimodal Inputs & Model Features](17-multimodal-features.md) · [⬅ Back to index](../README.md) · [Next: Responsible AI & Governance ➡](19-responsible-ai-governance.md)
