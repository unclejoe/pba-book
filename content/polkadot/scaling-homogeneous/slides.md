---
title: "Blockchain Scaling 1: Monolithic and Homogeneous"
duration: 45 mins
---

# 区块链扩容

---

## 单体式与同构式

---

## 课程形式

- 两种方法：
  - 从历史角度看，我们从单体式、同构式的协议转向模块化、异构式的协议。
  - 从结构上看，我们可以从安全假设和设计权衡方面进行比较。
- 前半部分涵盖理论、同构分片和共享安全。
  - 后半部分是汇总（rollups）及其他内容。

---

## 我们所说的扩容是什么意思？

<img rounded style="width: 700px" src="./img/VisaScale.png" />

- 提高吞吐量：通过状态转换执行的数据。
- 每秒交易量（TPS）：
  - 被广泛提及。
  - 经常被操纵：
    - 单独签名的用户交易（无固有交易）。
    - 峰值负载与持续负载。
    - 波卡（Polkadot）中使用的标准每秒交易量（sTPS，无数据库缓存）。
  - 目前不是吞吐量需求的主要驱动因素（去中心化金融（DeFi）+ 非同质化代币（NFT）发行）。

---

## 横向扩容与纵向扩容

<img rounded style="width: 800px" src="./img/horizontal-vs-vertical-scaling-diagram.png" />

- 纵向扩容：为每台机器添加更多资源。
- 横向扩容：添加更多机器。

---

## 可扩展性三难困境

<img style="width: 400px" rounded src="./img/trilemma.svg" />

- 为什么我们关心区块链的横向扩容？
  - 降低进入门槛 -> 更多的去中心化。

---

## 纵向扩容方法

- 自适应响应（HotStuff）。
- 内存池优化：
  - 流水线：在之前的区块被包含之前构建未来的区块。
  - 基于有向无环图（DAG，Narwhal）。
  - 避免最大可提取价值（MEV，对于波卡：Sassafras）。
- 并行执行：
  - 未花费交易输出（UTXOs）。
  - Move 语言：带有线性类型的软件事务内存（STM）。
  - 对于波卡：弹性扩容。

Notes:

- <https://dahliamalkhi.files.wordpress.com/2018/03/hot-stuff-arxiv2018.pdf>
- <https://arxiv.org/pdf/2305.13556.pdf>
- <https://arxiv.org/pdf/2105.11827.pdf>
- <https://eprint.iacr.org/2023/031.pdf>
- <https://move-book.com/>

---

## 再质押

<img rounded style="width: 700px" src="./img/Eigenlayer.webp" />

- 现有的验证者集合（Cosmos Hub、带有 Eigenlayer 的以太坊）可以选择使用相同的抵押来验证其他协议。
- 资本扩容，而非吞吐量扩容。
- 所有验证者必须验证所有协议，以实现相同的安全性。

---

## 再质押

- 两个支持的论点（与波卡相同）：
  - 共享经济安全，抵御市场购买代币攻击权益证明（PoS）。
  - 降低验证者的资本成本，同时增加收入来源 -> 对于客户端协议来说，安全性成本更低。
- 应用链论点：灵活的区块空间相对于通用智能合约平台具有优势（包括在吞吐量方面）。

---

## 分片

---

## 分片

<img rounded style="width: 700px" src="./img/committees.png" />

- 来自传统数据库的术语。
- 定义：分布在机器子集（委员会）上。
- 执行分片与数据分片。

Notes:

- <https://vitalik.ca/general/2021/04/07/sharding.html>

---

## 问题领域：拜占庭容错阈值

- 通常不能假设故障节点数 f 在委员会内成立。
  - 除非它们具有统计代表性。
  - 或者我们依赖于 n 选 1 的假设。

---

## 问题领域：自适应破坏

- 破坏（拒绝服务攻击（DOS）、贿赂等）小委员会比破坏整个验证者集合更容易。
- 必须通过强链上随机性（例如，可验证随机函数（VRFs）而非工作量证明（PoW）哈希）进行排序。
- 必须频繁轮换。
- 较弱的假设：自适应破坏不是即时的。

---

## 问题领域：跨分片消息传递

- 消息队列不平衡（异构分片与同构分片不同）。
- 当分片回滚时会产生依赖关系 -> 当最终性绑定在一起且速度快时更容易处理。
- 无向图方法（Casper/Chainweb）：
  - 只允许相邻分片之间进行消息传递。
  - 相邻分片一起验证。

