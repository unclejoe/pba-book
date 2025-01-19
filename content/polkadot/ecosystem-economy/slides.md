---
title: Polkadot Ecosystem and Economy
description: A review of the parts of Polkadot which develop its ecosystem and economy.
duration: 1 hour
---

# 生态系统与经济

---

## 生态系统与经济

本次演讲将为你提供 Polkadot 网络生态系统和经济的高级概述。

不幸的是，本次演讲不可能涵盖所有内容，但或许它能为你揭示一些此前未知的领域。

---

# 经济

---

## DOT代币

<div class="grid grid-cols-2">

<div>

<img src="./img/token.avif" style="width: 400px;">

</div>

<div class="text-left">

DOT代币可以处于以下状态之一：

1. 可转移
2. 锁定（冻结）
3. 预留（持有）

</div>

</div>

---

## 预留余额与锁定余额

- 新术语“冻结”和“持有”在 Polkadot 中尚未广泛使用……
- 这两种状态下的余额都属于用户……但不能用于支出或转移。
- 预留余额可以叠加。
  - 这对于用户存款或存在女巫攻击风险的其他用例很有用。
  - 例如：在链上存储数据的押金。
- 锁定余额可以重叠。
  - 当你想将相同的代币用于多个用例时，这很有用。
  - 例如：将相同的代币同时用于质押和治理投票。

---

## 存储膨胀

区块链扩容的一个问题是随着时间推移的存储膨胀。

<br />

考虑在以太坊上存储数据的“成本”：

- 基于存储数据量的一次性gas费用。
- 一旦数据被放置在网络上，它就会永远存在，不再产生额外费用。
- 在足够长的时间后，单位时间的存储成本将降至零。

---

## 存储押金

为了解决这个问题， Polkadot 对于存储在区块链中的任何数据都额外收取存储押金（以预留余额的形式）。

- 当用户从链上移除数据时，这笔押金将退还给用户。

- 这笔押金可能相当高，因为它会退还给用户，这也体现了数据的临时性或“不重要性”。

---

## 粉尘账户与存在性押金

大多数区块链中最占用存储空间的是用户账户：

- 以太坊和比特币都充斥着“粉尘账户”，这些账户的余额非常小，不值得“清理”。

-  Polkadot 通过设置“存在性押金”来解决这个问题，所有用户必须持有最低数量的DOT，否则他们的账户数据将被清理。

- 存在性押金可以被视为账户数据的存储押金。

---

## DOT是一种实用型代币

<div class="grid grid-cols-3">

<div>

<img src="./img/utility.avif" style="width: 500px;">

</div>

<div class="text-left col-span-2">

DOT代币有多种用途，以帮助 Polkadot 网络正常运行：

- 质押
- 为平行链插槽/执行核心提供抵押
- 链上决策
- 用于交易/使用的价值承载

</div>
</div>

---

## DOT代币的理想用途

<img src="./img/ideal-token-distribution.svg" style="width: 1000px;">

大致比例如下：

Notes:

- 50% 用于质押/治理
- 30% 用于平行链
- 20% 可用于交易/使用

---

## DOT通胀

<div class="grid grid-cols-2">

<div>

<img src="./img/inflation.svg" style="width: 500px;">

</div>

<div class="text-left">

DOT目前的配置是每年固定10%的通胀率。

新铸造的代币会分发给质押者（验证者/提名者）和国库。

</div>

</div>

---

## 理想质押率

我们不能强迫或告诉用户如何使用他们的代币，因此我们通过将DOT代币的使用与通胀分配方式相关联来鼓励“理想”行为。

当`理想质押率 != 实际质押率`时，有一个函数会将10%通胀中的一部分重新分配到国库，而不是质押者手中。

代币持有者在经济上有动力最大化他们的质押回报，从而合理分配他们的代币。

---

## DOT通胀与质押

<img src="./img/staking-rate.png" style="width: 900px;">

> 蓝色：通胀率与质押率的关系
>
> 绿色：质押者的年化收益率与质押率的关系
>
> 黑色：总通胀率与质押率的关系

---

