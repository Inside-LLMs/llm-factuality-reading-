---
title: "Sparse Feature Circuits: Discovering and Editing Interpretable Causal Graphs in Language Models"
authors: Marks 等
year: 2025
venue: ICLR 2025
keywords:
  - sparse feature circuits
  - causal graphs
  - interpretable editing
  - attribution patching
  - SHIFT
status: draft
---

# Sparse Feature Circuits: Discovering and Editing Interpretable Causal Graphs in Language Models

## 1. 论文要解决什么问题？

之前 interpretability 有两条主线：

### 路线1：Circuit Analysis

研究：

```text
attention heads
MLP
neurons
```

问题：这些组件通常是 polysemantic 的，难以解释。

### 路线2：SAE / Feature Discovery

研究：

```text
Sparse feature
```

能够得到可解释 feature，但只能回答“feature 是什么”，
无法回答“feature 如何组成计算”。

作者的问题是：能否发现一个可解释、具有因果性的 feature circuit，
把 feature 串成一个从 feature 到输出的计算图？

## 2. 核心思想

作者提出 Sparse Feature Circuit：

节点包括：

- SAE feature
- SAE reconstruction error

边表示：

```text
feature_i causally influences feature_j
```

最终目标是得到：

```text
feature → feature → feature → prediction
```

这就是一个 interpretable causal graph。

## 3. 基础：Sparse Autoencoder

给定 activation：

\[
x \in \mathbb{R}^d
\]

SAE 分解为：

\[
x = \hat x + \epsilon(x) = \sum_{i=1}^{d_{SAE}} f_i(x) v_i + b + \epsilon(x)
\]

其中：

- $f_i(x)$ 表示 feature activation
- $v_i$ 表示 feature direction
- $\epsilon(x)$ 表示 reconstruction error

作者强调误差项不丢弃，
而是作为 circuit 的一个节点来建模。

## 4. Causal Effect：Indirect Effect (IE)

用来衡量某个节点对最终行为的影响。

定义：

\[
IE(m, a, x_{clean}, x_{patch}) = m(x_{clean} \mid do(a = a_{patch})) - m(x_{clean})
\]

其中 $m$ 是度量，例如：

```text
log P(has) - log P(have)
```

如果 $IE$ 上升，说明该 feature 对行为贡献大。

## 5. Attribution Patching Approximation

直接计算真实 $IE$ 很贵。

作者采用 Attribution Patching 近似：

\[
\hat{IE}_{atp}(m, a) = \nabla_a m \cdot (a_{patch} - a_{clean})
\]

优点是只需：

- 2 次 forward
- 1 次 backward

即可估计所有 feature 的影响。

为了提高精度，进一步使用 Integrated Gradients：

\[
\hat{IE}_{ig}(m, a) = \frac{1}{N} \sum_\alpha \nabla_a m\big\|_{\alpha a_{clean} + (1-\alpha)a_{patch}} (a_{patch} - a_{clean})
\]

论文最终主要采用 $IE_{ig}$ 作为因果归因指标。

## 6. Sparse Feature Circuit Discovery

输入：

- 数据集 $D$
- 模型 $M$
- 指标 $m$（例如 has vs have）

步骤：

### Step 1

用 SAE 分解 hidden state，得到：

```text
feature + error
```

### Step 2

计算每个 feature 的

\[
IE(m, a)
\]

### Step 3

保留 \(\|IE\| > T_N\) 的重要节点。

### Step 4

计算 feature-feature edge，保留 \(\|IE\| > T_E\) 的重要边。

这样得到一个 Sparse Feature Circuit。

整体流程是：

```text
Prompt → SAE decomposition → Feature attribution → Filter nodes → Filter edges → Circuit
```

## 7. 实验：Subject-Verb Agreement

任务例如：

```text
The girl ... has
The girls ... have
```

目标是理解模型为何预测 has/have。

作者发现的 circuit 形如：

```text
Noun number detector
↓
RC/PP detector
↓
NP tracker
↓
Verb discriminator
↓
has / have
```

这说明模型内部存在类似语法算法的结构。

## 8. Faithfulness（重要指标）

定义：Circuit 保留后，能解释多少行为。

\[
Faithfulness = \frac{m(C) - m(\varnothing)}{m(M) - m(\varnothing)}
\]

理想值为 1。

结果表明：

在 Pythia-70M 上，约 100 个 feature 就能解释大部分行为，
而需要约 1500 个 neuron 才能达到类似效果。

说明：

```text
Sparse feature >> Neuron
```

## 9. Completeness

Circuit 是否遗漏行为？

定义观察：

\[
M \setminus C
\]

还能保留多少能力。

理想值为 0。

论文发现：删除少量 feature 即可摧毁目标行为，
说明发现到的是真实 causal mechanism。

## 10. 应用：SHIFT（核心贡献）

Sparse Human-Interpretable Feature Trimming。

目标是删掉 task-irrelevant feature，改变模型泛化。

步骤：

1. 发现 classifier circuit
2. 人工检查 feature
3. 删除 spurious feature
4. 重新训练 classifier

即：

```text
Interpret → Edit → Control model
```

## 11. SHIFT 实验

训练集具有严重性别偏见：

```text
male → professor
female → nurse
```

SHIFT 删除 gender feature 后：

- Profession: $61.9 \rightarrow 88.5$
- Gender: $87.4 \rightarrow 54.0$
- Worst-group: $24.4 \rightarrow 76.0$

再训练后 Worst-group 达到 $89.0$，接近 Oracle。

说明解释得到的 feature 真正可编辑。

## 12. 无监督 Circuit Discovery

作者进一步自动发现：

```text
context → behavior cluster → feature circuit
```

例如发现：

- Successor pattern：A1 A2 A3 → A4
- “to” + infinitive：want to / need to / like to

说明可以自动挖掘 Transformer 内部算法。

## 13. 本文最大贡献

一句话：作者把解释对象从单个 sparse feature 推进到 sparse feature circuit，
得到 feature 之间的因果计算图，并证明这些 circuit 可用于模型编辑、去偏和自动解释。

核心贡献：

1. 提出 Sparse Feature Circuit
2. 发现 Feature-level causal graph
3. 提出 SHIFT，实现解释 → 编辑 → 去偏
4. 实现大规模自动 circuit discovery

## 14. 局限

作者承认：

1. 依赖高质量 SAE，训练成本高。
2. SAE 无法覆盖所有 hidden representation，遗漏不可解释部分。
3. 大量评估仍偏 qualitative。
4. Feature labeling 依赖人工。

## 15. Take-away

之前：

```text
Neuron → behavior
```

后来：

```text
Sparse feature → behavior
```

这篇：

```text
Sparse feature → Sparse feature → Sparse feature → Output
```

核心变化：真正的计算单位可能不是 neuron，也不是单个 feature，而是 sparse feature circuit。
