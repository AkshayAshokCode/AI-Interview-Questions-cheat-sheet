# AI Interview Questions – Cheat Sheet

A revision guide for interviews about **using AI well**: **299 questions with interview-ready answers**, split into 22 topics. It's about how AI is used, not how models are built: AI fluency, how LLMs behave, prompt and context engineering, structured outputs, building with LLM APIs, tool use, MCP, RAG, agents and workflows, AI coding assistants like Claude Code, evals, guardrails, efficiency (cost, latency, caching), AI product design, governance, system design, scenarios and company-wise questions. It follows the topics in the Anthropic Academy courses (AI Fluency, Building with the Claude API, Introduction to MCP, Claude Code in Action, agent skills and subagents).

Every answer is hidden in a collapsible block, so you can **test yourself first** and then click to check.

> **📈 [Open the interactive study tracker](https://akshayashokcode.github.io/AI-Interview-Questions-cheat-sheet/)**: mark questions ✓ Done or ↻ Revise, see your progress per topic, search every question, listen to answers read aloud, and continue where you left off.
>
> **No account needed.** Your progress saves automatically on your device. Want to keep it safe and in sync across your devices? Sign in (optional).

Also see: **[Android](https://github.com/AkshayAshokCode/Android-Interview-Questions-cheat-sheet)** · **[iOS](https://github.com/AkshayAshokCode/iOS-Interview-Questions-cheat-sheet)** · **[System Design](https://github.com/AkshayAshokCode/System-Design-Interview-Questions-cheat-sheet)**

---

## How to use this repo

1. **Start with AI fluency and how LLMs behave** (topics 02–03). Everything else builds on them.
2. **Read the question, answer it out loud**, then expand **Answer** to compare.
3. **Practise hands-on**: write prompts, call an API with tools, build a tiny RAG app, add an eval set. Interviewers can tell who has actually built things.
4. Drill the [scenarios](topics/21-scenario-based.md) and one [AI system design](topics/20-ai-system-design.md) out loud before the interview.

> Tip: whatever you propose (a prompt, RAG, an agent, a model), say **how you'd measure that it works** and **what you'd do when it's wrong**. That's what separates strong answers.

---

## Topics

### 🧭 Foundations

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 01 | [AI Interviews: Roles & Rounds](topics/01-ai-interviews-overview.md) | 8 | AI engineer and AI-native roles, interview rounds, scoring, project deep dives, preparation |
| 02 | [How LLMs Work (For People Who Use Them)](topics/02-how-llms-work.md) | 18 | Tokens, context window, temperature, hallucinations, knowledge cutoff, statelessness, extended thinking, strengths and limits |
| 03 | [AI Fluency: Working Effectively With AI](topics/03-ai-fluency.md) | 12 | The 4Ds (Delegation, Description, Discernment, Diligence), automation/augmentation/agency, verifying output, responsible use |

### ✍️ Prompting & Context

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 04 | [Prompt Engineering](topics/04-prompt-engineering.md) | 21 | Clear and direct, specificity, XML tags, examples, chain-of-thought, long documents, giving an "out", iteration, prompt rewrites |
| 05 | [Context Engineering & Memory](topics/05-context-engineering.md) | 11 | What fills the context window, long-context limits, history management, compaction, memory files, subagents, caching-friendly ordering |
| 06 | [Structured Outputs & Extraction](topics/06-structured-outputs.md) | 8 | JSON and schemas, validation, document extraction, LLM classifiers, citations, stop reasons |

### 🔌 Building With AI

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 07 | [Building With LLM APIs](topics/07-building-with-llm-apis.md) | 14 | Requests and messages, multi-turn, system prompts, stop reasons, streaming, errors and rate limits, key security, batch, effort |
| 08 | [Tool Use (Function Calling)](topics/08-tool-use.md) | 11 | Tool definitions, the tool loop, parallel calls, errors, server tools (web search, code execution), many tools, tool security |
| 09 | [Model Context Protocol (MCP)](topics/09-mcp.md) | 10 | Hosts, clients and servers; tools vs resources vs prompts; transports; building and testing servers; sampling; MCP vs skills; security |
| 10 | [RAG & Agentic Search](topics/10-rag-search.md) | 23 | Pipeline, embeddings, chunking, BM25 + hybrid search, reranking, contextual retrieval, permissions, evaluation, agentic search |
| 11 | [Agents & Workflows](topics/11-agents-workflows.md) | 20 | Workflow patterns, when to build an agent, ReAct, planning, memory, subagents, skills, human-in-the-loop, long-running agents |
| 12 | [AI Coding Assistants & Claude Code](topics/12-ai-coding-assistants.md) | 14 | Explore-plan-code-verify, CLAUDE.md, plan mode, context, hooks, permissions, MCP, CI, TDD with AI, reviewing AI code |

### ✅ Quality, Safety & Efficiency

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 13 | [Evals (Evaluating AI Features)](topics/13-evals.md) | 22 | Eval workflow, test datasets, code/model/human grading, rubrics, LLM-as-judge, RAG/agent/chatbot evals, CI, production monitoring |
| 14 | [Guardrails, Safety & Security](topics/14-guardrails-safety.md) | 16 | Hallucination reduction, prompt injection, jailbreaks, moderation, PII, refusals, agent guardrails, OWASP LLM risks, data exfiltration |
| 15 | [Efficiency: Cost, Latency & Scale](topics/15-efficiency-cost-latency.md) | 12 | Token economics, cost estimates, prompt caching rules, batch, routing, effort and model size, latency levers, spend monitoring |
| 16 | [Choosing Models & Approaches](topics/16-choosing-models.md) | 10 | Model tiers, choosing a model, open vs API, on-device, prompting vs RAG vs fine-tuning, vendors, safe upgrades |
| 17 | [Multimodal Inputs & Model Features](topics/17-multimodal-features.md) | 9 | Images, PDFs, extended thinking, code execution, web search, computer use, voice, image generation, Files API |

### 🏗 Applied

| # | Topic | Questions | Key areas |
|---|---|---|---|
| 18 | [Designing AI Products & UX](topics/18-ai-product-design.md) | 10 | Where AI fits, expectations, latency UX, sources and editing, feedback, failure states, handoff, memory UX, success metrics |
| 19 | [Responsible AI & Governance](topics/19-responsible-ai-governance.md) | 10 | Bias, disclosure, privacy, IP, workplace AI policy, organisational rollout, EU AI Act, environmental impact |
| 20 | [AI System Design](topics/20-ai-system-design.md) | 13 | RAG assistant, support agent, ChatGPT-like app, coding assistant, AI search, document extraction, voice, meeting summariser, LLM gateway |
| 21 | [Scenario-Based Questions](topics/21-scenario-based.md) | 18 | Hallucinating bot, bad retrieval, slow/expensive features, prompt attacks, model upgrades, agent mistakes, over-trust, sensitive data |
| 22 | [Company-Wise Questions & What's New](topics/22-company-wise-whats-new.md) | 9 | AI labs, big tech, startups, Indian companies, most-asked questions, latest trends, talking about your own AI use |

---

## 🗓 Suggested revision plans

**2-week plan (≈1–2 hours/day)**

| Day | Topics |
|---|---|
| 1 | 01 Roles & Rounds + 02 How LLMs Work |
| 2 | 03 AI Fluency |
| 3 | 04 Prompt Engineering (rewrite 3 real prompts) |
| 4 | 05 Context Engineering + 06 Structured Outputs |
| 5 | 07 Building With LLM APIs (make your first API calls) |
| 6 | 08 Tool Use + 09 MCP (build a tiny MCP server) |
| 7 | 10 RAG & Agentic Search |
| 8 | 11 Agents & Workflows |
| 9 | 12 AI Coding Assistants (use one on a real task) |
| 10 | 13 Evals (build a 20-case eval set) |
| 11 | 14 Guardrails + 15 Efficiency |
| 12 | 16 Choosing Models + 17 Multimodal + 18 Product Design |
| 13 | 19 Governance + 20 AI System Design (practise two designs out loud) |
| 14 | 21 Scenarios + 22 Company-Wise + Last-Minute Revision |

**2 days before:** how LLMs work (02), AI fluency (03), prompting (04), tool use (08), RAG (10), workflows vs agents (11), evals (13), guardrails (14), caching and cost (15), one system design (20), scenarios (21) and the list below.

---

## ⚡ Last-Minute Revision (one-liners)

<details>
<summary>Open the quick revision list</summary>

**How LLMs behave**
- LLMs predict the next token; fluent ≠ true. They're stateless: the app re-sends the history every call.
- Tokens drive cost, latency and context limits. Output tokens cost more than input tokens.
- Hallucinations come from producing plausible text without verified knowledge. Ground with sources, allow "I don't know", verify.
- Extended/adaptive thinking + effort = more reasoning for hard tasks at more tokens and latency.

**AI fluency (4Ds)**
- Delegation: what to hand to AI vs keep. Description: explain the product, the process and the behaviour you want. Discernment: judge the output, the process and the behaviour. Diligence: you're responsible for the result.
- Modes: automation, augmentation, agency. More autonomy needs more setup and oversight.

**Prompting & context**
- Be clear, direct and specific; give context and the reason behind rules; say what to do, not only what not to do.
- XML tags separate instructions from data; 3–5 diverse examples; long documents first, question last; quote-first for long docs.
- Context engineering: include what's relevant, trim tool outputs, summarise history, use memory files, use subagents for isolation.
- Structured outputs guarantee shape, not truth: validate in code. Always check the stop reason.

**Building**
- Tool use: the model requests, your code executes, results go back. Return all parallel results in one message; return errors as results.
- MCP: hosts/clients/servers; tools (model-controlled), resources (app-controlled), prompts (user-controlled); stdio or streamable HTTP.
- RAG: parse → chunk → embed + BM25 → hybrid retrieve → rerank → answer with citations. Contextual retrieval adds chunk context before indexing.
- Workflows (chaining, routing, parallelisation, orchestrator-workers, evaluator-optimizer) before agents. Agents need tools, feedback, limits and checkpoints.
- Coding agents: explore → plan → implement → verify → review → commit. Keep CLAUDE.md short and current; hooks are deterministic.

**Quality, safety & efficiency**
- Evals: dataset (real + generated) → run → grade (code first, then model, then human) → iterate; hold out a test set.
- LLM judges need rubrics, position swapping, and calibration against humans.
- Prompt injection: treat all external content as data; least privilege; approvals for risky actions; enforce rules in code.
- Prompt caching is a prefix match: stable content first; timestamps break it; check cache-read tokens.
- Cost per completed task is the metric. Levers: caching, smaller model or lower effort per route, fewer output tokens, batch API.
- Order of approaches: prompt → context/RAG/tools → workflow → agent → fine-tune (rarely).

</details>
