---
title: Shallow Dive, Asynchronous Backing
description: Decoupling Backing and Inclusion Through Advance Work Based on Happy Path Assumptions
duration: 30 min
---

# 浅探异步支持

Notes:

大家好呀

今天我要给大家讲讲异步支持，这一新特性能够缩短平行链区块时间，并使波卡区块空间的数量提升一个数量级。

咱们开始吧

---

## 概述

<pba-flex center>

- 同步与异步
- 为什么异步支持是可取的？

<!-- .element: class="fragment" data-fragment-index="1" -->

- 异步支持的高级机制

<!-- .element: class="fragment" data-fragment-index="2" -->

- 未包含的片段以及潜在平行链

<!-- .element: class="fragment" data-fragment-index="3" -->

- 异步支持助力其他路线图项目

<!-- .element: class="fragment" data-fragment-index="4" -->

</pba-flex>

---

## 简化的同步支持

<img rounded style="width: 1100px" src="./img/synchronous_backing_simplified.svg" />

Notes:

- 左右的分界线是候选区块在链上得到支持的时刻
- 批准、争议和最终确认不会立即阻碍更多候选区块的产生。
  所以在这个模型中我们不需要表示这些步骤。

---

## 简化的异步支持

<div class="r-stack">
<img rounded style="width: 1100px" src="./img/async_backing_simplified_1.svg" />
<img rounded style="width: 1100px" src="./img/async_backing_simplified_2.svg" />
<!-- .element: class="fragment" data-fragment-index="1" -->
<img rounded style="width: 1100px" src="./img/async_backing_simplified_3.svg" />
<!-- .element: class="fragment" data-fragment-index="2" -->
</div>

Notes:

我们的平行链区块候选缓存允许我们在链上支持这一关键分界线之前暂停。

---

## 异步支持的乐观收集者假设

<pba-flex center>

1. “我所知道的最佳现有平行链区块最终将被包含在中继链中。”
1. “不会有影响该最佳平行链区块的链回滚。”

<!-- .element: class="fragment" data-fragment-index="1" -->

</pba-flex>

<br />
<br />

> 风险很低

<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:

最佳是通过类似于BABE分叉选择规则的过程来确定的。
简要回顾BABE分叉选择规则

---

## 异步支持的优势

<pba-flex center>

1. 每个区块的外部交易数量增加3 - 5倍
1. 平行链区块时间更短，从12秒缩短至6秒

<!-- .element: class="fragment" data-fragment-index="1" -->

1. 区块空间数量因此提升6 - 10倍

<!-- .element: class="fragment" data-fragment-index="2" -->

1. 浪费的平行链区块更少

<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-flex>

Notes:

1. 收集者有更多时间来填充每个区块
1. 前期工作确保每个平行链都有可支持的候选区块，以便每6秒在中继链上得到支持
1. 不言自明
1. 当平行链区块在第一次尝试时未能进入中继链时，允许其被“重用”

---

## 平行链区块验证流水线

<img rounded style="width: 1100px" src="./img/async_backing_pipelining.svg" />

---

## 同步支持，再看一眼

<div class="r-stack">
<img rounded style="width: 1100px" src="./img/synchronous_backing_1.svg" />
<img rounded style="width: 1100px" src="./img/synchronous_backing_2.svg" />
<!-- .element: class="fragment" data-fragment-index="1" -->
<img rounded style="width: 1100px" src="./img/synchronous_backing_3.svg" />
<!-- .element: class="fragment" data-fragment-index="2" -->
</div>

Notes:

图片版本1：

- 现在让我们更仔细地看看在同步和异步支持下，支持和包含的每个步骤分别在何时发生。

图片版本3：

- 整个过程是一个持续12秒（2个中继区块）的周期。
- 在第一个候选区块被包含之前，同一平行链的第二个候选区块的周期中的任何部分都无法开始。

---

## 异步支持，再看一眼

<div class="r-stack">
<img rounded style="width: 1500px" src="./img/async_backing_1.svg" />
<img rounded style="width: 1500px" src="./img/async_backing_2.svg" />
<!-- .element: class="fragment" data-fragment-index="1" -->
<img rounded style="width: 1500px" src="./img/async_backing_3.svg" />
<!-- .element: class="fragment" data-fragment-index="2" -->
</div>

