---
title: Deep Dive, Availability Cores
description: The Polkadot Abstraction for Flexible Blockspace Allocation
duration: 1 hour
---

# 深入探究可用性核心

Notes:

你好！

我是布拉德利·奥尔森。

起初是学院的一名学生。

目前在平行链核心团队工作。

将进行三场讲座，让你了解波卡核心的一些情况，包括我们目前所处的位置以及未来的发展方向。

首先来看看可用性核心，这是一种抽象概念，它使得在波卡共享安全的框架下能够灵活地购买区块空间。

让我们开始吧。

---

## 解决双重命名问题

<pba-flex center>

- 在代码中：可用性核心
- 在代码之外：执行核心

</pba-flex>

---

## 概述

<pba-flex center>

- 可用性核心代表什么？

<!-- .element: class="fragment" data-fragment-index="0" -->

- 核心是如何将平行链的租赁和声明映射到验证者子集的？

<!-- .element: class="fragment" data-fragment-index="1" -->

- 核心是如何控制平行链协议的每个步骤的？

<!-- .element: class="fragment" data-fragment-index="2" -->

- 核心现在给我们带来了哪些优势？

<!-- .element: class="fragment" data-fragment-index="3" -->

- 核心支持哪些路线图项目？

<!-- .element: class="fragment" data-fragment-index="4" -->

</pba-flex>

---

## 回顾，区块空间

> 区块空间是区块链完成并提交操作的能力。

波卡的主要产品是“区块空间”。

---

## 区块空间，用之则有，不用则失

波卡的区块空间有两种消耗方式：

<pba-flex center>

1. 当中继链验证、包含并最终确定一个平行链区块时。

<!-- .element: class="fragment" data-fragment-index="0" -->

1. 当验证平行链区块的能力未被使用并过期时。

<!-- .element: class="fragment" data-fragment-index="1" -->

</pba-flex>

<img rounded style="width: 500px" src="../async-backing-deep/img/stopwatch.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

---

## 可用性核心的定义

<pba-cols>
<pba-col center>

- 可用性核心是我们用于分配波卡区块空间的抽象概念。

<!-- .element: class="fragment" data-fragment-index="0" -->

- 通过租赁和按需声明进行分配。

<!-- .element: class="fragment" data-fragment-index="1" -->

- 核心将区块空间供应划分为离散的单元，每个核心在每个中继链区块中可处理一个平行链区块。

<!-- .element: class="fragment" data-fragment-index="2" -->

- 为什么是“可用性”？

<!-- .element: class="fragment" data-fragment-index="3" -->

- 为什么是“核心”？

<!-- .element: class="fragment" data-fragment-index="4" -->

</pba-col>
<pba-col center>

<img rounded style="width: 500px" src="./img/Processor_Cores.jpeg" />

</pba-col>
</pba-cols>

Notes:

- “可用性”，是因为当一个平行链区块候选者正在被提供可用性时，该核心被认为被该候选者占用。但核心调节的是整个平行链区块验证流程的访问，而不仅仅是可用性。
- “核心”，是因为许多候选者可以并行地被提供可用性，这类似于计算机处理器中每个核心的并行计算。

---

## 可用性

虽然核心控制着整个平行链区块流程，但仅可用性流程决定了这些核心何时被认为是占用的，何时是空闲的。

回顾一下，可用性的目标是：

<!-- .element: class="fragment" data-fragment-index="1" -->

1. 确保验证者始终能够恢复证明可用性的片段（PoVs）以验证他们所分配的区块。

<!-- .element: class="fragment" data-fragment-index="2" -->

2. 确保在平行链区块的作者因恶意或故障而未能共享区块的情况下，平行链节点能够恢复这些区块。

<!-- .element: class="fragment" data-fragment-index="3" -->

---

## 核心状态

<pba-cols>
<pba-col center>

空闲

<img rounded style="height: 300px" src="./img/free.png" />

</pba-col>
<pba-col center>

