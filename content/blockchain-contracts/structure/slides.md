---
title: Blockchain Structure
description: The Blockchain data structure including hash-linking, forks, header vs body, and extrinsics.
duration: 30 min
---

# 区块链结构

<img style="width: 1000px" src="./img/opaque-blockchain.svg" />

---

## 共享故事

区块链通过加密方式保证事件历史未被篡改。
这使得相关各方能够拥有一个**共享的历史记录**。

Notes:

并且他们可以通过比较链的末端，在 O(1) 时间复杂度内知道他们的历史记录是否相同。

---

## 哈希链表

<img style="width: 1000px" src="./img/hash-linked-1.svg" />

Notes:

这是一个简化的区块链。
每个区块都有一个指向前一个区块的指针以及一个负载。

---v

## 哈希链表

<img style="width: 1000px" src="./img/hash-linked-2.svg" />

Notes:

这个指针是前一个区块的加密哈希值。
这确保了整个链的历史数据的完整性。
这是区块链可能采取的最简单形式，实际上它使我们能够就共享历史达成共识。

---v

## 哈希链表

<img style="width: 1000px" src="./img/hash-linked-3.svg" />

Notes:

这确保了整个链的历史数据的完整性。
这是区块链可能采取的最简单形式，实际上它使我们能够就共享历史达成共识。

---v

### 创世区块

<img style="width: 1000px" src="./img/hash-linked-genesis.svg" />

Notes:

链中的第一个区块通常被称为“创世区块”，这个名字来源于犹太教 - 基督教神话中的第一本书 - 我们共享故事的开端。
父哈希值被选择为某个特定的值。
通常我们使用全零哈希值，不过任何固定且被广泛认可的值也可以。

---

## 状态机（再次提及）

状态机定义：

<pba-flex center>

- 有效状态的集合
- 状态之间转换的规则

</pba-flex>

<img style="width: 900px;" src="./img/state-machine-general.svg" />

---v

### 区块链与状态机的结合

<img style="width: 1000px" src="./img/blockchain-meet-state-machine.svg" />

Notes:

将区块链与状态机结合的最简单方法是使区块链的负载成为状态机的转换。
通过这样做，我们可以以加密保证的方式有效地跟踪状态机的历史。

---v

### 状态存储在哪里？

在其他地方！

<img style="width: 1000px" src="./img/blockchain-with-state-outside.svg" />

Notes:

每个区块都有一个与之关联的状态。
但通常状态不会存储在区块中。
这个状态信息是冗余的，因为它总是可以通过重新执行转换历史来获得。
将状态存储在区块中是可能的，但这种冗余是不可取的。
对于任何想要存储链历史的人来说，这会浪费磁盘空间。
目前，任何稍微流行的区块链都不会将状态存储在区块中。
如果你**想要**存储状态，你可以这样做。
执行此操作的软件称为归档节点或索引器。
但它与区块是分开存储的。
...暂停...
再重复一次，以确保你理解：状态不在区块中。

---v

### 状态根

状态的加密锚点

<img style="width: 1000px" src="./img/blockchain-with-state-roots.svg" />

Notes:

一些数据冗余可能有助于避免数据损坏等问题。
一个区块通常包含状态的加密指纹。
这被称为状态根。
你可以把它看作是状态的哈希值。
在实践中，状态通常被构建成类似默克尔树的结构，并且树的根被包含在内。
并非所有的区块链都这样做。
值得注意的是，比特币就不这样做。
但大多数区块链会这样做。
在接下来的两个模块中，我们将详细介绍 Substrate 中这个状态根是如何计算的，但现在我们只将状态根视为某种加密指纹。

---

## 分叉

<img style="width: 1000px" src="./img/forks.svg" />

一个状态机可以有不同的可能历史。
这些被称为分叉。

Notes:

你可以把它们想象成不同的现实。
我们需要决定众多可能的分叉中哪一个最终是“真实”的。
这是共识的核心工作，我们将在本模块的接下来两节课中讨论它。

---v

## 无效转换

<img style="width: 1000px" src="./img/forks-some-invalid.svg" />

Notes:

在我们进入硬核共识之前，我们可以根据状态机本身排除**一些**可能性。

---

## 现实的区块链结构

<img width="600px" src="./img/header-body.svg" />

- 头部：关于这个区块的最小重要信息的摘要
- 主体：一批状态转换的列表

Notes:

头部包含的信息最少。
在某些方面，它就像元数据。
主体包含真正的“负载”。
它几乎总是一批状态转换。
主体中包含的内容有许多别名：

- 转换
- 交易
- 外部交易

---v

## Substrate 中的区块

```rust
/// Substrate 区块的抽象。
pub struct Block<Header, Extrinsic: MaybeSerialize> {
	/// 区块头部。
	pub header: Header,
	/// 附带的外部交易。
	pub extrinsics: Vec<Extrinsic>,
}
```

Notes:

这个例子来自 Substrate，因此它力求成为一种通用且灵活的格式，我们将在下一个模块中更深入地介绍 Substrate。
这几乎代表了所有现实世界中的区块链。