## DOT的实用价值：平行链

 Polkadot 提供了许多实用功能，但可以说其最重要的实用功能是提供灵活、安全且可扩展的区块空间。

开发者可以使用DOT代币购买固定期限或按需使用的平行链来获取这些区块空间。

<br />

> 如果你认为灵活且安全的区块空间有价值，那么你就会认同DOT也有价值。

---

## 预计平行链成本

简单估算：

- 约10亿DOT
- 30% 用于平行链锁定 = 3亿
- 约100条平行链 = 每个平行链插槽300万DOT

在达到平衡状态时……

---

## 平行链经济机制更新

关于更新平行链的经济机制有很多讨论。

很可能这些机制会很快更新，并且会随着时间不断调整。

---

## DOT的实用价值：质押

<div class="grid grid-cols-3">

<div class="col-span-2 text-left">

鉴于存在一种具有价值承载功能的代币，它可以用于为 Polkadot 提供安全性：

- 如果用户想要为网络提供安全性，他们可以质押自己的代币。

- 质押者会因良好行为获得奖励，因不良行为受到惩罚。

- 惩罚措施足够严厉，以至于理性的参与者不会采取恶意行为。

<https://www.polkadot.network/features/staking/>

</div>

<div>

<img src="./img/staking.svg" style="width: 400px;">

</div>

</div>

---

## 质押：验证者和提名者

<div class="grid grid-cols-3">

<div class="col-span-2 text-left">

在质押系统中，有两个角色：

- 验证者：那些为 Polkadot 运行区块生产/平行链验证节点的人。
- 提名者：将自己的代币支持给他们认为能够出色完成工作的验证者的用户。

验证者（及其提名者）会根据为网络所做的工作获得奖励。奖励可能每天都有所不同，但在较长时间内应该是稳定的。

</div>

<div>

<img src="./img/collab.svg" style="width: 400px;">

</div>

</div>

---

## DOT的实用价值：治理

<div class="grid grid-cols-3">

<div class="col-span-2 text-left">

 Polkadot 的未来由代币持有者决定。

 Polkadot 有一个名为OpenGov的链上治理系统，用于：

- 支出国库资金
- 升级网络
- 管理研究员团队
- 支持平行链团队
- 等等……

<https://www.polkadot.network/features/opengov/>

</div>

<div>

<img src="./img/governance.avif" style="width: 500px;">

</div>

</div>

---

## 信念投票

 Polkadot 采用了一种名为自愿锁定/信念投票的机制。

这允许代币持有者通过更长时间地锁定他们的代币来增加他们的投票权。

```text
投票数 = 代币数 * 信念乘数
```

信念乘数会在锁定周期数每次翻倍时将投票乘数增加1。

<div class="text-small">

| 锁定周期数 | 投票乘数 | 时长（天） |
| ------------ | --------------- | -------------- |
| 0            | 0.1             | 0              |
| 1            | 1               | 7              |
| 2            | 2               | 14             |
| 4            | 3               | 28             |
| 8            | 4               | 56             |
| 16           | 5               | 112            |
| 32           | 6               | 224            |

</div>

---

## 投票轨道

OpenGov系统有不同的投票轨道，它们具有不同的权力级别，并且通过的难度也成比例不同。

以下是目前15个轨道中的一部分：

| ID  |       来源       | 决策押金 | 准备期 | 决策期 | 确认期 | 最短生效期 |
| :-: | :----------------: | :--------------: | :------------: | :-------------: | :------------: | :------------------: |
|  0  |        根         |    100000 DOT    |    2小时     |     28天     |     1天      |        1天         |
|  1  | 白名单调用者 |    10000 DOT     |   30分钟   |     28天     |   10分钟   |      10分钟      |
| 10  |   质押管理员    |     5000 DOT     |    2小时     |     28天     |    3小时     |      10分钟      |
| 11  |     财务主管      |     1000 DOT     |    2小时     |     28天     |    3小时     |        1天         |
| 12  |    租赁管理员     |     5000 DOT     |    2小时     |     28天     |    3小时     |      10分钟      |

---

## 批准和支持曲线

每个轨道都有自己的一组曲线，用于确定提案是通过还是失败。

