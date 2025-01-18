---
title: Substrate's Transaction Pool and its Runtime API
duration: 30 minutes
---

# Substrate的交易池

---

## 交易池

<pba-cols>
<pba-col>

<img style="width: 500px;" src="./img/BlockspaceBooth.png" />

</pba-col>

<pba-col>
<img style="width: 500px; margin-left: -100px; margin-top: 250px;" src="./img/short-line.png" /> <!-- .element: class="fragment" -->
</pba-col>

<pba-col>
<img style="width: 700px; margin-left: -100px; margin-top: 100px;" src="./img/long-line.png" /> <!-- .element: class="fragment" -->
</pba-col>

</pba-cols>

Notes:

区块链产生区块空间，用户购买这些区块空间。
他们为什么要购买呢？
这样他们就可以为共享的故事做出贡献。
这样他们就可以与共享的状态机进行交互。
你可以想象这些用户手持交易在排队，等待有机会将他们的交易放入链的区块空间中。
有时对区块空间的需求较低，队列很短。
在这种情况下，每次创建新块时，队列都会被完全清空。
其他时候，队列会变长并出现积压。
然后，当一个块到来时，只有队列的一部分会被清空。

这个简单的模型为交易池的工作原理提供了一些直观的理解，但它有点简化了。

首先，它实际上是一个优先队列。
你可以通过向块生产者提供贿赂来插队。

其次，更准确的说法是交易本身在排队，而不是发送这些交易的用户。

让我们仔细看看。

---v

### 交易的路径

<img style="width: 900px;" src="./img/blockchain_p2p.svg" />

Notes:

之前，在区块链模块中，我们看到了这个图。
它指出每个节点都有自己对区块链的视图。
现在我将向你展示另一个细节层面，即每个节点也有自己的交易池。
点击

---v

### 交易的路径

<img style="width: 900px;" src="./img/blockchain_p2p_with_pool.svg" />

Notes:

从签署交易的用户到最终确定的块，交易可以有许多路径。
我们来讨论一些路径。
直接到用户的创建节点并进入链是最简单的。
也可以传播给其他创建者。
甚至可以进入一个块，被孤立出来，回到交易池，然后进入一个新的块。

---v

### 池验证

- 检查签名
- 检查发送者是否能支付费用
- 确保状态已准备好进行应用

Notes:

当一个节点收到一个交易时，它会进行一些轻量级的预验证，有时也称为池验证。
这个检查确定交易是（现在有效、在某些潜在的未来有效、无效）。
如果交易在池中停留了很长时间，会进行定期的重新验证。

---v

### 池优先级

- 优先队列
- 按以下因素排序：
  - 费用
  - 贿赂
  - 每区块空间的费用
- 所有这些都是链下操作

Notes:

Substrate交易池还有一些其他功能，我们很快将详细介绍。

---

## 交易池运行时API

```rust
pub trait TaggedTransactionQueue<Block: BlockT>: Core<Block> {
    fn validate_transaction(
        &self,
        __runtime_api_at_param__: <Block as BlockT>::Hash,
        source: TransactionSource,
        tx: <Block as BlockT>::Extrinsic,
    ) -> Result<TransactionValidity, ApiError> { ... }
}
```

