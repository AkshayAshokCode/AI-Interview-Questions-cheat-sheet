# Prompt Engineering

[⬅ Back to index](../README.md)

Covers: being clear and direct, being specific, XML structure, examples, chain-of-thought, roles and system prompts, long documents, giving the model an "out", positive instructions, temperature, prompt chaining, iteration and versioning, prompt improvers, and when prompting isn't enough.

---

### 1. What makes a good prompt?

<details>
<summary>Answer</summary>

- **Clear task and goal** – what to do and why (context helps the model make good judgement calls).
- **Relevant context** – the documents, data or background needed; nothing irrelevant.
- **Constraints** – length, tone, audience, what to avoid.
- **Output format** – exact structure (JSON schema, headings, bullet list).
- **Examples** when the format or style is subtle.
- **Separation** of instructions from data (e.g. XML-style tags or clear delimiters) so the model doesn't confuse them.
- Permission to say "I don't know" or ask for clarification.

Then **iterate against an evaluation set**: prompts are code and should be tested like code.

</details>
### 2. What does "be clear and direct" mean in practice?

<details>
<summary>Answer</summary>

Treat the model like a brilliant new colleague who has **no context** about your situation:
- Say what the task is **for** and who the audience is.
- Give the **steps** as a numbered list when order matters.
- State exactly what output you want (format, length, sections).
- Don't hint – ask directly ("Write three subject lines under 50 characters" rather than "maybe some subject line ideas?").

A useful test: if a colleague would need to ask you clarifying questions after reading your prompt, the model will guess instead.

</details>

### 3. How do you make a prompt more specific?

<details>
<summary>Answer</summary>

Add the details that change the output:
- **Constraints** – length, reading level, tone, what to include and exclude.
- **Quality criteria** – what a great answer contains ("each recommendation must cite a line from the report").
- **Context** – relevant background, data, the user's goal.
- **Edge cases** – what to do with missing data, ambiguity or out-of-scope requests.

Compare: "Summarise this report" vs "Summarise this incident report for the VP of Engineering in 5 bullets: impact, root cause, customer effect, fixes done, open risks. Use plain language and include numbers from the report."

</details>

### 4. Why use XML tags in prompts?

<details>
<summary>Answer</summary>

