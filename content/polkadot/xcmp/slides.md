---
title: Cross-Chain Message Passing (XCMP)
description: Introduction to Cross-Chain Message Passing in Polkadot
duration: 45 minutes
---

# XCMP: 跨链消息传递

---

之前的讲座讨论了 Polkadot如何为通用代码执行提供安全的环境，以及它是如何进行扩展的。

---

本次讲座将讨论在该环境中进程是如何进行通信的。

---

<img rounded width="1200px" src="./img/large_environment.svg" />

---

<img rounded width="1200px" src="./img/small_environment.svg" />

---

<img rounded width="1200px" src="./img/small_environment_movement.svg" />

---

<img rounded width="1200px" src="./img/large_environment_movement.svg" />

---

中继链运行时为平行链维护消息队列。

它充当平行链之间以及平行链与中继链自身之间的桥梁。

---

为了实现这一点，它对候选区块添加了额外的限制，以确保消息传递规则得到遵守。

---

## XCMP 的实际应用

---

## XCMP 与 XCM 的区别

XCMP 是数据层，而 XCM 是语言层。

XCMP 仅专注于链之间原始字节的传输。<br />
在本课程中，我们将只关注 XCMP。

<img rounded width="400px" src="./img/people-talking.jpg" />

Notes:

通信需要一种媒介和一种语言来传达语义。
XCMP 是媒介，就像声音或文字一样，而不是语言。

---

<img rounded width="1300px" src="./img/messaging-kinds.svg" />

---

下行和上行通道是隐式可用的。

XCMP 通道必须显式打开。

---

XCMP 通道是单向的，要进行双向通信，必须打开两个通道。

---

## 消息传递的实际应用

本课程旨在介绍消息发送和接收所基于的底层协议和机制。

在实际应用中，这些机制由你所使用的库进行了抽象处理。

---

## 重新审视 PVF 和输出

<img rounded width="800px" src="./img/PVF.svg" />

```rust
fn validate_block(ValidationParams) -> Result<ValidationResult, ValidationFailed>;
```

---

## 验证输入

```rust
/// 提供给 PVF 用于验证的参数
pub struct ValidationParams {
	/// 父平行链区块的头部数据
	pub parent_head: HeadData,
	/// 有效性证明
	pub pov: PoV,
	/// 当前中继链的区块号
	pub relay_parent_number: RelayChainBlockNumber,
	/// 中继链区块的存储根
	pub relay_parent_storage_root: Hash,
}
```

这里需要注意的是，`relay_parent_storage_root` 允许我们在平行链内处理中继链状态的默克尔证明。

---

## 验证输出

```rust
/// 平行链区块成功验证的输出
pub struct ValidationResult {
	/// 执行产生的头部数据
	pub head_data: HeadData,
	/// 平行链发送的上行消息
	pub upward_messages: Vec<UpwardMessage>,
	/// 平行链发送的出站水平消息
	pub horizontal_messages: Vec<OutboundHrmpMessage>,
	/// 平行链处理的下行消息数量
	///
	/// 期望平行链按顺序从第一条消息开始处理。
	pub processed_downward_messages: u32,
	/// 标记指定处理所有入站 HRMP 消息的区块号
	pub hrmp_watermark: RelayChainBlockNumber,

	// ... 更多字段
}
```

---

`ValidationResult` 是 PVF 成功执行的输出。

验证者负责检查输出是否正确。

---

## 解包候选区块

---

候选区块会完整地发布到中继链上，除了有效性证明（PoV）之外的所有内容。

---

## 候选区块分解

<pba-flex center>

1. 描述符：定义验证函数的输入
1. 承诺：验证函数的预期输出

</pba-flex>

---

```rust
pub struct CandidateDescriptor {
  /// 中继链区块哈希
  pub relay_parent: RelayChainBlockHash,
  /// PoV 的哈希
  pub pov_hash: Hash,
  /// 父头部数据哈希
  pub parent_hash: Hash,
  /// 平行链的唯一 ID
  pub para_id: ParaId,

  // .. 还有一些其他字段
}
```

---

