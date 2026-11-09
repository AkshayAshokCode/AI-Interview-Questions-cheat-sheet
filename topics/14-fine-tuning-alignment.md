# Fine-Tuning & Alignment

[⬅ Back to index](../README.md)

Covers: when to fine-tune, full fine-tuning vs parameter-efficient methods, LoRA and QLoRA, preparing training data, supervised fine-tuning, catastrophic forgetting, RLHF (reward models, PPO), DPO and other preference methods, RL from AI feedback and constitutional approaches, RL with verifiable rewards, distillation, synthetic data, and evaluating a fine-tuned model.

---

### 1. When should you fine-tune an LLM instead of prompting or using RAG?

<details>
<summary>Answer</summary>

Fine-tune when you need:
- A consistent **format, style, tone or behaviour** that prompting can't reliably achieve.
- A **narrow task** at high volume where a **smaller fine-tuned model** can match a large model's quality at a fraction of the cost and latency.
- Domain-specific skills or language (medical notes, legal drafting, a niche programming language).
- Shorter prompts (behaviour baked in rather than long instructions every call).

Don't fine-tune mainly to add **facts** – use RAG (facts change, and fine-tuned facts can still be hallucinated). Always try prompting (and RAG) first and build an evaluation set: it tells you whether fine-tuning actually helps.

</details>

### 2. Full fine-tuning vs parameter-efficient fine-tuning (PEFT)?

<details>
<summary>Answer</summary>

- **Full fine-tuning** updates all weights. Highest capacity, but needs memory for weights + gradients + optimiser states (Adam keeps two extra values per parameter – roughly 16 bytes/parameter in mixed precision, so ~110+ GB for a 7B model before activations), and you store a full model copy per task. Higher risk of catastrophic forgetting.
- **PEFT** freezes the base model and trains a small number of extra parameters: **LoRA**, adapters, prefix/prompt tuning. Far less memory, small task-specific files, fast to switch tasks, and often close to full fine-tuning quality.

</details>

### 3. How does LoRA work?

<details>
<summary>Answer</summary>

**Low-Rank Adaptation** keeps the pretrained weight matrix W frozen and learns an update as the product of two small matrices: `W' = W + (α/r)·B·A`, where `A` is r × d and `B` is d × r with a small rank r (e.g. 8–64). Only A and B are trained – often <1% of the parameters. B starts at zero so training begins from the original model.

Why it works: the weight changes needed for adaptation tend to be **low rank**. Benefits: low memory, tiny adapter files (MBs), many adapters can share one base model and be swapped at serving time (multi-LoRA serving), and adapters can be merged into the weights for zero inference overhead. Usually applied to attention (and often MLP) projection matrices; key hyperparameters: rank r, alpha, target modules, learning rate.

</details>

### 4. What is QLoRA?

<details>
<summary>Answer</summary>

LoRA on top of a base model **quantised to 4-bit** (NF4 data type, double quantisation, paged optimisers). The frozen base weights take ~¼ of the memory of 16-bit, while LoRA adapters are trained in higher precision. It made fine-tuning 7–70B models possible on a single GPU with modest quality loss compared to 16-bit LoRA.

</details>

### 5. How do you prepare data for supervised fine-tuning (SFT)?

<details>
<summary>Answer</summary>

- Collect **prompt → ideal response** pairs in the exact chat format the model will see in production (system prompt, roles, tool calls).
- **Quality over quantity**: a few hundred to a few thousand clean, diverse, consistent examples often beat large noisy sets.
- Cover edge cases, refusals and "I don't know" cases you want.
- Deduplicate, remove PII, check label consistency (have experts review samples).
- Keep a held-out **evaluation set** that's never trained on.
- Usually compute the loss **only on the response tokens**, not the prompt.
- Synthetic data from a stronger model can bootstrap the dataset (check licences and quality).

</details>

### 6. What is catastrophic forgetting and how do you limit it?

<details>
<summary>Answer</summary>

Fine-tuning on a narrow dataset can degrade the model's general abilities (it "forgets" things it knew, or loses instruction-following and safety behaviour). Limit it with: PEFT (LoRA) instead of full fine-tuning, low learning rates and few epochs, **mixing general-purpose data** into the fine-tuning set, early stopping, and evaluating general benchmarks alongside the target task.

</details>

### 7. How does RLHF work?

<details>
<summary>Answer</summary>

**Reinforcement Learning from Human Feedback**, used to align chat models with human preferences:
1. **SFT** – start from an instruction-tuned model.
2. **Reward model** – humans compare pairs of responses to the same prompt; train a model to predict which one people prefer (a scalar score).
3. **RL optimisation** – generate responses, score them with the reward model, and update the policy with **PPO**, plus a **KL penalty** that keeps the model close to the SFT model (prevents it drifting into gibberish that exploits the reward model – "reward hacking").

