---
title: Designing DAG-based consensus
description: A formal, yet friendly consensus framework
---

# 基于有向无环图（DAG）的共识设计

---

## 本讲座的目标

<br />

1. 对共识问题及相关概念进行形式化定义 <!-- .element: class="fragment"-->
2. 提供一个设计基于DAG的共识协议的框架 <!-- .element: class="fragment"-->

---

## 什么是共识？

<br />

- 一组参与者就同一结果达成一致的**过程**
- 分布式计算中的一个基本**问题**
- 区块链技术栈的一个关键**组件**

---

## 共识的特性

<br />

活性、安全性、完整性

---

## 我们已经了解的一些共识算法

<br />

中本聪共识

Babe

Grandpa

Sassafras

Tendermint

...

---

## 谁在运行这个协议？

<br />

参与者，称为**节点**

---

## 节点

<br />

- 节点可以是诚实的或恶意的
- 诚实节点遵循协议
- 恶意节点可以以任何方式偏离协议
- 恶意节点可以相互勾结
- 恶意节点可以被敌手控制

---

## 公钥基础设施

<br />

- 每个节点都有自己的 <font color="#c2ff33">私钥</font> 和 <font color="#e6007a">公钥</font>
- 每个节点用其 <font color="#c2ff33">私钥</font> 对消息进行 <font color="#c2ff33">签名</font>
- 每个节点用其他节点的 <font color="#e6007a">公钥</font> 对消息进行 <font color="#e6007a">验证</font>

---

## 公钥基础设施

<br />

经过身份验证的点对点通信

---

## 敌手

<br />

敌手**可以**控制网络延迟，但它是**计算受限**的，即它**不能**破解加密（如伪造签名）。

---

## 网络

<br />

通过网络进行通信... 但是什么样的网络呢？

---

## 网络模型

<br />

同步

部分同步

异步

---

## 网络模型：同步

<br />

> 消息传递时间存在一个已知的上界 \\(\Delta\\)。

<br />
<br />

_直觉：存在一个明确定义的协议轮次概念_

---

## 网络模型：异步

<br />

> 消息延迟没有上界，但保证消息能够送达。

<br />
<br />

_直觉：你无法判断一个节点是崩溃了还是有很长的延迟_

---

## 网络模型：异步

<br />

> 消息延迟没有上界，但保证消息能够送达。

<br />
<br />

- 我们假设敌手可以完全控制消息延迟。
- 超时的概念基本上是无用的。

---

## 网络模型：部分同步

<br />

> 存在一个已知的界限 \\(\Delta\\)，以及一个未知的时间点 **GST**，在此时间点之后，通信将以延迟 \\(\Delta\\) 同步进行。

<br />
<br />

_直觉：协议最终将同步工作，但在此之前需要是安全的_

---

## 关键理论结果

<br />

> [FLP定理] 在一个至少有一个进程可能因崩溃而失败的异步系统中，不可能存在一个确定性的协议来解决共识问题。

<br />
<br />

> [Castro - Liskov定理] 在一个有 \\(3f + 1\\) 个节点且其中超过 \\(f\\) 个进程是拜占庭节点的部分同步系统中，不可能存在一个协议来解决共识问题。

---

## 关键理论结果

<br />

> [FLP定理] 在一个 <font color="#c2ff33">异步</font> 系统中，至少有一个进程可能因崩溃而失败的情况下，不可能存在一个 <font color="#c2ff33">确定性</font> 的协议来解决共识问题。

<br />
<br />

> [Castro - Liskov定理] 在一个 <font color="#c2ff33">部分同步</font> 系统中，有 \\(3f + 1\\) 个节点且其中 <font color="#c2ff33">超过</font> \\(f\\) 个进程是拜占庭节点的情况下，不可能存在一个协议来解决共识问题。

---

## 结论

<br />

在**异步** 场景中，人们所能期望的最好结果是一个**概率性** 协议，对于 \\(3f + 1\\) 个参与者，该协议能够容忍 **最多** \\(f\\) 个故障。

<br />

> ✅ <font color="#c2ff33"> **可行！**</font>

<!-- .element: class="fragment"-->

---

## 关于随机性的说明

<br />

在极其恶劣的环境中，真正的概率是必需的。
在敌手不是极其恶毒的情况下，即使是一个简单（但非平凡）的随机源也可以。

---

## 响应性

---

## 响应性

<br />

**非响应性** 协议必须 **等待** \\(\Delta\\) **时间** 才能进入下一轮。
<br />

---

## 响应性

<br />

**非响应性** 协议必须 **等待** \\(\Delta\\) **时间** 才能进入下一轮。
<br />
<br />

