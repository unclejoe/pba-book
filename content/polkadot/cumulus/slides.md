---
title: Cumulus Deep Dive
description: Cumulus, architecture and function
duration: 1.25 hours
---

# Cumulus 深度剖析

Notes:

Cumulus 是将基于 Substrate 的链连接到 Polkadot 的粘合剂，将它们转换为平行链。

---

### 大纲

<pba-flex center>

1. 什么是 Cumulus？
1. Cumulus 与平行链 - 中继链通信

<!-- .element: class="fragment" data-fragment-index="1" -->

1. Cumulus 如何让平行链节点保持信息同步
<!-- .element: class="fragment" data-fragment-index="2" -->

1. 收集生成与通告
<!-- .element: class="fragment" data-fragment-index="3" -->

1. Cumulus 收集如何实现平行链块验证
<!-- .element: class="fragment" data-fragment-index="4" -->

1. Cumulus 如何实现运行时升级
<!-- .element: class="fragment" data-fragment-index="5" -->

</pba-flex>

---

## 什么是 Cumulus

一组代码库，扩展了 Substrate FRAME 链，使其能够与 Polkadot API 进行交互，基于中继链的共识机制运行，并提交平行链块进行验证。

---

<div class="r-stack">
<img src="./img/spc_1.svg" style="width: 70%" />
<img src="./img/spc_2.svg" style="width: 70%" />
<!-- .element: class="fragment" data-fragment-index="1" -->
<img src="./img/spc_3.svg" style="width: 70%" />
<!-- .element: class="fragment" data-fragment-index="2" -->
</div>

Notes:

- Substrate 是一个用于构建区块链的框架
- 但只能构建“独立”链
- 分为运行时和节点端
- Polkadot 和 Cumulus 都对 Substrate 进行了扩展
- Polkadot 为收集者提供了 API

---

## 回顾：收集者和收集

<pba-flex center>

> 什么是收集者？

> 什么是收集？

> 什么是 PoV？

</pba-flex>

Notes:

- 收集者：
  - 共识权威集的一部分
  - 创作并提交收集
- 收集：验证者处理和验证平行链块所需的信息。
- 收集包括：上行和横向消息、新的验证代码、结果头数据、有效性证明、已处理的下行消息以及 hrmp_watermark（所有 hrmp 消息都已处理的中继链块）
- PoV：足以验证一个块的最小信息包。
  稍后将更详细地介绍。

---

## Cumulus 的关键流程

- 跟随中继链的“最新最佳头块”来更新平行链的“最新最佳头块”
- 跟随中继链的最终块来更新平行链的最终块

<!-- .element: class="fragment" data-fragment-index="1" -->

- 从中继链请求对等节点未共享的平行链块（数据恢复）

<!-- .element: class="fragment" data-fragment-index="2" -->

- 收集生成和通告

<!-- .element: class="fragment" data-fragment-index="3" -->

Notes:

- 最新最佳头块：BABE 最偏好的分叉链头部的新块

---

## Cumulus 与平行链 - 中继链通信

<div class="r-stack">
<img src="./img/para-relay_communication_1.svg" style="width: 1100px" />
<img src="./img/para-relay_communication_2.svg" style="width: 1100px" />
<!-- .element: class="fragment" data-fragment-index="1" -->
</div>

Notes:

- 这些通信渠道是如何服务于我们的关键流程的？

---

## 处理传入的中继链信息

在讨论收集生成之前，让我们先讨论 Cumulus 的其他三个关键流程。
这些流程驱动平行链共识并确保平行链块的可用性。

<br />
它们共同确保平行链节点保持最新状态，从而使收集成为可能。

Notes:

回顾一下，这些关键流程是：

- 跟随中继链的“最新最佳头块”来更新平行链的“最新最佳头块”
- 跟随中继链的最终块来更新平行链的最终块
- 从中继链请求对等节点未共享的平行链块（数据恢复）

---

### 共识机制

