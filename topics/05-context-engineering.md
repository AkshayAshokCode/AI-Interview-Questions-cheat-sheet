# Context Engineering & Memory

[⬅ Back to index](../README.md)

Covers: what goes into the context window, context engineering, long-context limits, managing conversation history, compaction and clearing old tool results, persistent memory and project instruction files, notes for long tasks, subagents for context isolation, and prompt caching's effect on context design.

---

### 1. What is context engineering?

<details>
<summary>Answer</summary>

The broader practice of deciding **everything that goes into the model's context window** for each call: system instructions, tool definitions, retrieved documents, conversation history, memory, examples and the user's input. Good context engineering means: include what's needed, exclude noise, order information sensibly (put key instructions where they won't be lost), compress long histories, and keep stable parts at the start so **prompt caching** works. For agents, managing context across many steps (summarising, pruning tool outputs, using external memory) is often the main challenge.

</details>
### 2. What fills a model's context window in a real application?

<details>
<summary>Answer</summary>

System prompt, tool definitions (often large), retrieved documents, conversation history, tool calls and their results, images/files, any memory loaded for the user, the current request, and the model's own thinking and output for the turn. In agents, **tool results** usually grow fastest (file contents, search results, logs). Knowing what's taking the space is the first step to managing quality and cost – log token usage by component.

</details>

### 3. Does a bigger context window mean you should put everything in it?

<details>
<summary>Answer</summary>

No. More context costs more, adds latency, and can **dilute attention** – models can miss or mix up details in very long inputs ("lost in the middle", sometimes called context rot). Include what's **relevant** for this step: retrieve the right chunks, summarise long histories, trim tool outputs, and drop stale information. Use long context when the task genuinely needs the whole document (e.g. reviewing a full contract), and evaluate quality at those lengths.

</details>

### 4. How do you handle text longer than a model's maximum length?

<details>
<summary>Answer</summary>

- **Truncate** (keep the most important part, e.g. the beginning).
- **Chunk** with overlap, process each chunk, then aggregate (max/mean pooling of predictions, map-reduce summarisation).
- Use a **long-context model**.
- **Retrieve** only the relevant chunks (RAG) instead of feeding everything.
- Hierarchical approaches: summarise sections, then summarise the summaries.

</details>
### 5. How do you manage conversation history in a long chat?

<details>
<summary>Answer</summary>

- Keep recent turns verbatim; **summarise older turns** into a running summary.
- Store durable facts (user preferences, decisions) separately in a memory store and re-inject when relevant.
- Drop or shorten large tool outputs once they've been used.
- Start a fresh conversation for a new task, carrying over only a short summary.
- Use server-side features where available (e.g. automatic **compaction**, which summarises older context as the window fills, or context editing that clears old tool results).

</details>

### 6. What is compaction, and what's the difference from clearing context?

<details>
<summary>Answer</summary>

- **Compaction** – when the conversation approaches a limit, earlier context is **summarised** and the summary replaces it, so the session can continue indefinitely with key information kept. Claude Code and the Claude API (server-side compaction) both offer it.
- **Clearing / context editing** – removing specific old content (e.g. old tool results) entirely, because it's no longer needed.
- **Starting fresh** (e.g. `/clear` in Claude Code) – best when switching to an unrelated task.

Compaction keeps continuity but loses detail; for long agent tasks, also write important state to files or notes that can be re-read.

</details>

### 7. How do persistent memory and project instruction files work?

<details>
<summary>Answer</summary>

Because models are stateless, "memory" is information an application **stores and re-injects**:
- **Project instruction files** – e.g. `CLAUDE.md` in Claude Code: conventions, commands, architecture notes loaded at the start of every session. Keep them short, specific and current.
- **Memory tools / stores** – the model (or app) writes facts to a store (files, database) and reads them back in later sessions.
- **User memory in assistants** – preferences and past context surfaced in new chats, ideally visible and editable by the user.

Design questions: what's worth remembering, how to avoid storing wrong or sensitive information, and how users can inspect and delete it.

</details>

### 8. How do agents keep track of long tasks across many steps or sessions?

<details>
<summary>Answer</summary>

- Keep a **plan or progress file** (to-do list, notes) the agent updates and re-reads.
- Use **git commits** and test results as durable checkpoints.
- Store structured state (JSON) for things like test status, and free-form notes for reasoning and next steps.
- At the start of a new context window, re-read the progress notes and the code rather than relying on a compressed summary.
- Break huge tasks into milestones, each verified before moving on.

</details>

### 9. How do subagents help with context management?

<details>
<summary>Answer</summary>

A **subagent** runs a sub-task in its **own fresh context window** (e.g. "search the codebase for how auth works" or "review this diff") and returns only a concise result to the main agent. The main context isn't flooded with everything the subagent read, so it stays focused and cheaper. Use subagents for exploration, research and parallelisable work; avoid them for tightly coupled steps where the main agent needs all the details.

</details>

### 10. What is prompt caching and how do you design for it?

<details>
<summary>Answer</summary>

Providers can cache the processed state (KV cache) of a **repeated prompt prefix**, making later requests with the same prefix cheaper and faster. Design for it by putting **stable content first** (system prompt, tool definitions, long documents, few-shot examples) and variable content (the user's question) last, and avoid changing the prefix unnecessarily (e.g. timestamps at the top). Big savings for chatbots with long system prompts, document Q&A and agents.

</details>
### 11. How should you order content in a prompt for both quality and caching?

<details>
<summary>Answer</summary>

Put **stable content first** – tool definitions, system prompt, long reference documents, few-shot examples – and **variable content last** (the latest user message, per-request data). This lets the provider cache the stable prefix, and long reference material at the top with the question at the end also tends to improve answers. Avoid putting changing values (timestamps, request IDs) early in the prompt; they break the cache for everything after them.

</details>

---

[⬅ Previous: Prompt Engineering](04-prompt-engineering.md) · [⬅ Back to index](../README.md) · [Next: Structured Outputs & Extraction ➡](06-structured-outputs.md)