```rust
pub struct CandidateCommitments {
  /// 旨在由中继链本身解释的消息
  pub upward_messages: UpwardMessages,
  /// 平行链发送的水平消息
  pub horizontal_messages: HorizontalMessages,
  /// 执行产生的头部数据
  pub head_data: HeadData,
  /// 从 DMQ 处理的消息数量
  pub processed_downward_messages: u32,
  /// 标记指定处理所有入站 HRMP 消息的区块号
  pub hrmp_watermark: RelayChainBlockNumber,
}
```

---

注意到和 `ValidationOutputs` 的相似之处了吗？

---

<img rounded style="width: 1000px" src="../../polkadot/execution-sharding/img/block_contents.svg" />

---

（简化的 Polkadot运行时）

```rust
// 中继链清空，平行链发布
UpwardMessages: StorageMap<ParaId, Deque<Message>>;

// 中继链发布，平行链清空
DownwardMessages: StorageMap<ParaId, Deque<Message>>;

// (发送者, 接收者)
// 发送者发布，接收者清空
HrmpChannels: StorageMap<(ParaId, ParaId), Deque<Message>>;
```

---

（简化的 Polkadot运行时，包含 pallet）

```rust
fn process_backed_candidate(CandidateDescriptor, CandidateCommitments) {
  let para_id = descriptor.para_id;

  assert!(is_scheduled_on_empty_core(para_id));
  assert!(descriptor.parent_hash == current_parachain_head);
  assert!(is_in_this_chain_recently(descriptor.relay_parent));

  // 如果消息数量过多则失败
  assert!(check_upward(para_id, commitments.upward_messages).is_ok());

  // 如果消息数量过多或发送到未打开通道的链则失败
  assert!(check_hrmp_out(para_id, commitments.hrmp_messages).is_ok());

  // 如果尝试处理的消息数量超过实际存在的数量则失败
  assert!(check_downward(para_id, commitments.processed_downward_messages).is_ok());

  // 如果水印低于前一个则失败
  // 更新所有此链为接收者的通道
  assert!(check_hrmp_in(para_id, commitments.hrmp_watermark).is_ok());
}
```

---

候选区块只有通过所有这些检查才能被“支持”。

中继链区块的作者负责选择通过这些检查的候选区块。

---

消息只有在候选区块被包含（可用）后才会被添加到队列中。

这允许消息在最终确认之前被传递和处理。

---

如果候选区块被证明是无效的，整个中继链将回滚到消息被排队或处理之前的某个点。

---

## 平行链主机配置

```rust
pub struct HostConfiguration {
  // ... 很多很多字段
}

// 在 Polkadot运行时存储中：
CurrentConfiguration: StorageValue<HostConfiguration>;
```

这些变量由治理机制进行更新。

---

主机配置指定了诸如以下内容：

- 平行链的上行、下行或 HRMP 队列中可以包含多少条消息
- 平行链的上行、下行或 HRMP 队列中可以包含多少字节的数据
- 上行、下行或 HRMP 队列中单个消息的最大大小。

---

<!-- .slide: data-background-color="#000" -->

## 什么是消息？

---

消息只是 `Vec<u8>` 字节字符串。

---

中继链将上行消息解释为 XCM。

目前的主要结论是，它允许平行链在中继链上执行 `Call` 调用。

---

```rust
// 作为一个具有基于 Para ID 的确定性 ID 的常规账户。
Origin::Signed(AccountId),
// 作为平行链本身，用于平行链可能发出的调用。
// 自定义的起源类型，添加到中继链中。
Origin::Parachain(ParaId),
```

Notes:

当平行链在中继链上执行 `Call` 调用时，它们可以使用两种起源类型。

请注意，这仅适用于中继链，平行链的消息在其他链上可能会有不同的解释。

---

平行链可以自由地以任何方式解释其接收到的下行或 HRMP 消息。

---

<!-- .slide: data-background-color="#000" -->

## 遵守限制

---

问题：平行链候选区块只有在遵守消息发送和接收的限制时才能被“支持”。它们是如何确保这一点的呢？

---

解决方案：PVF 可以“读取中继链状态”来找出这些限制。它们将这些证明包含在 PoV 中。

---

```rust
/// 提供给 PVF 用于验证的参数
pub struct ValidationParams {
	/// 中继链区块的存储根
	pub relay_parent_storage_root: Hash,
	pub pov: PoV,

	// ...
}
```

---

