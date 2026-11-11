# Tool Use (Function Calling)

[⬅ Back to index](../README.md)

Covers: how tool use works, writing tool definitions, the tool-use loop, parallel tool calls, error handling, tool choice, server-side tools (web search, web fetch, code execution), designing good tools, many tools and tool search, and tool security.

---

### 1. How does function / tool calling work?

<details>
<summary>Answer</summary>

1. You describe tools to the model: name, description, and a **JSON schema** for arguments.
2. Given the conversation, the model may respond with a **tool call** (tool name + arguments) instead of text.
3. **Your code** executes the tool (the model never runs anything itself), then sends the result back as a tool-result message.
4. The model continues: calls more tools or produces the final answer.

Good practice: clear tool names and descriptions (they're prompts), small focused tools, validated arguments, helpful error messages returned to the model, and idempotent/safe tools where possible.

</details>
### 2. What makes a good tool definition?

<details>
<summary>Answer</summary>

- A clear, specific **name** (`get_order_status`, not `helper`).
- A **detailed description**: what it does, when to use it (and when not), what it returns, important limitations. The description is effectively part of the prompt – it's the most important factor in correct tool use.
- An **input schema** with typed fields, descriptions, enums and required fields; strict schema validation where supported.
- Inputs that are natural for the model (IDs it has seen, plain dates) rather than internal codes it must guess.
- Useful, concise **outputs** – return the fields the model needs, not huge raw payloads.

</details>

### 3. Walk through the tool-use loop in code.

<details>
<summary>Answer</summary>

```python
tools = [{
    "name": "get_weather",
    "description": "Get the current weather for a city. Use when the user asks about weather.",
    "input_schema": {"type": "object",
                     "properties": {"city": {"type": "string", "description": "City name, e.g. Pune"}},
                     "required": ["city"]},
}]
messages = [{"role": "user", "content": "Do I need an umbrella in Pune today?"}]

while True:
    response = client.messages.create(model="claude-opus-5-5", max_tokens=2048,
                                      tools=tools, messages=messages)
    messages.append({"role": "assistant", "content": response.content})
    if response.stop_reason != "tool_use":
        break
    results = []
    for block in response.content:
        if block.type == "tool_use":
            try:
                output = run_tool(block.name, block.input)          # your code
                results.append({"type": "tool_result", "tool_use_id": block.id, "content": output})
            except Exception as e:
                results.append({"type": "tool_result", "tool_use_id": block.id,
                                "content": f"Error: {e}", "is_error": True})
    messages.append({"role": "user", "content": results})        # all results in ONE user message

print("".join(b.text for b in response.content if b.type == "text"))
```

SDKs also offer a **tool runner** helper that runs this loop for you.

</details>

### 4. What are parallel tool calls, and how should you handle them?

<details>
<summary>Answer</summary>

A single model response can contain **several tool calls** (e.g. check weather in three cities, or read three files). Run them **concurrently**, then return **all** tool results together in one message, each matched to its call ID. Splitting results across separate messages, or dropping failed ones, confuses the model and can make it stop using parallel calls. Disable parallel calls only if your tools must run strictly one at a time.

</details>

### 5. How should tools report errors?

<details>
<summary>Answer</summary>

Return the error **to the model** as a tool result marked as an error, with an actionable message ("Order ID not found. IDs look like ORD-12345."), instead of crashing the loop. The model can then fix its arguments, try another approach, or explain the problem to the user. Validate tool inputs before executing (types, ranges, permissions) and keep errors free of secrets and stack traces.

</details>

### 6. What is tool choice, and how do you make the model use a tool?

<details>
<summary>Answer</summary>

Tool choice controls whether the model may, must, or must not call tools: `auto` (model decides – the default), `none`, and on some models forcing any tool or a specific tool. Some newer models no longer allow forcing a tool; instead, use `auto` and say clearly in the prompt when the tool must be used, enable strict schemas, or use structured outputs if you only need JSON. Good descriptions usually matter more than forcing.

</details>

### 7. What are server-side tools?

<details>
<summary>Answer</summary>

Tools that run on the **provider's infrastructure** rather than in your code – you just declare them and results come back in the same response. Claude examples: **web search** (with domain allow/block lists and citations), **web fetch** (retrieve full page/PDF content), and **code execution** (run code in a sandbox for analysis, charts, file generation). Benefits: no loop or infrastructure to build. Considerations: per-use pricing, data leaving your environment, and limits on what they can access.

</details>

### 8. What are client-side "computer" tools like a text editor or bash tool?

<details>
<summary>Answer</summary>

Provider-defined tools whose behaviour the model is trained on, but which **you execute** in your environment: a **text editor** tool (view, create, edit files with string replacement), a **bash** tool (run shell commands), and **computer use** (screenshots, mouse and keyboard). They power coding agents and automation. Because they act on real systems, run them in sandboxes or containers, restrict paths and commands, and require approval for risky operations.

</details>

### 9. What do you do when you have dozens or hundreds of tools?

<details>
<summary>Answer</summary>

Many tool definitions consume context, add cost and make selection less accurate. Options:
- **Consolidate** overlapping tools; design fewer, higher-level tools.
- **Route** first (a classifier picks the relevant toolset).
- **Tool search** – mark tools for deferred loading and let the model search for the ones it needs (supported in the Claude API).
- Give **code execution plus a library/CLI** instead of many tiny tools.
- Use **skills** that load instructions and tools only when relevant.

</details>

### 10. How do you secure tool use?

<details>
<summary>Answer</summary>

- **Least privilege** – tools only do what the feature needs, scoped to the current user's permissions (enforce auth in the tool, not in the prompt).
- **Validate** all arguments; never pass model output directly to shells, SQL or file paths without checks.
- **Confirmation** for irreversible or external actions (payments, emails, deletions).
- Treat **tool results as untrusted** (web pages and documents can contain prompt injection).
- Rate limits, audit logs and monitoring for unusual tool usage.

</details>

---

[⬅ Previous: Building With LLM APIs](07-building-with-llm-apis.md) · [⬅ Back to index](../README.md) · [Next: Model Context Protocol (MCP) ➡](09-mcp.md)