平行链共识机制被修改为：

<pba-flex center>

- 实现排序共识
- 将最终性交给中继链

</pba-flex>

Notes:

- 排序共识：确定块以及块内交易的接受顺序
- 排序共识要求我们更新关于平行链最新最佳头块的认知。
  这样，节点就可以就基于哪个块进行构建达成一致。
- 排序选项：Aura 共识、Tendermint 风格共识
- 当一个平行链块被包含在一个最终确定的中继链块中时，该平行链块也随之最终确定。

---

### 导入驱动的块创作

收集者负责创作新块，他们在导入中继链块时进行创作。
诚实的收集者会选择从最佳头块派生的块进行创作。

```rust[|4-8]
// 极大简化
loop {
    let imported = import_relay_chain_blocks_stream.next().await;

    if relay_chain_awaits_parachain_candidate(imported) {
        let pov = match parachain_trigger_block_authoring(imported) {
            Some(p) => p,
            None => continue,
        };

        relay_chain_distribute_pov(pov)
    }
}
```

<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

- `parachain_trigger_block_authoring` 本身可以决定是否要构建一个块。
- 例如，平行链的块时间为 30 秒
- 有了异步支持，平行链块的创作就不再依赖于中继链块的导入。

---

### 最终性

为了实现共享安全，平行链从中继链继承其最终性。

<br />

```rust[|4-8]
// 极大简化
loop {
    let finalized = finalized_relay_chain_blocks_stream.next().await;

    let finalized_parachain_block =
      match get_parachain_block_from_relay_chain_block(finalized) {
        Some(b) => b,
        None => continue,
    };

    set_finalized_parachain_block(finalized_parachain_block);
}
```

---

### 确保块的可用性

作为平行链协议的一部分，Polkadot 会在平行链块被支持后的几个小时内使其保持可用状态。
<br /><br />

<pba-flex center>

- 为什么需要这样做？
  - 批准
  - 恶意收集者

</pba-flex>

Notes:

- 批准者需要 PoV 来进行验证
- 不能仅仅信任支持者忠实地分发 PoV
- 恶意或有故障的收集者可能会向验证者通告收集，而不与其他平行链节点共享。
- 在这种情况下，Cumulus 负责请求缺失的块

---

#### 简短题外话：候选收据

当一个平行链块被支持时，PoV 太大而无法包含在链上，因此验证者会生成一个固定大小的**候选块收据**来表示刚验证的块及其输出

Notes:

候选收据主要包含哈希值，因此其唯一有价值的用途是用于验证已知 PoV 的正确性
候选收据仅引用 PoV，不能替代它

---

### 恶意收集者示例

<div class="r-stack">
<img src="./img/malicious_collator_1.svg" style="width: 900px" />
<!-- .element: class="fragment fade-out" data-fragment-index="1" -->
<img src="./img/malicious_collator_2.svg" style="width: 900px" />
<!-- .element: class="fragment" data-fragment-index="1" -->
<img src="./img/malicious_collator_3.svg" style="width: 900px" />
<!-- .element: class="fragment" data-fragment-index="2" -->
</div>

Notes:

- 在平行链上，一个块只需被中继链验证者接受即可成为规范链的一部分，
- 问题在于，收集者可以将一个块发送到中继链，而不在平行链网络中分发它
- 因此，中继链可能期望某个父块作为下一个块的基础，而没有人知道这个父块

---

### 可用性流程

<pba-flex center>

- 对 PoV 应用纠删编码，将其分解为多个块
- 是原始 PoV 大小的 3 倍，而存储副本则需要 300 倍的空间

<!-- .element: class="fragment" data-fragment-index="1" -->

- 1/3 的块足以组装 PoV

<!-- .element: class="fragment" data-fragment-index="2" -->

- 2/3 的验证者必须声明拥有他们的块

<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-flex>

---

## 可用性结果

<img src="./img/malicious_collator_4.svg" style="width: 70%" />

---

