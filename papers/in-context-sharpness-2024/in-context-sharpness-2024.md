---
title: "In-Context Sharpness as Alerts: An Inner Representation Perspective for Hallucination Mitigation"
authors: Chen 等
year: 2024
venue: 2024
keywords:
  - hallucination mitigation
  - hidden states
  - activation entropy
  - constrained decoding
status: draft
---

# In-Context Sharpness as Alerts: An Inner Representation Perspective for Hallucination Mitigation

## 1. 论文要解决什么问题？

LLM 经常生成 factual error / hallucination。

本文目标：

> 不使用外部知识库  
> 不做额外 fine-tuning  
> 只利用模型内部 hidden states  
> 在 decoding 阶段缓解 hallucination

---

## 2. 核心问题

作者想回答：

> 模型内部表示是否包含 hallucination 的信号？

换句话说：

当模型生成正确答案和错误答案时，它的 hidden states 是否有可区分的模式？

---

## 3. 核心观察

正确答案通常具有更强的：

> in-context sharpness

也就是：

正确 token 往往会被 prompt 中某些关键 token 强烈激活。

错误 token 的激活更分散。

因此：

\[
correct\ answer
\Rightarrow
sharp\ activation
\Rightarrow
low\ entropy
\]

\[
incorrect\ answer
\Rightarrow
diffuse\ activation
\Rightarrow
high\ entropy
\]

---

## 4. 基础符号

给定输入序列：

\[
\{v_1,\dots,v_T\}
\]

第 \(l\) 层 hidden states：

\[
\{x_1^l,\dots,x_T^l\}
\]

LM head：

\[
W
\]

原始 next-token probability：

\[
P(v_{T+1}|v_{1:T})
=
softmax(Wx_T^H)_{v_{T+1}}
\]

---

## 5. Activation Score

作者把某个位置的 hidden state 投影到 vocabulary space。

对上下文位置 \(i\) 和候选 token \(v\)，定义：

\[
s(i,v)
=
softmax(Wx_i)_v
\]

含义：

> 候选 token \(v\) 在上下文位置 \(i\) 的 hidden state 中被激活的程度

如果某个答案 token 被 prompt 中关键位置强激活，它更可能是事实正确答案。

---

## 6. In-Context Activation Distribution

给定 prompt：

\[
C=\{v_1,v_2,\dots,v_t\}
\]

候选预测 token：

\[
v_p
\]

把候选 token 对所有上下文 token 的 activation score 归一化：

\[
\tilde P(activation=i|v_p,v_{1:t})
=
\frac{
 e^{s(i,v_p)}
}{
 \sum_{m=1}^{t}e^{s(m,v_p)}
}
\]

这个分布表示：

> 候选 token \(v_p\) 的知识主要从 prompt 中哪些位置被激活出来

---

## 7. Contextual Entropy

用 entropy 衡量 in-context sharpness：

\[
E(v_p,v_{1:t})
=
-
\sum_{i=1}^{t}
\tilde P(activation=i|v_p,v_{1:t})
\log
\tilde P(activation=i|v_p,v_{1:t})
\]

如果 activation 很集中：

\[
E(v_p,v_{1:t}) \downarrow
\]

说明 sharpness 高，答案更可能正确。

如果 activation 很分散：

\[
E(v_p,v_{1:t}) \uparrow
\]

说明 sharpness 低，答案更可能 hallucinated。

---

## 8. 方法：Activation Decoding

作者把 contextual entropy 加入 decoding。

原始概率：

\[
P(v_p|v_{1:p-1})
\]

调整后：

\[
P(v_p|v_{1:p-1})
\propto
e^{-\lambda E(v_p,v_{1:t})}
P(v_p|v_{1:p-1})
\]

其中：

\[
\lambda\in[0,1]
\]

控制 entropy penalty 的强度。

---

## 9. 方法直觉

如果候选 token 的 contextual entropy 小：

\[
E(v_p,v_{1:t}) \downarrow
\]

则：

\[
e^{-\lambda E(v_p,v_{1:t})}
\]

较大，token probability 被增强。

如果候选 token 的 contextual entropy 大：

\[
E(v_p,v_{1:t}) \uparrow
\]

则：

\[
e^{-\lambda E(v_p,v_{1:t})}
\]

较小，token probability 被压低。

---

