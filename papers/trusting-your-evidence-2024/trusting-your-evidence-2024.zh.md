# Trusting your Evidence：Hallucinate less with Context-Aware Decoding
*NAACL 2024*

**别名：** 看证据再说

## 1. 论文要解决什么问题？

语言模型经常没有充分利用输入上下文，
反而过度依赖参数里已经记住的 prior knowledge。

典型失效模式是：

```text
context 说 A
model prior 说 B
最后模型输出 B
```

这篇论文想解决的就是：

> 如何让 decoding 更相信眼前证据，而不是老习惯？

## 2. 核心思想

作者提出 Context-Aware Decoding (CAD)。

它对比两种情况下的输出分布：

- 有上下文
- 无上下文

如果某个 token 主要是被上下文支持的，就增强它；
如果某个 token 主要来自模型原有 prior，就压低它。

## 3. 方法直觉

CAD 特别适合处理下面这种冲突：

```text
上下文信息
vs
模型已有知识
```

它的目标可以概括为：

```text
先看证据，再回答
```

而且它完全发生在 inference-time，不需要额外训练。

## 4. 主要结果

论文报告：

- 在 OPT、GPT、LLaMA、FLAN-T5 等模型上都提升了 faithfulness
- 在 summarization factuality 上有明显增益
- 当 context 和 model prior 冲突时，效果尤其明显

所以它改善的不是表面 fluency，而是 context-faithfulness。

## 5. 和 DoLa 的区别

DoLa 对比的是：

```text
late layer vs early layer
```

CAD 对比的是：

```text
with context vs without context
```

两者都是 contrastive decoding，但信任的信号来源不同。

## 6. Take-away

这篇论文说明：只要在 decoding 时更明确地“信任证据”，
模型就能显著减少忽视上下文导致的 hallucination。
