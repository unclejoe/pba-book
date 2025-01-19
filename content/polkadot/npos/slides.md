---
title: Nominated Proof of Stake
description: An introduction to Nominated Proof of Stake in Polkadot
duration: 1 hour
---

# 提名权益证明

## _ Polkadot 网络中的_

---

## 为什么要采用权益证明呢？

我们为什么要使用权益证明（PoS）呢？

<div>

- 代币被锁定 + 可能会被削减。
- 经济安全 💸🤑。

</div>

<!-- .element: class="fragment" -->

- 其他所有的（如最终确定性、平行链等）都是建立在这个经济安全的基础层之上的。

<!-- .element: class="fragment" -->

---v

### 为什么要采用权益证明呢？

- 要记住， Polkadot 网络本质上是一种验证者集合即服务。
- 安全的区块空间， Polkadot 网络的主要产品，是由这些验证者提供的。

<img style="width: 1000px" src="./img/dev-6-x-npos-vsas.svg" />

---

## 什么是NPoS：假设

假设：

- **验证者**：那些打算创建区块的人。即 _验证者候选人_。

<!-- .element: class="fragment" -->

- **提名者/委托者**：那些打算支持想要成为验证者的人。

<!-- .element: class="fragment" -->

- 验证和提名的意愿可能会发生变化，因此我们需要进行**定期选举**，以始终选出**活跃/获胜的验证者/委托者**，并让他们能够被削减（惩罚）。

<!-- .element: class="fragment" -->

- 每个选举周期被称为一个**_纪元_**，例如在 Polkadot 网络中为24小时。

<!-- .element: class="fragment" -->

---

### 什么是NPoS：重新设计

---v

**单人权益证明（Solo-POS）**

<img style="width: 1000px" src="./img/dev-6-x-npos-0.svg" />

---v

### 什么是NPoS：重新设计

- 想要成为权威节点的人，也就是验证者，拿出自己的权益。没有其他参与方式。选出权益最高的验证者。

- 有什么问题呢？

Notes:

我们能够获得的权益数量较少，对于那些不想运行硬件设备的人来说，无法参与。

---v

**单一委托权益证明（Single-Delegation-POS）**

<img style="width: 1000px" src="./img/dev-6-x-npos-1.svg" />

---v

### 什么是NPoS：重新设计

- 任何人都可以将自己的权益分散到任何给定的验证者那里。根据总权益选出排名靠前的验证者。
- 投票者被称为 **委托者**。

- 有什么问题呢？

Notes:

- 这种方式更好一些，但资金可能会被委托给未获胜的验证者，从而被浪费。
- 换句话说，没有激励措施去委托给那些未获胜的验证者。

---v

**多重委托权益证明（Multi-Delegation-POS）**

<img style="width: 1000px" src="./img/dev-6-x-npos-2.svg" />

---v

### 什么是NPoS：重新设计

你的权益被平均分成 $\frac{1}{N}$（或任意比例）分配给 $N$ 个验证者。

有什么问题呢？

Notes:

和之前的问题一样。

---v

**提名权益证明**

<img style="width: 1000px" src="./img/dev-6-x-npos-3.svg" />

---v

**提名权益证明**

<img style="width: 1000px" src="./img/dev-6-x-npos-4.svg" />

---v

### 什么是NPoS：重新设计

- 你可以提名最多 `N` 个候选人，一个 _算法_（可以在链上或链下计算）会决定 **获胜者** 以及 **如何在他们之间分配权益**。
- 投票者被称为 **提名者**。

---v

### 什么是NPoS：重新设计

- ✅ 作为一名提名者，你可以自由地表达你支持未获胜者的意愿。一旦有足够多的人表达了同样的意愿，未获胜者就会成为获胜者。

<!-- .element: class="fragment" -->

- ✅ 有更大的机会确保抵押的代币不会被浪费。

<!-- .element: class="fragment" -->

- ✅ 可以优化除了“谁获得的赞成票最多”之外的其他标准。

<!-- .element: class="fragment" -->

---

## NPoS的缺点

- 我们决定在链上解决一个NP难的、多获胜者的、基于批准的选举问题 🤠。

<pba-flex center>

- 可扩展性。 <!-- .element: class="fragment" -->
- 可扩展性。 <!-- .element: class="fragment" -->
- 可扩展性。 <!-- .element: class="fragment" -->
- 可扩展性。 <!-- .element: class="fragment" -->
- 还是可扩展性。 <!-- .element: class="fragment" -->

</widget-text>

- 但作为回报，我们（努力）获得了更好的经济安全措施 🌈。

<!-- .element: class="fragment" -->

- 从长远来看，这个问题本身可以通过 Polkadot 网络最擅长提供的东西——更多的区块空间来解决 🎉！

## <!-- .element: class="fragment" -->

