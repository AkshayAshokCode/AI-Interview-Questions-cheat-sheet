# AI in Software Engineering Interviews

[⬅ Back to index](../README.md)

Covers: why software engineering interviews now include AI questions, the kinds of questions asked, answering "how do you use AI?", AI-allowed coding rounds, what AI knowledge a software engineer is expected to have (and what isn't expected), talking about AI features you've built, opinion questions, common mistakes, and a preparation plan.

---

### 1. Why do software engineering interviews now ask about AI?

<details>
<summary>Answer</summary>

Because AI has changed day-to-day engineering. Most teams use AI coding assistants, and many products now include AI features (chat assistants, smart search, summaries, recommendations, support bots). Interviewers want to know whether you:
- **use AI tools effectively and responsibly** to work faster without lowering quality,
- **understand AI well enough** to build features on top of LLM APIs and explain their limits,
- can reason about **risks** – hallucinations, security, privacy, cost.

This is expected of Android, iOS, backend, frontend and full-stack engineers – not just "AI engineers". You don't need machine-learning maths for it.

</details>

### 2. What kinds of AI questions do software engineers get asked?

<details>
<summary>Answer</summary>

- **Your AI usage** – "How do you use AI in your work?", "How do you make sure AI-generated code is correct?"
- **Fundamentals** – tokens, context windows, temperature, hallucinations, why outputs vary. See [How LLMs Work](02-how-llms-work.md).
- **Prompting and context** – how you'd get reliable output. See [Prompt Engineering](04-prompt-engineering.md).
- **Building AI features** – calling an LLM API from your app, streaming, tool use, RAG, structured outputs. See [Building With LLM APIs](07-building-with-llm-apis.md).
- **Quality and risk** – how you'd test an AI feature, prevent prompt injection, protect user data, control cost.
- **Design** – "Add an AI assistant to our app" as a system design or product question. See [AI System Design](20-ai-system-design.md).
- **Opinion** – "How is AI changing software engineering?"

</details>

### 3. How should you answer "How do you use AI in your day-to-day work?"

<details>
<summary>Answer</summary>

Be concrete and show judgement. A good structure (based on the AI Fluency 4Ds – see [AI Fluency](03-ai-fluency.md)):
1. **What you delegate** – e.g. "boilerplate, tests, exploring unfamiliar code, first drafts of docs, debugging with stack traces".
2. **How you describe tasks** – context, constraints, project instruction files, examples, plan-first for bigger changes.
3. **How you check results** – tests, reading every diff, verifying APIs against docs.
4. **Where you don't use it, and why** – security-sensitive code without review, confidential data, decisions you must own.
5. **An outcome** – "cut the time to add a new screen from two days to one, with test coverage going up".

Avoid both extremes: "I don't use AI" (sounds out of date) and "AI writes all my code" (sounds like you don't own the result).

</details>

### 4. What happens in an AI-allowed coding interview, and how should you behave?

<details>
<summary>Answer</summary>

Some companies now allow (or provide) AI assistants in coding rounds, and others ban them – **ask the recruiter beforehand** and never use AI where it isn't allowed. When it's allowed, interviewers assess how you **work with** the AI, not whether it can solve the problem:
- Understand and plan the problem yourself first; explain your approach.
- Give the AI clear, scoped requests rather than pasting the whole problem.
- **Read and explain every line** it produces; catch its mistakes out loud.
- Test the code (edge cases, complexity) and fix issues yourself.
- Stay in control – you should be able to continue if the tool disappeared.

</details>

### 5. What AI knowledge is a software engineer expected to have – and not expected to have?

<details>
<summary>Answer</summary>

**Expected:**
- How LLMs behave: tokens, context windows, sampling, hallucinations, knowledge cutoff, statelessness.
- Effective prompting and giving context; structured outputs.
- How to call an LLM API safely from an app: backend proxy, streaming, errors, rate limits, cost.
- Tool use / function calling, RAG and agents at a conceptual level; what MCP is.
- How to use AI coding assistants well and review their output.
- Testing and evaluating AI features; guardrails, privacy and security basics.

**Not expected** (unless the role is ML-specific): training models, backpropagation, transformer maths, statistics and probability theory, GPU optimisation.

</details>

### 6. How do you talk about an AI feature you built (or haven't built yet)?

<details>
<summary>Answer</summary>

If you've built one: the user problem, the approach (prompt, context/RAG, tools), how you tested quality (an eval set, user feedback), how you handled failures (hallucinations, refusals, timeouts), cost and latency numbers, and what you'd improve.

If you haven't: build a small one before interviews – e.g. a feature in your app that summarises or answers questions over your own data via an LLM API, with streaming, a few tool calls and a 20-case test set. Being able to describe real problems you hit is far more convincing than theory.

</details>

### 7. How should you answer "Will AI replace software engineers?" or "How is AI changing engineering?"

<details>
<summary>Answer</summary>

Give a balanced, experience-based answer: AI is automating more of the **writing** of code (boilerplate, tests, migrations), which shifts engineers' value towards **understanding problems, designing systems, specifying work clearly, reviewing and verifying, and owning quality, security and outcomes**. Engineers who use AI well become more productive; those who accept output uncritically create risk. Mention what you've seen in practice and how you're adapting (e.g. writing better specs, investing in tests, reviewing more carefully).

</details>

### 8. What are common mistakes when discussing AI in interviews?

<details>
<summary>Answer</summary>

- Over-claiming ("AI does everything for me") or dismissing it entirely.
- No mention of verification, testing or review.
- Casual attitude to confidential code and customer data in AI tools.
- Buzzwords without substance (saying "RAG" or "agents" but not explaining when or why).
- Over-engineering design answers (multi-agent systems for a simple summary feature).
- Ignoring cost, latency, privacy and failure handling when designing AI features.

</details>

### 9. How should you prepare in 1–2 weeks?

<details>
<summary>Answer</summary>

1. Read topics 02–06 (how LLMs behave, AI fluency, prompting, context, structured outputs).
2. Make a few API calls yourself: a chat, a tool call, a streamed response (topics 07–08).
3. Read topics 09–12 (MCP, RAG, agents, AI coding assistants) – and use a coding agent on a real task, noting what worked.
4. Read topics 13–15 (testing/evals, guardrails, cost) – the areas interviewers use to separate strong candidates.
5. Practise one AI feature design (topic 20) and a few scenarios (topic 21) out loud.
6. Prepare your "how I use AI" story with concrete examples and numbers.

The free Anthropic Academy courses (AI Fluency, Claude Code in Action, Building with the Claude API, Introduction to MCP) are good hands-on companions.

</details>

---

[⬅ Back to index](../README.md) · [Next: How LLMs Work (For People Who Use Them) ➡](02-how-llms-work.md)
