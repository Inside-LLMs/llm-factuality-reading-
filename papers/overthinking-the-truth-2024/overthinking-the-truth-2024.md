# OVERTHINKING THE TRUTH: Understanding How Language Models Process False Demonstrations
*ICLR 2024*

**Alias:** 想太多会错

## 1. What problem does the paper solve?

LLMs can learn tasks from few-shot demonstrations.

But the problem is that the model learns not only the task, but also:


Wrong labels



False patterns



Harmful demonstrations


For example, incorrect few-shot examples like:

```text
Positive → Negative
```

cause the model to copy the wrong label.

The authors ask:

> Why does the model imitate false demonstrations? 
> In which Transformer layers does the wrong imitation arise? 
> Which components drive this behavior?

---

## 2. Core observation

The authors find that under false demonstrations, early computation still leans toward the correct answer, while later computation begins to trust the wrong examples.

That is:

Early layers:


Correct demos approx Incorrect demos


Later layers:


Correct ≠ Incorrect


A clear bifurcation appears.

They call this phenomenon:

# Overthinking

Meaning:

> the more the model computes, the more it is misled by false demonstrations.

Early exit can be more correct.

---

## 3. Experimental setup: False Demonstrations

Few-shot classification uses examples:


(x_i, y_i)


with true labels:


y_i = class(x_i)


The authors create false prompts by permuting labels:


y_i = sigma(class(x_i))


where sigma is a cyclic permutation.

For example:

```text
positive → negative
negative → positive
```

This constructs false demonstrations and tests whether the model copies the wrong labels.

---

## 4. Core tool: Logit Lens

The authors analyze what each layer predicts.

Define Transformer layer ell hidden state:


h_ell^(n)


Final output logits:


logits = W_U · LayerNorm(h_L^(n))


For intermediate layers, Logit Lens gives:


logits_ell = W_U · LayerNorm(h_ell^(n))


and the implied probability:


p_ell(t_n+1 | t_1:n)


This shows what layer ell thinks the next token should be.

The Logit Lens allows observing intermediate reasoning without extra probes.

---

## 5. Finding: Critical layer

The authors find that correct and incorrect demonstrations behave similarly in early layers:


Acc_correct approx Acc_incorrect


Then at a certain layer they diverge:


Acc_correct uparrow



Acc_incorrect downarrow


They call this the # Critical Layer — the layer where wrong imitation starts forming.

Examples:

- GPT-J: critical layer around 13sim14
- Llama2-7B: critical layer around 13sim17

---

## 6. Overthinking (core phenomenon)

Under false demonstrations, the full model accuracy drops, while early exit accuracy improves.

That is:


Early Exit > Full Model


The authors define Overthinking as cases where:


p_k > p_L


for some intermediate layer k < L.

Example:

GPT-J: running only layers 1sim16 beats the full 28 layers.

---

## 7. Important conclusion

Wrong imitation is not random error.

The model is not merely confused.

The authors define a Permuted Score to measure whether the model truly copies the wrong label.

They find that as false demonstrations increase, the model increasingly outputs:


sigma(class(x))


i.e. the wrong label.

This means:


Wrong prompt → Wrong imitation


not:


Wrong prompt → Random


---

## 8. Which components cause Overthinking?

The authors ablate:

### (1) MLP

### (2) Attention heads

They find removing late attention heads has almost the same effect as removing the entire late layer.

This suggests:


Late Attention dominates Overthinking


while MLP has smaller impact.

---

## 9. False Induction Heads (core contribution)

The authors propose special attention heads that:

1. attend historical demonstrations
2. attend same-class examples
3. copy the wrong label

They call them # False Induction Heads.

They are like induction heads, but they reproduce false knowledge.

They should satisfy:

### (1) Label-attending

Focus on old labels.

### (2) Class-sensitive

Focus on same-class examples.

### (3) Label-promoting

Promote the wrong label probability.

So:


Attend → Copy → Promote


---

## 10. Prefix Matching Score

The authors define a score that measures whether a head attends to same-class wrong labels.

Define:


PM_h = sum_i Att_h(x, y_i) 1_class(x) = class(x_i)


minus attention to other classes.

Full definition:


PM_h = sum Att_h(x, y_i) 1_same - (1)/(k-1) sum Att_h(x, y_i) 1_different


If:


PM_h uparrow


then the head is more likely to copy the wrong label.

---

## 11. Ablation experiments

The authors identify the top 5 heads by PM score and delete them.

Across 14 datasets, the accuracy gap caused by false demonstrations is reduced by:


38.9% 


while correct demonstrations are almost unaffected.

This shows these heads causally contribute to false imitation.

---

## 12. Results: Early exit

GPT-J under false demonstrations:

Full model:


37.4


with late layers removed:


47.7


Improvement:


+10.3


GPT2-XL:


42.0 → 44.6


Llama2 also shows overthinking, indicating this is not a small-model artifact.

---

## 13. Results: Head ablation

Deleting top heads reduces the accuracy gap by:


38.98%


Random head deletion reduces it by only:


3.97%


This indicates the identified heads have causal effect.

---

## 14. Main contribution

In one sentence:

> The authors show that LLMs overthink false demonstrations: late layers begin copying wrong knowledge, and this effect is driven by a few late attention heads (false induction heads).

Core contributions:

1. Define Overthinking
2. Identify Critical Layer
3. Propose False Induction Heads
4. Validate causality via head ablation

---

## 15. Limitations

The authors acknowledge that deleting heads does not fully eliminate the accuracy gap.

This means there are still unknown mechanisms.

Also, experiments are mainly based on few-shot classification and have not been fully validated on more complex generation.

---

## 16. Take-away

Problem:


False demonstrations → Hallucination


Findings:

Early layers:


Correct approx Incorrect


Late layers:


Divergence


Definition:

# Overthinking

Meaning:


Early Exit > Full Model


Cause:


False Induction Heads


Mechanism:


Attend → Copy → Promote


Contribution:

> hallucination / harmful imitation may primarily emerge in late attention mechanisms.
