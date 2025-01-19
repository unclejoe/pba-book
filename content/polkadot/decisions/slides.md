---
title: The Decisions of Polkadot
description: A High Level Review of the Key Decisions of Polkadot
duration: 1 hour
---

#  Polkadot 的决策

---

##  Polkadot 的决策

本次演讲将尝试解释定义 Polkadot 网络的核心决策。

---

## 打造一台“发明机器”

杰夫·贝索斯在致亚马逊股东的年度信中阐述了他的决策方法，即将决策分为一类决策和二类决策。

Notes:

<https://www.sec.gov/Archives/edgar/data/1018724/000119312516530910/d168744dex991.htm>

---

## 一类决策

> 有些决策会产生重大影响，且不可逆转或几乎不可逆转——<span style="color:#d92f78"> **单向门**</span>——对于这些决策，必须有条不紊、谨慎、缓慢地做出，要经过深思熟虑并充分咨询。如果你穿过这扇门后，不喜欢门另一边的景象，你也无法回到原来的地方。我们可以将这些决策称为一类决策。

---

## 二类决策

> 但大多数决策并非如此——它们是可变的、可逆的——<span style="color:#d92f78"> **它们是双向门**</span>。如果你做出了一个不太理想的二类决策，你不必长期承受其后果。你可以重新打开这扇门，回到原来的状态。二类决策可以而且应该由判断力强的个人或小团体迅速做出。

---

## 在区块链的背景下……

<pba-cols>
<pba-col>

### 一类决策

未来不容易改变的决策。

- 必须是原始协议设计的一部分。
- 更改可能被视为一个新的协议。

</pba-col>
<pba-col>

### 二类决策

未来容易改变的决策。

- 可以在稍后的时间纳入协议中。
- 更改可以被视为协议演进的一部分。

</pba-col>
</pba-cols>

---

##  Polkadot 的理念

---

<img style="width: 1000px; filter: invert();" src="./img/less-trust-more-truth.svg" />

Notes:

这是Web3峰会的口号，鉴于 Polkadot 是我们对Web3未来的最大押注，将这句话作为支撑 Polkadot 的核心理念是恰当的。我们开发区块链技术的根本原因是为了解决我们在面对世界上掌握权力的人时所面临的信任问题。

我想强调的是，这句话不是“无需信任，唯有真相”。就我目前所知，这几乎是不可能的。我们不应该用信任问题来否定可行的解决方案。例如，我们不期望每个使用 Polkadot 的人在运行它之前都要阅读开源代码的每一行。我们的目标应该是在可能的情况下尽量减少信任，并让每个使用我们工具的人都清楚他们所依赖的信任假设是什么。

---

## 反对区块链最大化主义

<img width="1000px" src="../../substrate/intro/img/dev-4.1-maximalism.png" />

Notes:

 Polkadot 从根本上相信多链未来。在这个未来中，各条链相互协作，为彼此创造更大的价值，而不是相互竞争，试图消灭对方。这种情况在今天并不常见，因为加密货币往往会成为“投资工具”，新的区块链的创建可能会被视为对现有“投资”的威胁。在区块链最大化主义的思维下，人们会选择看重他们的“投资”，而不是创新和进步，这对我们向世界提供最佳技术的目标来说并非最优选择。

---

## “今天最好的区块链明天可能就不是最好的了。”

Notes:

这个理念认识到，构建区块链就像是要构建一个可以永久使用的软件。如果我们仅仅为了解决当下的问题而设计 Polkadot ，那我们不会成功。当我们构建出X的时候，世界可能已经需要Y了，以此类推。这就是为什么我们花了这么多时间来构建平台和软件开发工具包（SDK），而不仅仅是产品。我们需要确保这些技术能够适应和发展，以保持对用户的相关性。

---

##  Polkadot 的目标

---

## 区块链可扩展性三难困境

<div class="grid grid-cols-2">

<div>

<img style="height: 400px;" src="./img/trilemma.svg" />

</div>

<div>

- 安全性：攻击网络的成本是多少？
- 可扩展性：网络能处理多少工作量？
- 去中心化：网络的去中心化程度如何？

</div>

</div>

---

## 用一句话概括……

>  Polkadot 的使命是为Web3应用程序和服务提供安全、可扩展且有弹性的基础设施。

Notes:

注意“去中心化”作为使命和“弹性”之间的区别。

---

 Polkadot 试图通过解决三个问题来实现这一使命：

<pba-flex center>

1. 计算可扩展性
1. 共享安全
1. 互操作性

</pba-flex>

---

## 决策

哪些一类决策造就了 Polkadot ……使其成为 Polkadot ？

---

## WebAssembly（Wasm）

<div class="grid grid-cols-3">

<div>

<img style="width: 400px;" src="./img/webassembly-blue.png" />

</div>

<div class="col-span-2">

WebAssembly是 Polkadot 的支柱。它是一种快速、安全、开放的元协议，为我们的生态系统中的所有状态转换提供动力。