Downsides: complex, unstable, expensive (several models in memory), and the reward model can be gamed (e.g. rewarding length or flattery).

</details>

### 8. What is DPO and why did it become popular?

<details>
<summary>Answer</summary>

**Direct Preference Optimisation** trains directly on preference pairs (prompt, chosen response, rejected response) with a simple classification-style loss that increases the probability of the chosen response relative to the rejected one, compared with a frozen reference model. It's mathematically derived from the RLHF objective but needs **no separate reward model and no RL loop**, so it's simpler, more stable and cheaper. Variants: IPO, KTO (works with thumbs-up/down labels instead of pairs), ORPO, SimPO. Online RL methods are still used for frontier models, especially for reasoning.

</details>

### 9. What are RLAIF and constitutional approaches?

<details>
<summary>Answer</summary>

**RL from AI Feedback** uses an AI model (guided by written principles) to produce preference labels or critiques instead of, or in addition to, humans – much cheaper and more scalable. Anthropic's **Constitutional AI** has the model critique and revise its own outputs according to a set of principles (a "constitution"), then uses AI-generated preferences for RL. Human oversight is still needed to write principles and validate results.

</details>

### 10. What is RL with verifiable rewards, and how does it create reasoning models?

<details>
<summary>Answer</summary>

For tasks with checkable answers (maths with a known result, code with unit tests, formatted outputs), the reward comes from an **automatic verifier** instead of a learned reward model. The model generates long chains of thought; correct final answers are rewarded. Algorithms like **GRPO** (group relative policy optimisation: compare several samples for the same prompt against each other instead of training a value model) made this efficient. This is how models like DeepSeek-R1 learned extended reasoning, self-checking and backtracking. Risks include reward hacking (e.g. gaming weak tests) and overly long reasoning.

</details>

### 11. What is knowledge distillation?

<details>
<summary>Answer</summary>

Training a smaller **student** model to imitate a larger **teacher**: either on the teacher's soft probability distributions (the classic approach, using a temperature-softened softmax and KL loss) or simply on teacher-generated outputs (sequence-level distillation – common for LLMs). It transfers much of the teacher's ability into a model that's cheaper and faster to serve. Check model licences – some providers forbid using outputs to train competing models.

</details>

### 12. What are the risks of training on synthetic data?

<details>
<summary>Answer</summary>

Benefits: cheap, scalable, can cover rare cases and generate preference pairs. Risks: copying the generator's errors and biases, low diversity (repetitive phrasing), **model collapse** when models are trained repeatedly on their own outputs without fresh real data, and benchmark contamination. Mitigate with filtering (automatic checks + LLM judges + human spot checks), diversity prompts and seeds, mixing with real data, and careful evaluation.

</details>

### 13. How do you evaluate a fine-tuned model?

<details>
<summary>Answer</summary>

- **Task metrics** on a held-out set (accuracy, F1, exact match, pass rate, LLM-judge scores against a rubric).
- **Comparison** with baselines: the base model with a good prompt, and a larger model.
- **Regression checks** on general abilities and **safety** (did fine-tuning remove refusals or increase toxicity?).
- **Human review** of samples, especially for style and tone.
- Online A/B testing before full rollout.

</details>

### 14. What hyperparameters matter most when fine-tuning an LLM?

<details>
<summary>Answer</summary>

Learning rate (much smaller for full fine-tuning, e.g. 1e-5, than for LoRA, e.g. 1e-4 to 2e-4), number of epochs (1–3 is typical; more overfits small datasets), batch size (with gradient accumulation), LoRA rank/alpha/target modules, sequence length and packing, warmup and schedule. Watch training and validation loss, and evaluate checkpoints on the actual task – the lowest loss isn't always the best behaving model.

</details>

### 15. What is instruction tuning and why did it matter?

<details>
<summary>Answer</summary>

Fine-tuning a pretrained base model on many tasks phrased as **natural-language instructions with responses**. A base model only continues text; after instruction tuning it **follows instructions** and generalises to unseen tasks described in plain language (FLAN, InstructGPT). It's the step that turns a raw language model into a usable assistant, followed by preference tuning for helpfulness and safety.

</details>

---

[⬅ Previous: Retrieval-Augmented Generation (RAG)](13-rag.md) · [⬅ Back to index](../README.md) · [Next: Agents, Tool Use & MCP ➡](15-agents-tool-use.md)
