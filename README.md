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

## Paper Index

### Decoding / Inference-time Methods

- [DoLa: Decoding by Contrasting Layers Improves Factuality in Large Language Models (ICLR 2024)](papers/dola-2024/dola-2024.md) / [中文](papers/dola-2024/dola-2024.zh.md)
- [In-Context Sharpness as Alerts: An Inner Representation Perspective for Hallucination Mitigation (2024)](papers/in-context-sharpness-2024/in-context-sharpness-2024.md) / [中文](papers/in-context-sharpness-2024/in-context-sharpness-2024.zh.md)
- [Monitoring Decoding: Mitigating Hallucination via Evaluating the Factuality of Partial Response during Generation (ACL 2025)](papers/monitoring-decoding-2025/monitoring-decoding-2025.md) / [中文](papers/monitoring-decoding-2025/monitoring-decoding-2025.zh.md)

### Mechanistic Interpretability

- [OVERTHINKING THE TRUTH: UNDERSTANDING HOW LANGUAGE MODELS PROCESS FALSE DEMONSTRATIONS (ICLR 2024)](papers/overthinking-the-truth-2024/overthinking-the-truth-2024.md) / [中文](papers/overthinking-the-truth-2024/overthinking-the-truth-2024.zh.md)
- [Sparse Autoencoders Find Highly Interpretable Features in Language Models (2023)](papers/sparse-autoencoders-2023/sparse-autoencoders-2023.md) / [中文](papers/sparse-autoencoders-2023/sparse-autoencoders-2023.zh.md)
- [Sparse Feature Circuits: Discovering and Editing Interpretable Causal Graphs in Language Models (ICLR 2025)](papers/sparse-feature-circuits-2025/sparse-feature-circuits-2025.md) / [中文](papers/sparse-feature-circuits-2025/sparse-feature-circuits-2025.zh.md)

---

## How to use this repository

- Open the paper folder under `papers/`.
- Read the PDF and update the corresponding `*.md` note in that folder.
- Use the YAML frontmatter in each note to track authors, year, venue, keywords, and status.

## Folder structure

- `papers/`
  - `dola-2024/`
    - `dola-2024.pdf`
    - `dola-2024.md`
  - `in-context-sharpness-2024/`
    - `in-context-sharpness-2024.pdf`
    - `in-context-sharpness-2024.md`
  - `monitoring-decoding-2025/`
    - `monitoring-decoding-2025.pdf`
    - `monitoring-decoding-2025.md`
  - `overthinking-the-truth-2024/`
    - `overthinking-the-truth-2024.pdf`
    - `overthinking-the-truth-2024.md`
  - `sparse-autoencoders-2023/`
    - `sparse-autoencoders-2023.pdf`
    - `sparse-autoencoders-2023.md`
  - `sparse-feature-circuits-2025/`
    - `sparse-feature-circuits-2025.pdf`
    - `sparse-feature-circuits-2025.md`

If you want, I can继续帮你增加 `meta.yml` 结构化元数据，或者把每个 paper folder 再加一个摘要文件。