它规范了链的执行方式，通过沙盒隔离执行以提高安全性，并允许团队使用任何可以编译成Wasm的语言在 Polkadot 上进行开发。

</div>

</div>

---

## 分片

<div class="grid grid-cols-2">

<div>

 Polkadot 主要通过在不同的数据分片上并行执行来实现扩展。

这些并行链（分片）称为平行链。

</div>

<div>

<img style="width: 500px;" src="./img/sharding.svg" />

</div>

</div>

---

## 应用链

<div class="grid grid-cols-2">

<div>

<img style="width: 500px;" src="./img/app-chain.svg" />

</div>

<div>

另一个关键的扩展决策是选择异构分片，以支持特定于应用程序的链。

针对特定问题的专门解决方案比通用解决方案性能更高，因为它们可以纳入更多关于问题领域的细节。

</div>

</div>

---

## 互操作性

<div class="grid grid-cols-2">

<div>

单个应用链本身往往无法为终端用户提供一套完整的优化解决方案。

互操作性允许平行链协同工作，完成复杂的端到端场景。

</div>

<div>

<img style="height: 500px;" src="./img/xcmp-2.svg" />

平行链之间的XCMP通道示意图。

</div>

</div>

---

## 共享安全

<div class="grid grid-cols-2">

<div>

一个经常被忽视的问题是整个区块链生态系统的经济扩展性。

 Polkadot 的独特之处在于，它为所有连接的平行链提供与中继链本身相同的安全保障。

</div>

<div>

<img style="width: 500px" src="./img/parachains-transparent.png" />

</div>

</div>

Notes:

在权益证明网络中，安全性取决于经济因素，因此世界上的安全性是有限的，因为经济价值从定义上来说是有限的。随着由于单链的扩展性问题而导致区块链数量的增加，它们的经济价值——进而它们的安全性——会分散到多个链上，使得每个链都比以前更脆弱。

 Polkadot 引入了一种共享安全模型，这样各条链在与其他链交互时，就能清楚地知道其交互对象拥有与自己的链相同的安全保障。基于桥接的解决方案——即每条链自行处理其安全性——会迫使接收方信任发送方。 Polkadot 的安全模型提供了必要的保障，使得跨链消息具有实际意义，而无需信任发送方的安全性。

---

## 执行核心

 Polkadot 的共享安全是通过创建和分配执行核心来实现的。

<img style="width: 1000px;" src="./img/exotic-scheduling.png" />

执行核心提供块空间即服务，并且设计为可以与任何类型的共识系统配合使用。

---

## 无信任交互

<div class="grid grid-cols-3">

<div>

<img style="height: 500px;" src="./img/xcmp-finalization.svg" />

</div>

<div class="col-span-2 text-left">

通过中继链实现共享安全的一个关键结果是，它可以跟踪所有平行链的状态，并使它们保持同步。

这意味着在 Polkadot 上最终确定的区块意味着**所有平行链之间在同一高度的所有交互都已最终确定**。

因此，共享安全不仅保障了单个链的安全，还保障了链与链之间的交互安全。

随着“协议”/ SPREE的加入，这一点还在不断发展。

</div>

</div>

---

## 混合共识

<div class="grid grid-cols-2">

<div>

### 区块生成

<img style="width: 500px;" src="./img/babe.svg" />

当前的实现是BABE，它具有分叉且概率性最终确定的特点。

</div>

<div>

### 最终确定性工具

<img style="width: 400px;" src="./img/grandpa.png" />

当前的实现是GRANDPA，它对网络分区具有强大的适应性和可扩展性。

</div>

</div>

---

## 轻客户端优先理念

<div class="text-left">

 Polkadot 坚信轻客户端是Web3未来的必要组成部分。在其开发过程中， Polkadot 一直坚定不移地将一流的轻客户端支持作为首要任务：

- 浏览器内Wasm客户端（Substrate Connect）
  - 还有Wasm状态转换函数！
- 共识数据集成到区块头中
- 默克尔树和其他轻客户端兼容的数据结构
- 最大限度地利用静态已知元数据，以减少对全节点的依赖。

</div>

---

## 链上运行时与无分叉升级

<div class="grid grid-cols-3">

<div class="col-span-2 text-left">

 Polkadot 协议规范明确区分了区块链客户端和运行时（状态转换函数）。

这主要用于实现平行链协议，但也允许链“无分叉”地升级其代码。

这使得 Polkadot 中继链和所有连接的平行链在区块链领域相对于其他链具有进化优势。

</div>

<div>

<img style="width: 400px;" src="./img/runtime-upgrade.png" />

</div>

</div>

---

## 链上治理

<div class="grid grid-cols-3">

<div class="col-span-2 text-left">

 Polkadot 及其平行链需要随着时间的推移不断变化以保持相关性，并且该网络从一开始就被设计为具有透明且复杂的流程，不仅可以批准或拒绝更改，还可以**自动实施这些更改**。