[`TaggedTransactionQueue` Rust文档](https://paritytech.github.io/substrate/master/sp_transaction_pool/runtime_api/trait.TaggedTransactionQueue.html)

引入于 [paritytech/substrate#728](https://github.com/paritytech/substrate/issues/728)

Notes:

这是另一个运行时API，类似于用于创建和导入块的块构建器和核心API。
像大多数其他API一样，它要求也实现核心API。

这个API略有不同，因为它实际上是从链下调用的，并且不是你的状态转换函数（STF）的一部分。
所以我们来稍微讨论一下这个问题。

---v

### 运行时与状态转换函数

<img style="width: 1100px;" src="./img/peter-parker-glasses-off.png" />

Notes:

通常说运行时基本上就是你的状态转换函数。
这是一个很好的一阶近似。
几乎是正确的。

---v

### 运行时与状态转换函数

<img style="width: 1100px;" src="./img/peter-parker-glasses-on.png" />

Notes:

但正如我们在这里看到的，当我们戴上眼镜时，实际上只有一部分API是状态转换函数的一部分。

---v

## 为什么池逻辑在运行时中？

- 交易类型是不透明的
- 运行时逻辑是不透明的
- 你必须理解交易才能对其进行优先级排序

Notes:

那么如果这不是状态转换函数的一部分，为什么它会在运行时中呢？
这个逻辑与运行时应用逻辑紧密相关。
这些类型在运行时之外是不透明的。
所以这个逻辑必须放在运行时中。

但如果它不在链上，单个验证者可以定制它吗？
简而言之，是的。
有一个机制可以实现这一点。
我们不会深入探讨这个机制，但验证者可以指定使用替代的Wasm块而不是官方的块。

---

## API的任务

- 进行快速预检查
- 给交易赋予优先级
- 确定交易是现在准备好还是可能在未来准备好
- 确定交易之间的依赖关系图

Notes:

我们之前讨论了一般交易池的任务。
特别是预检查和优先级。
这里是Substrate的TaggedTransactionPool执行的更具体的任务列表。

后两点是新增加的，它们是“标签”的职责，带标签的交易队列就是以“标签”命名的。

所有这些结果都通过共享类型`ValidTransaction`或`InvalidTransaction`返回给客户端。

---v

### `ValidTransaction`

```rust
pub struct ValidTransaction {
    pub priority: TransactionPriority,
    pub requires: Vec<TransactionTag>,
    pub provides: Vec<TransactionTag>,
    pub longevity: TransactionLongevity,
    pub propagate: bool,
}
```

[`ValidTransaction` Rust文档](https://paritytech.github.io/substrate/master/sp_runtime/transaction_validity/struct.ValidTransaction.html)

Notes:

我们通过返回这个有效的交易结构体来表明交易通过了所有预检查。
如果它甚至无效，我们将返回一个不同的`InvalidTransaction`结构体。
你昨天已经学习了如何浏览Rust文档来找到关于那个结构体的文档。

我们已经讨论过优先级。
值得注意的是，优先级的概念对客户端是有意不透明的。
运行时可以根据自己的判断来分配这个值。

`provides`和`requires`共同构成了交易之间的依赖关系图。
`requires`是当前未满足的依赖交易的列表。
这个交易将在这些依赖被满足的未来时刻准备好，所以它会被保留在池中。

一个简单直观的例子是支付。
假设爱丽丝在交易1中向鲍勃支付一些代币。
然后鲍勃在交易2中把这些相同的代币支付给查理。
交易2只有在交易1被应用后才有效。
这是一个依赖关系。

`longevity`是一个我不太熟悉的字段。
它表示交易在被丢弃或重新验证之前应该在池中停留多长时间。

<!-- FIXME TODO 单位是什么？如何设置它？ -->

最后是交易是否应该被传播。
这通常是`true`。
只有在特殊的边缘情况下才会是`false`。

---v

### 示例1：未花费交易输出（UTXO）系统

<img src="./img/utxo.svg" />

Notes:

按隐含小费（输入和输出的差额）进行优先级排序
需要所有缺失的输入交易
提供此输入

---v

### 示例2：带随机数的账户系统

<img src="./img/accounts.svg" />

Notes:

按明确小费进行优先级排序
需要此账户的所有先前随机数
提供此账户的此随机数

这展示了账户系统的一个最大缺点。
交易无法确定性地指定它们操作的初始状态。
同一账户的交易之间只有一个固有的顺序。

---v

## 始终在链上重新检查

<img style="width: 900px;" src="./img/blockchain_p2p_with_pool.svg" />

Notes:

这些新的池信息都不会改变你上周学到的基础知识。
你必须在链上完整地执行状态转换。

大多数时候你不是块的创建者。
当你从另一个节点导入一个块时，你不能信任他们已经正确地进行了预检查。
```
