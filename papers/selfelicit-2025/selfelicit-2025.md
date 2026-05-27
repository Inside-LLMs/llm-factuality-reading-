# SelfElicit
*ACL 2025*

**Alias:** 自提证据

## 1. What problem does the paper solve?

Even when good evidence is already present in the prompt, LMs often fail to use
it fully.

This becomes worse when the context is noisy and contains irrelevant material.

The paper asks:

> Can the model help itself find the right evidence before answering?

## 2. Core idea

SELFELICIT is an inference-time method that automatically highlights key
contextual evidence for the model.

The authors leverage deeper-layer attention patterns to identify which parts of
the input are most relevant, and then explicitly emphasize those parts.

The central idea is:

```text
find the evidence first, answer second
```

## 3. Method intuition

SELFELICIT uses the model's own evidence-finding signal instead of external
retrieval or extra supervision.

Its appeal is that it:

- requires no additional training
- avoids iterative prompting
- improves evidence utilization directly at inference time

So it is lightweight but targeted.

## 4. Main claims and results

The paper reports:

- consistent improvements on multiple evidence-based QA benchmarks
- gains across different LM families
- preserved computational efficiency

The benefit comes from better use of context, not from adding new knowledge.

## 5. Why it matters for hallucination

Some hallucinations happen because the answer is absent from the model.

Others happen because the answer is already in the prompt but the model fails to
focus on it.

SELFELICIT targets the second type:

```text
evidence available
but not properly used
```

## 6. Take-away

The paper suggests that many grounded-generation failures can be reduced by
making the model explicitly surface its own best evidence before decoding.
