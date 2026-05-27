# VERIFYING CHAIN-OF-THOUGHT REASONING VIA ITS COMPUTATIONAL GRAPH
*2026*

**别名：** 算图验推理

## 1. 论文要解决什么问题？

已有 chain-of-thought verification 方法大多从下面两类信号判断推理对不对：

- final outputs
- intermediate activations

这些方法能告诉我们“错了没有”，
但往往不能很好解释“为什么会错”。

这篇论文想做一个更 white-box 的验证方法。

## 2. 核心思想

作者提出 Circuit-based Reasoning Verification (CRV)。

他们把 attribution graph 看成模型潜在 reasoning circuit 的执行轨迹，
再去判断：

> 正确推理和错误推理的图结构是否具有不同 fingerprint？

也就是说，验证对象从答案本身变成了计算图本身。

## 3. 方法直觉

CRV 从 attribution graph 中提取结构特征，
训练一个分类器来区分正确和错误的推理步骤。

更重要的是，作者认为这些结构特征不仅能做 detection，还能做 debugging：

- 帮你定位失败模式
- 指导对单个 feature 做 targeted intervention

## 4. 主要发现

摘要强调了三点：

- 图结构对 reasoning error 很有预测力
- 错误 signature 具有 domain-specific 特征
- 利用这些分析可以反向修正 faulty reasoning

这让方法从“发现错误”进一步走向“因果修复”。

## 5. 为什么和事实性有关？

hallucination 和 reasoning failure 往往彼此相关。

如果我们能直接检查模型内部计算是否连贯，
就比只看最终 token 序列多了一层更强的 white-box signal。

这对未来的 factuality monitor 很有启发。

## 6. Take-away

这篇论文说明：推理是否可靠，不一定只能看答案，
也可以直接看模型自己的 computational graph，并据此做 mechanistic repair。