- \\(\Delta\\) 必须足够长，以允许所有诚实节点发送它们的消息。
- \\(\Delta\\) 必须足够短，以允许协议取得进展。
- 在出现故障的情况下，它们必须执行一个相当昂贵的恢复过程（如领导者变更）。

---

## 响应性

<br />

**响应性** 协议 **等待** \\(2f + 1\\) **条消息** 才能进入下一轮。

<br />
<br />

> <font color="#c2ff33">为什么是 \\(2f + 1\\)？</font>

<!-- .element: class="fragment"-->

---

## 响应性

<br />

**响应性** 协议 **等待** \\(2f + 1\\) **条消息** 才能进入下一轮。

<br />
<br />

> <font color="#c2ff33">在 \\(2f + 1\\) 个节点中，至少有 \\(f + 1\\) 个诚实节点，即诚实节点占多数。</font>

---

## 响应性

<br />

**响应性** 协议 **等待** \\(2f + 1\\) **条消息** 才能进入下一轮。
<br />
<br />

- 异步协议必须是响应性的。
- 在网络条件良好的情况下，它们的速度要快得多。

---

## 检查点

<br />

到目前为止，我们已经涵盖了：

- 共识问题
- 节点类型和敌手
- 节点间通信
- 网络模型（同步性）
- 异步网络中协议的局限性（诚实节点比例和对随机性的需求）
- 响应性

---

## 热身练习：广播

<br />

> （在异步网络中）**可靠地** 向所有其他节点发送一条消息。

<br />
<br />

- （**有效性**）如果发送者是诚实的并且广播了一条消息 \\(m\\)，那么每个诚实节点都输出 \\(m\\)。

<!-- .element: class="fragment"-->

- （**完整性**）如果一个诚实节点输出了一条消息 \\(m\\)，那么这条消息必须是由发送者广播的。

<!-- .element: class="fragment"-->

- （**一致性**）如果一个诚实节点输出了一条消息 \\(m\\)，那么其他每个诚实节点都输出 \\(m\\)。

<!-- .element: class="fragment"-->

---

## 可靠广播协议（RBC）

<br />

<img rounded style="width: 1200px;" src="./img/3.10-rbc.svg" />

---

## 实际中的可靠广播

<br />

由于通信复杂度非常高，我们使用启发式方法或基于密码学的技巧。

---

## 区块链协议与原子广播

<br />

原子广播
<br />

<img rounded style="width: 700px;" src="./img/3.10-atomic-broadcast.svg" />

---

## 形式化的随机性

<br />

随机信标
<br />

<img rounded style="width: 700px;" src="./img/3.10-randomness-beacon.svg" />

---

## 原子广播：时间线

<br />

<img rounded style="width: 1200px;" src="./img/3.10-timeline-1.svg" />

---

## 原子广播：时间线

<br />

<img rounded style="width: 1200px;" src="./img/3.10-timeline-2.svg" />

---

## 趣闻

<br />

Aleph论文首次实现了完全异步的随机信标：

- 高效的设置（\\(O(1)\\) 轮次，\\(O(N^2)\\) 通信量）
- 期望在 \\(O(1)\\) 轮次内输出一个随机值，每轮的通信量为 \\(O(N)\\)

---

## 共识协议（精选）

<br />

<pba-cols>
<pba-col>

### 经典协议：

