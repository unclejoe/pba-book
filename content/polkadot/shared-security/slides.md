---
title: What is Shared Security?
description: A high level overview of Shared Security in Polkadot
duration: 1 hour
---

# 什么是共享安全？

---

## 从表面上看……

共享安全是区块链的一种经济扩展解决方案。

---

<div class="grid grid-cols-2">
<div>

但这只是一个表面的答案。
这个话题远比这要深入得多。

让我们来探索一下……

</div>
<div>

<img style="width: 500px" src="./img/iceburg.jpg" />

</div>
</div>

---

## 什么是安全？

几乎所有对区块链的攻击都可以归为以下两类之一：

- 技术安全（密码学）
- 经济安全（博弈论 + 经济学）

我们将重点关注经济安全。

---

经济安全体现在改变区块链规范历史所需的经济成本上。

<img style="width: 1000px" src="./img/chain-fork.svg" />

安全性较高的链对恶意活动（如双花攻击）的抵御能力更强。

---

## 请注意，双花本身并不是一种攻击！

<pba-cols>
<pba-col>

在所有区块链协议中，签署并提交两条相互冲突的消息是完全允许的。

由区块链来就这两笔交易中的哪一笔是规范交易达成共识。

</pba-col>
<pba-col>

<img style="width: 500px" src="./img/double-spend.svg" />

</pba-col>
</pba-cols>

---

## 攻击是什么样子的？

在这个例子中，有人故意利用网络中的碎片化问题，试图创建两条不同的规范链。

<img style="width: 900px" src="./img/network-attack.svg" />

---

## 攻击之后会发生什么？

最终，网络碎片化问题将得到解决，共识消息将使我们能够证明恶意节点**进行了双签**。

<img style="width: 900px" src="./img/network-attack-2.svg" />

也就是说，它们签署了验证两条相互冲突的链的消息。

---

## 经济成本是什么？

这将导致对恶意节点进行**惩罚性削减**，削减的力度应该足够大，以阻止这类恶意活动的发生。

---

因此，在区块链中，经济和安全是紧密相关的。

<img style="width: 900px" src="./img/economics-security.svg" />

---

# 启动问题

---

### 什么是启动问题？

启动问题是指新链在其代币经济尚未足够或稳定时，为保持链的安全性所面临的困境。

可以说，区块链中最稀缺的资源是经济安全——根本没有足够的资源可供分配。

---

<pba-cols>
<pba-col>

## 新链的市值较小

<img style="width: 500px" src="./img/small-market-cap.svg" />

</pba-col>
<pba-col>

## 新链更具投机性

<img style="width: 500px" src="./img/speculative-graph.svg" />

</pba-col>
</pba-cols>

---

# 我们如何解决这个问题？

---

# 共享安全

<img style="width: 900px" src="./img/spongebob.jpg" />

---

## 当今“共享安全”的不同形式

- 原生：原生共享安全是在协议层面实现的，表现为一个第 0 层区块链，在第 1 层链之下运行。
-  Rollups：乐观和零知识 Rollups使用结算层为其交易提供安全性和最终性。
- 再质押：一些协议允许使用已经质押的代币来保护另一个网络，通常是通过创建衍生代币来实现。

## 但这些不同的形式并不相同……

---

# 深入了解 Polkadot 共享安全

---

##  Polkadot 的共享安全

<pba-cols>

<pba-col>

<img style="width: 500px" src="../decisions/img/parachains-transparent.png" />

</pba-col>

<pba-col>

 Polkadot 的独特之处在于，它为所有连接的平行链提供与中继链本身相同的安全保障。

这是协议原生的功能，也是其核心功能之一。

</pba-col>

</pba-cols>

---

## 共享安全的构建模块

<pba-cols>

<pba-col>

1. 执行元协议
1. 协调 / 验证
1. 安全中心 / 结算层

</pba-col>

<pba-col>

- WebAssembly（Wasm）
- 平行链协议
- 中继链

</pba-col>

</pba-cols>

---

# Wasm

---

## 怎么强调 Wasm 都不为过

<pba-cols>

<pba-col>

<img style="width: 700px" src="./img/wasm-in-storage.png" />

</pba-col>

<pba-col>

在 Polkadot 生态系统中，每条链的状态转换函数都由一个 Wasm 二进制文件表示，该文件存储在区块链本身中。

