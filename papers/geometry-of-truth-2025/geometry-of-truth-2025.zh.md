# The Geometry of Truth：从层间语义轨迹检测幻觉
*2025*

**别名：** 语义轨迹验真

## 1. 论文要解决什么问题？

很多 hallucination detector 都有明显代价：

- 要采样多次
- 要依赖外部验证器
- latency 较高

这篇论文想做一个 single-pass、模型内部的幻觉检测方法。

## 2. 核心思想

作者提出 Layer-wise Semantic Dynamics (LSD)。

它不只看最终输出，而是追踪 hidden states 在不同层里的语义演化。

作者的核心判断是：

- factual generation 的语义轨迹更稳定
- hallucinated generation 会在层间发生明显 drift

## 3. 方法

LSD 用 margin-based contrastive learning，
把 hidden activations 和 factual encoder 产生的 ground-truth embedding 对齐。

于是生成过程会被看成一条轨迹：

```text
layer 1 -> layer 2 -> ... -> layer L
```

然后检查这条轨迹是否一直贴近“真实语义”。

## 4. 主要结果

摘要给出的结果包括：

- F1 = 0.92
- AUROC = 0.96
- clustering accuracy = 0.89
- 相比 sampling-based baseline 加速 5x 到 20x

同时还超过了 SelfCheckGPT 和 Semantic Entropy。

## 5. 为什么重要？

这篇论文把 hallucination 看成：

```text
语义轨迹是否偏离真相
```

而不是只看最后一句话对不对。

这样做的价值在于：

- 检测更快
- 解释性更强
- 能更早看到 factual consistency 在哪一层开始漂移

## 6. Take-away

这篇论文的核心信息是：truth 在层间表示里会留下可追踪的几何轨迹，
沿着这条轨迹做 single-pass 检测，可以高效发现 hallucination。
