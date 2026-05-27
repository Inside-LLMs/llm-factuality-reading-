# The Geometry of Truth: Layer-wise Semantic Dynamics for Hallucination Detection in Large Language Models
*2025*

**Alias:** 语义轨迹验真

## 1. What problem does the paper solve?

Most hallucination detectors either:

- require multiple sampled responses
- rely on external verification
- add substantial latency

This paper looks for a single-pass, intrinsic detector that works directly
inside the model's representation space.

## 2. Core idea

The paper proposes Layer-wise Semantic Dynamics (LSD).

Instead of checking only the final answer, LSD tracks how the semantic meaning
of hidden states evolves across layers.

The claim is:

- factual generations keep a stable semantic trajectory
- hallucinated generations drift away across depth

## 3. Method

The framework aligns hidden activations with factual embeddings from a separate
encoder using margin-based contrastive learning.

This produces a geometric view of generation:

```text
layer 1 -> layer 2 -> ... -> layer L
```

and asks whether that path stays semantically anchored to the truth.

## 4. Main results

The abstract reports:

- F1 = 0.92
- AUROC = 0.96
- clustering accuracy = 0.89
- 5x to 20x speedup over sampling-based baselines

It also beats SelfCheckGPT and Semantic Entropy while requiring only one
forward pass.

## 5. Why it matters

This paper treats hallucination as a trajectory problem rather than only an
output problem.

That is useful because it gives both:

- a practical detector
- a more interpretable picture of where factual consistency starts to drift

## 6. Take-away

The paper argues that truth leaves a geometric trace across layers, and that
tracking this trace is a fast and effective way to detect hallucinations.
