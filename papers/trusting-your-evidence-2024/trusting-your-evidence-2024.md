# Trusting your Evidence: Hallucinate less with Context-Aware Decoding
*NAACL 2024*

**Alias:** 看证据再说

## 1. What problem does the paper solve?

Language models often underuse the provided context and over-rely on their
parametric prior.

That causes a common failure pattern:

```text
context says A
model prior says B
model outputs B
```

The paper asks how to make decoding trust the evidence in context more strongly.

## 2. Core idea

The authors propose Context-Aware Decoding (CAD).

CAD compares the model's output distribution in two settings:

- with the context
- without the context

Tokens strongly supported by the context are amplified, while tokens mainly
coming from the model's prior are suppressed.

## 3. Why this helps

The method is especially useful when the context conflicts with what the model
"already knows".

CAD tries to enforce:

```text
follow the evidence, not the habit
```

Importantly, this is done at inference time, without extra training.

## 4. Main results

The paper reports:

- improved faithfulness across OPT, GPT, LLaMA, and FLAN-T5 families
- strong gains on summarization factuality
- particularly large benefit when context and model prior disagree

So the method is not just boosting fluency, but specifically reducing
context-ignoring hallucinations.

## 5. Relation to DoLa

DoLa contrasts:

```text
late layer vs early layer
```

CAD contrasts:

```text
with context vs without context
```

Both are inference-time contrastive methods, but they trust different signals.

## 6. Take-away

The paper shows that a simple contrastive decoding rule can make models follow
their evidence more faithfully and hallucinate less when the context matters.
