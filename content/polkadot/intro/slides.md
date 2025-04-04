---
title: Introduction to Polkadot
description: An introduction to the key concepts of Polkadot.
duration: 1 hour
---

<!-- .slide: data-background-color="#000" -->

#  Polkadot 简介

---

## 什么是 Polkadot ？

>  Polkadot 是一个可扩展的、异构的、分片的多链网络。

>  Polkadot 是一个无需许可的、无处不在的计算机。

>  Polkadot 是一个去中心化的开源社区。

>  Polkadot 是一个数字原生的主权网络。

---

## 环境

 Polkadot 有许多不同的方面，包括技术和社会层面。

它是本模块中所描述技术在现实世界中的实例化。

这些技术仅定义了环境的边界。
 Polkadot 是在这个环境中发生的一切。

---

### 参与者

在这个环境中，各种类型的参与者可以根据其规则采取行动。

这些参与者可能是人类用户、硅基用户、政府或法律实体。

他们可能有不同的议程和活动水平，并且可能位于世界上的任何地方。

 Polkadot 是智能参与者的在线经济。

---

### 博弈论

 Polkadot 通过一系列机制和博弈论来实现其目标，这些机制和博弈论为节点提供激励和惩罚措施，促使它们共同构建这个环境。

---

### 一切融合

<img rounded width="1200px" src="./img/gav_header_photo.jpg" />

Notes:

图片来源：<https://twitter.com/gavofyork> 的横幅图片

---

## 环境的目标

1. 对系统状态达成实时、安全的全球共识
1. 大规模的无需信任、抗审查和无需许可的交易
1. 明确的全网治理和共同演进
1. 具有完全安全性的通用可编程计算
1. 进程之间安全且信任最小化的互操作性

---

## 目标：详细解析

> （1）对系统状态达成实时、安全的全球共识

 Polkadot 的状态应尽可能接近实时地更新。

维护一个全球唯一的、记录所有已发生事件的历史记录。

Notes:

状态包括账户余额、链、治理投票等。

---

## 目标：详细解析

> （2）大规模的无需信任、抗审查和无需许可的交易

与网络进行交互只需要一个私钥和一定的余额，并且无需信任任何单一的第三方。

它旨在大规模地实现这一点。

---

## 目标：详细解析

> （3）明确的全网治理和共同演进

 Polkadot 的利益相关者明确地对网络进行治理和演进，<br />并有能力制定新规则。

---

## 目标：详细解析

> （4）具有完全安全性的通用可编程计算

 Polkadot 通过通用程序进行扩展，这些程序通常以区块链的形式存在，而这些区块链本身可能是可编程的环境。

通用计算允许网络的功能得到任意扩展，同时继承网络的全部安全性。

---

## 目标：详细解析

> （5）进程之间安全且信任最小化的互操作性

部署在 Polkadot 上的进程需要：

<pba-flex center>

- 相互通信。
- 在不完全信任对方的情况下进行“交易”。
- 保护交易路线并执行交易协议。

</pba-flex >

---

## 验证者

验证者决定参与网络的维护。

验证者参与 Polkadot 的核心**博弈论**。

---

## 验证者

验证者会受到**激励**去做一些事情，比如将用户交易打包进区块或参与其他活动，但他们也可以选择不执行其中许多任务。

验证者如果故意不履行职责，将会受到严厉的惩罚。

只要有足够多的验证者履行职责且不违规，这些博弈论就能正常运行。

---

<!-- .slide: data-background-color="#000" -->

#  Polkadot 架构

Notes:

对 Polkadot 架构及维护网络的参与者的高层概述。

---

##  Polkadot ：主要系统

<img rounded width="800px" src="./img/polkadot-components.svg" />

---

验证者负责为部署在 Polkadot 之上的进程提供准确的执行。

这些进程，被定义为 WebAssembly 代码，通俗地称为**平行链**。

 Polkadot 通过让众多验证者分担验证这些平行链的负载来实现扩展。

---