### NPoS协议概述

- 当前的NPoS协议围绕着一个 **选举轮次** 展开，这个轮次本身由4个阶段组成。
- 这让你对我们目前是如何解决可扩展性问题有了一个大致的了解。

---v

### NPoS协议概述：阶段1

**快照**

- 实现多区块选举。
- 使我们无需“冻结”抵押系统。
- 允许我们对抵押者进行索引，而不是对 `AccountIds` 进行索引。

---v

### NPoS协议概述：阶段2

**签名提交**

- 任何经过签名的账户都可以根据该快照提出一个 **NPoS解决方案**。
- 引入了押金、奖励、削减等其他博弈论工具，以确保安全性。

---v

### NPoS协议概述：阶段3

**验证者提交作为备用方案**

- 作为第一个备用方案，任何验证者都可以在创建区块时提交一个解决方案。

---v

### NPoS协议概述：阶段4

**备用方案**

- 如果上述所有方法都失败了，链将不会轮换验证者，治理机构可以选择：
  - 指定下一组验证者。
  - 触发一个链上选举（其功能有限）。

## 这最近在Kusama网络中被使用过 [点击查看](https://forum.polkadot.network/t/kusama-era-4543-slashing/1410) 🦜。

## NPoS的目标

- 鉴于NPoS这个强大的工具，我们应该追求什么目标呢？
- 让我们先来回顾一下：

1.  Polkadot 网络的验证者是中继链以及所有平行链和桥的状态转换的可信来源。

<!-- .element: class="fragment" -->

2.  Polkadot 网络的验证者被分配到平行链作为支持组，并随着时间进行轮换。

<!-- .element: class="fragment" -->

3.  Polkadot 网络的所有验证者创建的区块数量相同，也就是说，他们的重要性是相同的。

<!-- .element: class="fragment" -->

Notes:

第2点并不意味着 Polkadot 网络验证者集合的安全性是在平行链之间进行划分的，安全性来自于批准投票者。
<https://www.polkadot.network/blog/polkadot-v1-0-sharding-and-economic-security/>

---v

### NPoS目标：选举得分

```rust
pub struct ElectionScore {
  /// 按总支持权益计算的最小获胜者。
  ///
  /// 这个参数应该被最大化。
  pub minimal_stake: u128,
  /// 所有获胜者的总支持权益之和。
  ///
  /// 这个参数应该被最大化。
  pub sum_stake: u128,
  /// 所有获胜者的总支持权益的平方和，也就是方差。
  ///
  /// 这个参数应该被最小化。
  pub sum_stake_squared: u128,
}
```

---v

### NPoS目标：选举得分

- NPoS允许我们激励形成一个能够优化上述 `ElectionScore` 的验证者集合。

- 这个得分总是在链上进行计算和检查。这就是为什么我们可以接受来自外部世界的解决方案。

Notes:

一个常见的例子：我们允许签名提交。如果他们发送的解决方案排除了某个特定的验证者怎么办？如果它能获得更好的得分，那就没问题！我们不在乎。

---v

### NPoS目标：选举得分

- 链上和链下求解器中使用的默认算法是 [Phragmen算法](https://en.wikipedia.org/wiki/Phragmen%27s_voting_rules)。
- 该算法被证明可以提供高度的公平性和合理的代表性，同时可以在线性时间内进行验证。

---

## NPoS的未来

- 新鲜出炉（2023年1月）：[ Polkadot 网络论坛中关于 Polkadot 网络抵押的未来](https://forum.polkadot.network/t/the-future-of-polkadot-staking/1848/2)。
- [Github问题跟踪器/项目](https://github.com/orgs/paritytech/projects/33)

<hr>

- 提名池
- 多页选举提交
- 运营商作为一等公民。
- 快速解除抵押。

---

## 更多资源！ 😋

<img width="300px" rounded src="../../substrate/scale/img/thats_all_folks.png" />

> 查看演讲者备注（点击“s” 😉）

Notes:

### 进一步阅读

- 最近Kusama网络的削减事件：https://forum.polkadot.network/t/kusama-era-4543-slashing/1410
- [一种可验证安全且按比例的委员会选举规则](https://arxiv.org/abs/2004.12990)
- 《 Polkadot 网络概述及其设计考虑》中的4.1节：https://arxiv.org/abs/2005.13456
- [比例合理代表制](https://arxiv.org/abs/1611.09928)
- [合理代表制 - 维基百科](https://en.wikipedia.org/wiki/Justified_representation)

### NPoS协议：更多细节，备用幻灯片

- `bags-list`：如何在链上存储一个无界的半排序链表。
- 提名池：两全其美。
- 最小不可信得分。
- PJR检查：我们为什么不做。
- `reduce` 优化。

### 讲座后的反馈：
