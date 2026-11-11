# AI Coding Assistants & Claude Code

[⬅ Back to index](../README.md)

Covers: how coding agents work, an effective workflow (explore, plan, code, verify, commit), project instruction files, plan mode, context management, subagents, skills, hooks, permissions and safety, MCP integrations, headless and CI use, test-driven development with AI, reviewing AI code, parallel sessions, common pitfalls, and team adoption.

---

### 1. How do coding agents work?

<details>
<summary>Answer</summary>

They combine an LLM with tools to **read and search the codebase, edit files, run commands and tests**, and iterate on failures – in an IDE, a terminal, or a cloud sandbox. Key ideas: good repository context (search, project instructions files), small verifiable steps, running tests/linters as feedback, and human review of diffs. Risks: plausible but wrong code, security issues, deleting or changing things unexpectedly – so use sandboxes, permissions for destructive commands, version control and code review.

</details>
### 2. What workflow gets the best results from a coding agent?

<details>
<summary>Answer</summary>

1. **Explore** – ask it to read the relevant code and explain how things work before changing anything.
2. **Plan** – have it propose a plan (and review/edit the plan) for non-trivial changes.
3. **Implement** in small steps.
4. **Verify** – run tests, linters, type checks, the app itself; ask for tests first when practical.
5. **Review the diff** yourself.
6. **Commit** with a clear message; open a PR.

Skipping explore/plan is the most common reason for confident but wrong changes in unfamiliar code.

</details>

### 3. What is CLAUDE.md, and what should go in it?

<details>
<summary>Answer</summary>

A project instruction file that Claude Code loads automatically at the start of each session (there are user-level, project-level and directory-level files). Put in it what a new teammate would need every time:
- Build, test and lint commands.
- Code style and conventions, architecture notes, important directories.
- Workflow rules ("run tests before committing", "never edit generated files").
- Gotchas specific to the repo.

Keep it **concise and current** – it's added to every session's context, so stale or bloated instructions cost tokens and mislead the agent. Treat it like code: review and update it.

</details>

### 4. What is plan mode and when should you use it?

<details>
<summary>Answer</summary>

A mode where the agent **researches and proposes a plan without editing files or running changes**. You review and refine the plan, then let it execute. Use it for multi-file changes, unfamiliar code, refactors, migrations and anything risky – it catches misunderstandings before code is written and gives you a checklist to verify against.

</details>

### 5. How do you manage context in a long coding session?

<details>
<summary>Answer</summary>

- **Clear** the conversation between unrelated tasks; **compact** (summarise) when a long task fills the window.
- Point the agent at specific files rather than letting it read everything.
- Use **subagents** for exploration so their reading doesn't fill the main context.
- Keep durable knowledge in CLAUDE.md or notes files, not in a long chat history.
- Start a fresh session when the agent starts repeating mistakes – accumulated wrong turns hurt.

</details>

### 6. What are hooks in Claude Code?

<details>
<summary>Answer</summary>

User-defined commands that run **automatically at lifecycle events** – before or after a tool is used, when a prompt is submitted, when the agent stops, at session start, and others. Uses: auto-formatting files after edits, running a linter or tests after changes, blocking edits to protected files, enforcing policies (no `rm -rf`, no commits to main), sending notifications. Unlike instructions in a prompt, hooks are **deterministic** – they always run.

</details>

### 7. How do permissions and safety work with coding agents?

<details>
<summary>Answer</summary>

- **Permission modes** – ask before edits/commands, allow-lists of safe commands, or more autonomous modes with safeguards.
- **Sandboxing** – run in a container or restricted environment, especially for autonomous runs; limit network and filesystem access.
- **Scoped credentials** – no production secrets in the agent's environment.
- **Version control** as the safety net: work on branches, review diffs, small commits.
- Beware of **prompt injection** from files, issues or web pages the agent reads.

</details>

### 8. How do MCP servers extend a coding assistant?

<details>
<summary>Answer</summary>

They connect the agent to systems beyond the repo: issue trackers (read a ticket, update status), databases (inspect schema, run read-only queries), documentation, design tools, monitoring/logs (investigate an error), browsers (test a UI). This lets the agent work from real context ("implement ticket ABC-123") and verify its work. Install only trusted servers and scope their access.

</details>

### 9. How can coding agents run in CI or automation?

<details>
<summary>Answer</summary>

Coding agents can run **headless** (non-interactive, from a script) – e.g. Claude Code's print mode or the Agent SDK, and integrations like GitHub Actions. Uses: automated code review comments on PRs, fixing lint or simple test failures, triaging issues, generating release notes, migrations across many files. Use restricted permissions, explicit tool allow-lists, budgets, and human review before merging.

</details>

### 10. How do you do test-driven development with an AI assistant?

<details>
<summary>Answer</summary>

1. Ask the agent to write tests for the desired behaviour **first** (and confirm they fail), telling it not to implement yet.
2. Review the tests – they're the specification.
3. Ask it to implement until the tests pass, without modifying the tests.
4. Review the implementation and refactor.

This gives the agent a clear, verifiable target and protects against "plausible but wrong" code. Watch for agents weakening tests or special-casing test inputs – instruct against it and review.

</details>

### 11. How should you review AI-generated code?

<details>
<summary>Answer</summary>

Review it like any teammate's code, with extra attention to: correctness on edge cases, **security** (injection, auth checks, secrets, unsafe deserialisation), invented APIs or dependencies (hallucinated packages), unnecessary complexity or duplicated logic, tests that don't really test anything, and consistency with project conventions. You're accountable for what you merge – don't merge code you don't understand.

</details>

### 12. How do you run several coding agents in parallel?

<details>
<summary>Answer</summary>

Use separate **git worktrees** or checkouts (or separate containers) so sessions don't edit the same files, give each a well-scoped task, and merge results through normal PRs. Good for independent tasks (bug fixes, tests for different modules, exploring alternative approaches). Coordination overhead and review time grow with the number of parallel agents, so keep tasks independent.

</details>

### 13. What are the pitfalls of "vibe coding"?

<details>
<summary>Answer</summary>

Accepting AI-generated code without understanding it can lead to: hidden bugs and security holes, inconsistent architecture, code nobody on the team can maintain, licence or dependency risks, and skills atrophy. It's fine for throwaway prototypes; for production code, keep humans responsible for design, review and tests.

</details>

### 14. How do you measure whether AI coding tools actually help a team?

<details>
<summary>Answer</summary>

Look beyond "lines generated": lead time for changes, PR cycle time, review time, defect and rollback rates, incident rates, developer satisfaction, and time spent on toil vs design work. Compare over time and between teams with similar work. Watch for quality regressions offsetting speed gains, and share effective practices (CLAUDE.md files, skills, prompts) across the team.

</details>

---

[⬅ Previous: Agents & Workflows](11-agents-workflows.md) · [⬅ Back to index](../README.md) · [Next: Evals (Evaluating AI Features) ➡](13-evals.md)