已调度

<img rounded style="height: 300px" src="./img/scheduled.png" />

</pba-col>

<pba-col center>

已占用

<img rounded style="height: 300px" src="./img/occupied.png" />

</pba-col>
</pba-cols>

Notes:

- 在进一步讨论之前，我们需要谈谈核心状态。
- 核心状态：空闲 -> 核心尚未通过租赁或按需声明分配给平行链。
- 核心状态：已调度 -> 核心已分配给一个平行链，并且当前未被占用。
- 核心状态：已占用 -> 核心已分配，并且被一个等待可用性的平行链区块占用。

---

## 可用性流程

<pba-flex center>

1. 区块作者将一个候选区块作为已备份的区块放置到链上，立即占用其已调度的核心。

<!-- .element: class="fragment" data-fragment-index="1" -->

1. 候选区块的支持者分发经过纠删编码的证明可用性的片段（PoV）。

<!-- .element: class="fragment" data-fragment-index="2" -->

1. 验证者分发关于他们拥有哪些候选区块片段的声明。

<!-- .element: class="fragment" data-fragment-index="3" -->

1. 达到可用性阈值（2/3，而恢复PoV只需要1/3）。

<!-- .element: class="fragment" data-fragment-index="4" -->

1. 候选区块在链上被标记为已包含，核心被释放。

<!-- .element: class="fragment" data-fragment-index="5" -->

1. 验证者或平行链节点根据需要检索PoV片段。

<!-- .element: class="fragment" data-fragment-index="6" -->

</pba-flex>

---

## 核心和区块空间随时间的变化

<img rounded style="width: 1100px" src="./img/Train.jpeg" />

Notes:

比喻：

- 中继链：火车装载区
- 中继链区块：每6秒离开车站的火车
- 平行链区块：一节火车车厢的货物量
- 可用性核心：所有火车中的车厢编号

如果你租赁了核心4，那么你有权在每列火车上用你想要运输的任何东西装满第4节车厢。

问题：在这个比喻中，按需声明将如何表示？

---

# 将租赁和声明映射到验证者子集

---

## 全局概览

<img rounded style="width: 1100px" src="./img/cores_big_picture.svg" />

Notes:

- 平行链协议的哪些步骤缺失了，为什么？
- 我们将深入探讨每个部分。
- 有问题吗？

---

## 将租赁和声明分配给核心

<img rounded style="width: 1100px" src="./img/pairing_leases_and_claims_with_cores.svg" />

Notes:

- 租赁有索引，并且与具有相同索引的核心配对。
- 未被指定为按需使用且没有配对租赁的核心保持空闲，其区块空间被浪费。
- 当按需声明被排队时，它们会按升序依次分配给指定的核心，到达最后一个核心时会循环分配。

---

## 将支持组分配给核心

<img rounded style="width: 1100px" src="./img/pairing_backing_groups_with_cores.svg" />

Notes:

- 轮询调度，固定间隔。

这可以防止拜占庭式的支持组长时间中断任何一个平行链的运行。

---

## 支持组的形成

<img rounded style="width: 1100px" src="../execution-sharding/img/validator-groups.png" />

Notes:

- 在会话开始时，验证者被随机分配到各个组。
- 组的数量是活跃验证者数量除以最大组大小后向上取整。
- 组的划分使得最大的组和最小的组的大小相差为1。

---

<img rounded style="width: 300px" src="./img/dice.jpeg" />

## 将验证者分配给核心

<pba-flex center>

- 通过schnorrkel::vrf实现随机性。
- 验证者的分配通过延迟批次激活，直到达到阈值。

<!-- .element: class="fragment" data-fragment-index="1" -->

- 结果是每个区块有30 - 40个验证者进行检查。

<!-- .element: class="fragment" data-fragment-index="2" -->

- 每个区块的分配不同，以防止拒绝服务攻击（DOS）。

<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-flex>

---

## 整合各个部分

