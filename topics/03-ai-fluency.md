# AI Fluency: Working Effectively With AI

[⬅ Back to index](../README.md)

Covers: what AI fluency is and why it matters, the 4D framework (Delegation, Description, Discernment, Diligence) in depth, automation vs augmentation vs agency, project planning with AI, product/process/performance description, the description–discernment loop, creation/transparency/deployment diligence, deciding what to delegate, verifying output, data you shouldn't share, team habits, over-reliance, learning with AI, and applying the 4Ds to engineering work.

---

## The Framework

### 1. What is AI fluency?

<details>
<summary>Answer</summary>

The ability to work with AI systems **effectively, efficiently, ethically and safely** – knowing what to ask AI to do, how to communicate the task, how to judge what comes back, and how to take responsibility for the result. Anthropic's AI Fluency course (developed with Prof. Rick Dakan and Prof. Joseph Feller) organises it into four competencies, the **4Ds**: **Delegation, Description, Discernment and Diligence**. It's a skill set that transfers across tools and models, unlike knowledge of any single product.

</details>

### 2. Why does AI fluency matter, separately from knowing specific AI tools?

<details>
<summary>Answer</summary>

Tools and models change every few months; the underlying skills don't. AI fluency is about **how you think and work with AI** – choosing what to hand over, communicating clearly, judging results critically and using AI responsibly. Those habits transfer to any assistant, coding agent or API. Without them, people either under-use AI (missing real productivity gains) or over-trust it (shipping errors, leaking data). It also pairs with understanding **AI capabilities and limitations**: fluency is the human side, capabilities are the machine side.

</details>

### 3. What is the 4D framework in one sentence each?

<details>
<summary>Answer</summary>

- **Delegation** – deciding *whether, when and how* to involve AI in a task.
- **Description** – communicating clearly what you want, how it should be done, and how the AI should behave.
- **Discernment** – evaluating the AI's output, process and behaviour critically.
- **Diligence** – taking responsibility for how AI is used and for what you produce with it.

They form a cycle: delegate thoughtfully, describe well, discern carefully, and stay diligent throughout.

</details>

### 4. What are automation, augmentation and agency?

<details>
<summary>Answer</summary>

Three modes of working with AI described in the AI Fluency framework:
- **Automation** – AI performs a defined task based on your instructions (summarise this, translate that, extract these fields).
- **Augmentation** – you and AI collaborate as thinking partners on a task, iterating together (brainstorming, co-writing, debugging together).
- **Agency** – you configure AI to act more independently on your behalf, possibly interacting with others or tools (an agent handling a workflow, a customer-facing assistant).

Each mode needs different levels of description up front and different oversight: agency requires the most careful setup, guardrails and monitoring.

</details>

## Delegation

### 5. What is Delegation?

<details>
<summary>Answer</summary>

Deciding **what work should be done by you, by AI, or together** – based on a clear understanding of the goal, the task, and what the AI can and can't do well.
- **Problem awareness** – what are you actually trying to achieve, and what does "done well" look like?
- **Platform awareness** – which tool or model fits (chat assistant, coding agent, API with retrieval)? What are its limits?
- **Task delegation** – split the work: hand AI the parts where it adds speed or quality (first drafts, summarising, code scaffolding, data extraction), keep the parts that need your judgement, context, relationships or accountability.

Good delegation also means *not* using AI where it adds risk without value.

</details>

### 6. How does Delegation apply to planning a project?

<details>
<summary>Answer</summary>

Break the project into tasks and decide for each: **human only, AI-assisted, or AI-led with review**. For example, building a new app feature:
- Requirements and trade-off decisions – you (AI can help brainstorm edge cases).
- Exploring an unfamiliar library – AI-assisted (explanations, examples, verified against docs).
- Boilerplate, tests, migrations – AI-led, reviewed and tested by you.
- Security review and final sign-off – you.

Also plan **where you'll verify** each AI-produced piece and what context the AI will need. Revisit the split as you learn what works.

</details>

### 7. How do you decide whether a task is a good fit for AI?

<details>
<summary>Answer</summary>

Ask:
- **Value** – does AI save meaningful time or improve quality?
- **Verifiability** – can you (or a test, or a reviewer) check the output efficiently? Tasks that are easy to verify but slow to produce are ideal.
- **Cost of error** – what happens if it's wrong? High-stakes outputs need human review or shouldn't be delegated.
- **Context** – can you give the AI the information it needs?
- **Sensitivity** – does it involve confidential data, personal data or decisions about people that require care?

Good fits: first drafts, summaries, code with tests, data extraction with validation, research starting points. Poor fits: unverifiable facts presented as final, decisions with legal accountability, tasks where you can't judge quality.

