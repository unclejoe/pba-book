---
title: "Blockspace: The Product of Polkadot"
description: Deep Dive into Coretime and Blockspace Allocation
duration: 30-40 minutes
---

## Blockspace

在本节课中，我们将涵盖以下内容：

<pba-flex center>

1. Blockspace作为一个概念及其历史解读
2. Blockspace作为区块链的产物
3. 高效分配Blockspace的重要性
4. Polkadot内Blockspace分配机制的设计空间

</pba-flex>

Notes:

深度探讨文章见 <https://www.rob.tech/polkadot-blockspace-over-blockchains/>

---

## Blockspace: 定义

> Blockspace是区块链完成并提交操作的能力。

---

## 测量Blockspace

Blockspace可以通过以下几种不同方式进行测量：

<pba-flex center>

1. 大小：交易使用的字节数（例如比特币）
1. 计算量：交易使用的Gas（例如以太坊）
1. 数据：验证交易所需的数据大小（Polkadot中的PoV大小）
1. 或者以上几种方式的组合

</pba-flex>

---

## Blockspace市场

区块链中的费用市场是Blockspace市场的例子。

区块链按需出售Blockspace，用户支付费用以使用Blockspace。

---

## Blockspace市场

平行链插槽拍卖是Blockspace市场的另一个例子。

与按需出售Blockspace不同，Blockspace是通过拍卖机制提前批量出售的。

---

## Blockspace: 供需关系

<img rounded width="900px" src="./img/blockspace-supply-demand.svg" />

---

## 以太坊中的Blockspace: GasToken

GasToken (https://GasToken.io) 是以太坊上早期的Blockspace期货市场。

以太坊在智能合约执行时，为清除存储槽提供Gas退款。

通过在Gas价格低廉时“预购”存储，并在Gas价格昂贵时获得退款，用户可以在以太坊中进行Blockspace套利！

---

## 评估Blockspace

Blockspace的三个属性：

<pba-flex center>

1. 质量：Blockspace的安全性如何？最终性的经济保障是什么？
1. 可用性：市场上有多少Blockspace可用？
1. 灵活性：Blockspace可以用于多少种应用？

</pba-flex>

---

## Polkadot的Blockspace: 质量

Polkadot的执行分片保证了Polkadot生成的所有Blockspace都是高度安全的，在33% BFT假设下具有最终性的经济保障。

---

## Polkadot的Blockspace: 可用性

通过分片，Polkadot有能力产生大量的Blockspace。这是从另一个角度来看待区块链的扩展性问题：创造更多的Blockspace。

---

## Polkadot的Blockspace: 灵活性

由于关键的设计选择，Polkadot以高度灵活的格式提供Blockspace：

<pba-flex center>

1. WebAssembly：这种图灵完备的语言允许进行各种计算。
1. PoV Blobs：对存储格式或访问模式没有特定要求。
1. Head-Data blobs：平行链可以使用它们喜欢的任何头部格式，严格来说，甚至不一定是区块链。

</pba-flex>

---

## Substrate: Blockspace转换器

由于Polkadot提供了高度灵活的Blockspace，它可以被转化为各种不同的、更专业的Blockspace产品。

<img rounded width="900px" src="./img/blockspace-transformation.svg" />

---

## 区块链应用开发原则

<pba-flex center>

1. 获取通用的Blockspace（从Polkadot，直接从验证者处获取）
1. 为特定的用例或需求专门化Blockspace
1. 下游需求驱动上游需求。

</pba-flex>

Notes:

我所说的（3）是指对（2）的需求量应该决定应用程序进行（1）的程度。

---

## 问题: 幽灵链

链产生大部分为空的块是很常见的。

这是一个问题：链购买的Blockspace超过了它们的需求！

它们在向验证者支付费用却没有获得有价值的服务，这将导致它们的代币贬值。

---

## 解决方案: 按需获取Blockspace

区块链需要每隔X秒或X分钟产生一个块。

区块链应该只在有充分理由时才产生块。

目前没有这样做的主要原因是缺乏合适的基础工具。

---

## Polkadot的架构: 执行核心

<img rounded width="900px" src="./img/execution-cores-with-blockchains.svg" />

Notes:

打个比方，核心就像CPU核心。代码和数据由“操作系统”调度到这些核心上，然后执行。

---

## Polkadot的架构: 执行核心

<img rounded width="900px" src="./img/execution-cores.svg" />

---

## 长期 vs. 按需

按需获取类似于云计算中的“现货”实例，而插槽拍卖类似于“预留”实例。

如果总体需求较高，现货实例可能会更昂贵，但有助于缓解负载。

---

## Coretime

在Polkadot中，我们用Coretime来衡量应用程序可以使用的Blockspace量。

就像CPU和操作系统一样，有一个调度器将许多不同的进程多路复用到执行核心上。

Coretime可以通过一级或二级市场获得。

## 弹性扩展（计划中的升级）

如果平行链一次不仅可以获取一个执行核心，而是可以获取多个，会怎么样？

那么在需求较高的时期，平行链就能够进行弹性扩展。

Notes:

这种扩展之所以能够实现，是因为在Polkadot中，平行链块的状态转换是完全封装的，块X + 1的验证可以与块X的验证并行进行。

然而，平行链块仍然必须由收集者按顺序生成，因此这只能用于扩展到收集者生成块的最大吞吐量。

---

## 报销层: 现状

<img rounded width="900px" src="./img/reimbursement-status-quo.svg" />

---

## 报销层: 泛化

<img rounded width="1000px" src="./img/reimbursement-layer.svg" />

Notes:

用户可以是收集者本身，或者只是满足市场需求的人（Blockspace套利！）

---

## 报销层: 用例

<pba-flex center>

1. 在链外支付收集者费用
1. 用稳定币或其他代币支付收集者费用
1. 无代币平行链
1. 平行链启动平台（例如，从其他系统的“积分”中支付）
1. 通用收集者池（即插即用，无需运行特定于平行链的节点）

</pba-flex>

---

## Blockspace和互操作性

在可互操作的区块链应用中，应用的好坏取决于它所依赖的最薄弱的链。

由于毒性风险，重要的是不要将高质量的Blockspace与低质量的Blockspace混合使用。

---

## 临时链

区块链有必要永远运行吗？

为什么不创建只运行有限时间的区块链，例如运行某个特定的协议或计算，然后结束呢？

---

## Coretime期货市场

有了合适的核心级原语，就有可能转让未来Coretime的权益。

二级市场可能会出现，也许会使用NFT，在平行链本身进行，以促进未来Coretime的市场。

这将通过套利为Coretime创造一个高效的市场和价格发现机制。

---

## 未来Coretime分配机制

如果Coretime是Polkadot的“产品”，那么分配机制就是“包装”。

RFC - 1提出了批量Coretime的出售、续订、拆分、转售和转让机制。

---

## Blockspace: 结论

<pba-flex center>

1. Blockspace是区块链资源的概念提炼
1. Blockspace为区块链的调度和生命周期提供了新的视角
1. Polkadot使用Coretime来衡量Blockspace的分配
1. 随着Web3系统扩展到为80亿人服务，Blockspace的高效分配将至关重要。
1. Polkadot的架构是以Blockspace为中心，而不是以区块链为中心，为开发者提供了许多使用其产品的选择。

</pba-flex>

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题