# 收集生成与通告

---

## 收集生成

我们的最后一个关键流程

<pba-flex center>

1. 中继节点导入一个块，在该块中平行链的可用性核心被释放
1. `CollationGeneration` 向收集者请求一个收集

<!-- .element: class="fragment" data-fragment-index="1" -->

1. 平行链共识决定这个收集者是否可以创作块

<!-- .element: class="fragment" data-fragment-index="2" -->

1. 收集者提议、密封并导入一个新块

<!-- .element: class="fragment" data-fragment-index="3" -->

1. 收集者将新块和处理及验证所需的信息打包在一起，形成一个**收集！**

<!-- .element: class="fragment" data-fragment-index="4" -->

</pba-flex>

Notes:

- Aura 是当前默认的平行链共识机制，但这个共识机制是模块化且可更改的

---

## 收集分发

<img src="./img/para-relay_communication_1.svg" style="width: 1100px" />

Notes:

平行链 - 中继链通信的一个子集

---

#### 从收集者到中继节点和平行链节点

- 由收集者发送，收集者同时拥有 `CollatorService` 和 `ParachainConsensus`
- 发送到绑定的中继节点的 `CollationGeneration` 子系统，以便重新打包并转发给验证者
- 发送到平行链节点的导入队列

```rust[1|5]
let result_sender = self.service.announce_with_barrier(block_hash);

tracing::info!(target: LOG_TARGET, ?block_hash, "Produced proof-of-validity candidate.",);

Some(CollationResult { collation, result_sender: Some(result_sender) })
```

---

# Cumulus 收集如何实现平行链块验证

---

### 什么是运行时验证？

<pba-flex center>

- 中继链确保每个平行链块都遵循该平行链当前代码所定义的规则。

<!-- .element: class="fragment" data-fragment-index="1" -->

- 约束条件：中继链必须能够在不访问该平行链全部状态的情况下，对平行链块进行运行时验证

<!-- .element: class="fragment" data-fragment-index="2" -->

</pba-flex>

<div class="r-stack">
<img src="./img/runtime_validation_1.svg" style="width: 60%" />
<img src="./img/runtime_validation_2.svg" style="width: 60%" />
<!-- .element: class="fragment" data-fragment-index="1" -->
<img src="./img/runtime_validation_3.svg" style="width: 60%" />
<!-- .element: class="fragment" data-fragment-index="2" -->
</div>

<pba-flex center>

- 使这成为可能的构建模块，即 PVF 和 PoV，是在收集中传递的

<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-flex>

---

#### 平行链验证函数 - PVF

- 每个平行链的当前 STF 存储在中继链上，封装为 PVF

```rust [6]
/// 一个结构体，携带平行链验证函数的代码及其哈希值。
///
/// 应该易于克隆。
#[derive(Clone)]
pub struct Pvf {
    pub(crate) code: Arc<Vec<u8>>,
    pub(crate) code_hash: ValidationCodeHash,
}
```

<br />

- 平行链上发生的新状态转换必须根据 PVF 进行验证

Notes:

代码被哈希处理后存储在中继链的存储中。

---

#### 为什么是 PVF 而不是 STF？

<pba-cols>
<pba-col center>

<img src="./img/cumulus_sketch_4.svg" width = "100%"/>

</pba-col>
<pba-col center>

- PVF 不是简单地复制粘贴平行链运行时

<br />

- PVF 包含一个额外的函数，`validate_block`

<br />

**为什么？**

<!-- .element: class="fragment" data-fragment-index="1" -->

</pba-col>
</pba-cols>

Notes:

PVF 不仅包含运行时，还包含一个 `validate_block` 函数，该函数用于解释 PoV 中验证所需的所有额外信息。
这些额外信息对于每个平行链来说是唯一的，并且对中继链来说是不透明的。

---

#### 验证路径可视化
<div class="r-stack">
    <img src="./img/collation_path_1.svg" style="width: 70%" />
    <img src="./img/collation_path_2.svg" style="width: 70%" />
    <!-- .element: class="fragment" data-fragment-index="1" -->