</details>

### 8. What are signs you've delegated badly?

<details>
<summary>Answer</summary>

- You can't tell whether the output is correct (no way to verify).
- You spend longer fixing AI output than doing the task yourself.
- Errors reach users or reviewers that you should have caught.
- You've handed over decisions you're accountable for (security, hiring, legal).
- You stopped understanding your own codebase or work.

The fix is usually to narrow what's delegated, give better context, or add a verification step.

</details>

## Description

### 9. What is Description?

<details>
<summary>Answer</summary>

Communicating with AI clearly enough to get useful results. Three aspects:
- **Product description** – what you want as output: format, length, audience, style, constraints, examples of good output.
- **Process description** – how the AI should approach it: steps to follow, things to consider, what to do when unsure.
- **Performance description** – how the AI should behave in the collaboration: concise or detailed, challenge your assumptions or just execute, ask questions first or proceed.

This is prompting understood broadly – see [Prompt Engineering](04-prompt-engineering.md).

</details>

### 10. Give an example of product, process and performance description for one task.

<details>
<summary>Answer</summary>

Task: "Help me write release notes for version 3.2."
- **Product:** "Customer-facing release notes, under 200 words, grouped into New, Improved and Fixed, plain language, no internal ticket numbers."
- **Process:** "Use the merged PR titles below. Skip refactors and internal changes. If a change's user impact is unclear, list it separately as a question for me."
- **Performance:** "Be concise. Don't invent features. Ask me before guessing about anything ambiguous."

Each part prevents a different failure: wrong output shape, a wrong approach, or unhelpful collaboration.

</details>

### 11. What prompting techniques does good Description rely on?

<details>
<summary>Answer</summary>

- **Give context** – the goal, audience and why it matters.
- **Be specific** about format, length, style and constraints.
- **Show examples** of what good looks like.
- **Break complex tasks into steps**, or ask the AI to plan first.
- **Ask it to think through the problem** before answering when reasoning matters.
- **Define the role or perspective** you want (e.g. "review this as a security engineer").
- **Invite questions** – "ask me if anything is unclear before starting".

See [Prompt Engineering](04-prompt-engineering.md) for the detailed techniques.

</details>

## Discernment

### 12. What is Discernment?

<details>
<summary>Answer</summary>

Critically **evaluating what the AI produces and how it behaves**:
- **Product discernment** – is the output accurate, complete, relevant, well-reasoned, appropriate for the audience? Are facts and citations real?
- **Process discernment** – did the AI follow a sensible approach? Did it skip steps, misread the task, or make unjustified assumptions?
- **Performance discernment** – is the way it's collaborating helpful (too agreeable? overconfident? ignoring instructions)?

Discernment requires domain knowledge – the less you know about a topic, the more carefully you must verify AI output on it.

</details>

### 13. What are warning signs in AI output that should trigger closer checking?

<details>
<summary>Answer</summary>