<img rounded width="700px" src="./img/polkadot-architecture.svg" />

---

<img rounded width="900px" src="./img/polkadot-architecture-simple.png" />

Notes:

简化的 Polkadot 架构（平行链）

---

## 中继链

中继链是 Polkadot 的“枢纽”，提供了验证者参与的主要博弈论。
它是使用 Substrate 构建的。

值得注意的是，中继链的功能被最小化，预计更复杂的功能将被推到系统中不太关键的部分。

---

## 中继链功能：

<pba-flex center>

- 治理（转移到平行链）
- 质押
- 平行链的注册、调度和推进
- 平行链之间的通信
- 共识安全
- 余额转移

</pba-flex>

---

## 中继链博弈论：

中继链由两个关键博弈论组成：

<pba-flex center>

- 中继链共识
- 平行链共识

</pba-flex>

这些博弈论是 Polkadot 内所有活动的推动者。

---

## 博弈论：中继链共识（简化版）

<div style="font-size: 0.8em">

<pba-flex left>

**目标：**

- 增长并最终确定中继链，使其仅由有效区块组成

</pba-flex>
<pba-flex left>

**规则：**

</pba-flex>

- 验证者以代币的形式参与博弈论。
- 验证者有动力创建新的中继链区块（BABE）
- 验证者有动力对最近的中继链区块进行投票以最终确定（GRANDPA）
- 验证者有动力将用户交易包含在他们的中继链区块中。
- 验证者创建无效区块或在无效区块上继续构建不会获得任何奖励。
- 验证者如果违规创建区块会被削减代币。

</div>

<br />

**只要违规的验证者少于三分之一，博弈论就能正常运行。**

---

## 博弈论：平行链共识（简化版）

<div style="font-size: 0.8em">

<pba-flex left>

**目标：**

- 使已注册的平行链增长，并仅向中继链发布有效更新

</pba-flex>
<pba-flex left>

**规则：**

</pba-flex>

- 验证者有动力对新的平行链更新进行证明
- 无论哪个验证者创建下一个中继链区块，都会包含一些已证明的平行链更新
- 验证者如果对不正确的平行链更新进行证明，将会被削减代币
  - 不正确是指“不符合平行链的 Wasm 代码”
- 验证者会相互检查工作以启动削减代币的程序

</div>

<br />

**只要违规的验证者少于三分之一，博弈论就能正常运行。**

---

中继链的所有其他功能（质押、治理、余额等）都被融入到**有效区块**和**有效交易**的定义中。

---

## 质押：提名权益证明

随着中继链的发展，它运行着一个用于选择验证者并为其积累资本的系统。

 Polkadot 上的账户可以发出“提名”交易，以选择他们支持的验证者。
每天，会有一个自动选举选出接下来 24 小时的验证者。

提名者会与他们提名的验证者共同分享奖励和承担削减风险。

---

## 消息传递：无需信任的通信

中继链管理着平行链之间以及每个平行链与中继链本身之间的消息队列和通道。

验证者的部分工作是确保消息队列得到正确维护和更新。

---

## 注册平行链

在 1.0 版本中：这是通过插槽拍卖来获得大量批量分配完成的。

在未来：这将以更细粒度/临时的方式进行。

---

<!-- .slide: data-background-color="#000" -->

## 治理与演进

---

## 开放式治理（OpenGov）

 Polkadot 通过利益相关者公投进行链上治理，投票主题包括：

<pba-flex center>

- 网络的无分叉升级
- 国库资金的管理
- 平行链协议的配置
- 费用的配置
- 救援与恢复操作
- 对平台的所有其他控制机制

</pba-flex>

Notes:

<https://www.polkadot.network/features/opengov/>

---

## 国库

<pba-flex center>

-  Polkadot 确保一部分网络费用被收集到国库中。
- 国库由治理机构管理。
- 如果代币未被使用，将会被销毁。

</pba-flex>
<br />

