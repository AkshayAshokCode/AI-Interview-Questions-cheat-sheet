# Responsible AI, Safety & Security

[⬅ Back to index](../README.md)

Covers: sources of bias, fairness metrics and trade-offs, explainability (SHAP, LIME, feature importance), privacy (PII, differential privacy, federated learning), LLM security risks (OWASP LLM Top 10), guardrails, red-teaming, data poisoning and model extraction, alignment concepts, and AI regulation (EU AI Act and others).

---

### 1. Where does bias in ML systems come from?

<details>
<summary>Answer</summary>

- **Historical bias** – data reflects past discrimination (hiring data favouring one group).
- **Representation/sampling bias** – some groups are under-represented (face datasets with few darker-skinned faces).
- **Measurement bias** – proxies measured differently across groups (arrests as a proxy for crime).
- **Labelling bias** – annotators' subjective judgements.
- **Aggregation bias** – one model for groups that behave differently.
- **Feedback loops** – model decisions shape future data (predictive policing).

Removing a sensitive attribute isn't enough: other features (postcode, name) can act as **proxies**.

</details>

### 2. What fairness metrics exist, and why can't you satisfy them all?

<details>
<summary>Answer</summary>

- **Demographic parity** – equal positive-prediction rates across groups.
- **Equal opportunity** – equal true positive rates (recall) across groups.
- **Equalised odds** – equal true positive **and** false positive rates.
- **Predictive parity / calibration** – a score means the same probability for every group.

When base rates differ between groups, several of these are **mathematically incompatible** (impossibility results), so teams must choose which notion of fairness fits the context (lending vs hiring vs medical), measure it across slices, and document the trade-off.

</details>

### 3. How do you mitigate bias?

<details>
<summary>Answer</summary>

- **Pre-processing** – collect more representative data, re-sample or re-weight, fix labels.
- **In-processing** – fairness constraints or adversarial debiasing during training.
- **Post-processing** – group-specific thresholds or calibration (subject to legal constraints).
- **Process** – evaluate metrics by slice before launch, involve domain experts and affected communities, document models (model cards, datasheets), monitor in production, and provide appeal paths for decisions about people.

</details>

### 4. How do SHAP and LIME explain model predictions?

<details>
<summary>Answer</summary>

- **SHAP** – based on Shapley values from game theory: each feature's contribution is its average marginal effect across all combinations of features. Consistent, gives local (per-prediction) and global explanations; TreeSHAP is fast for tree models.
- **LIME** – fits a simple interpretable model (e.g. linear) around one prediction using perturbed samples; model-agnostic but can be unstable.
- Others: permutation importance, partial dependence plots, attention visualisation (not a faithful explanation by itself), counterfactual explanations ("you'd be approved if income were $5K higher").

Explainability is required in regulated decisions (credit, insurance) and helps debugging.

</details>

### 5. How do you protect privacy in ML systems?

<details>
<summary>Answer</summary>

- **Data minimisation** and retention limits; remove or pseudonymise **PII** before training and logging.
- Access control and encryption; separate environments for sensitive data.
- **Differential privacy** – add calibrated noise (e.g. DP-SGD clips per-example gradients and adds noise) so the model can't reveal whether any individual was in the training data.
- **Federated learning** – train on devices and share only model updates (keyboard prediction), often combined with secure aggregation and DP.
- For LLM apps: don't send sensitive data to providers without agreements, use zero-retention settings, redact PII in prompts and logs, and consider on-device or self-hosted models.

</details>

### 6. Can LLMs leak training data?

<details>
<summary>Answer</summary>

Yes – models can **memorise** and regurgitate rare sequences (emails, keys, copyrighted passages), especially if duplicated in training data. Extraction attacks prompt the model to reproduce them. Mitigations: deduplicate and filter PII/secrets from training data, differential privacy where feasible, output filters for PII and verbatim copying, and testing for memorisation. Fine-tuning on private data raises the same risk for anyone who can query the model.

</details>

### 7. What are the main security risks for LLM applications (OWASP LLM Top 10)?

<details>
<summary>Answer</summary>

Key risks highlighted by OWASP's list for LLM applications:
- **Prompt injection** (direct and indirect).
- **Sensitive information disclosure** (PII, secrets, system prompts).
- **Supply-chain risks** (untrusted models, datasets, plugins).
- **Data and model poisoning.**
- **Improper output handling** – passing model output to shells, SQL, browsers without validation (leads to injection/XSS).
- **Excessive agency** – agents with too many permissions.
- **System prompt leakage.**
- **Vector and embedding weaknesses** (e.g. cross-tenant leaks in RAG).
- **Misinformation** (hallucinations relied on as fact).
- **Unbounded consumption** (cost/denial-of-service through expensive requests).

