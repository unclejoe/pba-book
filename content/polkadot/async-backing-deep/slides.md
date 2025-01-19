---
title: Deep Dive, Asynchronous Backing
description: Decoupling Backing and Inclusion Through Advance Work Based on Happy Path Assumptions
duration: 1 hour
---

# 深入探究异步支持

Notes:

我将进行三场讲座中的第二场，为大家展示波卡核心的一个窗口，介绍我们目前所处的位置以及未来的发展方向。

这场讲座将涵盖异步支持这一新特性，它有可能缩短平行链的出块时间，并使波卡的区块空间数量增加一个数量级。

让我们开始吧。

---

## 概述

<pba-flex center>

- 异步支持的动机
- 奠定基础，平行链区块的上下文执行
- 潜在平行链，存储支持过程的产物
- 支持性变更
- 异步支持的优势，当前及未来

</pba-flex>

---

<!-- .slide: data-background-color="#4A2439" -->

# 异步支持的动机

---

## 术语：可支持与已支持

<pba-flex center>

- 可支持候选：
  - 链下支持过程的输出
  - 已获得其支持组的法定数量的“有效”投票
- 已支持候选：
  - 已被放置到链上的可支持候选
  - 也称为“待可用性”

</pba-flex>

Notes:

除非我们知道在可用性过程中有该候选的空间，否则我们避免在中继链上支持任何候选。
否则会有浪费链上工作的风险。

当一个候选在链上被支持时，它会立即占用一个可用性核心，并进入可用性（或纠删编码）过程。

---

## 同步支持

<img rounded style="width: 1500px" src="../async-backing-shallow/img/async_backing_3.svg" />

Notes:

有人能发现同步模型的问题吗？

- 问题 1

  - 只有在前一个平行链区块被包含后，才能开始处理新的平行链区块
  - 一个中继区块用于支持，一个用于包含
  - 最小出块时间为 12 秒

- 问题 2
  - 提交整理数据以达到 12 秒总出块时间的最短时间
  - 约 0.5 秒
  - 不足以完全填满区块

---

## 异步支持

<img rounded style="width: 1500px" src="../async-backing-shallow/img/async_backing_3.svg" />

Notes:

- 指出两个独立的过程以及它们之间的“停止点”
- 从未包含的部分开始逐步讲解

---

## 异步支持的合理整理者假设

<pba-flex center>

1. “我所知道的最好的现有平行链区块最终将被包含在中继链中。”
1. “不会有影响该最佳平行链区块的链反转。”

</pba-flex>

<br />
<br />

> 风险很低

Notes:

最佳是通过类似于 BABE 分叉选择规则的过程确定的。
简要回顾 BABE 分叉选择规则

---

<!-- .slide: data-background-color="#4A2439" -->

# 平行链区块的上下文执行

---

## 异步支持执行上下文

<pba-cols>
<pba-col center>

- 来自中继链
  - 基本约束
  - 中继父区块
- 来自未包含的部分
  - 约束修改
  - 所需父区块

</pba-col>
<pba-col>

<img rounded style="width: 500px" src="./img/context.jpeg" />

</pba-col>
</pba-cols>

Notes:

- 以前的情况：
  - 所需父区块包含在中继父区块中
  - 不需要约束修改
- 中继父区块与所需父区块的区别
- 基本约束与修改的区别

---

## 约束与修改

