# Company-Wise Questions & What's New

[⬅ Back to index](../README.md)

Covers: how AI labs, big tech, AI startups and Indian companies interview for AI-application roles; the most-asked questions; last-week preparation; and current trends (agents, MCP and skills, reasoning and effort controls, AI coding tools, long context and caching, on-device models, evaluation and governance).

---

### 1. What do AI labs (Anthropic, OpenAI, Google DeepMind) ask for applied AI and AI engineering roles?

<details>
<summary>Answer</summary>

**Commonly reported** – practical coding (realistic engineering tasks, sometimes in multiple timed stages), AI application design (how you'd build and evaluate an assistant, agent or integration), deep understanding of their own platform's capabilities (tools, context management, evals, safety features), customer-facing communication for solutions roles, and strong interest in **safety and responsible deployment**. Expect to discuss trade-offs honestly and how you'd measure success.

**Tip** – build something real with their API (an agent, an MCP server, an eval suite) and be ready to discuss what failed and how you fixed it.

</details>

### 2. What do big tech companies ask for AI engineering roles?

<details>
<summary>Answer</summary>

- **Google, Meta, Microsoft, Amazon** – DSA coding plus AI application design: RAG over enterprise data, AI assistants inside their products (Copilot-style features, shopping assistants, support bots), evaluation at scale, latency and cost, responsible AI reviews. Amazon adds Leadership Principles in every round.
- **Apple** – privacy-preserving and on-device AI, product quality and UX.
- Increasingly, interviews ask **how you use AI coding tools** in your own work, and some allow them during the interview – ask beforehand.

</details>

### 3. What do AI startups and AI-native companies ask?

<details>
<summary>Answer</summary>

Usually a **build exercise** (a small RAG app, agent or integration with evals in a few hours), a product-oriented design round, debugging scenarios (hallucinations, slow responses, cost blow-ups), and general engineering. They look for people who ship quickly **and** measure quality. A public project with an eval report stands out.

</details>

### 4. What do Indian companies ask for AI-application roles?

<details>
<summary>Answer</summary>

| Company type | Commonly reported focus |
|---|---|
| **E-commerce (Flipkart, Meesho, Myntra)** | Shopping assistants, search with LLM query understanding (multilingual, transliterated queries), catalogue enrichment, review summarisation |
| **Food/quick commerce (Swiggy, Zomato, Zepto)** | Support automation with tools (order status, refunds), menu and catalogue generation, conversational ordering |
| **Fintech (PhonePe, Paytm, Razorpay, CRED)** | Support agents with strict guardrails, document extraction (KYC), compliance, fraud-related summarisation |
| **IT services and consultancies** | Building RAG and agent solutions for clients, evaluation, governance, cost estimation, client communication |
| **Indian AI companies (Sarvam AI, Krutrim and others)** | Indic-language applications, voice assistants, evaluation across languages |

Common rounds: coding, an AI solution design case study, a build exercise, and a project deep dive.

</details>

### 5. Which questions come up most in AI-application interviews?

<details>
<summary>Answer</summary>

1. Tokens, context windows, temperature, and why outputs vary → [02](02-how-llms-work.md)
2. Why hallucinations happen and how to reduce them → [02](02-how-llms-work.md), [14](14-guardrails-safety.md)
3. How to write a good prompt; XML tags; examples; chain-of-thought → [04](04-prompt-engineering.md)
4. Managing context and memory → [05](05-context-engineering.md)
5. Getting reliable structured output → [06](06-structured-outputs.md)
6. How tool use works; designing tools → [08](08-tool-use.md)
7. What MCP is; tools vs resources vs prompts → [09](09-mcp.md)
8. Design a RAG system; chunking; hybrid search; reranking; contextual retrieval → [10](10-rag-search.md)
9. Workflows vs agents; workflow patterns; when to build an agent → [11](11-agents-workflows.md)
10. How you use AI coding tools effectively and safely → [12](12-ai-coding-assistants.md)
11. How to evaluate an LLM feature; LLM-as-judge → [13](13-evals.md)
12. Prompt injection and guardrails → [14](14-guardrails-safety.md)
13. Reducing cost and latency; prompt caching → [15](15-efficiency-cost-latency.md)
14. Prompting vs RAG vs fine-tuning; choosing a model → [16](16-choosing-models.md)
15. Design an AI feature end to end → [20](20-ai-system-design.md)

</details>

### 6. How should you prepare in the last week before an AI interview?

<details>
<summary>Answer</summary>

- **Days 1–2** – the most-asked list above and the README one-liners.
- **Day 3** – build or polish a small project: tool use + RAG + an eval set; know its numbers.
- **Day 4** – an AI system design mock out loud.
- **Day 5** – scenarios (topic 21) and guardrails; read the company's AI product announcements.
- **Day 6** – project deep dive and behavioral stories.
- **Day 7** – light review and rest.

</details>

### 7. Why are agents, MCP and skills such big trends?

<details>
<summary>Answer</summary>

Models became reliable enough at multi-step tool use to complete real tasks – coding, research, operations. **MCP** standardised connecting AI applications to tools and data, so integrations are reusable across apps. **Skills** package know-how (instructions, scripts, templates) that agents load on demand. Together they shift work from "chat with an AI" to "delegate a task to an AI that uses your tools". Interview angles: when agents are justified, reliability and evaluation, security and permissions, and cost.

</details>

### 8. What other recent developments should you know?

<details>
<summary>Answer</summary>

- **Adaptive thinking and effort controls** replacing fixed thinking budgets.
- **Long context (up to around a million tokens) with prompt caching**, changing when RAG is needed.
- **Context management features** – compaction, context editing, memory tools.
- **AI coding agents** working autonomously for long periods, in terminals, IDEs and CI.
- **Hosted/managed agents** with sandboxes, scheduling and persisted configurations.
- **On-device models** for private, offline features.
- **Evaluation as a core discipline**, and **governance** (EU AI Act obligations, enterprise AI policies).

Because this field moves fast, check the latest model and platform announcements before interviews.

</details>

### 9. How should you talk about using AI tools in your own work?

<details>
<summary>Answer</summary>

Be concrete: which tools you use, for what tasks, how you give them context (project instruction files, skills, examples), how you verify results (tests, review, sources), where you don't use them, and a measurable outcome. Interviewers want to see that AI makes you faster **and** that you remain accountable for quality – the 4D framework (Delegation, Description, Discernment, Diligence) is a good structure for this answer. See [AI Fluency](03-ai-fluency.md).

</details>

---

[⬅ Previous: Scenario-Based Questions](21-scenario-based.md) · [⬅ Back to index](../README.md)
