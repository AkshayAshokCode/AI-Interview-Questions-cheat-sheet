# AI Coding Assistants & Claude Code

[⬅ Back to index](../README.md)

Covers: types of AI coding tools, writing good coding requests, debugging, writing tests, understanding unfamiliar code, AI code review, what code is safe to share, refactors and migrations, keeping your skills sharp, how coding agents work, an effective workflow (explore, plan, code, verify, commit), project instruction files (CLAUDE.md), plan mode, context management, hooks, permissions and safety, MCP integrations, CI use, test-driven development with AI, reviewing AI code, parallel sessions, vibe-coding pitfalls, and measuring impact.

---

### 1. What kinds of AI coding tools exist, and when do you use each?

<details>
<summary>Answer</summary>

- **Inline completion** (autocomplete in the editor) – finishing the line or function you're writing; fast, low effort, you stay in control.
- **Chat in the IDE or a chat app** – explaining code, discussing approaches, writing a function or query, debugging with pasted errors.
- **Agentic coding tools** (e.g. Claude Code in the terminal or IDE) – multi-file changes: read the codebase, edit files, run tests and commands, iterate until done.
- **AI code review bots** – automated comments on pull requests.
- **App/prototype builders** – generate whole small apps from a description; good for prototypes, rarely for production.

Choose by task size: completion for lines, chat for questions and small pieces, agents for multi-step changes you can verify with tests.

</details>

### 2. How do you write a good request for a coding task?

<details>
<summary>Answer</summary>

- State the **goal and context**: what the feature does, where it lives, relevant files or functions.
- Give **constraints**: language version, libraries to use or avoid, patterns to follow ("use the existing `Repository` pattern", "no new dependencies").
- Define **done**: tests that must pass, behaviour for edge cases, performance or security requirements.
- Point to **examples** in the codebase to imitate.
- Ask for a **plan first** for anything non-trivial, and for small, reviewable steps.

Example: "Add pagination to `OrdersRepository.getOrders()` using the cursor pattern in `ProductsRepository`. Keep the public API backward compatible, add unit tests for empty pages and the last page, and run `./gradlew test` before finishing."

</details>

### 3. How do you use AI to debug a problem?

<details>
<summary>Answer</summary>

Give it the **evidence**: the exact error and stack trace, relevant code, logs, what you expected vs what happened, what you've already tried, and environment details (versions, device, OS). Ask it to list **likely causes ranked by probability** and how to confirm each, rather than jumping to a fix. With an agentic tool, let it reproduce the bug (write a failing test), then fix and re-run. Verify the root cause yourself – AI can "fix" symptoms (e.g. adding a null check) without addressing why the value was null.

</details>

### 4. How do you use AI to write tests?

<details>
<summary>Answer</summary>

- Ask for tests covering **specific behaviours and edge cases** you list (empty input, errors, boundaries, concurrency), not just "write tests".
- Point to existing tests so it follows your framework and style (JUnit/Kotest, XCTest/Swift Testing, Jest).
- Review that tests **assert meaningful behaviour** – AI-written tests sometimes only check that code runs, or mirror the implementation's bugs.
- Make sure new tests fail when the code is broken (try a quick mutation).
- For legacy code, ask for characterisation tests before refactoring.

</details>

### 5. How do you use AI to understand an unfamiliar codebase?

<details>
<summary>Answer</summary>

Ask an agentic tool to explore and explain: the architecture and main modules, how a request or screen flows end to end, where a feature is implemented, conventions used, and how to build and test. Ask it to cite files and functions so you can read them yourself. Good onboarding prompts: "Trace what happens when the user taps Checkout", "Which classes would I change to add a payment method?", "What are the riskiest parts of this module?". Capture useful findings in project docs or the project instruction file.

</details>

### 6. How can AI help with code review, and what are its limits?

<details>
<summary>Answer</summary>