```rust
pub struct Constraints {
	/// 在这些约束下可接受的最小中继父区块编号。
	pub min_relay_parent_number: BlockNumber,
	/// 允许的最大有效性证明大小，以字节为单位。
	pub max_pov_size: usize,
	/// 允许的最大新验证代码大小，以字节为单位。
	pub max_code_size: usize,
	/// 剩余的 UMP 消息数量。
	pub ump_remaining: usize,
	/// 剩余的 UMP 字节数。
	pub ump_remaining_bytes: usize,
	/// 每个候选允许的最大 UMP 消息数量。
	pub max_ump_num_per_candidate: usize,
	/// 剩余的 DMP 队列。仅包括发送时的区块编号。
	pub dmp_remaining_messages: Vec<BlockNumber>,
	/// 所有已注册的入站 HRMP 通道的限制。
	pub hrmp_inbound: InboundHrmpLimitations,
	/// 所有已注册的出站 HRMP 通道的限制。
	pub hrmp_channels_out: HashMap<ParaId, OutboundHrmpChannelLimitations>,
	/// 每个候选允许的最大 HRMP 消息数量。
	pub max_hrmp_num_per_candidate: usize,
	/// 平行链所需的父区块头数据。
	pub required_parent: HeadData,
	/// 该平行链的预期验证代码哈希。
	pub validation_code_hash: ValidationCodeHash,
	/// 截至该平行链的代码升级限制信号。
	pub upgrade_restriction: Option<UpgradeRestriction>,
	/// 未来的验证代码哈希（如果有），以及升级将至少应用的中继父区块编号。
	pub future_validation_code: Option<(BlockNumber, ValidationCodeHash)>,
}

/// 由于潜在候选而对约束进行的修改。
#[derive(Debug, Clone, PartialEq)]
pub struct ConstraintModifications {
	/// 要构建的所需父区块头。
	pub required_parent: Option<HeadData>,
	/// 新的 HRMP 水印
	pub hrmp_watermark: Option<HrmpWatermarkUpdate>,
	/// 出站 HRMP 通道修改。
	pub outbound_hrmp: HashMap<ParaId, OutboundHrmpChannelModification>,
	/// 已发送的 UMP 消息数量。
	pub ump_messages_sent: usize,
	/// 已发送的 UMP 字节数。
	pub ump_bytes_sent: usize,
	/// 已处理的 DMP 消息数量。
	pub dmp_messages_processed: usize,
	/// 是否已应用待处理的代码升级。
	pub code_upgrade_applied: bool,
}
```

Notes:

需要强调的约束：

- required_parent：片段将把其对应的候选放置在此处作为子节点
- min_relay_parent_number：单调递增规则，max_ancestry_len
- ump_messages_sent 会修改 ump_remaining
- code_upgrade_applied：在未包含的部分中每次只能有一个！

---

<!-- .slide: data-background-color="#4A2439" -->

# 潜在平行链

存储支持过程的产物

---

## 潜在平行链快照

<img rounded style="width: 1500px" src="../async-backing-shallow/img/Prospective_Parachains_Overview.svg" />

Notes:

- 片段树仅为活动叶节点构建
- 每个叶节点为每个已调度的平行链构建片段树
- 片段树可能有 0 个或多个片段，代表可能构成平行链状态未来的潜在平行链区块。
- 每个片段的整理数据生成、传递和支持工作已经完成。

---

## 片段树的剖析

<img rounded style="width: 1500px" src="./img/Fragment_Tree_Dissected.svg" />

Notes:

按此顺序：

- 范围
- 根节点：对应于最近包含的候选
- 子节点：提及所需父节点规则
- `FragmentNode` 内容
- `CandidateStorage`
- `GetBackableCandidate`

---

<img rounded style="width: 500px" src="./img/checklist.jpeg" />

## 片段树包含检查清单

候选在什么情况下可以包含在片段树中？

<pba-flex center>

- 所需父节点在树中
  - 如果可以，作为所需父节点的子节点包含
- `Fragment::validate_against_constraints()` 通过
- 中继父节点在范围内

</pba-flex>

---

## 片段的中继父节点限制

中继父节点在范围内是什么意思？
中继父节点在什么情况下可以超出范围？

Notes:

在范围内：

- 在中继链的同一分叉上
- 在 `allowed_ancestry_len` 之内

超出范围：

- 待可用性的候选已在链上被看到，即使它们超出范围也需要被考虑。
  待可用性候选最可能的结果是它们将变得可用，因此我们需要这些区块在 `FragmentTree` 中以接受它们的子节点。
