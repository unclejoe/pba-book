---
title: Execution Sharding in Polkadot
description: Details the Collation, Backing, Approval-Voting, and Disputes systems and how they work together to provide secure execution sharding in Polkadot.
duration: 45 minutes
---

#  Polkadot中的执行分片

---

## 执行分片

> 执行分片是指在验证者集合中分配区块链执行责任的过程。

---

##  Polkadot中的执行分片

> 在 Polkadot中，所有验证者都会执行每个中继链区块，但只有一部分验证者会执行每个平行链区块。

这使得 Polkadot能够实现扩展。

---

## 课程议程

<pba-flex center>

1. 讨论 Polkadot中执行分片的高级协议和原则
1. 介绍使用Substrate实现复杂的链上和链下逻辑的背景

</pba-flex>

Notes:

[ Polkadotv1.0：分片和经济安全](https://www.polkadot.network/blog/polkadot-v1-0-sharding-and-economic-security/) 是对这里内容的更详细的全面阐述。
如果您想全面了解 Polkadot的工作原理，请在课程结束后阅读。

---

## 执行分片的目标

<pba-flex center>

1. 在仍能保持安全性的前提下，应尽量少的验证者节点来检查每个平行链区块
1. 中继链将为平行链区块提供排序和最终确定性
1. 只有有效的平行链区块才会被最终确定

</pba-flex>

Notes:

由于GRANDPA最终确定性故障需要削减33%或更多的权益，目标（3）意味着共享安全。

---

## 分叉情况

在最终确定之前，中继链可能会“分叉”，通常是由于竞争而意外发生的。

方法：故意从我们不喜欢的未最终确定的区块分叉出去。

<img rounded style="width: 700px" src="./img/BABE-is-forkful.png" />

Notes:

在幻灯片中，我们将查看协议的单个实例，但应该知道，验证者实际上是彼此并行地执行这些步骤，并且通常一次会执行多次。

---

## 平行链协议

<pba-flex center>

1. **整理**：制作平行链区块
1. **支持**：验证者对区块进行初步检查并签署
1. **可用性**：分发检查所需的数据
1. **批准检查**：检查区块
1. **争议解决**：让支持者承担责任

</pba-flex>

---

验证者不断运行这些协议的多个实例，以处理处于生命周期不同阶段的候选区块。

---

### 候选区块生命周期

<img rounded style="width: 1000px" src="./img/candidate_paths.svg" />

---

## 宏观视角

 Polkadot的方法是在最佳情况下让尽可能少的验证者检查每个平行链区块。

首先，**支持者**将新的候选区块介绍给其他验证者，并提供“利益攸关”的担保。

然后，**批准检查者**对其进行监督。

---

目标：尽可能减少检查者的数量。

---

#### 验证者组分配和执行核心

<img rounded style="width: 1100px" src="./img/validator-groups.png" />

Notes:

每个会话（4小时），验证者会被**分组**为小团体共同工作。
这些小组被分配到特定的**执行核心**，并且这些分配每隔几个区块就会改变。

---

## 定义：候选区块

> **候选区块**是指尚未在中继链中最终确定的平行链区块。

---

## 定义：头部数据

> **头部数据**是平行链当前状态的不透明且紧凑的表示形式。
> 它可以是一个哈希值或一个小的区块头，但必须很小。

---

## 定义：平行链验证函数（PVF）

从验证者的角度来看，平行链是一个WebAssembly二进制文件，它公开了以下（简化）函数：

```rust
type HeadData = Vec<u8>;
struct ValidationResult {
  /// 应包含在中继链状态中的新头部数据。
  pub head_data: HeadData,
  // 更多字段，如传出消息、更新的代码等。
}

fn validate_block(parent: HeadData, relay_parent: RelayChainHash, pov: Vec<u8>)
  -> Result<ValidationResult, ValidationFailed>;
```

---

#### `validate_block` 函数可能失败的原因有哪些？

1. `parent` 或 `PoV` 格式错误 - 实现无法将其从不透明的表示形式转换为特定的表示形式
1. `parent` 和 `PoV` 解码正确，但不会导致有效的状态转换
1. `PoV` 是一个有效的区块，但不是从 `parent` 衍生而来

```rust
fn validate_block(parent: HeadData, relay_parent: RelayChainHash, pov: Vec<u8>)
  -> Result<ValidationResult, ValidationFailed>;
```

---

## 中继链区块内容

<img rounded style="width: 1000px" src="./img/block_contents.svg" />

---

任何节点都可以被选为下一个中继链区块的作者，因此这些数据必须广泛传播。

---

## 整理

整理者的工作是构建一个能够通过 `validate_block` 验证的内容。

在整理阶段，预定平行链的整理者构建一个平行链区块并生成一个候选区块。

整理者通过p2p网络将其发送给分配给该平行链的验证者组。

---

一些整理者的伪代码：

```rust
fn simple_collation_loop() {
  while let Some(relay_hash) = wait_for_next_relay_block() {
    let our_core = match find_scheduled_core(our_para_id, relay_hash) {
      None => continue,
      Some(c) => c,
    };

    let parent = choose_best_parent_at(relay_hash);
    let (pov, candidate) = make_collation(relay_hash, parent);
    send_collation_to_core_validators(our_core, pov, candidate);
  }
}
```

---

<img rounded style="width: 1000px" src="./img/pvf_duality.svg" />

---

## 支持

在支持阶段，分配组的验证者共享他们从整理者那里收到的候选区块，对其进行验证，并签署声明以证明其有效性。

验证大致意味着：执行 `validate_block` 并检查结果。

他们通过P2P层分发他们的候选区块和声明，然后下一个中继链区块的作者将候选区块和声明捆绑到中继链区块中。

---

## 支持：网络

<img rounded style="width: 1000px" src="./img/backing-networking.png" />

---

## 支持：利益攸关

支持的主要目标是提供“利益攸关”的担保。

支持者同意，如果平行链区块被证明是无效的，他们将损失100%的权益。

仅靠支持并不能提供安全性，只能提供问责制。

Notes:

截至2023年8月1日，当前的最低验证者抵押约为170万个DOT。

---

## 可用性

此时，支持者负责将检查平行链区块所需的数据提供给整个网络。

验证者签署关于他们拥有哪些数据的声明，并将其发布到中继链上。

如果平行链区块没有足够快地获得足够的声明，中继链运行时会将其丢弃。

---

#### 纠删编码

<div class="grid grid-cols-3">

<div>

<img rounded style="width: 450px" src="./img/erasure_coding.png" />

</div>

<div class="col-span-2">

每个验证者负责这部分数据的一块。
只要有足够多的这些块可用，数据就可以恢复。

验证者签署并分发给所有其他验证者的声明基本上是说“我有我的那一块”。

一旦2/3或更多这样的声明上链，候选区块就可以被检查并被**包含**。

</div>

</div>

---

一些关于可用性的伪代码：

```rust
fn get_availability_chunks() {
  while let Some(backed_candidate, backing_group) = next_backed_candidate() {
    let my_chunk = fetch_chunk(
      my_validator_index,
      backed_candidate.hash(),
      backing_group,
    );
    let signed_statement = sign_availability_statement(backed_candidate.hash());
    broadcast_availability_statement(signed_statement);
  }
}
```

---

<img rounded style="width: 1300px" src="./img/availability-inclusion.png" />

Notes:

实际上，我们允许可用性超时超过一个区块的时间。

---

## 平行链区块的包含和最终确定性

<img rounded style="width: 600px" src="./img/parachain-finality.png" />

---

## 平行链区块的包含和最终确定性

> （3）只有有效的平行链区块才会被最终确定

Notes:

还记得我们之前的目标吗？

---

## 平行链区块的包含和最终确定性

为了实现这个目标，我们需要两件事。

<pba-flex center>

1. 一个用于证明包含的候选区块有效性的协议
1. 中继链的共识规则，以避免在包含无效候选区块的中继链分叉上构建或最终确定。

</pba-flex>

---

## 什么是“检查”一个平行链区块？

检查涉及三个操作：

<pba-flex center>

1. 从网络中恢复数据（通过获取块）
1. 执行平行链区块，检查是否成功
1. 检查输出是否与支持者发布到中继链上的输出匹配

</pba-flex>

Notes:

第3步至关重要。
没有这一步，支持者可以凭空创建诸如消息和运行时升级之类的东西，方法是支持一个有效的候选区块，但对候选区块的输出进行撒谎。

---

## 安全模型：赌徒破产

 Polkadot的安全论证基于赌徒破产原理。

一个能够进行数十亿次尝试来暴力破解该过程的攻击者最终可能会成功。

但由于削减机制，每次失败的尝试都意味着大量的DOT被削减。

---

<!-- .slide: data-background-color="#000" -->

## 批准检查

---

每个验证者在本地的一个**状态机**中跟踪其对每个未最终确定的、已包含的候选区块的有效性的看法。

这个状态机总是输出“批准”或停滞。

---

#### 关键属性：

<pba-flex center>

1. 验证者上的状态机输出基于它收到的声明。
1. 如果平行链区块确实有效（即通过检查），那么它最终会在诚实节点上输出“批准”。
1. 如果平行链区块无效，那么它被检测到的可能性要比在足够多的诚实节点上输出“批准”的可能性大得多。

</pba-flex>

Notes:

诚实节点只有在存在大量恶意检查者，并且它们主要看到这些恶意检查者的投票而不是诚实检查者的投票时，才会输出“批准”。
这里的低概率意味着大约十亿分之一（假设3f < n）
不是密码学意义上的低概率，但对于加密经济学来说已经足够了。

---

验证者跟踪关于**每个**候选区块的声明。

验证者只对**少数**候选区块发表声明。

---

验证者发表两种类型的声明：

- 分配：“我打算检查X”
- 批准：“我检查并批准了X”

---

<img rounded style="width: 1300px" src="./img/approval_state.svg" />

---

每个验证者都被分配去检查每个平行链区块，但时间不同。

验证者总是**生成**他们的分配，但除非需要，否则会保持秘密。

---

<img rounded style="width: 1300px" src="./img/approval_flow.svg" />

---

验证者的分配在**揭示**之前是秘密的。

验证者在检查候选区块之前**分发**已揭示的分配。

没有后续行动的分配称为**未到场**。
未到场的情况很可疑，会导致验证者提高批准的门槛。

Notes:

如果验证者在揭示他们的分配之前就开始下载数据，攻击者可能会注意到这一点并攻击他们，而没有人会察觉到。

---

<img rounded style="width: 1300px" src="./img/approval_state.svg" />

<img rounded style="width: 700px" src="./img/lernaean-hydra.jpg" />

Notes:

批准检查就像九头蛇。
每次攻击者砍掉一个头，就会出现两个新的头。

---

只需要一个诚实的检查者就可以发起争议。

---

## 争议

当验证者对平行链区块的有效性存在分歧时，会自动引发争议。

争议涉及所有验证者，他们必须检查该区块并进行投票。

已经提交的支持和批准声明将被视为争议投票。

投票通过p2p传输，也会在链上收集。

---

## 争议解决

<img rounded style="width: 700px" src="./img/validator-dispute-participation.png" />

Notes:

解决争议需要在任何一个方向上获得超级多数的支持。

---

## 争议削减

在争议中失败的一方的验证者将被削减权益。

当候选区块被超级多数判定为无效时，惩罚会很大；当被判定为有效时，惩罚会很小。

---

## GRANDPA投票规则
验证者不是投票给最长链，而是投票给这样一条最长链：其中所有未最终确定且已包含的候选区块需满足以下条件：
<pba-flex center>
1. 根据其本地状态机已获批准
2. 据其所知无争议
</pba-flex>
<img rounded style="width: 650px" src="./img/grandpa-voting-rule.png" />

---

## BABE链选择规则
验证者拒绝在包含无效平行链区块或争议失败的平行链区块的分叉上生成中继链区块。
每当针对某个候选区块的争议得到解决时，这就会引发一次“重组”。
<img rounded style="width: 650px" src="./img/babe-chain-selection.png" />

---
<!-- .slide: data-background-color="#4A2439" -->
> 如何使用Substrate实现复杂的链下系统？

---

## 客户端与运行时之间的交互
由于 Polkadot不仅涉及链上逻辑，还涉及链下逻辑，运行时是有关验证者、任务分配、平行链状态等信息的核心事实来源。
客户端通过调用近期区块的**运行时API**来了解状态，而运行时则通过**新区块**进行更新。
<img rounded style="width: 900px" src="./img/runtime-node-interaction.png" />

Notes:
由于运行时由新区块进行更新，恶意或连接不佳的验证者在向运行时提供哪些信息方面有一定的选择权。
协议中必须考虑到这一点：我们不能假设运行时总能获取完美信息。

---

## Orchestra
> <https://github.com/paritytech/orchestra>
**Orchestra** 允许我们将节点的逻辑拆分为多个异步运行的“子系统”。
这些子系统通过消息传递进行通信，并且都会接收协调其活动的信号。

---

## Orchestra：信号
信号会发送到所有子系统，起到“心跳”的作用。
一个子系统接收到信号后发送的消息，在另一个子系统接收到相同信号之前，无法到达该子系统。

---

## Orchestra： Polkadot中的信号
```rust
/// 由监管者（ Polkadot中Orchestra的名称）发送给所有子系统的信号。
pub enum OverseerSignal {
    /// 子系统应调整其任务，开始或停止对相应区块哈希的处理工作。
    ActiveLeaves(ActiveLeavesUpdate),
    /// `Subsystem` 通过区块哈希和编号得知某个区块已最终确定。
    BlockFinalized(Hash, BlockNumber),
    /// 结束 `Overseer` 和所有 `Subsystem` 的工作。
    Conclude,
}
```
Notes:
 Polkadot中Orchestra的实例化名为“Overseer”。

---

## 没有Orchestra的情况：
```rust
fn on_new_block(block_hash: Hash) {
    let work_result = do_some_work(block_hash);
    inform_other_code(work_result);
}
```
问题：存在竞态条件！
其他代码可能在得知新区块之前就收到 `work_result`。

---

## 有Orchestra的情况：
```rust
fn handle_active_leaves_update(update: ActiveLeavesUpdate) {
    if let Some(block_hash) = update.activated() {
        let work_result = do_some_work(block_hash);
        inform_other_subsystem(work_result);
    }
}
```
这样就没问题了！
Orchestra确保发送给其他子系统的消息，只有在该子系统接收到关于新区块的相同更新后才会到达。

---

##  Polkadot中的子系统示例
<pba-flex center>
- 争议参与
- 候选区块支持
- 可用性分发
- 批准检查
- 整理者协议
- 涵盖所有方面！
</pba-flex>

---

## 开发者指南
[开发者指南](https://paritytech.github.io/polkadot/book/) 包含有关 Polkadot运行时和节点实现中使用的所有子系统、架构动机和协议的信息。

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题