AI reviewers are good at catching **common bugs, missing null/error handling, inconsistent naming, security smells, missing tests, and unclear code**, and at summarising large PRs. They're weaker at judging **product intent, architecture fit, performance in your real environment, and team context**, and they produce false positives. Use AI review as a first pass that saves reviewers' time; humans still approve changes. When reviewing your own code, ask the AI to review the diff before you open the PR.

</details>

### 7. Is it safe to paste company code into AI tools?

<details>
<summary>Answer</summary>

Only in **approved tools** under your company's policy. Check: whether the provider trains on your data (business/enterprise plans typically don't), data retention, where data is processed, and access controls. Never paste secrets (API keys, passwords, customer data) – and check that tools like coding agents don't read `.env` files or credentials. Be careful with code under restrictive licences or third-party confidentiality agreements. If unsure, ask your security team – mentioning this in interviews shows maturity.

</details>

### 8. How do you use AI for large refactors or migrations?

<details>
<summary>Answer</summary>

1. Agree on the **target pattern** with one hand-reviewed example (e.g. one screen migrated from XML Views to Compose, or callbacks to async/await).
2. Write the rules into the project instructions or a prompt/skill.
3. Migrate in **small batches** (per module or file), each compiling and passing tests.
4. Use automated checks (compiler, linters, tests, screenshot tests) to verify.
5. Review diffs, especially behaviour changes hidden in "mechanical" edits.

Agents can run these batches in parallel (separate branches/worktrees), but keep each change reviewable.

</details>

### 9. How do you keep your own skills sharp while using AI daily?

<details>
<summary>Answer</summary>

Understand every change you merge; write tricky or core logic yourself sometimes; ask AI to **explain** rather than just produce; use it to learn new areas (then verify with docs); practise problem-solving without AI (interview prep, katas); and review AI output critically – the review itself builds skill. Interviewers often probe this to check you're still the engineer, not just the operator.

</details>

### 10. How do coding agents work?

<details>
<summary>Answer</summary>

They combine an LLM with tools to **read and search the codebase, edit files, run commands and tests**, and iterate on failures – in an IDE, a terminal, or a cloud sandbox. Key ideas: good repository context (search, project instructions files), small verifiable steps, running tests/linters as feedback, and human review of diffs. Risks: plausible but wrong code, security issues, deleting or changing things unexpectedly – so use sandboxes, permissions for destructive commands, version control and code review.

</details>
### 11. What workflow gets the best results from a coding agent?

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

### 12. What is CLAUDE.md, and what should go in it?

<details>
<summary>Answer</summary>

A project instruction file that Claude Code loads automatically at the start of each session (there are user-level, project-level and directory-level files). Put in it what a new teammate would need every time:
- Build, test and lint commands.
- Code style and conventions, architecture notes, important directories.
- Workflow rules ("run tests before committing", "never edit generated files").
- Gotchas specific to the repo.

Keep it **concise and current** – it's added to every session's context, so stale or bloated instructions cost tokens and mislead the agent. Treat it like code: review and update it.

</details>

### 13. What is plan mode and when should you use it?

<details>
<summary>Answer</summary>

A mode where the agent **researches and proposes a plan without editing files or running changes**. You review and refine the plan, then let it execute. Use it for multi-file changes, unfamiliar code, refactors, migrations and anything risky – it catches misunderstandings before code is written and gives you a checklist to verify against.

</details>

### 14. How do you manage context in a long coding session?

<details>
<summary>Answer</summary>

- **Clear** the conversation between unrelated tasks; **compact** (summarise) when a long task fills the window.
- Point the agent at specific files rather than letting it read everything.
- Use **subagents** for exploration so their reading doesn't fill the main context.
- Keep durable knowledge in CLAUDE.md or notes files, not in a long chat history.
- Start a fresh session when the agent starts repeating mistakes – accumulated wrong turns hurt.

</details>

### 15. What are hooks in Claude Code?

<details>
<summary>Answer</summary>

