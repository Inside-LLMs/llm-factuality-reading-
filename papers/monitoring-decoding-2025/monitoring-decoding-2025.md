---
title: Monitoring Decoding: Mitigating Hallucination via Evaluating the Factuality of Partial Response during Generation
authors: Chang et al.
year: 2025
venue: ACL 2025
keywords:
  - decoding monitoring
  - partial response evaluation
  - hallucination mitigation
  - tree search
status: draft
---

# Monitoring Decoding: Mitigating Hallucination via Evaluating the Factuality of Partial Response during Generation

## 1. What problem does the paper solve?

Existing hallucination mitigation methods include:

- Best-of-N
- Self-consistency
- generating multiple full responses and filtering

Problem:

These methods require:

\[
\text{Generate multiple full responses}
\]

This leads to:

1. high latency
2. large computation
3. repeated errors if the model is highly confident in wrong tokens

The authors ask:

> Must we resample complete responses?  
> Can we correct only the critical tokens that cause hallucination?

---

## 2. Core observation

The authors find that only a small number of critical tokens cause the final hallucination.

That is:

\[
\text{Few critical tokens} \Rightarrow \text{Hallucination}
\]

Many tokens are easy and stable, and do not affect factuality.

The real danger comes from:

\[
\text{small number of difficult tokens}
\]

Thus the goal becomes:

> detect dangerous tokens in real time  
> revise only those tokens

instead of:

> regenerating the entire response

---

## 3. Monitoring Decoding (MD)

Monitoring Decoding consists of two parts.

### (1) In-process detection

During generation:

- monitor tokens in real time
- decide whether a token is likely to hallucinate
- if safe: continue generating
- if risky: enter revision

### (2) Tree-based revision

For risky tokens:

- resample candidates
- perform tree search
- keep the more factual tokens

Overall flow:

Prompt

↓

Generate

↓

Monitor

↓

Accept / Reject

↓

Tree revision

↓

Replace tokens

↓

Continue

---

## 4. Basic definitions

Input:

\[
x = \{x_1, \dots, x_T\}
\]

Base model:

\[
f_\theta
\]

At generation step \(t\):

\[
y^t = f_\theta(x, y^1, \dots, y^{t-1})
\]

where:

\[
y^t = (y_1^t, \dots, y_m^t)
\]

One generation step produces:

\[
m\]

tokens.

---

## 5. Monitor function (core)

Introduce a reference model,

\[
f^*
\]

typically larger and more knowledgeable.

Define:

\[
r_\beta(y^t \| x, y^{<t}) = \sum_{s=1}^m w_s^t \cdot \frac{p^*(y_s^t \| y^{<t}, y_{<s}^t)}{p_\theta(y_s^t \| y^{<t}, y_{<s}^t)}
\]

where:

\[
w_s^t = \frac{1}{\|(y^{<t}, y_{<s}^t)\|}
\]

Earlier tokens receive higher weight.

---

## 6. Monitor intuition

For correct tokens:

\[
p_\theta \uparrow
\]

and

\[
p^* \uparrow
\]

so

\[
r_\beta \uparrow
\]

indicating safety.

For incorrect tokens:

\[
p_\theta \uparrow
\]

but

\[
p^* \downarrow
\]

so

\[
r_\beta \downarrow
\]

indicating danger.

Core idea:

\[
\text{Low } r_\beta \Rightarrow \text{Hallucination}
\]

---

## 7. Token acceptance rule

Acceptance probability:

\[
p(\text{accept } y^t) = \min\{1, r_\beta\}
\]

Threshold:

\[
\gamma^t = \gamma_0 \sum_s w_s^t
\]

If:

\[
r_\beta > \gamma^t
\]

accept.

Otherwise reject.

If:

\[
r_\beta < \gamma^t
\]

trigger revision.

---

## 8. Tree-based revision

For risky tokens, launch tree search.

Each step:

- sample Top-N candidate tokens
- form \(N^m\) paths
- prune to Top-K paths by monitor score

Keep the path with the highest:

\[
\operatorname{TopK}(r_\beta)
\]

Finally select:

\[
\arg\max r_\beta
\]

as the revised token sequence.

---

## 9. Full algorithm

Prompt

↓

Generate \(m\) tokens

↓

Compute \(r_\beta\)

↓

If \(r_\beta > \gamma\): accept

otherwise:

Tree search

↓

Top-N sample

↓

Top-K prune

↓

Replace token

↓

Continue decoding

---

## 10. Experiment: TruthfulQA

Llama-2:

Greedy:

\[
\text{Truth*Info} = 37.9
\]

MD:

\[
44.1
\]

Improvement:

\[
+6.2
\]

Gemma-2:

Greedy:

\[
43.6
\]

MD:

\[
50.2
\]

Improvement:

\[
+6.6
\]

Maximum Truth improvement:

\[
+9.2
\]

---

## 11. Experiment: TriviaQA / NQ

Llama-3:

TriviaQA:

\[
72.4 \rightarrow 80.8
\]

Improvement:

\[
+8.4
\]

NQ-open:

\[
39.6 \rightarrow 47.4
\]

Improvement:

\[
+6.8
\]

Gemma-2:

TriviaQA:

\[
54.0 \rightarrow 64.6
\]

Improvement:

\[
+10.6
\]

---

## 12. Experiment: GSM8K

Llama-3:

\[
81.4 \rightarrow 85.2
\]

Improvement:

\[
+3.8
\]

Gemma-2:

\[
60.9 \rightarrow 79.9
\]

Improvement:

\[
+19.0
\]

This indicates the method does not hurt reasoning and may even improve it.

---

## 13. Efficiency

Latency:

Greedy:

\[
19.94\ \mathrm{ms/token}
\]

MD:

\[
113.78\ \mathrm{ms/token}
\]

About:

\[
5.7\times
\]

Compared to:

USC:

\[
12.3\times
\]

FSC:

\[
15.9\times
\]

MD is faster.

Reason:

It only revises dangerous tokens instead of regenerating the full response.

---

## 14. Ablation study

Increasing:

\[
N
\]

improves performance.

But for:

\[
N > 6
\]

returns saturate.

Threshold:

\[
\gamma_0
\]

has limited impact, indicating the method is stable.

---

## 15. Key finding

Only a small fraction of tokens are resampled.

For example, TriviaQA with Llama-3:

Only:

\[
17.9\%\]

tokens were modified.

Yet the improvement was significant.

This shows:

> hallucination mostly comes from a small number of critical tokens.

---

## 16. Main contribution

In one sentence:

> The authors argue that hallucination arises from a few dangerous tokens, so they propose real-time decoding monitoring and selective revision of high-risk tokens rather than full-response resampling.

Contributions:

1. In-process monitoring
2. A monitor score
3. Tree-based revision
4. Selective token resampling instead of full-response sampling

---

## 17. Limitations

If the correct knowledge is absent:

\[
\text{Missing knowledge} \Rightarrow \text{No correction}
\]

Future work can combine this with external knowledge.

---

## 18. Take-away

Problem:

\[
\text{Hallucination}
\]

Observation:

\[
\text{Few critical tokens} \Rightarrow \text{Hallucination}
\]

Detection:

\[
r_\beta = \sum w \frac{p^*}{p_\theta}
\]

Rule:

\[
r_\beta < \gamma \Rightarrow \text{Reject}
\]

Revision:

Tree Search

↓

Top-N sample

↓

Top-K prune

↓

Replace token

Core idea:

> Do not resample the full response  
> revise only dangerous tokens  
> improve factuality and reduce cost