XML-style tags (`<document>`, `<instructions>`, `<example>`, `<customer_email>`) clearly **separate parts of the prompt** so the model doesn't confuse your instructions with the data it's processing. Benefits:
- Clarity and fewer mix-ups when prompts contain documents, examples and instructions.
- Easy to refer to parts ("Using the contract in `<contract>` tags…").
- Easy to ask for structured output ("put your answer in `<answer>` tags") and parse it.
- Safer handling of untrusted content (it's clearly marked as data).

There are no special tag names – choose descriptive ones and use them consistently.

</details>

### 5. Zero-shot vs few-shot prompting?

<details>
<summary>Answer</summary>

- **Zero-shot** – only instructions. Works well with strong instruction-tuned models for common tasks.
- **Few-shot** – include a few input → output examples. Helps with specific formats, labels, style and edge cases.

Few-shot tips: use diverse, representative examples (including tricky ones), keep formats identical, watch for the model over-copying example content, and remember examples cost tokens on every request (prompt caching helps). If you need dozens of examples to get it right, consider fine-tuning.

</details>
### 6. What makes good examples in a prompt?

<details>
<summary>Answer</summary>

- **Relevant** – mirror real inputs, including messy ones.
- **Diverse** – cover different cases and edge cases so the model doesn't copy one pattern.
- **Clearly marked** – wrap in `<example>` tags, consistent format.
- **Showing the reasoning or format you want**, not just the answer.
- **Few but sufficient** – usually 3–5; add more for hard formats.

Watch for over-fitting: if every example has three bullets, outputs will too. If examples drive too much behaviour, describe the principle instead.

</details>

### 7. What is chain-of-thought (CoT) prompting?

<details>
<summary>Answer</summary>

Asking the model to **reason step by step** before giving the final answer ("think through this step by step, then give the answer"), or showing worked examples with reasoning. Writing out intermediate steps improves accuracy on maths, logic and multi-step problems because each step conditions the next.

Notes: it adds tokens and latency; for production, put reasoning in a separate field or tag and parse only the final answer; **reasoning models** do this internally, so explicit CoT prompts matter less for them (but clear problem statements still help).

</details>
### 8. What does "role prompting" do, and is it useful?

<details>
<summary>Answer</summary>

Telling the model to act as a role ("You are an experienced tax accountant…") sets tone, vocabulary and the level of detail. It helps style and focus, but it doesn't add knowledge the model lacks. Specific context and clear task descriptions usually matter more than the role line.

</details>
### 9. What is a system prompt?

<details>
<summary>Answer</summary>

Instructions placed before the conversation (in a dedicated "system" role) that set the model's role, behaviour, constraints, tone, output format and tool-use rules for the whole session. It's the main lever for product behaviour. It is not a security boundary: users can try to override it (prompt injection/jailbreaks), so enforce critical rules in code and with guardrails, and don't put secrets in it.

</details>
### 10. How should you place long documents in a prompt?

<details>
<summary>Answer</summary>

- Put **long documents near the top** of the prompt and your **question/instructions at the end** – this generally improves quality on long inputs.
- Wrap each document in tags with metadata (`<document><source>…</source><content>…</content></document>`).
- For long-document Q&A, ask the model to first **extract relevant quotes** (in `<quotes>` tags) and then answer based on them – it focuses attention and makes answers verifiable.
- Keep instructions short and explicit about where to look.

</details>

### 11. What does "giving the model an out" mean?

<details>
<summary>Answer</summary>

Explicitly allowing the model to say it doesn't know or can't do something: "If the answer isn't in the documents, say 'I couldn't find this in the provided documents'." Without it, the model tends to produce *some* answer, which is how many hallucinations happen. Also useful: "If the request is ambiguous, ask one clarifying question first."

</details>

### 12. Positive vs negative instructions – which work better?

<details>
<summary>Answer</summary>

Telling the model **what to do** usually works better than listing what not to do. "Write in flowing prose paragraphs" beats "Don't use bullet points"; "Reply in under 100 words" beats "Don't be verbose". Negative instructions can even prime the unwanted behaviour. Explain the **reason** behind important rules ("Avoid jargon – readers are new customers") so the model can generalise to cases you didn't list.

</details>

### 13. When should you adjust temperature or other sampling settings?

<details>
<summary>Answer</summary>

Lower randomness for tasks with one right answer (extraction, classification, code); allow more for creative writing and brainstorming. Note that some newer models don't accept sampling parameters at all and are tuned for good defaults – there, you steer with instructions, examples, structured outputs and **effort** instead. Either way, temperature doesn't fix a vague prompt: improve the prompt first, then evaluate settings on your eval set.

</details>

### 14. What is prompt chaining / decomposition, and when do you use it?

<details>
<summary>Answer</summary>

Breaking a complex task into a **sequence of simpler LLM calls**, each with a focused prompt, passing outputs forward (e.g. extract facts → draft answer → check against sources → format). Benefits: each step is easier to get right, test and debug; you can use different models per step (cheap model for extraction, strong model for reasoning); and you can add validation between steps. Costs: more latency and calls. It's the step before full **agents**, where the model decides the steps itself.

</details>
### 15. What is self-consistency?

<details>
<summary>Answer</summary>

Sample several reasoning paths for the same question (temperature > 0) and take the **majority answer**. Because errors tend to be random while correct reasoning converges, this improves accuracy on reasoning tasks – at N times the cost. Related techniques: generate multiple candidates and let a verifier/judge pick the best ("best-of-N"), or ask the model to critique and revise its answer.

</details>
### 16. How do you iterate on a prompt systematically?

<details>
<summary>Answer</summary>

1. Write a clear first version.
2. Build a small **test set** of realistic inputs (including hard cases).
3. Run the prompt on all of them and read the outputs.
4. Identify failure patterns (missed instruction, wrong format, hallucination).
5. Change **one thing** at a time (add an example, clarify a step, add structure).
6. Re-run the whole set – check you didn't break other cases.
7. Version the prompt and keep the results.

Tools like a prompt generator/improver in the provider console can draft a structured starting prompt, but evaluation decides what's actually better. See [Evals](13-evals.md).

</details>

### 17. How do you manage prompts in production?

<details>
<summary>Answer</summary>

- Store prompts as **versioned templates** (in code or a prompt registry), not scattered strings.
- Test changes against an **evaluation set** before release (regression tests).
- Roll out changes gradually / A/B test them; log prompt version with each request.
- Pin model versions; re-run evaluations when upgrading the model.
- Monitor outputs (format failures, refusals, user feedback, cost per request).

</details>
### 18. How should you write prompts for an LLM-as-judge or classifier?

<details>
<summary>Answer</summary>

- Define the criteria precisely with a **rubric** and examples of each score/label.
- Ask for brief reasoning **before** the verdict (improves consistency), and output a structured verdict.
- Use a binary or small ordinal scale rather than 1–10.
- Randomise answer order in pairwise comparisons (position bias) and check for length bias.
- Validate the judge against human labels before trusting it.

See [LLM Evaluation](13-evals.md).

</details>
### 19. How do you reduce costs and latency through prompting?

<details>
<summary>Answer</summary>

- Shorter prompts: remove redundant instructions and examples; retrieve fewer, better chunks.
- Ask for **concise outputs** and set max tokens – output tokens are slower and usually pricier than input tokens.
- Use **prompt caching** with stable prefixes.
- Route easy requests to smaller models.
- Batch offline work using batch APIs.
- Stream responses to improve perceived latency.

</details>
### 20. When is prompting not enough?

<details>
<summary>Answer</summary>

- The model lacks **knowledge** (private or recent data) → **RAG** or tools.
- You need a very specific **style/format/behaviour** consistently, or many examples don't fit → **fine-tuning**.
- Cost/latency require a **smaller model** → fine-tune or distil.
- The task needs **actions or real-time data** → tools/agents.
- High accuracy on a narrow classification at huge volume → a fine-tuned small model or classical ML.

See [RAG](10-rag-search.md) and [Fine-Tuning](16-choosing-models.md).

</details>
### 21. Rewrite this weak prompt: "Write about our product launch."

<details>
<summary>Answer</summary>

```
You're writing for our company blog. Audience: existing customers (small-business owners), not technical.

<launch_details>
{product name, what it does, price, availability date, 3 key benefits, one customer quote}
</launch_details>

Write a 400–500 word announcement post that:
1. Opens with the customer problem this solves (1 short paragraph).
2. Explains the 3 benefits with one concrete example each.
3. Includes the customer quote.
4. Ends with how to get it and the price.

Tone: warm and confident, no hype words like "revolutionary". Use short paragraphs and one subheading per benefit.
If any detail you need is missing from <launch_details>, write [MISSING: …] instead of inventing it.
```

What improved: audience and purpose, the data in tags, a structure, length, tone with a reason, and an explicit "out" for missing information.

</details>

---

[⬅ Previous: AI Fluency: Working Effectively With AI](03-ai-fluency.md) · [⬅ Back to index](../README.md) · [Next: Context Engineering & Memory ➡](05-context-engineering.md)
