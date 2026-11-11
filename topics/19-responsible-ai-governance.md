# Responsible AI & Governance

[⬅ Back to index](../README.md)

Covers: bias, fairness and inclusion, transparency and disclosure, privacy and data handling, intellectual property, workplace AI policies, rolling out AI in an organisation, regulation (EU AI Act), designing responsibly, and environmental considerations.

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
### 2. When and how should you disclose AI use?

<details>
<summary>Answer</summary>

Tell people when they're **interacting with an AI** (chatbots, voice agents), when content is **AI-generated** in contexts where it matters (journalism, academic work, client deliverables, synthetic media), and when AI influences **decisions about them**. Follow your organisation's and industry's rules; many regulations require disclosure for chatbots and deepfakes. Transparency builds trust; hidden AI use erodes it when discovered.

</details>

### 3. How do you protect privacy in ML systems?

<details>
<summary>Answer</summary>

- **Data minimisation** and retention limits; remove or pseudonymise **PII** before training and logging.
- Access control and encryption; separate environments for sensitive data.
- **Privacy-preserving techniques** some products use – on-device processing (data never leaves the phone), and approaches like federated learning and differential privacy that learn from many users without exposing any individual's data.
- For LLM apps: don't send sensitive data to providers without agreements, use zero-retention settings, redact PII in prompts and logs, and consider on-device or self-hosted models.

</details>
### 4. What intellectual-property questions come up with AI-generated content?

<details>
<summary>Answer</summary>

Who owns AI-assisted output (depends on jurisdiction and terms of service; purely machine-generated works may not be copyrightable in some places), whether outputs could reproduce copyrighted material, licensing of AI-generated code, and use of confidential inputs. Practical steps: read provider terms (ownership and indemnity), review outputs that will be published, avoid prompting for copies of protected works, track AI-generated code like third-party code, and involve legal for high-stakes uses.

</details>

### 5. What should a workplace AI policy cover?

<details>
<summary>Answer</summary>

Approved tools and plans; what data may and may not be shared; required human review for certain outputs; disclosure expectations; acceptable and prohibited uses; security practices (no secrets in prompts, scoped integrations); how to report problems; training expectations; and who owns AI governance decisions. Keep it practical and update it as tools change.

</details>

### 6. What decisions shape an organisation's rollout of an AI assistant?

<details>
<summary>Answer</summary>

Anthropic's enterprise rollout guidance frames five areas:
- **Structure & identity** – organisation and workspace structure, single sign-on, user provisioning.
- **Access** – who gets which tools and features, and in which phases.
- **Governance** – policies, data controls, integrations allowed, retention.
- **Spend** – budgets, limits and how costs are allocated.
- **Visibility** – usage analytics, audit logs and how adoption and impact are measured.

Recording these decisions in a rollout plan avoids ad-hoc, inconsistent adoption.

</details>

### 7. What is the EU AI Act, and how does regulation affect AI engineering?

<details>
<summary>Answer</summary>

The EU AI Act (adopted 2024, with obligations phasing in from 2025 onwards) regulates AI by **risk level**:
- **Unacceptable risk** – banned (e.g. social scoring by authorities, certain manipulative or biometric uses).
- **High risk** – e.g. AI in hiring, credit, education, critical infrastructure, medical devices: requirements for risk management, data quality, documentation, logging, human oversight, accuracy and robustness.
- **Limited risk** – transparency duties (tell users they're talking to AI; label deepfakes).
- **General-purpose AI models** – documentation and copyright-related obligations, with extra duties for models with systemic risk.

Other relevant rules: GDPR (data protection, automated decision-making), sector regulations, and frameworks such as the NIST AI Risk Management Framework. Engineering impact: documentation (model cards), audit logs, evaluation evidence, human-in-the-loop design, and data governance.

</details>
### 8. How do you design an AI feature responsibly from the start?

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
### 9. What is the environmental impact of AI use, and how can teams reduce it?

<details>
<summary>Answer</summary>

Training and running large models consumes significant energy and water in data centres. Teams can reduce their footprint mostly through **efficiency**: use the smallest model that meets quality, cache prompts, avoid unnecessary calls and huge contexts, batch offline work, and don't run expensive agents when a simple call works. Efficiency usually lowers cost too.

</details>

---

[⬅ Previous: Designing AI Products & UX](18-ai-product-design.md) · [⬅ Back to index](../README.md) · [Next: AI System Design ➡](20-ai-system-design.md)
