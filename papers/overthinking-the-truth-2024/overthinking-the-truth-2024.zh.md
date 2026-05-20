# OVERTHINKING THE TRUTH：理解语言模型如何处理错误演示
*ICLR 2024*

## 1. 论文要解决什么问题？

LLM 可以通过 few-shot demonstrations 学习任务。

但问题是模型不仅学习任务，还会学习：


错误标签



虚假模式



有害示例


例如错误 few-shot 示例：

```text
Positive → Negative
```

模型会复制错误标签。

作者问题：

> 为什么模型会模仿错误演示？ 
> 错误 imitation 在 Transformer 哪些层产生？ 
> 哪些组件导致这个现象？

---

## 2. 核心观察

作者发现面对错误演示，早期计算仍倾向正确答案，而后期计算开始相信错误示例。

即：

早层：


正确演示 approx 错误演示


后层：


正确 ≠ 错误


出现明显分叉。

作者称这种现象为：

# Overthinking

意思是：

> 模型计算越多，越容易被错误演示误导。

提前停止反而更正确。

---

## 3. 实验设置：错误演示

few-shot 分类输入：


(x_i, y_i)


真实标签：


y_i = class(x_i)


作者通过置换标签构造错误 prompt：


y_i = sigma(class(x_i))


其中 sigma 是循环置换。

例如：

```text
positive → negative
negative → positive
```

构造错误演示，观察模型是否复制错误标签。

---

## 4. 核心工具：Logit Lens

作者分析每层到底预测什么。

定义 Transformer 第 ell 层隐藏状态：


h_ell^(n)


最终输出 logits：


logits = W_U · LayerNorm(h_L^(n))


对中间层使用 Logit Lens：


logits_ell = W_U · LayerNorm(h_ell^(n))


得到：


p_ell(t_n+1 | t_1:n)


即第 ell 层认为下一个 token 是什么。

Logit Lens 可以观察中间推理过程，无需额外探针。

---

## 5. 发现：Critical Layer

作者发现正确和错误演示在早期层表现相似：


Acc_correct approx Acc_incorrect


到某层后出现分叉：


Acc_correct uparrow



Acc_incorrect downarrow


作者称其为 # Critical Layer，错模仿开始形成的层。

例如：

- GPT-J：约 13sim14
- Llama2-7B：约 13sim17

---

## 6. Overthinking（核心现象）

对于错误演示，完整模型准确率下降，提前退出准确率提高。

即：


Early Exit > Full Model


作者定义 Overthinking 为：

存在某个中间层 k < L 使得：


p_k > p_L


例如 GPT-J，仅运行层 1sim16 优于完整 28 层。

---

## 7. 一个重要结论

错误模仿不是随机错误。

模型不是迷惑的。

作者定义 Permuted Score 用于衡量模型是否真的复制错误标签。

结果表明，随着错误演示增加，模型越来越输出：


sigma(class(x))


即错误标签。

说明：


错误 prompt → 错误 imitation


而不是：


错误 prompt → 随机


---

## 8. 哪些组件导致 Overthinking？

作者分别 ablate：

### (1) MLP

### (2) Attention Heads

发现去掉 late attention heads 与去掉整个 late layer 效果几乎相同。

这表明：


Late Attention 主导 Overthinking


而 MLP 影响较小。

---

## 9. False Induction Heads（核心贡献）

作者提出存在特殊 attention heads， 它们会：

1. 关注历史演示
2. 关注相同类别样本
3. 复制错误标签

称为 # False Induction Heads。

它们类似 induction heads，但复制错误知识。

应满足：

### (1) Label-attending

关注旧标签。

### (2) Class-sensitive

关注相同类别示例。

### (3) Label-promoting

提升错误标签概率。

即：


Attend → Copy → Promote


---

## 10. Prefix Matching Score

作者定义一个评分，衡量一个 head 是否关注同类错误标签。

定义：


PM_h = sum_i Att_h(x, y_i) 1_class(x)=class(x_i)


减去对其他类别的注意力。

完整定义：


PM_h = sum Att_h(x, y_i) 1_same - (1)/(k-1) sum Att_h(x, y_i) 1_different


若：


PM_h uparrow


说明该 head 更可能复制错误标签。

---

## 11. 消融实验

作者找到 PM score 最大的 5 个 heads 并删除它们。

在 14 个数据集上，错误演示带来的准确率差距减少了：


38.9%


而正确演示几乎不受影响。

说明这些 heads 确实导致了错误模仿。

---

## 12. 结果：Early Exit

GPT-J 在错误演示下：

完整模型：


37.4


去掉后层：


47.7


提升：


+10.3


GPT2-XL：


42.0 → 44.6


Llama2 也存在 overthinking，说明这不是小模型现象。

---

## 13. 结果：Head ablation

删除 top heads 后，accuracy gap 下降：


38.98%


随机删除 heads 仅：


3.97%


说明找到的 heads 具有因果作用。

---

## 14. 主要贡献

一句话：

> 作者发现 LLM 面对错误演示时会 overthink：后层开始复制错误知识；这种现象主要来自少数 late attention heads（false induction heads）。

核心贡献：

1. 提出 Overthinking
2. 发现 Critical Layer
3. 提出 False Induction Heads
4. 用 head ablation 验证因果关系

---

## 15. 局限

作者承认删除 heads 不能完全消除 accuracy gap。

这说明仍有未知机制。

另外实验主要基于 few-shot classification，尚未充分验证到复杂 generation。

---

## 16. Take-away

问题：


错误演示 → 幻觉


发现：

早层：


正确 approx 错误


后层：


分叉


定义：

# Overthinking

即：


Early Exit > Full Model


原因：


False Induction Heads


机制：


Attend → Copy → Promote


贡献：

> 幻觉 / 有害模仿很可能主要发生在后层 attention 机制中。
