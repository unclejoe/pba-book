---
title: Introduction to Cross Consensus Messaging (XCM)
description: XCM Core Concepts, Terms, and Logic for web3 builders
duration: 1 hour
---

# 跨共识消息传递（XCM）简介

---

## _核心概念、术语和逻辑_

Notes:

**先决条件**

- FRAME（存储项、可调度函数、事件、错误等）
- Polkadot （Polkadot）及平行链的概念
- 资产（非同质化代币（NFT）和同质化代币）

---v

## _在本讲座结束时，你将能够：_

<pba-flex center>

- 定义XCM的概念、语法和术语
- 浏览与XCM相关的现有资源
- 区分XCM和诸如XCMP之类的消息传递协议

---

# 跨链用例

我们为什么要在不同的区块链上执行操作呢？

Notes:

练习：请同学们举手并推测一般情况下可以做些什么。
我们期望他们会提到转账，但跨链还可以做很多其他的事情，有很多值得解决的问题：

- 一个合约调用另一个合约
- 凭证检查
- 投票

---v

## 🎬 一些具体用例

<pba-flex center>

- 跨共识资产转移
- 执行特定于平台的操作，例如治理投票
- 启用单一用例链
  - [集合体](https://github.com/paritytech/polkadot-sdk/tree/72c4535/cumulus/parachains/runtimes/collectives)
  - 身份链

Notes:

虽然XCM的目标是通用、灵活且具有前瞻性，但它当然也需要满足一些实际需求，尤其是链间代币转移。
我们需要一种方法来考虑并支付接收共识系统所需的任何费用。
特定于平台的操作；例如，在一个Substrate链中，可能需要向其某个模块进行远程调用以访问一个小众功能。
XCM使单个链能够指导许多其他链的操作，从而将多链消息传递的复杂性隐藏在一个易于理解和声明式的应用程序编程接口（API）之后。

---

> XCM是一种用于在**共识系统**之间传达**意图**的**语言**。

---v

### 共识系统

一个链、合约或其他全局的、封装的、状态机单例。

<pba-flex center>

它甚至不一定是一个**分布式**系统，只要它能够形成**某种**共识即可。

Notes:

一个共识系统不一定是区块链或智能合约。
它可以是Web 2.0世界中已经存在的东西，例如亚马逊网络服务（AWS）服务器中的一个EC2实例。
XCM是跨共识的，因为它的意义远不止于跨链。

---v

### ✉️ 一种格式，而非协议

XCM是一种**消息格式**。

它类似于邮局的明信片。

它**不是**一种消息传递协议！

明信片不会自己发送！

Notes:

它不能用于实际在系统之间“发送”任何消息；它的用途仅在于表达接收方应该做什么。
就像Substrate的许多核心方面一样，这种关注点的分离使我们能够更加通用，并实现更多功能。
明信片依赖邮政服务将其发送给接收者，而这正是消息传递协议所做的。
传输层负责发送任意的数据块，它不关心格式。
不过，一种通用的格式有其好处，我们接下来会看到。

---v

### 版本控制

XCM是一种**版本化**的语言。

它目前是第3版。

每个版本的内容通过请求评论（RFC）流程来定义。

---v

### 术语：XCMs

**XCM**，即跨共识消息传递，是一种格式。

**一个XCM** 是一条跨共识消息。

它不被称为XCM消息，

就像自动取款机（ATM）不被称为ATM机器一样。

---

## 😬 为什么不使用**原生**消息呢？

依赖原生消息或交易格式的缺点：

<pba-flex center>

- 原生消息格式因系统而异，甚至在**同一个**系统内也可能发生变化，例如在系统升级时
- 常见的跨共识用例与单个交易不是一一对应的
- 不同的共识系统有不同的假设，例如费用支付

Notes:

- 一个打算向多个目的地发送消息的系统需要了解如何为每个目的地编写消息。
  值得注意的是，即使是单个目的地，其原生交易/消息格式也可能随时间而改变。
  智能合约可能会升级，区块链可能会引入新功能或更改现有功能，从而改变其交易格式。
- 可能需要一些特殊技巧才能在单个交易中完成提款、兑换资金然后存入结果的操作。
  对于一个连贯的储备资产框架来说，所需的转账后续通知在不了解其他链的链中是不存在的。
  有些用例不需要账户。
- 有些系统假设费用支付已经协商好，而有些系统则没有。
  <!--
  FIXME TODO: 为什么不直接发送以太坊虚拟机（EVM）程序？为什么是XCVM而不是EVM？
  添加肖恩的照片。
  -->
  由解释器来以合理的方式解释意图。

---v

### 消息格式变化

<img style="width: 1050px;" src="./img/against-native-messaging.svg" />

---v

### 消息格式变化

<img style="width: 1050px;" src="./img/against-native-messaging-2.svg" />

---v

### 消息格式变化

<img rounded style="width: 1050px" src="./img/xcm-executor-routing-calls.png" />

Notes:

XCM抽象出了实际将被调用的链上操作，这使得接收方可以重定向调用，以始终使其有效。

---v

### 没有一一对应的映射关系

<diagram class="mermaid limit size-50">
graph TD
    subgraph 消息
        WithdrawAsset(提取资产)-->DepositAlice("存入资产（爱丽丝）")
        DepositAlice-->DepositBob("存入资产（鲍勃）")
    end
</diagram>

Notes:

你可能想要提取一些资产，然后将一部分存入一个账户，另一部分存入另一个账户。
使用交易的话，你得发送多条消息才能实现这一点。

---v

### 不同的假设

<diagram class="mermaid">
graph LR
    A(链A)--"支付费用"-->B(链B)
    A--"不支付费用"-->C(链C)
</diagram>

Notes:

不同的系统有不同的假设。
使用原生消息的话，你得根据你想要通信的所有系统来定制你的消息。

---

## 四个“A”

XCM对底层环境有以下假设。

<pba-flex center>

- **无关性（Agnostic）**
- **绝对性（Absolute）**
- **异步性（Asynchronous）**
- **非对称性（Asymmetric）**

Notes:

这四个“A”是XCM对传输协议以及这些消息发送和处理的整体环境所做的假设。

---v

## 无关性

XCM对消息传递所涉及的共识系统的性质不做任何假设。

Notes:

XCM并不局限于Polkadot ，它是一种可以用于任何系统之间通信的语言。
例如，以太坊虚拟机（EVM）链或Cosmos枢纽。

---v

## 绝对性

XCM假设环境能够保证消息的传递、解释和排序。

Notes:

消息格式对消息可能无法传递的情况并没有太多的处理。
例如，在跨区块链通信协议（IBC）中，你需要将传输协议的不可靠性考虑到你的消息中。

---v

## 异步性

跨单个共识系统边界的XCM消息通常不能是同步的。

XCM绝不是假设发送方会阻塞等待消息。

Notes:

你不能在一个区块执行过程中阻塞执行，它必须是异步的。
不同的系统有不同的时间跟踪方式。
对发送方/接收方没有阻塞的假设。
一般来说，共识系统并不是设计用来与外部系统同步运行的。
它们本质上需要有一个统一的状态来进行推理，并且默认情况下没有办法验证其他共识系统的状态。
因此，每个共识系统都不能对交付结果所需的预期时间做出任何保证；随意这样做会导致接收方阻塞等待可能延迟或永远不会交付的响应，而其中一个可能的原因是即将进行的运行时升级，这会导致响应交付方式的改变。

---v

## 非对称性

XCM不假设会有消息在相反方向流动。

如果你想发送响应，你必须明确地进行操作。

Notes:

没有结果或回调。
任何结果都必须通过额外的消息单独传达给发送方。
接收方可以并且确实会处理错误，但发送方不会收到通知，除非错误处理程序专门尝试发送一个XCM消息，该消息会以某种方式将状态通知回原始发送方，但这样的操作应该被视为为了报告信息而专门构建的一个单独的XCM消息，而不是XCM内置的固有功能。
XCM有点像表示性状态转移（REST）。
XCMP有点像传输控制协议/互联网协议（TCP/IP），但并不完全一样。
类比往往弊大于利。

---

## 📍 XCM中的位置

<pba-flex center>

在向另一个系统发送消息之前，我们需要一种方法来寻址它。

<diagram class="mermaid">
graph LR
    消息(消息)
    爱丽丝(爱丽丝)--"?"-->鲍勃(鲍勃)
    爱丽丝--"?"-->资产中心(资产中心)
    爱丽丝--"?"-->模块(模块)
    爱丽丝--"?"-->智能合约(智能合约)
</diagram>

Notes:

XCM定义了一个`Location`类型，它充当共识系统的统一资源定位符（URL）。
`Location`类型标识共识世界中存在的任何单个**位置**。
例如，代表一个可扩展的多分片区块链（如Polkadot ）、平行链上的一个ERC - 20资产账户、某条链上的一个智能合约等。
它通常表示为相对于当前共识系统的一个**位置**。
相对位置更容易处理，因为网络结构可能会发生变化。
位置并不定义到达那里的实际路径，只是一种寻址方式。

---v

## 内部位置

> 给定两个共识系统A和B。如果A中的状态变化意味着B中的状态变化，则A是B的**内部**系统。

Notes:

例如，以太坊中的一个智能合约就是以太坊本身的内部系统。

---v

## 位置层次结构

<diagram class="mermaid">
graph TD;
    中继链(中继链)-->A(平行链A)
    中继链-->B(平行链B)
    B-->爱丽丝(账户A)
    B-->鲍勃(账户B)
    A-->模块(合约模块)
    模块-->SCA(智能合约A)
    模块-->SCB(智能合约B)
</diagram>

Notes:

位置使用内部关系形成一个层次结构。

---v

## 位置表示

<pba-flex center>

```rust
struct Location {
    parents: u8,
    junctions: Junctions,
}
```

<div style="margin-bottom: 2rem;"></div>

```rust
enum Junction {
    Parachain(u32),
    AccountId32 { id: [u8; 32], network: Option<NetworkId> },
    PalletInstance(u8),
    GeneralIndex(u128),
    GlobalConsensus(NetworkId),
    ...
}
```

Notes:

目前由于栈空间的限制，`Junctions`最多为8个。
我们也不期望`Junctions`的深度超过8层。
完全有可能创建指向任何地方的位置。

---v

### 网络ID

<pba-flex center>

```rust
enum NetworkId {
    ByGenesis([u8; 32]),
    ByFork { block_number: u64, block_hash: [u8; 32] },
    Polkadot,
    Kusama,
    Westend,
    Rococo,
    Wococo,
    Ethereum { chain_id: u64 },
    BitcoinCore,
    BitcoinCash,
}
```

</pba-col>

</pba-cols>

Notes:

`Junctions`是一种向下遍历位置层次结构的方式。

---v

## 文本表示法

<pba-flex center>

<pba-cols>

<pba-col>

```rust
Location {
    parents: 1,
    interior: Parachain(50)
}
```

</pba-col>
<pba-col>

-->

</pba-col>
<pba-col>

`../Parachain(50)`

</pba-col>

Notes:

这种表示法源于与文件系统的类比。

---v

## 通用位置

> 通用位置是一个**理论上**的位置。它是所有生成自己共识的位置的父位置。它本身没有父位置。

---v

## 通用位置

<diagram class="mermaid limit size-50">
graph TD;
    通用位置(通用位置)-->Polkadot (Polkadot )
    通用位置-->Kusama (Kusama )
    通用位置-->以太坊(以太坊)
    通用位置-->比特币(比特币)
</diagram>

Notes:
我们可以想象一个包含所有顶级共识系统的假设位置。

---v

## 绝对位置
<pba-flex center>
```rust
pub type InteriorLocation = Junctions;
```
有时，绝对位置是必要的，例如在跨链桥的场景中。

它们没有父位置。

第一个连接点（junction）必须是`GlobalConsensus`。

Notes:

要编写一个绝对位置，我们需要知道相对于通用位置的自身位置。
---v

## 位置（Locations）的用途
<pba-flex center>
- 寻址
- 确定来源
- 标识资产
- 处理费用
- 跨链桥相关操作
---v

## 跨链来源
当接收方收到一条XCM消息时，一个`Location`会指定发送方。

这个`Location`是相对于接收方而言的。

在FRAME运行时中，它可以转换为一个模块来源（pallet origin）。

用于在XCM执行期间确定权限。

Notes:

重新锚定（Reanchoring）：

由于`Location`是相对的，当一条XCM消息被发送到另一条链时，在将XCM消息发送给它之前，需要从接收方的角度重写来源位置。
---

## 位置示例
---v

### 兄弟平行链
`../Parachain(1001)`
<diagram class="mermaid">
graph TD
    Polkadot (Polkadot)-->资产中心("📍 资产中心 (1000)")
    Polkadot -->集合体("集合体 (1001)")
</diagram>
Notes:

如果在平行链(1000)上评估，这个位置会解析到哪里？
---v

### 兄弟平行链
`../Parachain(1001)`
<diagram class="mermaid">
graph TD
    Polkadot (Polkadot)-->资产中心("📍 资产中心 (1000)")
    Polkadot -->集合体("集合体 (1001)")
    资产中心-->Polkadot 
    linkStyle 0 opacity:0.3
    linkStyle 2 stroke-dasharray:5
</diagram>
---v

### 平行链账户
`Parachain(1000)/AccountId32(0x1234...cdef)`
<diagram class="mermaid">
graph TD
    Polkadot ("📍 Polkadot ")-->资产中心("资产中心 (1000)")
    Polkadot -->集合体("集合体 (1001)")
    资产中心-->账户("AccountId32 (0x1234...cdef)")
</diagram>
Notes:

如果在中继链上评估，这个位置会解析到哪里？
---v

### 平行链账户
`Parachain(1000)/AccountId32(0x1234...cdef)`
<diagram class="mermaid">
graph TD
    Polkadot ("📍 Polkadot ")-->资产中心("资产中心 (1000)")
    Polkadot -->集合体("集合体 (1001)"):::disabled
    资产中心-->账户("AccountId32 (0x1234...cdef)")
    linkStyle 1 opacity:0.3
    classDef disabled opacity:0.3
</diagram>
---v

### 跨链桥
`../../GlobalConsensus(Kusama)/Parachain(1000)`
<diagram class="mermaid">
graph TD
    宇宙(通用位置)-->Polkadot (Polkadot)
    宇宙-->Kusama (Kusama)
    Polkadot -->Polkadot A("📍 资产中心 (1000)")
    Polkadot -->Polkadot B(跨链桥枢纽)
    Polkadot A-->爱丽丝(Alice)
    Polkadot A-->资产模块(Pallet Assets)
    资产模块-->资产(USDT)
    Kusama -->Kusama A("资产中心 (1000)")
    Kusama -->Kusama B(跨链桥枢纽)
</diagram>
Notes:

举例说明一个非平行链的多位置场景，该场景会用到跨链桥。
XCM在处理寻址（就像邮政地址一样）时，必须包括对自身所在位置的理解，而不仅仅是目标位置！
这在后面（确定来源相关内容）会非常有用。
---v

### 跨链桥
`../../GlobalConsensus(Kusama)/Parachain(1000)`
<diagram class="mermaid">
graph TD
    宇宙(通用位置)-->Polkadot (Polkadot)
    宇宙-->Kusama (Kusama)
    Polkadot -->Polkadot A("📍 资产中心 (1000)")
    Polkadot -->Polkadot B(跨链桥枢纽):::disabled
    Polkadot A-->爱丽丝(Alice):::disabled
    Polkadot A-->资产模块(Pallet Assets):::disabled
    资产模块-->资产(USDT):::disabled
    Kusama -->Kusama A("资产中心 (1000)")
    Kusama -->Kusama B(跨链桥枢纽):::disabled
    Polkadot A-->Polkadot 
    Polkadot -->宇宙
    linkStyle 0 opacity:0.3
    linkStyle 2 opacity:0.3
    linkStyle 3 opacity:0.3
    linkStyle 4 opacity:0.3
    linkStyle 5 opacity:0.3
    linkStyle 6 opacity:0.3
    linkStyle 8 opacity:0.3
    linkStyle 9 stroke-dasharray:5
    linkStyle 10 stroke-dasharray:5
    classDef disabled opacity:0.3
</diagram>
Notes:

即使有跨链桥枢纽，相对位置也符合预期。
跨链桥枢纽只是消息路由的一种方式。
它们是传输层的实现细节。
---v

### 跨链桥（实际路由）
<diagram class="mermaid">
graph TD
    宇宙(通用位置):::disabled-->Polkadot (Polkadot):::disabled
    宇宙-->Kusama (Kusama)
    Polkadot -->Polkadot A("📍 资产中心 (1000)")
    Polkadot -->Polkadot B(跨链桥枢纽)
    Polkadot A-->爱丽丝(Alice):::disabled
    Polkadot A-->资产模块(Pallet Assets):::disabled
    资产模块-->资产(USDT):::disabled
    Kusama -->Kusama B(跨链桥枢纽)
    Kusama -->Kusama A("资产中心 (1000)")
    Polkadot A-->Polkadot B
    Polkadot B--"跨链桥"-->Kusama B
    Kusama B-->Kusama 
    linkStyle 0 opacity:0.3
    linkStyle 1 opacity:0.3
    linkStyle 2 opacity:0.3
    linkStyle 3 opacity:0.3
    linkStyle 4 opacity:0.3
    linkStyle 5 opacity:0.3
    linkStyle 6 opacity:0.3
    linkStyle 7 opacity:0.3
    linkStyle 11 stroke-dasharray:5
    classDef disabled opacity:0.3
</diagram>
Notes:

实际消息通过跨链桥枢纽进行路由。
---

## 主权账户
本地系统外部的位置可以由一个本地账户来表示。

我们将这个账户称为该位置的**主权账户**。

它们是从一个`Location`到一个账户ID的映射。
<diagram class="mermaid">
graph TD
    Polkadot (Polkadot)-->A(A) & B(B)
    A-->爱丽丝(Alice)
    B-->爱丽丝的主权账户("Alice's sovereign account")
</diagram>
Notes:

主权账户是一个系统上的账户，由另一个不同系统上的账户控制。
一个系统上的单个账户可以在许多其他系统上拥有多个主权账户。
在这个例子中，爱丽丝是资产中心上的一个账户，它控制着集合体上的一个主权账户。

在共识系统之间进行转账时，主权账户是在目标系统上接收资金的账户。
---v

## 再谈主权账户
<diagram class="mermaid">
graph TD
    Polkadot (Polkadot)-->A(A) & B(B)
    A-->爱丽丝(Alice)
    B-->爱丽丝的主权账户("Alice's sovereign account")
    B-->资产中心的主权账户("Asset Hub's sovereign account")
    A-->集合体的主权账户("Collective's sovereign account")
</diagram>
---

<pba-col>
## 💰 XCM中的资产
大多数消息都会以某种方式涉及资产。

我们如何对这些资产进行寻址呢？
---v

### 资产表示
```rust
struct Asset {
    pub id: AssetId,
    pub fun: Fungibility,
}

struct AssetId(Location); // <- 我们复用了位置！

enum Fungibility {
    Fungible(u128),
    NonFungible(AssetInstance),
}
```
Notes:

我们使用之前讨论过的位置来指代资产。

一个资产由一个资产ID和一个表示资产可替代性的枚举组成。
资产ID是指向发行该资产的系统的位置，例如，它可以只是资产模块中的一个索引。

资产也可以分为可替代和不可替代的：
可替代的 - 这种资产的每个代币与其他任何代币具有相同的价值
不可替代的 - 这种资产的每个代币都是唯一的，不能被视为与该资产下的任何其他代币具有相同的价值
---v

### 资产过滤和通配符
```rust
enum AssetFilter {
    Definite(Assets),
    Wild(WildAsset),
}

enum WildAsset {
    All,
    AllOf { id: AssetId, fun: WildFungibility },
    // 计数变体
}

enum WildFungibility {
    Fungible,
    NonFungible,
}
```
Notes:

有时我们不想指定具体的资产，而是想过滤一组资产。
在这种情况下，我们可以列出所有想要的资产，或者使用通配符来选择所有资产。
实际上，出于基准测试的目的，最好使用通配符的计数变体。
---

## 重新锚定
不同的位置如何引用同一资产呢？
<diagram class="mermaid limit size-70">
graph TD
    Polkadot (Polkadot)-->资产中心("资产中心 (1000)")
    Polkadot -->跨链桥枢纽("跨链桥枢纽 (1002)")
    资产中心-->爱丽丝(Alice)
    资产中心-->资产模块(Pallet Assets)
    资产模块-->资产(USDT)
</diagram>
Notes:

位置是相对的，因此当它们被发送到另一条链时，必须进行更新和重写，以便能够正确解释。

<!-- FIXME TODO: 移到其他地方。 -->

原生代币通过指向其所在系统的位置来引用。
---v

### 资产中心的USDT
`PalletInstance(50)/GeneralIndex(1984)`
<diagram class="mermaid limit size-70">
graph TD
    Polkadot (Polkadot):::disabled-->资产中心("📍 资产中心 (1000)")
    Polkadot -->跨链桥枢纽("跨链桥枢纽 (1002)"):::disabled
    资产中心-->爱丽丝(Alice):::disabled
    资产中心-->资产模块(Pallet Assets)
    资产模块-->资产(USDT)
    linkStyle 0 opacity:0.3
    linkStyle 1 opacity:0.3
    linkStyle 2 opacity:0.3
    classDef disabled opacity:0.3
</diagram>
---v

### 跨链桥枢纽的USDT
`../Parachain(1000)/PalletInstance(50)/GeneralIndex(1984)`
<diagram class="mermaid limit size-70">
graph TD
    Polkadot (Polkadot)-->资产中心("资产中心 (1000)")
    Polkadot -->跨链桥枢纽("📍 跨链桥枢纽 (1002)")
    资产中心-->爱丽丝(Alice):::disabled
    资产中心-->资产模块(Pallet Assets)
    资产模块-->资产(USDT)
    跨链桥枢纽-->Polkadot 
    linkStyle 1 opacity:0.3
    linkStyle 2 opacity:0.3
    linkStyle 5 stroke-dasharray:5
    classDef disabled opacity:0.3
</diagram>
---v

### 重新锚定来帮忙
<diagram class="mermaid">
graph LR
    subgraph 出站消息[跨链桥枢纽发出的出站消息]
        跨链桥枢纽视角的USDT(USDT from Bridge Hub's perspective)
    end
    跨链桥枢纽视角的USDT--重新锚定-->资产中心视角的USDT
    subgraph 入站消息[资产中心收到的入站消息]
        资产中心视角的USDT(USDT from Asset Hub's perspective)
    end
</diagram>
---

## 🤹 跨共识转移
Notes:

在共识系统之间转移资产有两种方式：跨链传送（teleports）和储备转移（reserve transfers）。
---v

### 1. 资产跨链传送
<img rounded style="width: 500px;" src="./img/teleport.png" />
Notes:

跨链传送的工作原理是在源链上销毁资产，然后在目标链上铸造这些资产。
这种方法是最简单的，但需要高度的信任，因为任何一方未能销毁或铸造资产都会影响总发行量。
---v

### 1.1. 示例：系统平行链之间？
<diagram class="mermaid">
graph LR
    跨链桥枢纽(Bridge Hub)--"信任"-->资产中心(Asset Hub)
</diagram>
---v

### 1.2. 示例：Polkadot 和Kusama 之间？
<diagram class="mermaid">
graph LR
    Polkadot (Polkadot)--"不信任"-->Kusama (Kusama)
</diagram>
---v

### 2. 储备资产转移
<img rounded style="width: 400px;" src="./img/reserve-tx.png" />
Notes:

储备资产转移更为复杂，因为它们引入了一个称为储备链的第三方参与者。
链A和链B无需相互信任，它们只需要信任储备链。
储备链持有真正的资产，链A和链B只处理衍生资产。
转移过程是通过在链A上销毁衍生资产，将其从链A的主权账户转移到储备链中链B的主权账户，然后在链B上铸造资产来完成的。

在某些情况下，发送方链A也可以是特定资产的储备链，在这种情况下，过程会简化，无需销毁衍生资产。
这种情况通常发生在平行链的原生代币上。

你总是会信任代币的发行方不会铸造无限数量的代币。
---v

### 2.1. 示例：平行链原生代币
<diagram class="mermaid">
graph LR
    subgraph A [A = R]
        发送方(Sender account)--"转移X个真实资产"-->B的主权账户(B's Sovereign Account)
    end
    A--"铸造X个衍生资产"-->B(B)
</diagram>
Notes:

大多数平行链充当其自身代币的储备链。
为了将其代币转移到其他链上，它们将真实资产转移到一个主权账户，然后通知目标链铸造等量的衍生资产。
---v

### 2.2. 示例：从Polkadot 到Kusama 
<diagram class="mermaid">
graph LR
    Polkadot (Polkadot)-->Polkadot 资产中心
    subgraph Polkadot 资产中心 [Polkadot 的资产中心]
        发送方(Sender account)--"转移X个真实DOT"-->Kusama 主权账户("Kusama's sovereign account")
    end
    Polkadot 资产中心--"铸造X个DOT衍生资产"-->Kusama (Kusama)
</diagram>
Notes:

Kusama 资产中心充当KSM的储备链。
Kusama 不信任Polkadot 将KSM跨链传送给它，但它信任自己的储备链，即资产中心。
Polkadot 在Kusama 的资产中心有一个主权账户，其中存有一定数量的KSM。
每当Polkadot 上的某个用户想要在Kusama 上获得KSM时，他们只需将DOT交给Polkadot ，KSM就会从一个主权账户转移到另一个主权账户。
不会添加新的信任关系。
---

## 总结
- XCM
- XCM与XCMP的对比
- 位置
- 主权账户
- 资产
- 重新锚定
- 跨共识转移
  - 跨链传送
  - 储备资产转移
---v

## 下一步
<pba-flex center>
1. 介绍XCM的博客系列文章：[第1部分](https://medium.com/polkadot-network/xcm-the-cross-consensus-message-format-3b77b1373392)、[第2部分](https://medium.com/polkadot-network/xcm-part-ii-versioning-and-compatibility-b313fc257b83)和[第3部分](https://medium.com/polkadot-network/xcm-part-iii-execution-and-error-management-ceb8155dd166)。
2. XCM格式[代码库](https://github.com/
3. XCM [Docs](https://paritytech.github.io/xcm-docs/)

---v

<figure>
    <img rounded style="width: 50%;" src="./img/subscan-xcm-dashboard.png" />
    <figcaption>Source: <a href="https://polkadot.subscan.io/xcm_dashboard">Subscan</a></figcaption>
</figure>