```rust
fn validate_block(ValidationParams) -> Result<ValidationResult, ValidationFailed> {
  // 简化版
  let storage_proof = extract_storage_proof(pov);

  // 队列状态，以及 `HostConfiguration` 的子集。
  let (message_queues, current_config) = check_storage_proof(
    relay_parent_storage_proof,
    storage_proof,
  )?;

  // 处理入站消息并发送出站消息，同时遵守配置中的限制。
}
```

---

<!-- .slide: data-background-color="#000" -->

## 打开通道

---

打开 HRMP 通道的协议如下：

<pba-flex center>

1. 链 A 发送一个上行消息，请求与链 B 建立通道
1. 链 B 收到一个下行消息，通知其通道请求
1. 链 B 发送一个上行消息，接受或拒绝该通道
1. 结果是该通道在中继链上被打开或拒绝

</pba-flex>

---

XCMP 消息没有费用，但每个通道都有一个 `max_capacity` 和 `max_message_size`。

每个通道都需要相应的 DOT 代币作为“押金”，以支付中继链的状态使用费用。

当通道关闭时，押金将被退还。

---

<img rounded width="1000px" src="./img/hrmp-channel.svg" />

---

## 消息队列链（MQC）

让我们稍微绕个弯，了解一下 DMP 和 XCMP 中使用的一种数据结构。

**问题**：平行链应该能够廉价地确定整个消息队列的状态。

**问题**：中继链状态证明成本高昂，应该尽量减少使用。

解决方案：消息队列链（MQC）

---

## MQC 架构

<img rounded width="700px" src="./img/mqc.svg" />

---

使用 MQC，了解单个队列的所有入站消息只需要一个存储证明和每个入站消息一个 MQC 条目（70 字节）。

---

## UMP 配置

```rust
pub struct HostConfiguration {
	/// 平行链到中继链消息队列中允许的单个消息的总数
	pub max_upward_queue_count: u32,
	/// 平行链到中继链消息队列中允许的消息总大小
	pub max_upward_queue_size: u32,
	/// 候选区块可以发送的上行消息的最大大小
	///
	/// 此参数影响 `CandidateCommitments` 的大小上限。
	pub max_upward_message_size: u32,
	/// 候选区块可以包含的最大消息数量
	///
	/// 此参数影响 `CandidateCommitments` 的大小上限。
	pub max_upward_message_num_per_candidate: u32,
	// ... 更多字段
}
```

---

## UMP 的验证输出

```rust
/// 平行链区块成功验证的输出
pub struct ValidationResult {
	/// 平行链发送的上行消息
	pub upward_messages: Vec<UpwardMessage>,
	// ... 更多字段
}
```

---

## DMP 配置

```rust
pub struct HostConfiguration {
	/// 可以放入下行消息队列的消息的最大大小
	pub max_downward_message_size: u32,
}
```

---

## DMP 的验证输出

```rust
/// 平行链区块成功验证的输出
pub struct ValidationResult {
	/// 平行链处理的下行消息数量
	///
	/// 期望平行链按顺序从第一条消息开始处理。
	pub processed_downward_messages: u32,
	// ... 更多字段
}
```

Notes:

平行链可以通过简单地忽略消息来“处理”消息。
中继链并不关心平行链对消息做了什么。
这些消息可以直接被丢弃。

---

## HRMP 的验证输出

```rust
/// 平行链区块成功验证的输出
pub struct ValidationResult {
	/// 平行链发送的出站水平消息
	pub horizontal_messages: Vec<OutboundHrmpMessage>,
	/// 标记指定处理所有入站 HRMP 消息的区块号
	pub hrmp_watermark: RelayChainBlockNumber,

	// ... 更多字段
}

pub struct OutboundHrmpMessage {
	/// 将在其下行消息队列中接收此消息的平行链
	pub recipient: ParaId,
	/// 消息有效负载
	pub data: sp_std::vec::Vec<u8>,
}
```

---

## HRMP 配置

```rust
pub struct HostConfiguration {
	pub hrmp_max_parachain_outbound_channels: u32,

	pub hrmp_sender_deposit: Balance,
	pub hrmp_recipient_deposit: Balance,

	pub hrmp_channel_max_capacity: u32,
	pub hrmp_channel_max_total_size: u32,

	pub hrmp_max_parachain_inbound_channels: u32,
	pub hrmp_channel_max_message_size: u32,

  // 更多字段...
}
```

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题
