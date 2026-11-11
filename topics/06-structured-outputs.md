# Structured Outputs & Extraction

[⬅ Back to index](../README.md)

Covers: getting reliable JSON, schemas and structured-output modes, strict tool inputs, validation and retries, extraction from documents, classification outputs, citations, and checking stop reasons.

---

### 1. How do you get reliable structured output (JSON) from an LLM?

<details>
<summary>Answer</summary>

- Use the provider's **structured outputs / JSON schema mode** or **tool/function calling**, which constrains generation to a schema (constrained decoding) – the most reliable option.
- Otherwise: give the exact schema and an example, ask for JSON only, use low temperature.
- **Validate** with a schema validator (Pydantic, Zod); on failure, retry with the validation error in the prompt.
- Keep schemas simple; use enums for categorical fields; make optional fields explicit.

</details>
### 2. How do you design a good output schema?

<details>
<summary>Answer</summary>

- Keep it **flat and small**; nest only when needed.
- Use **enums** for categorical fields and clear field names with descriptions.
- Make optional fields explicit and allow `null` for "not found" instead of forcing a guess.
- Include a field for the model's **confidence or a reason** when useful for review.
- Put a free-text `reasoning` field **before** the decision field if you want the model to think first.
- Version your schema; downstream code depends on it.

</details>

### 3. Structured outputs vs tool calling for getting JSON?

<details>
<summary>Answer</summary>

- **Structured outputs** (a response format constrained to a JSON schema) – best when the whole response should be data, e.g. extraction or classification.
- **Tool calling** with a schema (and strict mode, where available) – best when the model decides **whether and which** action to take, with validated arguments.

Both use constrained decoding to guarantee valid JSON matching the schema on supported models. Note that some newer models don't allow *forcing* a specific tool; use structured outputs when you just need JSON back.

</details>

### 4. Why validate model output even with structured outputs?

<details>
<summary>Answer</summary>

A schema guarantees **shape**, not **truth**: fields can be valid JSON but wrong (an invoice total that doesn't match line items, a date in the future, an ID that doesn't exist). Add business validation in code (sums, ranges, lookups against your database), route failures to a retry with the error message or to human review, and log failure rates as a quality metric.

</details>

### 5. How do you extract data from documents reliably?

<details>
<summary>Answer</summary>

1. Provide the document (text, PDF or images) clearly delimited.
2. Define a schema with descriptions and "null if not present".
3. Ask for **quotes or page references** supporting each extracted field.
4. Validate in code; cross-check against known data.
5. Send low-confidence or failed cases to human review, and feed corrections into your eval set.

See the [document extraction design](20-ai-system-design.md).

</details>

### 6. How do you build a reliable classifier with an LLM?

<details>
<summary>Answer</summary>

- Define each label precisely with **examples and edge cases**; include an "other/unclear" label.
- Constrain output to the label set (enum in a schema).
- Optionally ask for a one-sentence reason first.
- Evaluate on a labelled set and look at the confusion between similar labels.
- For very high volume, use a small fast model or bootstrap labels with an LLM and train a cheaper classifier.

</details>

### 7. What are citations features, and why use them?

<details>
<summary>Answer</summary>

Some APIs (e.g. Claude's citations on document inputs) return answers split into parts that point to the **exact passages** (character ranges, pages) of the supplied documents that support each claim. Benefits: users can verify answers, you can automatically check grounding, and hallucinated claims are easier to spot (no citation). It's a better option than asking the model to write citations as free text, which can itself be hallucinated.

</details>

### 8. Why must you check the stop reason of a response?

<details>
<summary>Answer</summary>

The stop reason tells you **why generation ended**: finished normally, hit `max_tokens` (output truncated – JSON may be incomplete), wants to call a tool, paused, or **refused** (safety classifiers declined). Code that reads the text without checking can parse truncated JSON, ignore a pending tool call, or show an empty answer. Handle each case: raise max tokens or continue, run the tool loop, or show a graceful refusal message (and use fallback options where the provider supports them).

</details>

---

[⬅ Previous: Context Engineering & Memory](05-context-engineering.md) · [⬅ Back to index](../README.md) · [Next: Building With LLM APIs ➡](07-building-with-llm-apis.md)
