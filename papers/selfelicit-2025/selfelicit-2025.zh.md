# SelfElicit
*ACL 2025*

**别名：** 自提证据

## 1. 论文要解决什么问题？

即使正确证据已经在 prompt 里，LM 也常常不能充分利用它。

当上下文里有噪声、无关信息很多时，这个问题会更严重。

作者的问题是：

> 模型能不能先自己把关键证据找出来，再回答？

## 2. 核心思想

SELFELICIT 是一个 inference-time 方法。

它会自动把输入上下文中的关键信息显式高亮出来，
帮助模型把注意力集中到真正相关的证据上。

核心思路就是：

```text
先找证据
再给答案
```

## 3. 方法直觉

作者利用 deeper-layer attention，
把模型自身的 evidence-finding 能力拿来反过来帮助解码。

它的优点是：

- 不需要额外训练
- 不需要 iterative prompting
- 直接在推理时改善 evidence utilization

所以这是一个比较轻量、但很针对性的干预。

## 4. 主要结果

论文报告：

- 在多个 evidence-based QA 任务上都有稳定提升
- 对不同 LM family 都有效
- 计算开销保持较低

它提升的关键不是增加知识，而是更好利用已有上下文。

## 5. 为什么和幻觉有关？

有些 hallucination 是因为模型本来就不知道答案。

还有一类是：

```text
证据已经给了
但模型没用好
```

SELFELICIT 主要解决的就是第二类问题。

## 6. Take-away

这篇论文说明：很多 grounded generation 的失败，
并不是缺知识，而是没把关键证据提取出来。
