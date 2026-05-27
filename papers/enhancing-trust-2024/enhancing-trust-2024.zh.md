# Enhancing Trust in Large Language Models with Uncertainty-Aware Fine-Tuning
*2024*

**别名：** 会说没把握

## 1. 论文要解决什么问题？

LLM 经常会生成看起来很自然、但事实上错误的回答。

更麻烦的是：

- 模型的置信度并不可靠
- 用户很难判断它到底有没有把握
- hallucination 检测也会因此更难做

这篇论文的目标是让模型更会表达“不确定”。

## 2. 核心思想

作者提出 uncertainty-aware fine-tuning。

不是只优化标准的 causal language modeling loss，
而是把“答案质量”和“置信度校准”一起纳入训练目标。

也就是说，训练目标不再只是：

```text
答对
```

而是：

```text
答对 + 知道自己有没有把握
```

## 3. 方法直觉

论文设计了一个基于 decision theory 的 uncertainty-aware causal LM loss。

它希望模型做到两件事：

- 知道时，正常高质量作答
- 不确定时，给出更可靠的 uncertainty signal

也就是把 uncertainty 从“附带产物”变成“显式训练目标”。

## 4. 主要结论

作者报告该方法能够：

- 改善 free-form QA 中的 uncertainty calibration
- 提升 hallucination detection 能力
- 更好识别 out-of-domain prompt
- 同时不牺牲回答准确率

## 5. 为什么重要？

很多 factuality 方法都在想办法让模型“更敢答对”。

这篇论文换了一个角度：

> 有时候更可信的行为，不是更自信，而是更会承认自己不确定。

这在模型本来就没有知识时尤其关键。

## 6. Take-away

可信的模型不只是“答得好”，还应该“知道自己什么时候可能答错”。
