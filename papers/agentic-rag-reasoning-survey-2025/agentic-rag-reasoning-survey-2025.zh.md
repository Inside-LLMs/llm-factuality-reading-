# Towards Agentic RAG with Deep Reasoning：RAG 与推理系统综述
*2025*

**别名：** 推理RAG全景图

## 1. 论文要解决什么问题？

经典 RAG 能提升 factuality，因为它把外部知识注入进来；
但一旦任务需要多步推理，普通 RAG 往往不够。

反过来，纯 reasoning 系统虽然会推理，
却又容易 hallucinate 或 grounding 不稳。

这篇综述研究的就是：

> retrieval 和 reasoning 到底该怎么结合？

## 2. 核心思想

作者把 RAG-reasoning 系统分成三大类：

- Reasoning-Enhanced RAG
- RAG-Enhanced Reasoning
- Synergized / Agentic RAG-Reasoning

也就是说，它的核心贡献是给出一个设计空间地图，
说明 retrieval 和 reasoning 不只是串联关系，还可以循环耦合。

## 3. 关键视角

这篇综述最重要的一句话可以概括成：

```text
retrieval 提供前提
reasoning 把前提变成答案
```

而更强的系统通常会形成闭环：

```text
search -> reason -> search again -> revise
```

这就是 agentic 的来源。

## 4. 综述覆盖了什么？

论文系统整理了：

- reasoning 如何增强 RAG 各阶段
- 检索到的知识如何支持复杂推理
- 相关数据集与 benchmark
- trustworthiness、多模态、自适应、人机协作等开放问题

## 5. 为什么和幻觉相关？

它不是一篇直接做 hallucination mitigation 的论文，
但对事实性研究很重要，因为它说明了一个趋势：

> 更强的 factuality，越来越依赖 retrieval 和 reasoning 的联合设计，
> 而不是把两者当成彼此独立的补丁。

## 6. Take-away

这篇综述适合作为“推理型 RAG”路线图，
帮助理解 grounded retrieval 和 multistep reasoning 是如何逐步融合成 agentic loop 的。