<img rounded style="width: 1100px" src="./img/cores_big_picture.svg" />

---

## 占用已分配的核心：通过租赁

<img rounded style="width: 1100px" src="./img/occupying_assigned_cores_with_lease.svg" />

Notes:

问题：在“提供可备份的候选区块”和“可用性流程”之间，平行链协议的哪个步骤会发生？

---

## 占用已分配的核心：按需使用

<img rounded style="width: 1100px" src="./img/occupying_assigned_cores_on_demand.svg" />

---

## 运行时中的核心状态

文件路径：polkadot/runtime/parachains/src/scheduler.rs

<div style="font-size: 0.82em;">

```rust[1|3-8|5,10-16]
pub(crate) type AvailabilityCores<T> = StorageValue<_, Vec<Option<CoreOccupied>>, ValueQuery>;

pub enum CoreOccupied {
    /// 一个并行线程（按需使用的平行链）。
    Parathread(ParathreadEntry),
    /// 一个持有租赁的平行链。
    Parachain,
}

/// 并行线程 = 按需使用的平行链
pub struct ParathreadEntry {
	/// 声明。
	pub claim: ParathreadClaim,
	/// 重试次数。
	pub retries: u32,
}
```

</div>

Notes:

问题：当Option为None时，这表示什么？

- 哪个平行链占用了一个核心是单独存储在以下结构中的。

---

## 运行时中的核心分配

文件路径：polkadot/runtime/parachains/src/scheduler.rs

<div style="font-size: 0.82em;">

```rust[1|3-10|9,12-17]
pub(crate) type Scheduled<T> = StorageValue<_, Vec<CoreAssignment>, ValueQuery>;

pub struct CoreAssignment {
    /// 被分配的核心。
    pub core: CoreIndex,
    /// 分配给该核心的平行链的唯一ID。
    pub para_id: ParaId,
    /// 分配的类型。
    pub kind: AssignmentKind,
}

pub enum AssignmentKind {
	/// 一个平行链。
	Parachain,
	/// 一个并行线程（按需使用的平行链）。
	Parathread(CollatorId, u32),
}
```

</div>

Notes:

- 所有核心分配的向量。
- 将ParaId与CoreIndex配对。
- AssignmentKind携带按需使用的重试信息。

---

## 核心是如何控制平行链协议的每个步骤的

---

## 核心分配如何调节支持

每个平行链区块候选者是在特定的`relay_parent`的上下文中构建的。

验证者查询他们在`relay_parent`时的核心分配，并拒绝为与他们的支持组无关的候选者提供支持。

<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

- 中继父上下文：最大PoV大小、当前平行链运行时代码和支持组分配。

---

### 核心分配如何调节支持（续）

`handle_second_message()`在支持子系统中。

```rust[1|6]
if Some(candidate.descriptor().para_id) != rp_state.assignment {
	gum::debug!(
		target: LOG_TARGET,
		our_assignment = ?rp_state.assignment,
		collation = ?candidate.descriptor().para_id,
		"Subsystem asked to second for para outside of our assignment",
	);

	return Ok(())
}
```

---

## 核心和链上支持

<pba-flex center>

- 对于每个空闲或即将有新候选者的核心。
- 为该核心上接下来调度的平行链的候选者提供给区块作者。

<!-- .element: class="fragment" data-fragment-index="1" -->

- 在链上备份 -> 立即占用核心。

<!-- .element: class="fragment" data-fragment-index="2" -->

</pba-flex>

Notes:

- 回顾可能的核心状态。
- 提到超时与可用性的关系。

问题：“立即占用核心”意味着什么？

---