</div>

Notes:
运行时验证过程的输入是有效性证明（PoV），在平行链验证函数（PVF）中调用的函数是`validate_block`，该函数会利用PoV来调用有效的运行时，然后创建一个表示状态转换的输出，这个输出被称为候选收据（CandidateReceipt）。

---

#### validate_block实际做什么？
<pba-flex center>
    - 平行链运行时期望与平行链客户端协同运行。
    - 但验证是在中继链节点中进行的。
    <!-- .element: class="fragment" data-fragment-index="1" -->
    - 我们需要实现平行链客户端向运行时暴露的API，即宿主函数。
    <!-- .element: class="fragment" data-fragment-index="2" -->
    - 宿主函数最重要的作用是允许运行时查询其状态，所以我们需要一个轻量级的平行链状态替代方案，足以支持单个区块的执行。
    <!-- .element: class="fragment" data-fragment-index="3" -->
    - `validate_block`会准备上述状态和宿主函数。
    <!-- .element: class="fragment" data-fragment-index="4" -->
</pba-flex>

---

#### 代码中的验证块
```rust [2|3-4|6|8-11]
// 非常简化的代码
fn validate_block(input: InputParams) -> Output {
    // 首先初始化状态
    let state = input.storage_proof.into_state().expect("存储证明无效");

    replace_host_functions();

    // 在该状态之上运行Substrate的`execute_block`
    with_state(state, || {
        execute_block(input.block).expect("区块无效")
    })

    // 创建结果输出
    create_output()
}
```
<br />
> 但是`storage_proof`从何而来呢？

Notes:
我们根据存储证明构建稀疏的内存数据库，然后确保存储根与`parent_head`中的存储根匹配。

---

##### 宿主函数替换可视化
<div class="r-stack">
    <img src="./img/replace_host_function_1.svg" style="width: 70%" />
    <!-- .element: class="fragment fade-out" data-fragment-index="1" -->
    <img src="./img/replace_host_function_2.svg" style="width: 70%" />
    <!-- .element: class="fragment" data-fragment-index="1" -->
</div>

---

### 再看收集
```rust[1|2-5|12-15|6-7|8-11]
pub struct Collation<BlockNumber = polkadot_primitives::BlockNumber> {
    /// 旨在由中继链自身解释的消息。
    pub upward_messages: UpwardMessages,
    /// 平行链发送的横向消息。
    pub horizontal_messages: HorizontalMessages,
    /// 新的验证代码。
    pub new_validation_code: Option<ValidationCode>,
    /// 执行产生的头部数据。
    pub head_data: HeadData,
    /// 用于验证平行链状态转换的证明。
    pub proof_of_validity: MaybeCompressedPoV,
    /// 从下行消息队列（DMQ）处理的消息数量。
    pub processed_downward_messages: u32,
    /// 标记，表示所有入站HRMP消息已处理到的区块编号。
    pub hrmp_watermark: BlockNumber,
}
```
Notes:
代码重点说明：
 - 候选承诺（CandidateCommitments）：向上传递的消息、已处理的下行消息、新代码（与验证输出进行对比）
 - head_data和PoV（验证输入）

---

### 有效性证明（见证数据）
 - 用于在验证单个区块时替代平行链的前状态。
 - 它允许重建稀疏的内存默克尔树。
    <!-- .element: class="fragment" data-fragment-index="1" -->
 - 然后可以将状态根与父区块头中的状态根进行比较。
    <!-- .element: class="fragment" data-fragment-index="2" -->

---

### 见证数据构建示例
<div class="r-stack">
    <img src="./img/pov_witness_data_1.svg" style="width: 70%" />
    <!-- .element: class="fragment fade-out" data-fragment-index="1" -->
    <img src="./img/pov_witness_data_2.svg" style="width: 70%" />
    <!-- .element: class="fragment" data-fragment-index="1" -->