- [DLS’88]，[CR’92]，
- PBFT [CL’99]
- 随机预言机 … [CKS’05]
- Honey Badger BFT [MXCSS’16]
- Tendermint [BKM’18]
- VABA [AMS’19]
- Flexible BFT [MNR’19]
- HotStuff [YMRGA’19]
- Streamlet [CS’20]
- Grandpa [SKK'20]

</pba-col>
<pba-col>

### 基于DAG的协议：

- [L. Moser, P. Meliar - Smith ‘99]
- Hashgraph [B’16]
- Aleph [GLSS’18]
- DAG - Rider [KKNS’21]
- Highway [KFGS’21]
- Narwhal&Tusk [DKSS’22]
- Bullshark [SGSK’22]

</pba-col>
</pba-cols>

---

## 基于DAG的协议

---

## DAG

<br />

有向无环图
<br />

<img rounded style="width: 700px;" src="./img/3.10-dag.svg" />

---

## 它与共识有什么关系？

<br />

直觉：图表示消息（单元）之间的依赖关系。
<br />

<img rounded style="width: 700px;" src="./img/3.10-message-dependency.svg" />

---

## 框架核心

<br />

1. 我们维护一个本地DAG，代表我们对这些单元的认知。 <!-- .element: class="fragment"-->
2. 我们对我们的DAG进行本地的、离线共识。 <!-- .element: class="fragment"-->

---

## 框架核心

<br />

1. 我们维护一个本地DAG，代表我们对这些单元的认知。
2. 我们对我们的DAG进行本地的、<font color="#c2ff33"> **离线共识**</font>。

---

## 框架核心（换句话说）

<br />

1. （在线）：发送和接收有助于构建本地DAG的单元
2. （离线）：每个人只需查看DAG就可以对其进行本地共识。

---

## 关键观察

<br />

- 本地DAG可能会有所不同... <!-- .element: class="fragment"-->
- 但它们保证会收敛到同一个DAG <!-- .element: class="fragment"-->
- 离线共识保证会产生相同的结果 <!-- .element: class="fragment"-->

---

## 敌手控制

<br />

<img rounded style="width: 600px;" src="./img/3.10-adversarial-control.svg" />

---

## 随机性？随机性在哪里？

<br />

它被放入本地共识协议中。

---

## 与原子共识问题的关系

<br />

- 节点接收交易并将它们放入单元中
- 节点相互发送它们的新单元
- （本地）节点对单元进行线性排序，并从块中构建块

---

## 题外话：块生成、信息传播和最终确定

<br />

常见方法（例如在Substrate中）：

- 生成和传播在同一层进行
- 之后，节点对传播的块进行共识以最终确定。

<br />

基于DAG的协议的自然方法：

- 信息传播作为“第一阶段”发生
- 块构建和（即时）最终确定在本地进行。

---

## 不同分离的主要后果

<br />

- 块签名
- 速度

---

## 本地共识：目标

<br />

本地副本可能有很大差异，块可能还没有到达所有节点等等...
但我们必须就单元排序做出共同决定！

---

## 关键概念：可用性
<br>
直观来讲，一个单元若满足以下条件，则可认定为**可用**：
<br>
- 大多数节点拥有该单元 <!-- .element: class="fragment"-->
- 其分发相当迅速（若一个单元一个月后才在所有节点出现，我们不会称其可用） <!-- .element: class="fragment"-->
- 大多数节点知道大多数节点知道大多数节点知道……该单元可用（相互知晓） <!-- .element: class="fragment"-->
---
## 可用性
<br>
若一个单元可用，那么它便是扩展当前排序时作为 “锚点” 的理想候选对象。
---
## 轻量级案例研究
<br>
Aleph Zero拜占庭容错（BFT）协议
---
## 头部
<br>
<img rounded style="width: 600px;" src="./img/3.10-heads.svg" />
---
## 构建模块
<br>
<img rounded style="width: 800px;" src="./img/3.10-building-blocks.svg" />
---
## 选择头部
<br>
<img rounded style="width: 500px;" src="./img/3.10-choosing-head.svg" />
---
## 可用性判定
<br>
各单元相互对彼此的可用性进行投票。
---
## （部分）可用性判定
<br>
Vote<sub><font color="#c2ff33">U</font></sub>(<font color="#e6007a">V</font>) 的取值规则如下：
- 若 <font color="#e6007a">V</font> 来自 <font color="#c2ff33">U</font> 所在轮次的下一轮，则Vote<sub><font color="#c2ff33">U</font></sub>(<font color="#e6007a">V</font>) = \[\[<font color="#c2ff33">U</font> 是 <font color="#e6007a">V</font> 的父单元\]\] 
- 若 <font color="#c2ff33">U</font> 的所有子单元都投了 `0` 或都投了 `1`，则Vote<sub><font color="#c2ff33">U</font></sub>(<font color="#e6007a">V</font>) = `0`/`1`
- 其他情况，Vote<sub><font color="#c2ff33">U</font></sub>(<font color="#e6007a">V</font>) = `CommonVote(round(`<font color="#c2ff33">U</font> `), round(`<font color="#e6007a">V</font> `))`

<br><br>
（<font color="#c2ff33">U</font> 所在轮次早于 <font color="#e6007a">V</font>）
---
## 额外内容：生成随机数
<br>
Sig<sub>`sk`</sub>(nonce)
<!-- .element: class="fragment"-->
<br>
<div class="fragment">
- 生成的随机数必须不可预测
- 延迟揭示
- 必须依赖 \(f + 1\) 个节点
- 不能被敌手干扰
</div>
---
## 标准方法
<br>
<img rounded style="width: 600px;" src="./img/3.10-shamir.svg" />
---
## 标准方法
<br>
<img rounded style="width: 600px;" src="./img/3.10-shamir-2.svg" />
问题：需要可信分发者！ <!-- .element: class="fragment"-->
---
## 一个简单技巧
<br>
<font color="#c2ff33">每个人都分发秘密</font> <!-- .element: class="fragment"-->
---
## 随机数组合
<br>
<img rounded style="width: 600px;" src="./img/3.10-xoring.svg" /> 