所有投票最终都会以某种方式得到解决。

<img src="./img/pjs-curves.png" style="width: 1000px;">

你可以在 Polkadot JS开发者控制台中找到这些曲线。

---

## 示例：根

具有最高特权级别的来源。要想在早期通过提案，需要极高的批准率和支持率。准备期和生效期也较长。

<img src="./img/root-curve.png" style="width: 800px;">

例如，在这个轨道上提出的公投提案需要在第一天结束时获得48.2%的支持率（全网发行量），且批准率超过93.5%，才能进入确认期。

---

## 治理代币机制

- 当你对提案进行投票时，DOT代币会被锁定。
- 你可以在多个提案中重复使用锁定的代币。
  - 对一个提案进行投票不会影响你对其他提案的投票能力。
- 你还可以重复使用质押的代币（这些代币也只是被锁定）。
- 在提案进行期间，你可以更新你的投票。
- 如果你使用了信念投票，你的代币可能会在提案结束后被锁定很长时间。

---

## 国库

 Polkadot 有一个链上国库，用于支持网络的无许可和去中心化开发。

国库的资金来源包括通胀曲线的低效部分、惩罚扣除以及80%的交易费用。

国库会在每个支出周期（24天）自动销毁其资金的1%，这会促使资金的使用。

---

## 国库支出渠道

- 提案：经治理批准后向个人的即时支付。
- 赏金：由治理和指定的赏金管理员管理的分阶段向个人支付。
- 小费：可以通过特定治理轨道更轻松地向个人支付的小额款项。

 Polkadot 国库目前拥有超过45,000,000 DOT。

---

# 生态系统

---

##  Polkadot 的替代客户端

 Polkadot 的主要客户端是使用Rust语言在Substrate上构建的。

然而，其他 Polkadot 客户端正在开发中：

- Kagome (C++17)：<https://github.com/qdrvm/kagome>
- Gossamer (Go)：<https://github.com/ChainSafe/gossamer>

随着时间的推移，这可以帮助网络从软件漏洞中获得额外的弹性。

---

## 平行链的类型

-  Polkadot 系统链
- 平行链的购买市场

<br />

一旦实施更灵活的核心分配系统，这个列表可能会进一步扩展。

---

## 系统链

- 系统平行链包含 Polkadot 核心协议功能，这些功能位于平行链而非中继链上。
-  Polkadot 使用自己的并行执行扩展技术来实现自身的扩展。
- 系统平行链将交易从中继链中移除，从而使更多的中继链区块空间可用于 Polkadot 的主要目的：验证平行链。
- 系统链由治理机构分配。

Notes:

<https://wiki.polkadot.network/docs/learn-system-chains>

---

## 当前和未来的系统链

当前：

- 资产中心：允许创建和注册代币（FT和NFT）。
- 集体：作为 Polkadot 去中心化自治组织（DAO）的协调场所。
- 桥接中心：用于管理与其他网络的桥接的链。
- Encointer：第三方构建的提供身份验证的链。

未来：

- 质押：管理所有验证者和提名者的逻辑、奖励等……
- 治理：管理所有各种提案和轨道。
- 最终会涵盖所有方面……

Notes:
<https://wiki.polkadot.network/docs/learn-system-chains>

---

## 平行链的购买市场
<div class="grid grid-cols-2">
  <div>
    任何有好想法且拥有DOT代币的人，都可以在 Polkadot 上启动一条平行链。
    全球已有数十个团队这么做了，他们正在利用 Polkadot 提供的各种功能。
  </div>
  <div>
    <img src="./img/polkadot-parachains.svg" style="width: 500px;">
  </div>
</div>
Notes:
<https://polkadot.subscan.io/parachain>

---

## 生态系统垂直领域
虽然这个列表并不详尽，但我们在 Polkadot 生态中看到的一些垂直领域包括：
<div class="grid grid-cols-5">
  <div class="col-span-3">
    - 智能合约链
    - 去中心化金融（DeFi）
    - 去中心化社交（DeSo）
    - 去中心化身份（DID）服务
    - 通证化（现实世界资产）
  </div>
  <div class="col-span-2">
    - 游戏
    - 非同质化代币（NFT，如音乐、艺术等领域）
    - 跨链桥
    - 文件存储
    - 隐私保护
  </div>