这有很多影响，我们已经讨论过了，但在这个背景下的关键一点是，它非常易于共享、通用且执行安全。

</pba-col>

</pba-cols>

---

## 游戏主机类比

<pba-cols>

<pba-col>

<img style="width: 500px" src="./img/nintendo-console-2.png" />

基本的 Substrate 客户端

</pba-col>

<pba-col>

<img style="width: 250px" src="./img/nintendo-game-acala.png" />
<img style="width: 250px" src="./img/nintendo-game-astar.png" />
<img style="width: 250px" src="./img/nintendo-game-moonbeam.png" />
<img style="width: 250px" src="./img/nintendo-game-polkadot.png" />

Wasm 运行时

</pba-col>

</pba-cols>

---

##  Polkadot 验证节点

<img style="width: 1000px" src="./img/nintendo-console-extreme.png" />

---

## 简而言之……

- 如你所知， Polkadot 客户端基本上是一个 Wasm 执行器。
- 我们生态系统中的所有链都使用 Wasm 作为其状态转换函数。
- Wasm 元协议允许 Polkadot 即时执行任何链！

> 请注意，我们实际上是在执行其他链的区块。
>
> 减少信任，增加真实性！

---

# 平行链验证

---

## 最大化扩展

一个可扩展的权益证明系统是这样的：

- 安全性尽可能**共享**
- 执行尽可能**分片**

注意：

你不应该将共享安全与分片安全混淆。

也就是说，Cosmos 是一个分片安全网络。

---

## 执行分片

执行分片是指在验证者集合中分配区块链执行职责的过程。

在 Polkadot 中，所有验证者都执行每个中继链区块，但只有一个子集执行每个平行链区块。

这使得 Polkadot 能够扩展。

---

## 如何验证一个区块？

<img style="width: 1200px" src="./img/parachain-validation.svg" />

---

## 提交平行链区块

平行链将带有有效性证明的新区块提交给网络。

<img style="width: 900px" src="./img/parachain-validation-multiple.svg" />

平行链的 Wasm 运行时和最新状态根已经存储在中继链上。

---

平行链协议有需要验证和包含的新区块。

<img style="width: 600px" src="./img/parachain-validators.svg" />

##  Polkadot 验证者

---

随机选择的一部分验证者被分配来执行平行链区块。

<img style="width: 600px" src="./img/parachain-validators-colored.svg" />

然后，新的状态根被提交到中继链，以便这个过程可以重复进行。

---

## 我们如何防止出错？

- 数据可用性
  -  Polkadot 使用纠删编码在分配给平行链的验证者之间进行编码，以确保验证区块所需的数据始终可用。
- 批准检查
  - 每个验证者节点在每个中继链区块中对随机选择的平行链区块运行批准检查过程。
    如果一个平行链区块的初始指定批准者“未出现”，那么我们就假设发生了攻击，在最坏的情况下，会让整个验证者集合来检查该区块。
- 争议处理
  - 当有人对一个平行链区块的有效性提出争议时，所有验证者都必须检查该区块并进行投票。
    在争议中失败的一方的验证者将被削减。

---

# 中继链

---

##  Polkadot 的安全中心

<pba-cols>

<pba-col>

<img style="width: 500px" src="../../polkadot/decisions/img/parachains-transparent.png" />

</pba-col>

<pba-col>

中继链是 Polkadot 网络的核心。

- 提供以 DOT 为基础的经济实用代币。
- 提供一组高质量的验证者。
- 存储每个平行链所需的基本数据。
- 为平行链区块确立最终性。

</pba-col>

</pba-cols>

---

## 平行链区块最终确定

<pba-cols>

<pba-col>

<img style="width: 500px" src="./img/parachain-finalization.svg" />

</pba-col>

<pba-col>

一旦平行链协议完成，中继链区块生产者就会将新的状态根提交到中继链。

因此，当中继链区块最终确定时，所有包含的平行链区块也将最终确定！

在 Polkadot 上提交的平行链状态就是**规范链**。

</pba-col>

</pba-cols>

---

## 无信任交互

<pba-cols>

<pba-col>

<img style="width: 500px" src="../decisions/img/xcmp-finalization.svg" />

</pba-col>

<pba-col>

这也意味着， Polkadot 上的最终确定意味着同一高度上所有平行链之间的所有交互都最终确定。