- 中继父节点相对于所需父节点不能向后移动

---

## 组装基本约束

摘自 `runtime/parachains/src/runtime_api_impl/vstaging.rs` 中的 `backing_state()`

```rust
let (ump_msg_count, ump_total_bytes) = <ump::Pallet<T>>::relay_dispatch_queue_size(para_id);
let ump_remaining = config.max_upward_queue_count - ump_msg_count;

let constraints = Constraints {
		min_relay_parent_number,
		max_pov_size: config.max_pov_size,
		max_code_size: config.max_code_size,
		ump_remaining,
		ump_remaining_bytes,
		max_ump_num_per_candidate: config.max_upward_message_num_per_candidate,
		dmp_remaining_messages,
		hrmp_inbound,
		hrmp_channels_out,
		max_hrmp_num_per_candidate: config.hrmp_max_message_num_per_candidate,
		required_parent,
		validation_code_hash,
		upgrade_restriction,
		future_validation_code,
	};
```

---

## 应用约束修改

摘自 `Constraints::apply_modifications()`

```rust
if modifications.dmp_messages_processed > new.dmp_remaining_messages.len() {
	return Err(ModificationError::DmpMessagesUnderflow {
		messages_remaining: new.dmp_remaining_messages.len(),
		messages_processed: modifications.dmp_messages_processed,
	})
} else {
	new.dmp_remaining_messages =
		new.dmp_remaining_messages[modifications.dmp_messages_processed..].to_vec();
}
```

---

## 针对约束进行验证

摘自 `Fragment::validate_against_constraints()`

```rust
if relay_parent.number < constraints.min_relay_parent_number {
	return Err(FragmentValidityError::RelayParentTooOld(
		constraints.min_relay_parent_number,
		relay_parent.number,
	))
}
```

---

## AsyncBackingParams

```rust
pub struct AsyncBackingParams {
	/// 中继父区块中的平行链头部与新候选之间的最大平行链区块数。限制节点构建任意长的链并向其他验证者发送垃圾信息。
	///
	/// 当异步支持被禁用时，唯一有效的值是 0。
	pub max_candidate_depth: u32,
	/// 允许在中继父区块的多少个祖先上构建候选。
	///
	/// 当异步支持被禁用时，唯一有效的值是 0。
	pub allowed_ancestry_len: u32,
}
```

</br>
<pba-flex center>

用于测试潜在平行链的数字：

- `max_candidate_depth` = 4
- `allowed_ancestry_len` = 3

</pba-flex>

---

<!-- .slide: data-background-color="#4A2439" -->

# 支持性变更

---

## 声明分发变更

<img rounded style="width: 1500px" src="./img/Statement_Distribution.svg" />

Notes:

我们为什么需要重构？

答案：每个支持组的同时候选上限提高约 3 倍

提及：

- 公告 - 确认
- 请求 - 响应

---