</div>
<br />
 - 仅包含本区块中修改的数据以及默克尔树其余部分数据的哈希值。
    <!-- .element: class="fragment" data-fragment-index="2" -->
 - 这构成了收集中的大部分数据（最大5MiB）。
    <!-- .element: class="fragment" data-fragment-index="3" -->

Notes:
橙色：本区块中修改的数据值
绿色：PoV所需的兄弟节点哈希值
白色：由橙色和绿色节点构建的节点哈希值
红色：不需要的哈希值
蓝色：默克尔树的头部，即前一个区块头中的哈希值

---

#### 平行链块验证总结
```rust [2|3-4|6]
// 非常简化的代码
fn validate_block(input: InputParams) -> Output {
    // 首先初始化状态
    let state = input.storage_proof.into_state().expect("存储证明无效");

    replace_host_functions();

    // 在该状态之上运行`execute_block`
    with_state(state, || {
        execute_block(input.block).expect("区块无效")
    })

    // 创建结果输出
    create_output()
}
```
 - 现在我们知道**存储证明**从何而来了！
 - **into_state**构建了我们的存储默克尔树。
    <!-- .element: class="fragment" data-fragment-index="1" -->
 - 编写宿主函数以访问这个新存储。
    <!-- .element: class="fragment" data-fragment-index="2" -->

---

## Cumulus与平行链运行时升级
<pba-flex center>
    - 每个Substrate区块链都支持运行时升级。
    <!-- .element: class="fragment" data-fragment-index="0" -->
    ##### 问题
    <!-- .element: class="fragment" data-fragment-index="1" -->
    - 如果PVF编译时间过长会怎样？
        <!-- .element: class="fragment" data-fragment-index="1" -->
        - 批准环节出现“未出席”情况。
        - 在争议中，双方可能都无法达到超级多数。
        <!-- .element: class="fragment" data-fragment-index="1" -->
    > 更新平行链运行时并不像更新独立区块链运行时那么容易。
    <!-- .element: class="fragment" data-fragment-index="2" -->
</pba-flex>

---

### 解决方案
中继链需要有一个较为可靠的保证，即PVF能够在合理时间内完成编译。
<!-- .element: class="fragment" data-fragment-index="0" -->
<br />
 - 收集者执行运行时升级，但暂不应用。
 - 收集者在收集中将新的运行时代码发送给中继链。
 - 中继链执行**PVF预检查流程**。
 - 该流程结束后，第一个被包含的平行链块应用新的运行时。
    <!-- .element: class="fragment" data-fragment-index="1" -->
> Cumulus跟随中继链，等待继续进行的信号来应用运行时更改。
    <!-- .element: class="fragment" data-fragment-index="2" -->

Notes:
<https://github.com/paritytech/polkadot-sdk/blob/9aa7526/cumulus/docs/overview.md#runtime-upgrade>

---

##### PVF预检查流程
 - 中继链跟踪所有需要检查的新PVF。
 - 每个验证者检查PVF的编译是否有效且耗时不长，然后进行投票。
    <!-- .element: class="fragment" data-fragment-index="1" -->
    - 二元投票：接受或拒绝。
    <!-- .element: class="fragment" data-fragment-index="1" -->
 - 超级多数决定投票结果。
    <!-- .element: class="fragment" data-fragment-index="2" -->
 - 新PVF的状态在中继链上更新。
    <!-- .element: class="fragment" data-fragment-index="3" -->

Notes:
参考：<https://paritytech.github.io/polkadot/book/pvf-prechecking.html>

---

## 参考文献
1. 🦸 [Gabriele Miotti](https://github.com/gabriele-0201)，他为整理这些幻灯片提供了巨大帮助
1. <https://github.com/paritytech/polkadot-sdk/blob/9aa7526/cumulus/docs/overview.md>

---

<!-- .slide: data-background-color="#4A2439" -->
# 问题