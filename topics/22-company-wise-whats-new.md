# Company-Wise Questions & What's New

[⬅ Back to index](../README.md)

Covers: how big tech, product companies, Indian companies and startups ask software engineers about AI; AI-allowed coding rounds; the most-asked AI questions for engineers; the trends worth knowing (coding agents, MCP and skills, long context and caching, on-device AI, AI features in apps); and talking about your own AI use.

> These come from commonly reported interview experiences and change often. Use them to prepare for the **style** of questions, not as a guaranteed list.

---

### 1. How do big tech companies ask software engineers about AI?

<details>
<summary>Answer</summary>

- **Behavioral / hiring-manager rounds** – "How do you use AI tools in your work?", "Tell me about a time AI-generated code caused a problem", "How do you ensure quality when using AI?"
- **System design** – AI features inside familiar products: a smart reply or summarisation feature, an in-app assistant, AI-powered search, a support bot – with emphasis on latency, cost, privacy and failure handling.
- **Coding** – mostly classic coding rounds; some companies have started allowing or providing AI assistants in certain rounds, where they assess how you work with the AI. Ask the recruiter which format you'll get.
- **Platform roles** (Android/iOS) – questions about on-device AI vs cloud, streaming UIs, and privacy (e.g. Gemini Nano on Android, Apple's Foundation Models framework on iOS – see the Android and iOS repos).

</details>

### 2. How do Indian product companies ask engineers about AI?

<details>
<summary>Answer</summary>

| Company type | Typical AI angle for engineers |
|---|---|
| **E-commerce (Flipkart, Meesho, Myntra)** | AI search and shopping assistants, review summaries, multilingual/transliterated queries, catalogue tools |
| **Food & quick commerce (Swiggy, Zomato, Zepto)** | Support bots that check orders and start refunds via tools, AI-generated menu/catalogue content |
| **Fintech (PhonePe, Paytm, Razorpay, CRED)** | Support assistants with strict guardrails, document (KYC) extraction, privacy and compliance |
| **SaaS & product startups** | Building AI features end to end, AI usage in the dev workflow, cost control |

Expect "how would you add an AI feature to our app?" design questions and "how do you use AI coding tools?" discussions, alongside the usual rounds.

</details>

### 3. What do startups and AI-native companies ask engineers?

<details>
<summary>Answer</summary>

Often a **practical build** – add an AI feature to a small app or build a mini tool with an LLM API in a few hours – followed by discussion of trade-offs: prompt design, structured outputs, error handling, cost, how you tested it. They also expect fluent use of AI coding tools, and care that you **ship quickly and verify carefully**.

</details>

### 4. Which AI questions come up most for software engineers?

<details>
<summary>Answer</summary>

1. How do you use AI in your work, and how do you verify its output? → [01](01-ai-interviews-overview.md), [03](03-ai-fluency.md)
2. What are tokens, context windows and temperature? Why do outputs vary? → [02](02-how-llms-work.md)
3. Why do LLMs hallucinate, and how do you reduce it? → [02](02-how-llms-work.md), [14](14-guardrails-safety.md)
4. How do you write a good prompt? → [04](04-prompt-engineering.md)
5. How do you get reliable JSON out of a model? → [06](06-structured-outputs.md)
6. How should an app call an LLM safely (keys, streaming, limits)? → [07](07-building-with-llm-apis.md)
7. What is tool use / function calling? → [08](08-tool-use.md)
8. What is MCP? → [09](09-mcp.md)
9. What is RAG, and when would you use it? → [10](10-rag-search.md)
10. What's the difference between a workflow and an agent? → [11](11-agents-workflows.md)
11. How do you use AI coding assistants effectively and safely? → [12](12-ai-coding-assistants.md)
12. How do you test or evaluate an AI feature? → [13](13-evals.md)
13. What is prompt injection, and how do you defend against it? → [14](14-guardrails-safety.md)
14. How do you control the cost and latency of an AI feature? → [15](15-efficiency-cost-latency.md)
15. Design an AI feature for an app. → [20](20-ai-system-design.md)

</details>

### 5. How should you talk about using AI tools in your own work?

<details>
<summary>Answer</summary>

Be concrete: which tools you use, for which tasks, how you give them context (project instruction files, examples, plans), how you verify results (tests, review, docs), where you deliberately don't use them, and a measurable outcome. Show that AI makes you faster **and** that you remain accountable for quality – the 4D framework (Delegation, Description, Discernment, Diligence) is a good structure. See [AI Fluency](03-ai-fluency.md).

</details>

### 6. What are the biggest AI trends software engineers should know about?

<details>
<summary>Answer</summary>

- **Coding agents** that read codebases, edit files, run tests and work for long periods in terminals, IDEs and CI.
- **MCP** – a standard way to connect AI tools to systems (issue trackers, databases, docs), and **skills** that package team know-how for agents.
- **Long context and prompt caching** – models handle very large inputs, and caching makes repeated context cheap.
- **Adaptive thinking and effort controls** – trading speed and cost against depth per request.
- **AI features inside every app** – assistants, summarisation, smart search, and agents that take actions through tools.
- **On-device AI** – small models on phones and laptops for private, offline features.
- **Evaluation and governance** – teams treat evals like tests, and companies adopt AI policies (and regulation such as the EU AI Act).

Because this moves fast, check recent announcements from the major AI providers and your target company before interviews.

</details>

### 7. How should you prepare for AI questions at a specific company?

<details>
<summary>Answer</summary>

Use the company's product and look for its AI features (or obvious places AI could help); read its engineering blog posts about AI; find out which AI tools its engineers use (job descriptions often say); and prepare one design answer for an AI feature in its product plus your personal "how I use AI" story. Ask the recruiter whether AI tools are allowed in the coding rounds.

</details>

### 8. What should you do in the last week before the interview?

<details>
<summary>Answer</summary>

- **Days 1–2** – the most-asked list above and the README one-liners.
- **Day 3** – make a few API calls yourself (chat, tool call, streaming) or build a tiny AI feature.
- **Day 4** – use a coding agent on a real task and note what worked and what didn't.
- **Day 5** – one AI feature design out loud, plus scenarios (topic 21).
- **Day 6** – your "how I use AI" story and behavioral examples.
- **Day 7** – light review and rest.

</details>

---

[⬅ Previous: Scenario-Based Questions](21-scenario-based.md) · [⬅ Back to index](../README.md)