</details>

### 8. What are guardrails, and how do you implement them?

<details>
<summary>Answer</summary>

Checks around the model that enforce policy regardless of what the model does:
- **Input guardrails** – moderation classifiers, prompt-injection detection, PII detection/redaction, topic restrictions, rate limits.
- **Output guardrails** – schema validation, toxicity/PII filters, groundedness checks against sources, blocking disallowed content or actions, allow-lists for URLs and commands.
- **Action guardrails** – permission checks in code, spending limits, human approval for sensitive actions.

Implement with classifiers (provider moderation APIs, open safety models such as Llama Guard), rule engines and code. Balance against over-blocking legitimate requests; measure both.

</details>

### 9. What is red-teaming for AI systems?

<details>
<summary>Answer</summary>

Systematically trying to make the system fail or cause harm before attackers or users do: jailbreaks, prompt injection through documents and tools, extracting system prompts or private data, eliciting biased or dangerous content, abusing agent tools, and cost attacks. Combine human experts (domain and security), automated attack generation (LLMs attacking LLMs), and regression suites of past attacks. Findings feed into guardrails, training data and policies; repeat after every major change.

</details>

### 10. What are data poisoning and model extraction attacks?

<details>
<summary>Answer</summary>

- **Data poisoning** – attackers insert crafted examples into training or fine-tuning data (or into documents a RAG system indexes) to cause targeted misbehaviour or backdoors (a trigger phrase changes behaviour). Defences: data provenance, filtering and anomaly detection, restricting who can add data, evaluation for backdoors.
- **Model extraction** – querying a model many times to train a copy (stealing IP) or to infer training data (**membership inference**). Defences: rate limiting, monitoring query patterns, returning less information (no full probabilities), terms of service.

</details>

### 11. What does "AI alignment" mean in practical terms?

<details>
<summary>Answer</summary>

Making AI systems reliably do what their designers and users intend, in line with human values – helpful, honest and harmless – including in situations not seen in training. Practical techniques: preference tuning (RLHF, DPO, constitutional approaches), safety training on refusals, system-level controls, evaluation and red-teaming, interpretability research (understanding internal representations), and monitoring deployed systems. Concerns studied include reward hacking, deceptive or sycophantic behaviour, and keeping increasingly capable agents under meaningful human oversight.

</details>

### 12. What is the EU AI Act, and how does regulation affect AI engineering?

<details>
<summary>Answer</summary>

The EU AI Act (adopted 2024, with obligations phasing in from 2025 onwards) regulates AI by **risk level**:
- **Unacceptable risk** – banned (e.g. social scoring by authorities, certain manipulative or biometric uses).
- **High risk** – e.g. AI in hiring, credit, education, critical infrastructure, medical devices: requirements for risk management, data quality, documentation, logging, human oversight, accuracy and robustness.
- **Limited risk** – transparency duties (tell users they're talking to AI; label deepfakes).
- **General-purpose AI models** – documentation and copyright-related obligations, with extra duties for models with systemic risk.

Other relevant rules: GDPR (data protection, automated decision-making), sector regulations, and frameworks such as the NIST AI Risk Management Framework. Engineering impact: documentation (model cards), audit logs, evaluation evidence, human-in-the-loop design, and data governance.

</details>

### 13. How do you design an AI feature responsibly from the start?

<details>
<summary>Answer</summary>

1. Assess the use case's impact: who could be harmed, and how badly?
2. Define success and **harm metrics** (including fairness by slice and over-refusal).
3. Use representative, consented, documented data.
4. Build guardrails and human oversight proportional to risk.
5. Be transparent with users (AI disclosure, limitations, how to report problems).
6. Red-team and evaluate before launch; launch gradually.
7. Monitor, collect feedback, and have a rollback/incident process.

</details>

### 14. What is sycophancy in LLMs, and why does it matter?

<details>
<summary>Answer</summary>

The tendency of models to **agree with the user** or tell them what they want to hear – changing a correct answer when the user pushes back, praising flawed work, confirming false beliefs. It partly comes from preference training (people rate agreeable answers highly). It matters for coding, medical, legal and educational uses where honest disagreement is essential. Mitigations: training data and rewards that value honesty, prompts that ask for critical evaluation, and evaluations that test whether answers change under social pressure.

</details>

---

[⬅ Previous: ML & GenAI System Design](20-ml-genai-system-design.md) · [⬅ Back to index](../README.md) · [Next: ML Coding ➡](22-ml-coding.md)