---

## 解决方案：n 选 1 假设

- 波卡（积极模式）。
- 乐观汇总（懒惰模式）。
- Nightshade（Near）：
  - 乐观同构分片。
  - 基于波卡的可用性协议。

Notes:

- <https://near.org/papers/nightshade>

---

## 解决方案：

## 具有统计代表性的委员会

<img rounded style="width: 700px" src="./img/Omniledger.png" />

- 具有统计代表性的委员会（Omniledger、波卡带有多个中继链）。
- 非常大的验证者集合（数千个）。
- 大型（数百个）具有统计代表性的委员会。
- 委员会不是每个区块都轮换（较弱的自适应破坏假设）。
- 验证者集合中的 4f 信任假设 -> 委员会中的 2f + 1 信任假设。
- 单独的“信标”链用于抵御女巫攻击。

Notes:

- <https://eprint.iacr.org/2017/406.pdf>

---

## 解决方案：有效性证明（零知识汇总，zk-rollups）

- 执行的加密证明。
- 证明时间和验证时间的不对称性：
  - 证明过程缓慢。
  - 验证过程快速且时间恒定。
  - 证明简洁，可以上链。
  - 通常是零知识证明，但并非必要。

---

## 状态通道、Plasma 及其他

---

## 状态通道

<img rounded style="width: 700px" src="./img/Lightning-layers.png" />

- 部分状态被锁定，在封闭的参与者集合之间进行链下更新，然后解锁并提交到链上。
- 支付通道，例如闪电网络，是一种特殊情况。
- 共享参与者的通道之间的组合。
- 可以是特定应用的或通用的（例如反事实）。

Notes:

- <https://vitalik.ca/general/2021/01/05/rollup.html>
- <https://www.jeffcoleman.ca/state-channels/>
- <https://lightning.network/lightning-network-paper.pdf>

---

## 状态通道

- 更强的活性假设：
  - 链将接受陈旧的状态转换为最终状态。
  - 必须有人定期在线以提交后续状态转换。
  - 这可以外包给瞭望塔网络。
  - 通常在通道关闭后有挑战期。

---

## 状态通道

- 不能用于所有类型的操作：
  - 向通道外的新参与者发送资金。
  - 无所有者的状态转换（例如去中心化交易所（DEX）操作）。
  - 基于账户的系统。

---

## Plasma

<img rounded style="width: 700px" src="./img/Plasma.jpg" />

<div style="font-size: 0.82em;">

- “以太币 + 闪电网络”。
- 类似于状态通道，但哈希值会定期发布到第一层（L1）的检查点。
- “Map-reduce” 区块链，在基于工作量证明（PoW）的以太坊之上的权益证明（PoS）。
- 缺点：
  - 状态转换仍然需要“所有者”。
  - 对于基于账户的系统仍然不理想。
  - 在数据不可用的情况下存在大规模退出问题。

</div>

Notes:

- <https://plasma.io/plasma-deprecated.pdf>

---

## Plasma 的变体

<img rounded style="width: 700px" src="./img/plasma_world_map.png" />

- Plasma 最小可行产品（MVP）：基于未花费交易输出（UTXO）。
- Plasma Cash：基于非同质化代币（NFT） -> 仅证明自己代币的所有权。
- Polygon：Plasma 和权益证明（PoS）桥。

Notes:

- <https://ethresear.ch/t/plasma-world-map-the-hitchhiker-s-guide-to-the-plasma/4333>
- <https://ethresear.ch/t/plasma-cash-plasma-with-much-less-per-user-data-checking/1298>
- <https://ethresear.ch/t/minimal-viable-plasma/426>

---

## Plasma 的兴衰

<pba-cols>
<pba-col>

<img rounded style="width: 500px" src="./img/L2Evolution.png" />

</pba-col>
<pba-col>

- 2017 - 2019 年：从 Plasma 论文到汇总的出现：
  - 零知识汇总（zk-rollups）。
  - 合并共识。
  - 通用欺诈证明。
- Plasma Group 演变为 Optimism。

</pba-col>
</pba-cols>

Notes:

- <https://medium.com/dragonfly-research/the-life-and-death-of-plasma-b72c6a59c5ad>
- <https://ethresear.ch/t/minimal-viable-merged-consensus/5617>