因此，共享安全不仅保护了各个链的安全，还保护了链之间的交互安全。

</pba-col>

</pba-cols>

---

## 共享安全的构建模块

<pba-cols>

<pba-col>

1. 执行元协议
1. 协调 / 验证
1. 安全中心 / 结算层

</pba-col>

<pba-col>

其他协议声称它们提供共享安全……但它们有这些关键构建模块吗？

</pba-col>

</pba-cols>

---

# 比较选项

---

## 再质押解决方案

<div class="grid grid-cols-3 text-small">

<div>

### 优点

- 似乎与协议无关，并且可以“回移植”到新的和现有的链上。
- 较小 / 较新的链可以依赖更有价值和稳定的经济体系。

</div>

<div class="col-span-2">

### 缺点

- 随着代币不断被再质押，攻击受保护链所需的经济“成本”会降低。
- 这些系统没有提供真正的计算验证或保护。
- 似乎最终还是要依赖中心化的信任来源。

</div>

</div>

注意：

请参阅此处的“关键风险和漏洞”部分：

<https://consensys.net/blog/cryptoeconomic-research/eigenlayer-a-restaking-primitive/>

> 一般来说，EigenLayer 有两个主要的攻击向量。
> 一个是许多验证者合谋同时攻击一组中间件服务。
> 另一个是利用 EigenLayer 构建的协议可能存在意外的削减漏洞，存在诚实节点被削减的风险。
>
> EigenLayer 机制的很大一部分依赖于一种重新平衡算法，该算法会考虑不同的验证者及其相应的质押、安全容量和使用情况。
> 这是该协议成功的基础。
> 如果这个重新平衡机制失败（例如调整缓慢、延迟、参数不正确），那么 EigenLayer 就会面临不同的攻击向量，特别是在加密经济安全方面。
> 它基本上复制了它试图通过合并挖矿解决的相同漏洞。
> 因此，必须注意确保系统准确更新任何未清偿的再质押 ETH，并保持其完全抵押。

---

## 乐观 Rollups

<div class="grid grid-cols-3 text-small">

<div>

### 优点

- 不受链上虚拟机复杂性的限制。
- 可以并行处理。
- 它们可以在其状态转换函数中处理大量数据。
- 它们可以使用现代处理器的本机编译代码。

</div>

<div class="col-span-2">

### 缺点

- 对定序器的中心化和审查问题存在担忧。
- 由于挑战期的存在，最终确定时间较长。
  （可能需要数天）
- 结算层可能会受到攻击，从而影响乐观 Rollups协议。
- 在分配区块空间方面存在与链上交易相同的问题。
  - 执行交互式协议的链上成本。
  - 网络拥堵。

</div>

</div>

---

## 零知识 Rollups

<div class="grid grid-cols-3 text-small">
<div>

### 优点
- 说实话，它们相当出色。
- 无需信任即可证明。
- 数据可用性要求低。
- 可实现即时最终确定性（但成本高昂）。
- 如果递归证明可行，未来前景广阔。
</div>
<div class="col-span-2">
### 缺点
- 存在定序器和证明者中心化的问题。
- 编写零知识电路颇具挑战性。
  - 虽然具有图灵完备性，但通常计算复杂度较高。
  - 在构建应用程序时，难以限制电路的复杂度。
- 在分配区块空间方面，与链上交易存在相同的问题。
  - 在结算层提交和执行证明的链上成本较高。
  - 网络拥堵。
</div>
</div>

---

##  Polkadot 原生共享安全
<div class="grid grid-cols-2 text-small">
<div>
### 优点
- 在协议层面处理分片、共享安全和互操作性。
- 易于开发状态转换函数（STF）：任何能编译成Wasm的内容都可以。
- 最终确定性时间可能是最优的，通常在一分钟以内。
- 现有验证者提供数据可用性。
- 与定序器和证明者相比，对整理者中心化的担忧要少得多。
</div>
<div>
### 缺点
- 为确保平行链与平行链协议兼容，存在一些限制。
  - 使用Wasm作为状态转换函数
  - 不支持自定义宿主函数
  - 执行环境受限
- 遗憾的是，Wasm的速度仍比原生编译慢两倍。
- 需要在有效性证明（PoV）中提供并获取大量数据。
</div>
</div>

---

<!-- .slide: data-background-color="#4A2439" -->
# 问题