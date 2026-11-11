# Multimodal Inputs & Model Features

[⬅ Back to index](../README.md)

Covers: image understanding, PDFs and documents, citations, extended thinking, code execution and file outputs, web search and fetch, computer use, voice, image generation, and the Files API.

---

### 1. How do you get good results from image inputs?

<details>
<summary>Answer</summary>

- Use clear, sufficiently high-resolution images (but not huge – large images cost more tokens and may be downscaled).
- Put images **before** the question, and refer to them explicitly ("In image 2…").
- Ask specific questions; for charts and screenshots, ask the model to describe what it sees first, then answer.
- For counting, precise measurements or tiny text, verify results – these are weaker spots.
- Images consume tokens roughly in proportion to their size – resize sensibly.

</details>

### 2. How do models handle PDFs and documents?

<details>
<summary>Answer</summary>

APIs like Claude's accept PDFs directly: the model sees both the **extracted text and page images**, so it can read charts, tables and layouts, not just text. Limits apply (file size and page count), and long PDFs use many tokens – for repeated questions over the same document, use prompt caching or a Files API upload. For huge document sets, use retrieval instead of sending everything.

</details>

### 3. When should you turn on extended thinking or higher effort?

<details>
<summary>Answer</summary>

For tasks needing multi-step reasoning: complex analysis, maths, planning, debugging, difficult coding, agentic tasks with many decisions, and evaluation/grading with nuanced rubrics. Not worth it for simple lookups, short rewrites, classification or chat where speed matters. Thinking increases tokens and latency, so compare quality vs cost on your evals.

</details>

### 4. What can a code-execution tool add to an AI feature?

<details>
<summary>Answer</summary>

The model can **run code in a sandbox**: analyse uploaded CSVs and spreadsheets precisely, do exact calculations, create charts, transform data, and generate files (reports, documents, presentations) that are returned to the user. It turns "the model estimates" into "the model computes", which is much more reliable for data work. Mind the sandbox's limits (no internet by default, time and memory limits) and verify outputs.

</details>

### 5. How do web search and web fetch tools work in an assistant?

<details>
<summary>Answer</summary>

**Web search** lets the model issue search queries and receive results with URLs and snippets, then cite them in its answer; **web fetch** retrieves the full content of a specific page or PDF. Configure domain allow/block lists, limit the number of uses per request, and show citations to users. Treat fetched content as untrusted (prompt injection) and remember search adds latency and per-use cost.

</details>

### 6. What is computer use, and when is it appropriate?

<details>
<summary>Answer</summary>

A capability where the model operates a computer through **screenshots and mouse/keyboard actions** – for applications without APIs (legacy systems, some websites), UI testing, and complex web tasks. It's slower and more error-prone than API integrations and exposed to prompt injection from what's on screen. Use it in isolated virtual machines or containers, with restricted sites and credentials, and human confirmation for consequential actions. Prefer APIs or MCP integrations when available.

</details>

### 7. How do voice AI features work, and what makes them hard?

<details>
<summary>Answer</summary>

Either a pipeline (speech-to-text → LLM → text-to-speech) or a speech-native model. The hard parts are **latency** (users expect replies within about a second – stream everything), **turn-taking** (detecting when the user finished, handling interruptions), noisy audio and accents, and confirming high-impact actions verbally. Design shorter, conversational answers – long lists and tables don't work well when spoken.

</details>

### 8. What should you consider when using AI image generation in a product?

<details>
<summary>Answer</summary>

Prompting for style and composition, consistency across images (reference images, style guides), safety filters and policy (real people, brands, harmful content), copyright and licensing terms of the provider, disclosure/watermarking (e.g. content credentials), cost per image, and human review for anything published. Text rendering, hands and exact layouts are common weak spots to check.

</details>

### 9. What is a Files API, and why use it?

<details>
<summary>Answer</summary>

It lets you **upload a file once** (PDF, image, dataset) and reference it by ID in many requests, instead of re-sending it each time. Useful for multi-turn analysis of the same document, code-execution workflows over datasets, and retrieving files the model generated. Manage retention and deletion like any stored user data.

</details>

---

[⬅ Previous: Choosing Models & Approaches](16-choosing-models.md) · [⬅ Back to index](../README.md) · [Next: Designing AI Products & UX ➡](18-ai-product-design.md)