Notes:

图片版本1：

- 候选区块存储在潜在平行链中（稍后会详细介绍）

图片版本2：

- 现在我们看到了我们的中继区块周期。
- 它是6秒而不是12秒。
- 每个周期完成一个候选区块的链上支持和另一个候选区块的包含。

图片版本3：

- 整理生成和链下支持在中继区块周期之外。
- 因此，收集者可以自由地提前处理几个区块。实际上，即使提前处理2 - 3个区块，收集者也有足够的时间来完全填充区块（PoV大小为5MiB）
- 注意，整理生成上下文的一部分，即未包含的片段，来自收集者本身。

---

## 未包含的片段

<pba-flex center>

- 平行链在特定链分叉上产生但尚未被包含的所有平行链区块的记录
- 在构建未来区块时用于施加限制

<!-- .element: class="fragment" data-fragment-index="1" -->

- 存在于平行链运行时中

<!-- .element: class="fragment" data-fragment-index="2" -->

- 从正在构建的新平行链区块的角度来看

<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-flex>

Notes:

限制示例：在中继链不得不丢弃来自我们平行链的传入消息之前，剩余的上行消息数量

---

## 未包含的片段

<img rounded style="width: 1100px" src="./img/unincluded_segment_1.svg" />

Notes:

- 每当有新的区块被导入到平行链运行时时，该片段就会增加
- 当该片段的某个祖先区块被包含时，该片段会缩小
- 未包含片段的最大容量在平行链和中继链上都有设定

---

## 未包含的片段

<img rounded style="width: 1100px" src="./img/unincluded_segment_2.svg" />

Notes:

已使用带宽：

- pub ump_msg_count: u32,
- pub ump_total_bytes: u32,
- pub hrmp_outgoing: BTreeMap<ParaId, HrmpChannelUpdate>,

---

## 潜在平行链

<pba-flex center>

- 中继链对所有平行链的所有链分叉上的所有候选区块的记录
- 就好像你把所有未包含的片段折叠成一个巨大的结构

<!-- .element: class="fragment" data-fragment-index="1" -->

- 用于存储候选区块，并在稍后将其提供给链上支持过程

<!-- .element: class="fragment" data-fragment-index="2" -->

- 存在于中继客户端（链下）

<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-flex>

---

## 潜在平行链快照

<img rounded style="width: 1500px" src="./img/Prospective_Parachains_Overview.svg" />

Notes:

- 仅为活动叶节点构建片段树
- 在每个叶节点为每个已调度的平行链构建片段树
- 片段树可能有0个或多个片段，代表构成平行链状态可能未来的潜在平行链区块。
- 每个片段的整理生成、传递和支持工作已经完成。

---

## 简化的异步支持

<div class="r-stack">
<img rounded style="width: 1100px" src="./img/async_backing_simplified_3.svg" />
</div>

Notes:

回到我们最基本的图表

- 问题：为了简化，我遗漏了哪个结构的名称，这个名称应该放在我们图表的哪个位置？
- 问题：我完全省略了哪个结构？

---

## 异步支持与特殊核心调度

<img rounded style="width: 1100px" src="./img/exotic_scheduling.svg" />

Notes:

- 什么是特殊核心调度？
  - 每个平行链有多个核心
  - 多个不同长度的租赁期重叠
  - 租赁 + 按需
- 异步支持有什么帮助？
- 未包含的片段对于在单个中继区块中构建2个或更多平行链区块是必要的

---

## 资源

<pba-col center>

1. [波卡异步支持拉取请求](https://github.com/paritytech/polkadot/pull/5022)
2. [Cumulus异步支持拉取请求](https://github.com/paritytech/cumulus/pull/2300)
3. [实施者指南：潜在平行链](https://github.com/paritytech/polkadot/blob/631b66d5daa642fad7ed0a9712194c5b85b96563/roadmap/implementers-guide/src/node/backing/prospective-parachains.md)
   <!-- FIXME 上述指南已弃用 -->
   </pba-col>

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题