## 供应者变更
供应者子系统中的`request_backable_candidates`函数
```rust
/// 根据核心状态从潜在平行链子系统请求可支持候选。
///
/// 当潜在平行链启用时应调用。
async fn request_backable_candidates(
    availability_cores: &[CoreState],
    bitfields: &[SignedAvailabilityBitfield],
    relay_parent: Hash,
    sender: &mut impl overseer::ProvisionerSenderTrait,
) -> Result<Vec<CandidateHash>, Error> {
    let block_number = get_block_number_under_construction(relay_parent, sender).await?;

    let mut selected_candidates = Vec::with_capacity(availability_cores.len());

    for (core_idx, core) in availability_cores.iter().enumerate() {
        let (para_id, required_path) = match core {
            CoreState::Scheduled(scheduled_core) => {
                // 核心空闲，从片段树中选择第一个符合条件的候选。
                (scheduled_core.para_id, Vec::new())
            },
            CoreState::Occupied(occupied_core) => {
                if bitfields_indicate_availability(core_idx, bitfields, &occupied_core.availability)
                {
                    if let Some(ref scheduled_core) = occupied_core.next_up_on_available {
                        // 占用核心的候选可用，选择其在片段树中的子节点。
                        (scheduled_core.para_id, vec![occupied_core.candidate_hash])
                    } else {
                        continue
                    }
                } else {
                    if occupied_core.time_out_at != block_number {
                        continue
                    }
                    if let Some(ref scheduled_core) = occupied_core.next_up_on_time_out {
                        // 候选的可用性超时，实际上与已调度的情况相同。
                        (scheduled_core.para_id, Vec::new())
                    } else {
                        continue
                    }
                }
            },
            CoreState::Free => continue,
        };

        let candidate_hash =
            get_backable_candidate(relay_parent, para_id, required_path, sender).await?;

        match candidate_hash {
            Some(hash) => selected_candidates.push(hash),
            None => {
                gum::debug!(
                    target: LOG_TARGET,
                    leaf_hash =?relay_parent,
                    core = core_idx,
                    "潜在平行链未返回可支持候选",
                );
            },
        }
    }

    Ok(selected_candidates)
}
```
Notes:
- 逐个核心分析
    - 讨论核心的空闲、已调度、已占用状态
    - 讨论核心释放的标准
        - `bitfields_indicate_availability`
            - `next_up_on_available`
        - 可用性超时
            - `next_up_on_timeout`
    - 解释`required_path`是什么
    - 为什么`required_path`留空？

---
<img rounded style="width: 500px" src="./img/cumulus.png" />
## Cumulus变更
<pba-flex center>
- 共识驱动的区块生成
- 平行链共识重构
    - Aura重写
    - 自定义排序共识：
        - Tendermint
        - Hotshot共识
</pba-flex>
---
<!-- .slide: data-background-color="#4A2439" -->
# 异步支持的优势，当前及未来
---
## 异步支持的优势
<pba-flex center>
1. 每个区块的外部交易数量增加3 - 5倍
1. 平行链区块时间缩短，从12秒减至6秒
1. 区块空间数量相应提升6 - 10倍
1. 减少浪费的平行链区块
</pba-flex>
Notes:
1. 整理者有更多时间填充每个区块
1. 提前工作确保每个平行链每6秒都有可支持候选在中继链上进行支持
1. 不言而喻
1. 当平行链区块首次尝试未进入中继链时，允许“重用”这些区块
---
## 异步支持与特殊核心调度
<pba-flex center>
- 什么是特殊核心调度？
    - 每个平行链多个核心
    - 多种长度的重叠租赁
    - 租赁 + 按需使用
- 异步支持如何提供帮助？
</pba-flex>
Notes:
- 未包含的部分对于在单个中继区块中构建2个或更多平行链区块是必要的
---
<img rounded style="width: 500px" src="./img/stopwatch.png" />
## 更短的区块时间？
<pba-flex center>
- 异步支持实现了未包含区块的排队
- 为了实现有效的更短时间，我们还需要：
    - 软最终性
    - 包含依赖（随弹性扩展而来）
</pba-flex>
Notes:
- 软最终性意味着如果一个平行链区块候选被丢弃，整理者将根据需要提交尽可能多包含相同外部交易的新区块，以保持相同的顺序。
- 包含依赖：假设有两个平行链区块a和b，a构建在b之上。如果在同一区块时间内，a和b在两个不同核心上进行可用性处理，我们需要确保b等待被包含，直到a也被包含。
---
## 资源
<pba-col center>
1. [波卡异步支持拉取请求](https://github.com/paritytech/polkadot/pull/5022)
1. [实施者指南：潜在平行链](https://github.com/paritytech/polkadot/blob/631b66d5daa642fad7ed0a9712194c5b85b96563/roadmap/implementers-guide/src/node/backing/prospective-parachains.md)
   <!-- FIXME上述指南已过时 -->
</pba-col>
---
<!-- .slide: data-background-color="#4A2439" -->
# 问题