## 核心与链上支持：代码
<div style="font-size: 0.82em;">
用于确定是否支持某个候选者以及支持哪个候选者的代码，已大幅简化。
```rust[1|24|2-4|5-13|13-23|28-29]
let (para_id) = match core {
    CoreState::Scheduled(scheduled_core) => {
        scheduled_core.para_id
    },
    CoreState::Occupied(occupied_core) => {
        if current_occupant_made_available(core_idx, &occupied_core.availability)
        {
            if let Some(ref scheduled_core) = occupied_core.next_up_on_available {
                scheduled_core.para_id
            } else {
                continue
            }
        } else {
            if occupied_core.time_out_at != current_block {
                continue
            }
            if let Some(ref scheduled_core) = occupied_core.next_up_on_time_out {
                scheduled_core.para_id
            } else {
                continue
            }
        }
    },
    CoreState::Free => continue,
};

let candidate_hash =
    get_backable_candidate(relay_parent, para_id, required_path, sender).await?;
```
</div>
Notes:
- 讨论核心释放的标准
- `bitfields_indicate_availability`
    - `next_up_on_available`
- 可用性超时
    - `next_up_on_timeout`

---
## 核心与批准、争议、最终确定性
每个被包含的候选者都已经占用了一个可用性核心。
批准、争议和最终确定性仅适用于已被包含的候选者。

---
## 核心目前带来的优势
<pba-flex center>
1. 平行链执行的可预测性
2. 执行分片分配的可预测性
<!-- .element: class="fragment" data-fragment-index="1" -->
</pba-flex>
<img rounded style="width: 500px" src="./img/advantage.png" />
Notes:
- 核心与支持组之间定期轮换的配对关系降低了潜在恶意验证者子集的影响。
- 核心支持预分配，使平行链的成本具有可预测性。
- 核心时间可以转售或拆分，以实现精确分配。

---
# 核心与路线图项目
<img rounded style="width: 600px" src="./img/road.jpeg" />

---
## 特殊核心调度
<pba-flex center>
- 每个平行链有多个核心
- 多种时长的重叠租赁
<!-- .element: class="fragment" data-fragment-index="1" -->
- 租赁 + 按需使用
<!-- .element: class="fragment" data-fragment-index="2" -->
</pba-flex>
<img rounded style="width: 1500" src="./img/exotic_core_scheduling.svg" />
Notes:
- 每种颜色代表一个平行链
比喻：
- 同一列火车上的多节车厢
- 填充不同火车车厢的权利在时间上有重叠
- 对某一节车厢有长期使用权，同时可以购买其他车厢仅一趟列车行程的使用权

---
## 可分割且可交易的区块空间
我们希望平行链能够购买、出售和分割区块空间，使其分配量恰好满足自身需求。
<pba-flex center>
- 通过使用阈值划分的区块空间区域实现核心共享
- 针对区块空间区域的二级市场
</pba-flex>
Notes:
- 例如：一个跨越100个区块的区域。对该区域的使用进行划分，使两方各自最多可提交50个平行链区块。在整个区域内执行所有权比例规则，即每一方在前10个区块中提交的数量不能超过5个。

---
## 概念转变：区块空间与核心时间
区块空间是区块链产物的通用术语，而核心时间是波卡用于分配区块空间或其他计算资源的特定抽象概念。
<!-- .element: class="fragment" data-fragment-index="1" -->
核心可以保障除区块之外的计算。例如，智能合约可以直接部署在一个核心上。
<!-- .element: class="fragment" data-fragment-index="2" -->
按时间而不是固定的区块大小来分配核心，能够无缝处理更小或更大的平行链区块。
<!-- .element: class="fragment" data-fragment-index="3" -->

---
## 资源
<pba-col center>
1. [实施者指南：调度器模块](https://paritytech.github.io/polkadot/book/runtime/scheduler.html)
2. [RFC：敏捷核心时间](https://github.com/polkadot-fellows/RFCs/pull/1)
3. [RFC：核心时间调度区域](https://github.com/polkadot-fellows/rfcs/pull/3)
4. [Rob Habermeier的博客：波卡的区块空间与区块链](https://www.rob.tech/polkadot-blockspace-over-blockchains/)
</pba-col>

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题