User-defined commands that run **automatically at lifecycle events** – before or after a tool is used, when a prompt is submitted, when the agent stops, at session start, and others. Uses: auto-formatting files after edits, running a linter or tests after changes, blocking edits to protected files, enforcing policies (no `rm -rf`, no commits to main), sending notifications. Unlike instructions in a prompt, hooks are **deterministic** – they always run.

</details>

### 16. How do permissions and safety work with coding agents?

<details>
<summary>Answer</summary>

- **Permission modes** – ask before edits/commands, allow-lists of safe commands, or more autonomous modes with safeguards.
- **Sandboxing** – run in a container or restricted environment, especially for autonomous runs; limit network and filesystem access.
- **Scoped credentials** – no production secrets in the agent's environment.
- **Version control** as the safety net: work on branches, review diffs, small commits.
- Beware of **prompt injection** from files, issues or web pages the agent reads.

</details>

### 17. How do MCP servers extend a coding assistant?

<details>
<summary>Answer</summary>

They connect the agent to systems beyond the repo: issue trackers (read a ticket, update status), databases (inspect schema, run read-only queries), documentation, design tools, monitoring/logs (investigate an error), browsers (test a UI). This lets the agent work from real context ("implement ticket ABC-123") and verify its work. Install only trusted servers and scope their access.

</details>

### 18. How can coding agents run in CI or automation?

<details>
<summary>Answer</summary>

Coding agents can run **headless** (non-interactive, from a script) – e.g. Claude Code's print mode or the Agent SDK, and integrations like GitHub Actions. Uses: automated code review comments on PRs, fixing lint or simple test failures, triaging issues, generating release notes, migrations across many files. Use restricted permissions, explicit tool allow-lists, budgets, and human review before merging.

</details>

### 19. How do you do test-driven development with an AI assistant?

<details>
<summary>Answer</summary>

1. Ask the agent to write tests for the desired behaviour **first** (and confirm they fail), telling it not to implement yet.
2. Review the tests – they're the specification.
3. Ask it to implement until the tests pass, without modifying the tests.
4. Review the implementation and refactor.

This gives the agent a clear, verifiable target and protects against "plausible but wrong" code. Watch for agents weakening tests or special-casing test inputs – instruct against it and review.

</details>

### 20. How should you review AI-generated code?

<details>
<summary>Answer</summary>

Review it like any teammate's code, with extra attention to: correctness on edge cases, **security** (injection, auth checks, secrets, unsafe deserialisation), invented APIs or dependencies (hallucinated packages), unnecessary complexity or duplicated logic, tests that don't really test anything, and consistency with project conventions. You're accountable for what you merge – don't merge code you don't understand.

</details>

### 21. How do you run several coding agents in parallel?

<details>
<summary>Answer</summary>

Use separate **git worktrees** or checkouts (or separate containers) so sessions don't edit the same files, give each a well-scoped task, and merge results through normal PRs. Good for independent tasks (bug fixes, tests for different modules, exploring alternative approaches). Coordination overhead and review time grow with the number of parallel agents, so keep tasks independent.

</details>

### 22. What are the pitfalls of "vibe coding"?

<details>
<summary>Answer</summary>

Accepting AI-generated code without understanding it can lead to: hidden bugs and security holes, inconsistent architecture, code nobody on the team can maintain, licence or dependency risks, and skills atrophy. It's fine for throwaway prototypes; for production code, keep humans responsible for design, review and tests.

</details>

### 23. How do you measure whether AI coding tools actually help a team?

<details>
<summary>Answer</summary>

Look beyond "lines generated": lead time for changes, PR cycle time, review time, defect and rollback rates, incident rates, developer satisfaction, and time spent on toil vs design work. Compare over time and between teams with similar work. Watch for quality regressions offsetting speed gains, and share effective practices (CLAUDE.md files, skills, prompts) across the team.

</details>

---

[⬅ Previous: Agents & Workflows](11-agents-workflows.md) · [⬅ Back to index](../README.md) · [Next: Evals (Evaluating AI Features) ➡](13-evals.md)
