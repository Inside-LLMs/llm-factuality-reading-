---
title: Sparse Autoencoders Find Highly Interpretable Features in Language Models
authors: Cunningham 等
year: 2023
venue: 2023
keywords:
  - 稀疏自编码器
  - 可解释特征
  - 机制可解释性
  - superposition
status: draft
---

# Sparse Autoencoders Find Highly Interpretable Features in Language Models

## 1. 问题：为什么 neuron 不可解释？

以前 mechanistic interpretability 认为：

\[
Neuron = Feature
\]

但发现很多神经元是 polysemantic 的，混合表示例如：

- 法语
- 括号
- 毒品
- 数学

这种现象叫 polysemanticity。

作者认为原因不是 neuron 差，而是：

\[
N_{feature} > N_{dimension}
\]

模型必须压缩：

\[
Many Features \rightarrow Few Dimensions
\]

多个 feature 共享一个方向，形成 superposition。

论文核心假设：

```text
polysemantic neuron = projection(superposed features)
```

---

## 2. 想法：学习真实 feature basis

假设 residual activation：

\[
x \in \mathbb{R}^d
\]

真实 feature 是：

\[
g_1, g_2, \dots, g_n
\]

希望：

\[
x = \sum_i a_i g_i
\]

其中稀疏系数 \(a_i\)：

大多数 \(a_i = 0\)

只有少数 feature 激活。

问题变成寻找 feature 字典，类似稀疏编码。

---

## 3. 方法：Sparse Autoencoder

作者对模型 activation 训练稀疏自编码器。

Encoder：

\[
c = \text{ReLU}(Mx + b)
\]

Decoder：

\[
\hat x = M^T c
\]

这里 \(c\) 不是 hidden state，而是 feature activation。

它表示真正可解释的 feature。

---

## 4. SAE 损失

目标是在重建 activation 的同时保持稀疏：

\[
L(x) = \|x - \hat x\|_2^2 + \alpha \|c\|_1
\]

重建损失：

\[
\|x - \hat x\|^2
\]

稀疏损失：

\[
\alpha \|c\|_1
\]

随着 \(\alpha\) 增大，feature 更稀疏，但重建更差。

论文观察到平滑的 tradeoff，没有唯一最佳 \(\alpha\)。

---

## 5. SAE 学出的 feature 长什么样？

作者发现许多 feature 近乎单义（monosemantic）。

例如：

- 只对撇号 `'
- 对 `don't`, `won't`, `wouldn't` 激活
- 识别代码上下文中的 `If`
- 专门表示括号 `(` 或 `)`

这些 feature 远比 neuron 更可解释。

---

## 6. 自动解释（Autointerpretability）

作者用 GPT 为 feature 做注释。

流程：

1. 查看 feature 的 top 激活文本
2. 让 GPT 写解释，如“legal terms”
3. 让 GPT 预测其他文本激活
4. 计算相关系数作为 interpretability score

分数越高，feature 越单义。

---

## 7. SAE 比 PCA/ICA 更可解释

论文比较了：

- 原始 neuron
- 随机方向
- PCA
- ICA
- SAE

SAE 的 interpretability 显著最高。

Figure 2 基本上全赢。

核心结论：

```text
SAE features > PCA components > neurons
```

---

## 8. SAE 不只是解释，还能做因果分析

作者研究 IOI 任务：

```text
Alice gave snack to ...
```

预测 Bob 还是 Alice。

他们做 activation patching：

\[
c_i \rightarrow \bar c_i
\]

观察输出变化。

发现少量 SAE feature 就能精确控制行为，比 PCA 更准确。

---

## 9. 一个惊人的发现

修改几个 SAE feature 就能改变模型最终预测。

说明这些 feature 是因果真实的，

而不是统计幻觉。

即：

```text
feature -> computation -> output
```

这是 SAE 真正重要的地方。

---

## 10. Circuit Discovery

作者进一步追踪某个 feature 的来源。

例如关闭括号 `)` feature 时，发现上游 feature 包括：

- dates
- acronyms
- phrases

形成跨层因果树：

```text
feature -> feature -> feature -> token
```

开始接近 Transformer circuit。

---

## 11. 局限

作者承认 SAE 仍不完美：

重建误差仍存在。

替换 residual 后 perplexity 从 25 增加到 40，

说明仍有信息丢失。

另外，MLP layer 很难学，dead features 多。

---

## 12. 这篇论文真正改变了什么

以前：

```text
Neuron = feature
```

现在：

```text
Feature ≠ neuron
Feature = sparse direction
Neuron = mixture(feature)
```

这是 mechanistic interpretability 的一次重大转向。

后续 Anthropic 体系几乎建立在这条路上：

Toy Models of Superposition
→ SAE
→ Monosemantic Features
→ Feature Steering
→ Circuit Discovery
→ Model Editing

---

## 一句话总结

这篇论文证明：

> Transformer 的真正计算单位可能不是 neuron，而是隐藏在 activation 空间里的 sparse features；SAE 能把这些 feature 从 superposition 中解出来，并得到可解释、可操控、具有因果性的内部表示。

这就是为什么现在大多数 LLM interpretability 研究，几乎都会先学 SAE。
