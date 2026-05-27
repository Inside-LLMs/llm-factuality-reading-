# VERIFYING CHAIN-OF-THOUGHT REASONING VIA ITS COMPUTATIONAL GRAPH
*2026*

**Alias:** 算图验推理

## 1. What problem does the paper solve?

Existing chain-of-thought verification methods usually judge reasoning from:

- final outputs
- intermediate activations

These methods may predict whether a solution is wrong, but they do not clearly
show why the computation failed.

The paper proposes a more white-box way to verify reasoning.

## 2. Core idea

The authors introduce Circuit-based Reasoning Verification (CRV).

They view attribution graphs as execution traces of the model's latent
reasoning circuit and ask whether correct and incorrect traces have different
structural fingerprints.

So verification becomes:

```text
inspect the computation graph, not just the answer
```

## 3. Method intuition

CRV extracts structural features from attribution graphs and trains a
classifier to distinguish correct from incorrect reasoning steps.

The paper further argues that these signatures are not merely descriptive:

- they diagnose where failure happens
- they can guide targeted interventions on individual features

## 4. Main findings

The abstract emphasizes three claims:

- graph structure is strongly predictive of reasoning errors
- error signatures are domain-specific
- interventions guided by the graph can correct faulty reasoning

That pushes the work from detection toward causal debugging.

## 5. Why it matters for factuality

Hallucination and reasoning error are often linked.

If we can verify whether the internal computation is coherent, we gain a richer
signal than simply checking the final token sequence.

This is especially interesting for future white-box factuality monitors.

## 6. Take-away

The paper argues that reasoning quality can be verified from the model's own
computational graph, opening a path from error spotting to mechanistic repair.