- Very specific facts, numbers, quotes or citations you didn't provide.
- Confident claims about recent events, versions or internal systems.
- Code that uses APIs you don't recognise, or skips error handling and edge cases.
- Answers that agree a little too readily with your assumptions.
- Generic, padded text that doesn't actually address your question.
- Internal inconsistencies (numbers that don't add up, contradictory statements).
- Reasoning that jumps to a conclusion without showing why.

</details>

### 14. What is the Description–Discernment loop?

<details>
<summary>Answer</summary>

Working with AI is iterative: you **describe** the task, **discern** what's good and bad in the result, then **refine the description** based on what you learned (add constraints, examples, missing context, corrections) – and repeat. Skilled users treat the first output as a draft and the conversation as a feedback loop, rather than expecting a perfect answer from one prompt. Over time, the refinements become reusable prompts, templates, project instructions or skills.

</details>

### 15. Walk through a Description–Discernment loop on a real task.

<details>
<summary>Answer</summary>

1. **Describe:** "Write a function to parse our CSV export into `Order` objects."
2. **Discern:** it works on the happy path, but ignores quoted commas and empty totals, and invents a date format.
3. **Refine description:** add a sample file, the real date format, and "handle quoted fields and missing totals; reject malformed rows with a clear error".
4. **Discern again:** better – now check performance on a 100 MB file and ask for tests.
5. **Refine:** "stream the file instead of loading it all; add tests for each edge case".

Each loop adds missing context the AI couldn't know. Save the final description as a reusable prompt or project note.

</details>

### 16. How do you verify AI output efficiently?

<details>
<summary>Answer</summary>

- **Check claims that matter**: numbers, names, dates, quotes, citations and code behaviour – spot-check against sources.
- Ask the AI to **cite or quote sources** from provided documents, then check them.
- For code: **run tests**, read the diff, check edge cases and security.
- Ask the AI to **critique its own output** or list assumptions and uncertainties (useful, but not a substitute for checking).
- Use a second, independent method for important results (another source, a calculation, a colleague).
- Scale scrutiny with stakes – a brainstorm needs less checking than a contract or medical summary.

</details>

## Diligence

### 17. What is Diligence?

<details>
<summary>Answer</summary>

Taking **responsibility** for how AI is used and what is produced:
- **Creation diligence** – choosing tools thoughtfully, considering data privacy (what you share with the AI), and avoiding harmful uses.
- **Transparency diligence** – being honest with others about AI's role in your work where it matters (colleagues, clients, teachers, readers).
- **Deployment diligence** – verifying and standing behind outputs before using or sharing them; you remain accountable for the result, not the AI.

</details>

### 18. Give examples of creation, transparency and deployment diligence for an engineer.

<details>
<summary>Answer</summary>

- **Creation diligence** – using an approved AI tool, not pasting customer data or secrets, not using AI to work around licences or security controls.
- **Transparency diligence** – telling reviewers when a large PR was mostly AI-generated so they review it appropriately; following team or company disclosure norms; being honest in interviews about how you use AI.
- **Deployment diligence** – testing and reviewing AI-generated code before merging, checking for security issues, and owning any bugs it causes – "the AI wrote it" is never an excuse.

</details>

### 19. What should you avoid sharing with AI tools at work?

<details>
<summary>Answer</summary>

Follow your organisation's AI policy. Generally be careful with: customer personal data, credentials and secrets (API keys, passwords), confidential financials or unreleased product information, legal or HR matters, and anything covered by contracts or regulations – unless you're using an approved tool with appropriate data-protection terms (enterprise plans with no training on your data, retention controls). When in doubt, anonymise or summarise instead of pasting raw data.

</details>

### 20. When should you not use AI at all?

<details>
<summary>Answer</summary>

When the cost of an error is high **and** you can't verify the output; when the data involved isn't allowed in your AI tools; when the work requires personal accountability or judgement you can't delegate (e.g. performance reviews, sensitive personal decisions, legal commitments); when you're deliberately building a skill and need the practice; or when policy or the task context (some exams, interviews or clients) forbids it.

</details>

## Fluency in Practice

### 21. How can a team build good AI habits?

<details>
<summary>Answer</summary>

- Agree on **approved tools** and data rules.
- Share **effective prompts, templates, project instructions and skills** so knowledge compounds.
- Define where **human review is mandatory** (customer-facing text, code merges, legal content).
- Be transparent about AI use where it matters.
- Measure impact (time saved, quality, error rates), not just usage.
- Run regular show-and-tell sessions on what works and what failed.

</details>

### 22. How do you avoid over-relying on AI?

<details>
<summary>Answer</summary>

Keep doing the thinking that matters: form your own view before asking AI on important decisions, use AI to challenge your reasoning rather than replace it, stay hands-on with fundamentals (especially when learning), and notice when you accept outputs you couldn't evaluate yourself. Over-reliance shows up as declining skills, unverified errors in your work, and loss of ownership – all of which interviewers probe when they ask how you use AI tools.

</details>

### 23. How can you use AI to learn effectively instead of just getting answers?

<details>
<summary>Answer</summary>

Ask it to **explain concepts and trade-offs**, quiz you, review your attempt rather than write the solution, give hints before answers, and point you to official documentation to verify. Try the problem yourself first, then compare. Used this way, AI is a patient tutor; used only for answers, it can stop you from building the understanding you need.

</details>

### 24. How would you apply the 4Ds to adding a new feature to an app?

<details>
<summary>Answer</summary>

- **Delegation** – you decide the design and UX; AI helps explore the codebase, scaffold code and write tests; security-sensitive parts get extra review.
- **Description** – give the agent the ticket, relevant files, conventions (project instruction file), constraints and a definition of done; ask for a plan first.
- **Discernment** – review the plan and every diff, run tests, check edge cases and performance, verify unfamiliar APIs in docs.
- **Diligence** – no secrets or customer data in prompts, disclose heavy AI use to reviewers if your team expects it, and own the result in production.

This is a strong structure for interview answers about how you use AI.

</details>

---

[⬅ Previous: AI Capabilities & Limitations](02-how-llms-work.md) · [⬅ Back to index](../README.md) · [Next: Prompt Engineering ➡](04-prompt-engineering.md)