</div>
Notes:
<https://substrate.io/ecosystem/projects/>

---

## 钱包
得益于 Polkadot 财政部和社区，生态系统中开发出了许多不同的钱包。
<div class="text-small">
| 钱包名称 | 支持平台 | 质押和提名池 | NFT支持 | 众贷支持 | Ledger硬件钱包支持 | 治理支持 |
| :-------------: | :-----------------------------------------: | :--------------------------: | :--: | :--------: | :------------: | :--------: |
| Enkrypt | Brave、Chrome、Edge、Firefox、Opera、Safari | 否，否 | 是 | 否 | 是 | 否 |
| PolkaGate | Brave、Chrome、Firefox、Edge | 是，是 | 否 | 是 | 是 | 是 |
| SubWallet | Brave、Chrome、Edge、Firefox、iOS、Android | 是，是 | 是 | 是 | 是 | 否 |
| Talisman | Brave、Chrome、Edge、Firefox | 是，是 | 是 | 是 | 是 | 否 |
| Fearless Wallet | Brave、Chrome、iOS、Android | 是，是 | 否 | 否 | 否 | 否 |
| Nova Wallet | iOS、Android | 是，是 | 是 | 是 | 是 | 是 |
| Polkawallet | iOS、Android | 是，是 | 否 | 是 | 否 | 是 |
</div>
<!-- FIXME TODO确保信息更新！考虑使用图标或其他图片让表格更好看 -->
Notes:

---

## 带有元数据的Ledger支持
<div class="grid grid-cols-3">
  <div class="col-span-2">
     Polkadot 一直在与Ledger合作，为 Polkadot 网络提供丰富的支持。
    用户可以清晰地查看他们正在签署的交易，并执行诸如批量操作、多重签名、质押、参与治理等复杂任务。
  </div>
  <div>
    <img src="./img/ledger.webp" style="width: 500px;">
  </div>
</div>

---

## 区块浏览器
- Polkadot-JS Apps Explorer -  Polkadot 仪表盘式的区块浏览器。支持数十个其他网络，包括Kusama、Westend以及其他远程或本地端点。
- Polkascan - 用于 Polkadot 、Kusama及其他相关链的区块链浏览器。
- Subscan - 用于Substrate链的区块链浏览器。
- DotScanner -  Polkadot 和Kusama区块链浏览器。
- 3xpl.com - 最快的无广告通用区块浏览器和支持 Polkadot 的JSON API。
- Blockchair.com - 支持 Polkadot 的通用区块链浏览器和搜索引擎。
- Polkaholic.io - 支持 Polkadot 和Kusama的区块链浏览器，提供API和对40多条平行链的DeFi支持。
Notes:
<https://wiki.polkadot.network/docs/build-tools-index#block-explorers>

---

## 治理仪表盘
目前最受欢迎的有：
<div class="grid grid-cols-2">
  <div>
    ### Polkassembly
    <img src="./img/polkassembly.png" style="width: 600px;">
  </div>
  <div>
    ### Subsquare
    <img src="./img/subsquare.png" style="width: 600px;">
  </div>
</div>

---

##  Polkadot 论坛
<img src="./img/forum.png" style="width: 1200px;">
Notes:
<https://forum.polkadot.network/>

---

##  Polkadot 开发者社区（Fellowship）
 Polkadot 开发者社区是 Polkadot 网络上的一个去中心化技术团体，旨在认可、培养并激励 Polkadot 核心协议的贡献者。

---

## 开发者社区宣言
<img src="./img/fellowship-manifesto.png" style="width: 1200px;">
Notes:
<https://github.com/polkadot-fellows>

---

## 开发者社区成员
<img src="./img/fellowship-members.png" style="width: 1200px;">
Notes:
<https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fkusama-rpc.polkadot.io#/fellowship>

---

## 请求评论（RFCs）
<img src="./img/rfcs.png" style="width: 1200px;">

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题

有遗漏什么内容吗？ 