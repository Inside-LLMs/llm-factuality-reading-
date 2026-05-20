# Sparse Feature Circuits: Discovering and Editing Interpretable Causal Graphs in Language Models
*ICLR 2025*

## 1. 论文要解决什么问题？

之前 interpretability 主要有两条路线：

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

可以得到可解释 feature，但只能回答“feature 是什么”，
无法回答“feature 如何组成计算”。

作者的问题是：是否可以发现一个可解释、具有因果性的 feature circuit，
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

最终得到：

```text
feature → feature → feature → prediction
```

形成 interpretable causal graph。

## 3. 基础：Sparse Autoencoder

给定 activation：


x in mathbbR^d


SAE 分解为：


x = hat x + epsilon(x) = sum_i=1^d_SAE f_i(x) v_i + b + epsilon(x)


其中：

- f_i(x) 表示 feature activation
- v_i 表示 feature direction
- epsilon(x) 表示 reconstruction error

作者强调误差项不丢弃，而是作为 circuit 节点。

## 4. Causal Effect：Indirect Effect (IE)

衡量某节点是否影响最终行为。

定义：


IE(m, a, x_clean, x_patch) = m(x_clean | do(a = a_patch)) - m(x_clean)


其中 m 是 metric，例如：

```text
log P(has) - log P(have)
```

如果 IE 上升，说明该 feature 对行为贡献大。

## 5. Attribution Patching Approximation

真实计算 IE 成本高。

作者用 Attribution Patching 近似：


hat IE_atp(m, a) = nabla_a m · (a_patch - a_clean)


优点是仅需：

- 2 次 forward
- 1 次 backward

即可估计所有 feature 的影响。

为了提升精度，进一步使用 Integrated Gradients：


hat IE_ig(m, a) = (1)/(N) sum_alpha nabla_a m ||_alpha a_clean + (1-alpha)a_patch (a_patch - a_clean)


论文主要采用 IE_ig。

## 6. Sparse Feature Circuit Discovery

输入：

- 数据 D
- 模型 M
- 指标 m（如 has vs have）

步骤：

### Step 1

用 SAE 分解 hidden state，得到 feature 和 error。

### Step 2

计算每个 feature 的


IE(m, a)


### Step 3

保留 ||IE|| > T_N 的重要节点。

### Step 4

计算 feature-feature edge，保留 ||IE|| > T_E 的重要边。

最终得到 Sparse Feature Circuit。

整体流程：

```text
Prompt → SAE decomposition → Feature attribution → Filter nodes → Filter edges → Circuit
```

## 7. 实验：Subject-Verb Agreement

任务如：

```text
The girl ... has
The girls ... have
```

目标理解模型为何预测 has/have。

作者发现的 circuit 如：

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

说明模型内部存在类似语法算法的结构。

## 8. Faithfulness（重要指标）

定义：Circuit 保留后还能解释多少行为。


Faithfulness = (m(C) - m(varnothing))/(m(M) - m(varnothing))


理想值为 1。

结果显示：

Pythia-70M 上约 100 个 feature 就能解释大部分行为，
而需要约 1500 个 neuron 才能达到类似效果。

说明：

```text
Sparse feature >> Neuron
```

## 9. Completeness

Circuit 是否遗漏行为？

通过观察：


M setminus C


还能保留多少能力。

理想值为 0。

结果表明：删除少量 feature 即可摧毁目标行为，说明找到的是真实 causal mechanism。

## 10. 应用：SHIFT（核心贡献）

Sparse Human-Interpretable Feature Trimming。

目标：删掉 task-irrelevant feature，改变模型泛化。

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

训练集：

```text
male → professor
female → nurse
```

SHIFT 删除 gender feature 后：

- Profession: 61.9 → 88.5
- Gender: 87.4 → 54.0
- Worst-group: 24.4 → 76.0

再训练后 Worst-group 达到 89.0，接近 Oracle。

说明解释得到的 feature 真正可编辑。

## 12. 无监督 Circuit Discovery

作者进一步自动发现：

```text
context → behavior cluster → feature circuit
```

例如：

- Successor pattern：A1 A2 A3 → A4
- “to” + infinitive：want to / need to / like to

说明可以自动挖掘 Transformer 内部算法。

## 13. 本文最大贡献

一句话：作者把解释对象从单个 sparse feature 推进到 sparse feature circuit，得到 feature之间的因果计算图，并证明这些 circuit 可用于模型编辑、去偏和自动解释。

核心贡献：

1. 提出 Sparse Feature Circuit
2. 发现 feature-level causal graph
3. 提出 SHIFT，实现解释 → 编辑 → 去偏
4. 实现大规模自动 circuit discovery

## 14. 局限

作者承认：

1. 依赖高质量 SAE，训练成本高。
2. SAE 无法覆盖所有 hidden representation，遗漏部分不可解释。
3. 大量评估仍偏 qualitative。
4. feature labeling 依赖人工。

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