---

## 头部

确切内容因区块链而异。
总是包含父哈希值。
头部是**实际的**哈希链表，而不是整个区块。

Notes:

父哈希值将区块链接在一起（加密链表）。
其他信息对于其他基础设施和应用程序很有用（稍后会详细介绍）。

---v

## 头部示例

<pba-cols>
<pba-col>

<pba-flex center>

**比特币**

</pba-flex>

- 版本
- 前一个哈希值
- 交易默克尔根
- 时间
- N_Bits
- 随机数

</pba-col>
<pba-col>

<pba-flex center>

**以太坊**

</pba-flex>

- 时间
- 区块编号
- 基础费用
- 难度
- 混合哈希值
- 父哈希值
- 状态根
- 随机数

</pba-col>
</pba-cols>

---v

## Substrate 头部

- 父哈希值
- 编号
- 状态根
- 外部交易根
- 共识摘要

Notes:

外部交易根是指向区块主体的加密链接。
它与状态根非常相似。
共识摘要是共识算法确定区块有效性所必需的信息。
它会随着所使用的共识算法而有很大差异，我们将在接下来的两节课中讨论它。

---v

## Substrate 头部（完整视图）

<img style="width: 1000px" src="./img/headers-link-state-body.svg" />

---

## 外部交易

来自外部世界的数据包，附带**零个**或多个签名。

- 对状态转换函数（STF）的函数调用
- 一些函数需要签名（例如，转移一些代币）
- 其他函数不需要签名，但通常有一些验证手段

---

## 有向无环图（DAG）

**Directed Acyclic Graphs**

<img style="margin-left: 215px;" src="./img/forks.svg" />

Notes:

在数学中，有一个有向无环图的概念。
定义图，然后是有向的，然后是无环的。
区块链是有向无环图的例子。
实际上，区块链是一种特殊的有向无环图，称为树。
有时你会听到我提到“区块树”，它实际上指的是链的所有历史。

但有向无环图不仅仅是树。
考虑一下，如果有人创建了一个这样的区块。

点击

---v

## 有向无环图（DAG）

**Directed Acyclic Graphs**

<img style="width: 1000px" src="./img/dag.svg" />

Notes:

如果一个区块可以有多个父区块会怎么样？
这可能允许并行处理并提高吞吐量！
但这也会带来问题。
如果两个父历史记录中存在冲突的交易怎么办？
你甚至如何知道是否存在冲突的历史记录？

---

<!-- FIXME TODO

这可能是一个很好的地方来分割课程。
在此之前的部分是关于数据结构的。
在此之后的部分是关于跟踪此数据结构的点对点网络。
-->

## 区块链 💒 点对点网络

<img style="width: 900px;" src="./img/blockchain_p2p.svg" />

Notes:

希望这个图的某些部分看起来很熟悉。
你在这里看到了什么？

- 多样化的服务器。
- 在一个点对点网络中。
- 每个服务器都有自己对区块链的视图。

---v

## 节点

参与区块链网络的软件代理。<br />
可能执行以下工作：

<pba-cols>
<pba-col>
<pba-flex center>

- 传播区块
- 执行和验证区块
- 存储区块
- 存储状态
- 传播交易

</pba-flex>
</pba-col>
<pba-col>
<pba-flex center>

- 维护交易池
- 生成区块
- 存储区块头部
- 响应用户的数据请求（RPC）

</pba-flex>
</pba-col>
</pba-cols>
Notes:

许多节点只执行这些任务的一个子集。

---v

## 节点类型

<pba-flex center>

- 完整节点
- 轻节点（又名轻客户端）
- 生成节点
- 归档节点
- RPC 节点

</pba-flex>

---

## 区块空间

由去中心化的区块链网络创建并经常出售的一种资源。

<img style="width: 700px;" src="./img/Web2Web3Stacks.png" />

#### 了解更多：

- 文章：<https://a16zcrypto.com/posts/article/blockspace-explained/>
- 文章：<https://www.rob.tech/polkadot-blockspace-over-blockchains/>
- 播客：<https://www.youtube.com/watch?t=5330&v=jezH_7qEk50>

Notes:

区块链网络是中心化服务器的替代品。
它向应用程序部署者出售一种产品。
状态机是应用层，而区块链是服务器的替代品。
就像应用程序向数据中心支付服务器资源（如 CPU 时间、磁盘空间、带宽等）一样。
应用程序（可能通过其开发者或用户）为获得其历史记录被证明以及其状态被一个无需信任且不可阻挡的共识层跟踪的特权而付费。

---v

## 交易池

- 包含尚未包含在区块中的交易。
- 不断对交易进行优先级排序和重新排序。
- 作为区块空间市场运行。

Notes:

有时也被称为内存池（感谢比特币 🙄）
生成节点确定即将到来的交易的顺序。
在某种意义上，它们可以看到未来。

预示着分叉，即参与者对规则存在分歧
历史：DAO 分叉、BCH 分叉
预示着共识：区块有效的任意额外约束

---

# 让我们开始构建吧

```
