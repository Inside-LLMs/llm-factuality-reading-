# Monitoring Decoding: Mitigating Hallucination via Evaluating the Factuality of Partial Response during Generation
*ACL 2025*

## 1. 论文要解决什么问题？

已有缓解 hallucination 方法：

- Best-of-N
- Self-consistency
- 多次生成完整 response 后筛选

问题：

这些方法需要：


Generate multiple full responses


导致：

1. latency 高
2. computation 大
3. 如果模型对错误 token 很自信，多次采样仍会重复错误

作者问题：

> 是否必须重采样完整回答？
>
> 能否只修正导致 hallucination 的关键 token？

---

## 2. 核心观察

作者发现：

只有少量关键 token 导致最终 hallucination。

即：


Few critical tokens
→
Hallucination


很多 token 属于：

easy tokens

它们稳定且不会影响事实性。

真正危险的是：


small number of difficult tokens


因此目标变成：

> 实时找出危险 token 
> 只修改这些 token

而不是：

> 重生成整个 response

---

## 3. Monitoring Decoding (MD)

MD 包含两个部分：

### (1) In-process Detection

生成过程中：

实时监控 token

↓

判断是否可能 hallucinate

↓

安全：

继续生成

危险：

进入 revision

---

### (2) Tree-based Revision

对危险 token：

重新采样

↓

树搜索

↓

保留更 factual token

---

总体：

Prompt

↓

Generate

↓

Monitor

↓

Accept / Reject

↓

Tree revision

↓

Replace tokens

↓

Continue

---

## 4. 基础定义

输入：


x=x_1,dots,x_T


基础模型：


f_theta


第 t 步生成：


y^t
=
f_theta
x, 
y^1, dots, 
y^t-1



其中：


y^t
=
y_1^t,dots,y_m^t


一次生成：


m


个 token。

---

## 5. Monitor Function（核心）

引入：

reference model

记：


f^*


通常：

reference model 更大

知识更多。

定义：


r_beta
(y^t||x,y^<t)
=
sum_s=1^m
 w_s^t
·
frac
 p^*(y_s^t||y^<t,y_<s^t)

 p_theta(y_s^t||y^<t,y_<s^t)



其中：


w_s^t
=
(
1
)/(
||(y^<t),y_<s^t)||



越早 token：

权重越高。

---

## 6. Monitor 的直觉

正确 token：

基础模型：


p_theta
uparrow


参考模型：


p^*
uparrow


因此：


r_beta
uparrow


安全。

错误 token：

模型很自信：


p_theta
uparrow


参考模型：


p^*
downarrow


因此：


r_beta
downarrow


危险。

核心：


Low
 r_beta
→
Hallucination


---

## 7. Token Acceptance Rule

接受概率：


p(accept y^t)
=
min1, 
r_beta


阈值：


gamma^t
=
gamma_0
sum_s
 w_s^t


若：


r_beta
>gamma^t


接受。

否则：

拒绝。

即：


r_beta
<gamma^t


↓

Trigger revision

---

## 8. Tree-based Revision

危险 token：

启动树搜索。

每步：

采样：


Top-N


candidate tokens

形成：


N^m


路径。

然后：

保留：


Top-K


monitor score 最大路径：


TopK(r_beta)


最终：

选择：


argmax r_beta


作为修正 token。

---

## 9. 完整算法

Prompt

↓

Generate：


m

tokens

↓

计算：


r_beta


↓

若：


r_beta>gamma


Accept

↓

否则：

Tree search

↓

Top-N sample

↓

Top-K prune

↓

Replace token

↓

Continue decoding

---

## 10. 实验：TruthfulQA

Llama-2：

Greedy：


Truth*Info
=
37.9


MD：


44.1


提升：


+6.2


---

Gemma-2：

Greedy：


43.6


MD：


50.2


提升：


+6.6


---

最高 Truth 提升：

约：


+9.2


---

## 11. 实验：TriviaQA / NQ

Llama-3：

TriviaQA：


72.4
→
80.8


提升：


+8.4


---

NQ-open：


39.6
→
47.4


提升：


+6.8


---

Gemma-2：

TriviaQA：


54.0
→
64.6


提升：


+10.6


---

## 12. 实验：GSM8K

Llama-3：


81.4
→
85.2


提升：


+3.8


---

Gemma-2：


60.9
→
79.9


提升：


+19.0


说明：

方法不会破坏 reasoning，
甚至可能提升。

---

## 13. 效率

Latency：

Greedy：


19.94
ms/token


MD：


113.78
ms/token


约：


5.7times


---

相比：

USC：


12.3times


FSC：


15.9times


MD 更快。

原因：

只修改危险 token，
不重生成完整回答。

---

## 14. 消融实验

增加：


N


性能提高。

但：


N>6


收益趋于饱和。

---

阈值：


gamma_0


变化影响较小，
说明方法稳定。

---

## 15. 一个重要发现

最终只有少量 token 被重采样。

例如：

TriviaQA：

Llama-3：

仅：


17.9%


token 被修改。

却获得明显提升。

说明：

> hallucination 确实主要来自少数关键 token。

---

## 16. 最大贡献

一句话：

> 作者认为 hallucination 来自少数危险 token，因此提出实时监控 decoding，仅修改高风险 token，而非重复生成完整回答。

贡献：

1. 提出 in-process monitoring
2. 提出 monitor score
3. 提出 tree-based revision
4. selective token resampling 替代 full-response sampling

---

## 17. 局限

若正确知识不存在：

模型无法修复。

即：


Missing knowledge
→
No correction


未来：

结合：

external knowledge

---

## 18. Take-away

问题：


Hallucination


观察：


Few critical tokens
→
Hallucination


检测：


r_beta
=
sum
 w
 (p^*)/(p_theta)


规则：


r_beta
<gamma
→
Reject


修正：

Tree Search

↓

Top-N sample

↓

Top-K prune

↓

Replace token

核心：

> 不重采样完整回答 
> 只修正危险 token 
> 提升 factuality + 降低成本