国库的目的是向帮助 Polkadot 发展的人支付报酬。
随着代币被销毁，这会产生资助公共项目的压力。

---

###  Polkadot 开发者社区（The Fellowship）

一个由开发者组成的集体，通过链上活动进行协调。

这些人包括节点维护者、开发者或研究人员。

他们通过请求评论（RFC）为网络设定技术方向。

---

<!-- .slide: data-background-color="#111" -->

<pba-cols>

<img rounded width="400px" src="./img/rfc_1.png" />
<img rounded width="400px" src="./img/rfc_3.png" />
<img rounded width="400px" src="./img/rfc_10.png" />

</pba-cols>

---

<!-- .slide: data-background-color="#000" -->

## 回顾目标

---

> （1）对系统状态达成实时、安全的全球共识

这是由参与**中继链共识**的验证者提供的。
他们创建和最终确定的每个区块都会推进系统状态。

---

> （2）大规模的无需信任和无需许可的交易

“大规模”是 Polkadot 大部分工程设计的关键考量因素。

 Polkadot 通过**平行链共识**博弈论实现扩展，其中平行链本身处理网络中的大部分交易。
这个博弈论的实现越高效， Polkadot 的扩展性就越强。

---

> （3）明确的全网治理和共同演进

这由开放式治理（OpenGov）和国库来处理。
 Polkadot 是一个“元协议”，OpenGov 可以更新网络中的任何内容，包括网络自身的规则。

---

> （4）具有完全安全性的通用可编程计算

这由**平行链共识**博弈论提供，因为大多数平行链是由用户注册的，验证者负责验证更新。
平行链本身也可以提供通用的计算功能，例如以太坊虚拟机（EVM）合约。

---

> （5）进程之间安全且信任最小化的互操作性

中继链维护链之间的消息队列以提供互操作性（受保护的交易路线），然而，要实现完全的信任最小化（强制执行的交易协议）还需要未来的协议功能落地。

---

<!-- .slide: data-background-color="#000" -->

## 大规模应用： Polkadot 的价值主张

---

## 从这个

<img rounded width="600px" src="./img/terrarium_bottle.jpg" />

---

## 到这个

<img rounded width="800px" src="./img/amazon_terrarium.jpg" />

---

## 区块链可扩展性三难困境

<pba-cols>
<pba-col>
<pba-flex center>

1. 安全性：攻击网络的成本是多少？
1. 可扩展性：网络能处理多少工作量？
1. 去中心化：网络的去中心化程度如何？

</pba-flex>
</pba-col>
<pba-col>

<img rounded width="800px" src="./img/scalability-trilemma.svg" />

</pba-col>
</pba-cols>

挑战：在应对三难困境的同时实现扩展。

---

## 扩展与调度

扩展很重要，但必须高效地**分配**资源，以充分利用它。

 Polkadot 通过**执行核心**将资源分配给平行链。

---

## 执行核心

就像一个去中心化的中央处理器（CPU）， Polkadot 通过核心对多个进程进行多路复用。

当一个平行链被分配到一个核心时，它可以继续运行。<br />
否则，它将处于休眠状态。

执行核心通过**核心时间**交易实现高效分配。

---

<!-- .slide: data-background-color="#000000" -->

## 每个核心一条链

<img rounded width="1000px" src="./img/dumb_coretime.png" />

时间 -->

---

<!-- .slide: data-background-color="#000000" -->

## 执行核心：最终目标

<img rounded width="1000px" src="./img/smart_coretime.png" />

时间 -->

---

## 核心时间： Polkadot 的产品

核心时间是应用程序在 Polkadot 上构建所需购买的资源。<br />
目标：像云服务一样。

一级市场和二级市场是关键推动因素。

可扩展性 + 调度

---

## 回归原点

>  Polkadot 是一个可扩展的、异构的、分片的多链网络。

>  Polkadot 是一个无需许可的、无处不在的计算机。

>  Polkadot 是一个去中心化的开源社区。

>  Polkadot 是一个数字原生的主权网络。

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题