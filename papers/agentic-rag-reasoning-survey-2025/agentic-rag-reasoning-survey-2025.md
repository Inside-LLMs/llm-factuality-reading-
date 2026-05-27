# Towards Agentic RAG with Deep Reasoning: A Survey of RAG-Reasoning Systems in LLMs
*2025*

**Alias:** 推理RAG全景图

## 1. What problem does the paper solve?

Classic RAG helps factuality by injecting external knowledge, but it often
breaks on tasks that require multistep reasoning.

Pure reasoning approaches face the opposite problem:

- they may reason well
- but they can hallucinate or become poorly grounded

The survey organizes work that tries to combine these two strengths.

## 2. Core idea

The paper maps the space of RAG-reasoning systems into three broad directions:

- Reasoning-Enhanced RAG
- RAG-Enhanced Reasoning
- Synergized / Agentic RAG-Reasoning

So the main contribution is a taxonomy of how retrieval and reasoning can be
interleaved rather than treated as separate modules.

## 3. Key perspective

The survey's central message is:

```text
retrieval gives missing premises
reasoning turns premises into answers
```

The strongest systems increasingly do both in a loop:

```text
search -> reason -> search again -> revise
```

This is where the "agentic" framing comes from.

## 4. What the survey highlights

The paper reviews:

- how reasoning improves each RAG stage
- how retrieved evidence supports deeper inference
- datasets and benchmarks for this hybrid setting
- open challenges such as trustworthiness, multimodality, and human alignment

## 5. Why it matters for hallucination work

This is not a direct hallucination mitigation paper, but it is relevant because
it explains a major trend:

> stronger factuality increasingly depends on combining retrieval with explicit
> reasoning, not on treating them as separate fixes

## 6. Take-away

The survey provides a useful map of the emerging design space where grounded
retrieval and multistep reasoning are fused into a single agentic loop.
