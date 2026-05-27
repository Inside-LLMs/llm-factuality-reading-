# LLM Factuality Reading

Papers and notes on LLM factuality, decoding methods, and mechanistic interpretability.

This repository collects reading notes on improving factuality and mitigating hallucinations in large language models, with a focus on:

- inference-time intervention
- decoding methods
- hallucination mitigation
- internal representations
- mechanistic interpretability
- feature-level understanding and editing

The repository is organized so that each paper lives in its own folder inside `papers/`, with the PDF and note document together.

---

## Hallucination Collection Alias Index

- `后层打假` — [DoLa](papers/dola-2024/dola-2024.md) / [中文](papers/dola-2024/dola-2024.zh.md)
- `尖激活预警` — [In-Context Sharpness as Alerts](papers/in-context-sharpness-2024/in-context-sharpness-2024.md) / [中文](papers/in-context-sharpness-2024/in-context-sharpness-2024.zh.md)
- `想太多会错` — [Overthinking the Truth](papers/overthinking-the-truth-2024/overthinking-the-truth-2024.md) / [中文](papers/overthinking-the-truth-2024/overthinking-the-truth-2024.zh.md)
- `看证据再说` — [Trusting your Evidence](papers/trusting-your-evidence-2024/trusting-your-evidence-2024.md) / [中文](papers/trusting-your-evidence-2024/trusting-your-evidence-2024.zh.md)
- `会说没把握` — [Enhancing Trust in Large Language Models with Uncertainty-Aware Fine-Tuning](papers/enhancing-trust-2024/enhancing-trust-2024.md) / [中文](papers/enhancing-trust-2024/enhancing-trust-2024.zh.md)
- `自提证据` — [SelfElicit](papers/selfelicit-2025/selfelicit-2025.md) / [中文](papers/selfelicit-2025/selfelicit-2025.zh.md)
- `语义轨迹验真` — [The Geometry of Truth](papers/geometry-of-truth-2025/geometry-of-truth-2025.md) / [中文](papers/geometry-of-truth-2025/geometry-of-truth-2025.zh.md)
- `无害提示也会骗` — [BEYOND PROMPT-INDUCED LIES](papers/beyond-prompt-induced-lies-2026/beyond-prompt-induced-lies-2026.md) / [中文](papers/beyond-prompt-induced-lies-2026/beyond-prompt-induced-lies-2026.zh.md)
- `算图验推理` — [VERIFYING CHAIN-OF-THOUGHT REASONING VIA ITS COMPUTATIONAL GRAPH](papers/verifying-cot-graph-2026/verifying-cot-graph-2026.md) / [中文](papers/verifying-cot-graph-2026/verifying-cot-graph-2026.zh.md)
- `推理RAG全景图` — [Towards Agentic RAG with Deep Reasoning](papers/agentic-rag-reasoning-survey-2025/agentic-rag-reasoning-survey-2025.md) / [中文](papers/agentic-rag-reasoning-survey-2025/agentic-rag-reasoning-survey-2025.zh.md)

---

## Paper Index

### Decoding / Inference-time Methods

- [DoLa: Decoding by Contrasting Layers Improves Factuality in Large Language Models (ICLR 2024)](papers/dola-2024/dola-2024.md) / [中文](papers/dola-2024/dola-2024.zh.md)
- [In-Context Sharpness as Alerts: An Inner Representation Perspective for Hallucination Mitigation (2024)](papers/in-context-sharpness-2024/in-context-sharpness-2024.md) / [中文](papers/in-context-sharpness-2024/in-context-sharpness-2024.zh.md)
- [Trusting your Evidence: Hallucinate less with Context-Aware Decoding (NAACL 2024)](papers/trusting-your-evidence-2024/trusting-your-evidence-2024.md) / [中文](papers/trusting-your-evidence-2024/trusting-your-evidence-2024.zh.md)
- [Enhancing Trust in Large Language Models with Uncertainty-Aware Fine-Tuning (2024)](papers/enhancing-trust-2024/enhancing-trust-2024.md) / [中文](papers/enhancing-trust-2024/enhancing-trust-2024.zh.md)
- [SelfElicit (ACL 2025)](papers/selfelicit-2025/selfelicit-2025.md) / [中文](papers/selfelicit-2025/selfelicit-2025.zh.md)
- [Monitoring Decoding: Mitigating Hallucination via Evaluating the Factuality of Partial Response during Generation (ACL 2025)](papers/monitoring-decoding-2025/monitoring-decoding-2025.md) / [中文](papers/monitoring-decoding-2025/monitoring-decoding-2025.zh.md)

### Detection / Trust / Verification

- [The Geometry of Truth: Layer-wise Semantic Dynamics for Hallucination Detection in Large Language Models (2025)](papers/geometry-of-truth-2025/geometry-of-truth-2025.md) / [中文](papers/geometry-of-truth-2025/geometry-of-truth-2025.zh.md)
- [BEYOND PROMPT-INDUCED LIES: Investigating LLM Deception on Benign Prompts (2026)](papers/beyond-prompt-induced-lies-2026/beyond-prompt-induced-lies-2026.md) / [中文](papers/beyond-prompt-induced-lies-2026/beyond-prompt-induced-lies-2026.zh.md)
- [VERIFYING CHAIN-OF-THOUGHT REASONING VIA ITS COMPUTATIONAL GRAPH (2026)](papers/verifying-cot-graph-2026/verifying-cot-graph-2026.md) / [中文](papers/verifying-cot-graph-2026/verifying-cot-graph-2026.zh.md)

### Mechanistic Interpretability

- [OVERTHINKING THE TRUTH: UNDERSTANDING HOW LANGUAGE MODELS PROCESS FALSE DEMONSTRATIONS (ICLR 2024)](papers/overthinking-the-truth-2024/overthinking-the-truth-2024.md) / [中文](papers/overthinking-the-truth-2024/overthinking-the-truth-2024.zh.md)
- [Sparse Autoencoders Find Highly Interpretable Features in Language Models (2023)](papers/sparse-autoencoders-2023/sparse-autoencoders-2023.md) / [中文](papers/sparse-autoencoders-2023/sparse-autoencoders-2023.zh.md)
- [Sparse Feature Circuits: Discovering and Editing Interpretable Causal Graphs in Language Models (ICLR 2025)](papers/sparse-feature-circuits-2025/sparse-feature-circuits-2025.md) / [中文](papers/sparse-feature-circuits-2025/sparse-feature-circuits-2025.zh.md)

### RAG / Survey

- [Towards Agentic RAG with Deep Reasoning: A Survey of RAG-Reasoning Systems in LLMs (2025)](papers/agentic-rag-reasoning-survey-2025/agentic-rag-reasoning-survey-2025.md) / [中文](papers/agentic-rag-reasoning-survey-2025/agentic-rag-reasoning-survey-2025.zh.md)

---