- 治理决策实际上可以改变链的底层代码（因为它是链上的）。
- 系统中总质押的50%应该能够控制系统的未来。

</div>

<div>

<img style="width: 400px;" src="./img/voting.svg" />

</div>

</div>

---

## 链上国库

<div class="grid grid-cols-2">

<div class="text-left">

 Polkadot 在其核心设计了一个自筹资金的国库池，以激励协议的开发和演进。

它完全由 Polkadot 的治理系统在链上控制，这意味着它不受通常施加于中心化实体的监管限制。

</div>

<div>

<img style="width: 600px;" src="./img/treasury.svg" />

</div>

</div>

---

##  Polkadot 的实现

 Polkadot 的二类决策有哪些？

---

## 平行链

 Polkadot 是围绕平行链设计的，但平行链的确切含义和表现形式正在不断演变。

<div class="grid grid-cols-2">

<div>

<img style="height: 200px;" src="./img/original-scheduling.png" />

</div>

<div>

<img style="height: 200px;" src="./img/exotic-scheduling.png" />

</div>

</div>

<br />

- 最初，平行链将是长期的应用链。
- 按需平行链（以前称为平行线程）改变了这种观点，现在也包括可以随时启动和停止的链。
- 未来的协议将具有更加奇特的核心调度和更加灵活的核心使用方式，这一切都是因为关于平行链的一类决策实际上是**执行核心**。

Notes:

- <https://hackmd.io/9xhCNYIOQny0v0QTsWuNwQ#/>
- <https://www.youtube.com/watch?v=GIB1WeVuJD0>

---

## XCM
### 跨共识消息格式
<div class="grid grid-cols-2">
  <div>
    <img style="height: 500px;" src="./img/xcm-stack.svg" />
    在平行链之间转移资产的指令。
  </div>
  <div>
    虽然跨链互操作性（XCMP）是一类决策，但链与链之间具体交互所使用的语言却并非如此。
    XCM是Parity当前采用的一种跨共识消息格式，不过我们已经看到其他团队在尝试他们自己的想法，或者推动XCM格式规范的更新。
  </div>
</div>
Notes:
<https://github.com/paritytech/xcm-format>

---

## 提名权益证明
<div class="grid grid-cols-3 text-small">
  <div>
     Polkadot 的主要功能之一，不仅是为自身提供安全性保障，还为连接的平行链提供安全防护。
    质押系统是该网络的关键关注点，并且我们拥有目前最先进的质押系统之一。
  </div>
  <div class="col-span-2">
    - 采用提名权益证明（NPoS）而非委托权益证明（DPoS），以便更好地分配质押权益。
      - 与DPoS相比，NPoS中当选验证者背后的质押权益多出约25%。
      - 代价是复杂度增加和可扩展性受限。
    - 通过经济激励措施，让资金在验证者之间平均分配。
    - 采用超线性惩罚机制，抑制验证者的中心化趋势。
    - 质押产生实际价值，使奖励机制合理。
  </div>
</div>
<br />
随着时间推移，该协议一直在积极发展，例如通过引入提名池，使其性能更高且更便于用户使用。

---

## SASSAFRAS
虽然混合共识是一类决策，但底层协议可以持续演进，例如从BABE演变为SASSAFRAS。
> 用于固定时间节奏性时隙分配的质押受让人半匿名抽签

这是BABE的扩展，是一种恒定时间出块协议。这种方法试图解决BABE的缺点，确保以恒定时间间隔准确生成一个区块。该协议利用零知识证明（zk-SNARKs）构建环VRF（可验证随机函数），目前仍在开发中。

---

## OpenGov
 Polkadot 链上治理系统的具体细节在其发展历程中多次变更。
- 为启动网络，曾使用Sudo账户初始化链。
- 之后采用了多方系统，参与者包括代币持有者、选举产生的理事会以及技术理事会。
- 最近，这些理事会被取消，代币持有者现在完全掌控 Polkadot 的治理系统。

---

## 国库与 Polkadot 开发者社区（Fellowships）
链上国库一直存在，并将持续存在；但资金的具体使用方式和受益对象随时间发生了变化：
- 最初只有简单的提案，需经治理系统批准。
- 随后引入了悬赏（Bounties）和小费（Tips）机制，增加了获取大额和小额资金的途径。
- 最近， Polkadot 开发者社区开始形成，这些社区代表着能够从网络本身获得固定收入的组织。

---

## 还有更多……
 Polkadot 旨在不断发展，并让二类决策的制定快速且轻松。
它是一台“发明机器”。

---

## 讨论决策的框架
在了解一个协议的决策时，你应该提出哪些问题呢？
<br />
- （这个决策）是什么？
- 做出（这个决策）时我们需要考虑什么？
  - 它是一类决策还是二类决策？
- （这条链）做出了哪些决策，原因是什么？
  - 他们选择了哪些权衡取舍？
- 其他人做出了哪些决策？
  - 那些决策可能有哪些优点或缺点？
- 区块链社区在（这个决策）方面还有哪些可以改进的地方？

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题