## 10. 为什么只看 prompt tokens？

计算：

\[
E(v_p,v_{1:t})
\]

时，作者只考虑原始 prompt tokens：

\[
v_{1:t}
\]

不考虑新生成的 tokens。

原因：

> 避免 decoding 过程中由已生成内容继续放大 deductive hallucination。

---

## 11. 完整流程

输入 prompt

↓

选定 informative layer

↓

对候选 token \(v_p\) 计算每个上下文位置的 activation score：

\[
s(i,v_p)
\]

↓

归一化得到：

\[
\tilde P(activation=i|v_p,v_{1:t})
\]

↓

计算 contextual entropy：

\[
E(v_p,v_{1:t})
\]

↓

调整 next-token probability：

\[
P(v_p)
\leftarrow
e^{-\lambda E(v_p,v_{1:t})}
P(v_p)
\]

↓

decode 下一个 token

---

## 12. 实验验证：entropy 是否能区分正确和错误答案？

作者在 COUNTERFACT 上验证。

发现：

正确答案：

\[
E \text{ 更小}
\]

错误答案：

\[
E \text{ 更大}
\]

在 26th / 28th layer 上，contextual entropy 区分 true / false answer 的 AUROC 约为：

\[
0.75 \sim 0.76
\]

说明 entropy 可以作为 hallucination alert signal。

---

## 13. 实验结果：TruthfulQA

在 TruthfulQA open-ended generation 上，方法提升 Truth*Info。

LLaMA2-7B-chat：

\[
55.8 \rightarrow 59.1
\]

LLaMA2-13B-chat：

\[
57.5 \rightarrow 62.3
\]

LLaMA2-70B-chat：

\[
47.1 \rightarrow 55.7
\]

最大提升：

\[
+8.6
\]

---

## 14. 实验结果：Knowledge-Seeking QA

测试数据集：

- TriviaQA
- HotpotQA
- Natural Questions

评价指标：

- Exact Match
- F1 score

结果：

Activation Decoding 在大多数设置下提升 EM / F1。

例如 LLaMA2-70B-chat：

TriviaQA F1：

\[
68.4 \rightarrow 73.2
\]

HotpotQA F1：

\[
25.5 \rightarrow 30.1
\]

NQ F1：

\[
34.1 \rightarrow 37.8
\]

---

## 15. 效率

作者可以预先计算所有 vocabulary token 的 entropy。

因为：

\[
E(v_p,v_{1:t})
\]

只依赖 prompt，不依赖之后生成的 token。

所以可以预计算一个 vocabulary-size entropy vector。

在 LLaMA-2 中：

\[
|V|=32000
\]

推理时直接查表即可。

效率结果：

相比 greedy decoding，推理时间增加约：

\[
23.4\%
\]

---

## 16. 本文最大贡献

一句话总结：

> 本文发现正确答案在模型 hidden states 中通常具有更尖锐的 in-context activation，并用 contextual entropy 量化这种 sharpness，再把它作为 decoding penalty 来降低 hallucination。

核心贡献：

1. 从 inner representation 角度分析 hallucination
2. 提出 in-context sharpness 作为 factuality signal
3. 用 contextual entropy 量化 sharpness
4. 提出 Activation Decoding
5. 无需 fine-tuning / external knowledge 即可提升 factuality

---

## 17. 局限

1. 只能缓解模型内部已有知识没有被正确激发的 hallucination

如果正确知识不在模型内部，方法无法解决。

2. 不能处理 outdated facts 或 training data 本身错误的问题

因为没有使用外部知识。

3. representation-based signal 不是万能的

修正某些错误时，可能引入新的错误。

---

## 18. Take-away

问题：

\[
LLM hallucination
\]

核心观察：

\[
correct\ token
\Rightarrow
sharper\ in-context\ activation
\Rightarrow
lower\ contextual\ entropy
\]

关键指标：

\[
E(v_p,v_{1:t})
\]

decoding 公式：

\[
P(v_p|v_{1:p-1})
\propto
e^{-\lambda E(v_p,v_{1:t})}
P(v_p|v_{1:p-1})
\]

效果：

提升 TruthfulQA、TriviaQA、HotpotQA、Natural Questions 上的 factuality。

优势：

> 不需要 fine-tuning  
> 不需要外部知识库  
> 不需要额外模型
