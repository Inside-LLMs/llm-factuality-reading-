# Enhancing Trust in Large Language Models with Uncertainty-Aware Fine-Tuning
*2024*

**Alias:** 会说没把握

## 1. What problem does the paper solve?

LLMs often produce fluent but wrong answers, yet their confidence is poorly
calibrated in open-ended generation.

This creates two linked problems:

- users cannot tell when the answer is unreliable
- hallucination detection becomes harder because confidence is not trustworthy

The paper aims to make the model better at saying when it is unsure.

## 2. Core idea

The authors propose uncertainty-aware fine-tuning.

Instead of only optimizing the standard causal language modeling objective,
they fine-tune the model so that its uncertainty estimates become better
calibrated while preserving answer quality.

The target is not just:

```text
good answer
```

but also:

```text
good answer + reliable uncertainty
```

## 3. Method intuition

The paper introduces an uncertainty-aware causal language modeling loss derived
from decision-theoretic principles.

In effect, the model is encouraged to:

- stay accurate when it knows the answer
- express calibrated uncertainty when it does not

This makes uncertainty a first-class training target rather than an afterthought.

## 4. Main claims and results

The paper reports that the method:

- improves uncertainty calibration on free-form QA tasks
- strengthens hallucination detection
- improves out-of-domain prompt identification
- does so without sacrificing answer accuracy

The benefit is therefore both practical and diagnostic.

## 5. Why it matters for factuality

Many hallucination defenses try to force the model toward a better answer.

This paper takes a different angle:

> sometimes the most trustworthy behavior is not higher confidence, but better
> awareness of uncertainty

That is especially useful when the model lacks knowledge entirely.

## 6. Take-away

The paper's message is simple: a trustworthy model should not only answer well,
but also know when it might be wrong.
