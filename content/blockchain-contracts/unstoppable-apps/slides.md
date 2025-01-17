---
title: Unstoppable Applications
description: Unstoppable Applications in web3
duration: 1 hour
---

# 不停机的应用程序

Notes:

很像代币经济学设计，这是融入加密货币或其他激励因素的不停机应用程序的一个重要组成部分，这节课的时间远远不够，无法为你提供所有工具和技术来设计一个强大的去中心化应用程序（DApp）。

相反，我们致力于突出我们所面临的问题领域以及一些解决方案。

---

## 动机

到目前为止，我们已经孤立地讨论了状态机和共识……

它们运行的上下文重要吗？

Notes:

- 到目前为止，大多是关于简化的、理想化的系统。
  - 加密学的“黑匣子”
  - 理性行为者以及经济学中假定的完整行为模型
  - 区块链在某种程度上是一个“[孤立系统](https://en.wikipedia.org/wiki/Isolated_system)”——外部系统无法以同样的方式进行推理……
    我们将讨论预言机问题。
- 在实践中，有更多的“[未知的未知](https://en.wikipedia.org/wiki/Black_swan_theory)”和“黑天鹅”行为。
  本节课将对此进行更多讨论。

---

<!-- .slide: data-background-color="#4A2439" -->

## 讨论

> 系统的哪些特性使其“可停止”？

Notes:

- Web2 背景：中心化的提供商和权威机构，……
- Web3 背景：去中心化，……
- 系统的哪些特性使其“可停止”？

---

### 不停机的应用程序的特性

<pba-flex center>

- 反脆弱性
- 无需信任*
- 无审查*
- 可访问性*
- ……也许还有更多？

</pba-flex>

Notes:

“*”表示在定义特性时的 Web3 背景，并非普遍适用。
并非所有这些特性都适用，也不可能同时全部适用。
我们需要根据我们必须实现的目标来设计系统特性。
实际上，我们追求绝对的不停机性，但在每一种可能的情况下可能都无法保证。

---

## 反脆弱性

<pba-cols>
<pba-col>
<div style="font-size:smaller">

> 有些事物从冲击中受益；当暴露于波动性、随机性、无序性和压力源时，它们会茁壮成长，并且热爱冒险、风险和不确定性。
> 然而，尽管这种现象无处不在，但却没有一个词来准确描述脆弱的反义词。
> 让我们称之为反脆弱性。
> 反脆弱性超越了弹性或稳健性。
> 有弹性的事物能够抵御冲击并保持不变；而反脆弱的事物则会变得更好。
>
> ——《反脆弱》（[Antifragile](https://en.wikipedia.org/wiki/Antifragile_%28book%29)）

</div>

</pba-col>
<pba-col>
<img rounded style="width: 550px" src="./img/hydra-Gustave_Moreau.jpg" />
</pba-col>
</pba-cols>

Notes:

- 阅读《反脆弱》中的引文，推荐这本书，课后可查看幻灯片中的链接了解更多信息。
- 九头蛇的寓言和传说：<https://en.wikipedia.org/wiki/Lernaean_Hydra>——尽管几乎可以被完全摧毁，但它具有韧性并能够恢复。
  绝对的不停机并不意味着它不会受到损害或暂时暂停，而是意味着它不会停止存在，并且最终会恢复，理想情况下会因此变得更强大。

---

## 一个 N 引理

假设：绝对不停机的应用程序不可能存在。

我们必须在绝对不停机的应用程序所具备的所有 N 个特性中进行权衡。

<pba-cols>
<pba-col>
<img rounded style="width: 600px" src="./img/trilemma.svg" />
</pba-col>
<pba-col>
<img rounded style="width: 600px" src="./img/scalability-trilemma.svg" />
</pba-col>
</pba-cols>

Notes:

就像加密领域一样，我们可能有极高的成功率……
但这并不是完美的。
考虑到共识系统所处的环境和背景，我们希望构建尽可能强大的系统。

更相关的三难困境：

- [可扩展性](https://vitalik.ca/general/2021/04/07/sharding.html#the-scalability-trilemma)
- [佐科三角](https://en.wikipedia.org/wiki/Zooko's_triangle)（网络标识）
- 更有可能！

---

## Web3 技术栈

<img style="width: 1200px" src="./img/3.4-web3-stack.png" />

Notes:

随着该领域的发展，这个图表有点过时了，但它是一个不错的近似表示。

观察和澄清：DApp 通常指智能合约应用程序。
这些应用程序存在于共识系统的背景中，而共识系统本身继承了不停机性的特性。
学术界更关注共识系统工程——我们对区块链本身进行推理——而不是将这些区块链作为平台来运行的“DApp”。
智能合约课程可能会包含关于不停机的 DApp 设计考虑因素的详细信息。

<!-- FIXME TODO: 更新此图形，已经过时（乔在剑桥提到过） -->
<!-- FIXME TODO: 根据智能合约内容进行澄清 - 它是否为不停机的 DApp 的系统设计考虑因素提供了信息？ -->

---

## 远不止区块链架构……

<pba-cols>
<pba-col>

区块链只是技术栈的一部分。

Web3 应用程序必须在所有层防止攻击。

</pba-col>
<pba-col>

<pba-flex center>

- 网络
- 共识
- 节点访问
- 验证者权力
- 共识间信任
- 人为因素
- 外部因素

</pba-flex>
</pba-col>
</pba-cols>

Notes:

这些是今天要讨论的内容，但还有很多其他内容未列出！

---

<!-- .slide: data-background-color="#4A2439" -->

# 人的层面

---

## 攻击 Web3

<img rounded style="width: 1000px" src="./img/3.4-xkcd-security.png" />

Notes:

关键点：你“完美”的系统可能对“规则”之外的事物很脆弱！尤其是

图片来源：<https://xkcd.com/538/>

---v

## Web3 的批评

<pba-cols>
<pba-col>

对于如今许多 Web3 应用程序的运作方式，存在[合理的批评](https://moxie.org/2022/01/07/web3-first-impressions.html)。

</pba-col>
<pba-col>

<pba-flex center>

- 人是廉价且懒惰的……
  没有个人运行服务器。
- RPC 节点提供商
- 协议的改进速度比平台慢。
- 虚假营销、欺诈和骗局！

</pba-flex>
</pba-col>
</pba-cols>

Notes:

<https://moxie.org/2022/01/07/web3-first-impressions.html> 对该领域的现状进行了精彩的批评，其作者是 [Signal 信使](https://signal.org) 的创始人。

并非毫无希望！
这些批评大多在当下是合理的，我们将讨论这些问题以及我们正在构建的东西，以实现一个更好的技术栈。

<!-- TODO FIXME: 本节需要更多幻灯片！ -->

---

<!-- .slide: data-background-color="#4A2439" -->

# 系统层面

---v

## 证明它！

我们经常使用“证明”这个词……它在[不同的语境](https://en.wikipedia.org/wiki/Provable)中有很多含义：

<pba-flex center>

- 数学 → **可证明正确**（算法）
- 共识 → 某种证明（安全性）
- 加密 → [零知识 | 可验证随机函数 | 有效性 | ……] 证明

</pba-flex>

Notes:

到目前为止还未涉及的一个方面是可证明正确性——我们可以使用数学来证明我们的逻辑不会出现意外行为。
一个有趣的例子是 [Cardano 的设计价值主张](https://docs.cardano.org/explore-cardano/cardano-design-rationale/)，它使用 Haskell 语言并证明了其平台的大部分是正确的。

> 我们稍后有关于形式验证方法的课程和练习——这就是我们在 Rust 以及 Substrate 语境中实现可证明正确性的方法。

但这个属性假设了一个完整的系统模型！
Nuke 提出，当考虑共识系统之外的因素时，不可能有严格的正确性证明，因为我们无法对整个宇宙进行建模。

---v

## 🔮 预言机问题

[预言机](https://en.wikipedia.org/wiki/Category:Computation_oracles) 为共识系统提供外部数据。（即外部链的部分状态）

[预言机问题](https://chain.link/education-hub/oracle-problem) 与对预言机的信任有关。

Notes:

- 示例：随机预言机，与我们在加密模块中看到的可验证随机函数（VRF）不同，VRF 可以存在于共识系统中。
- 对于来自共识系统边界之外的任何输入，都需要预言机。
  - 链中的所有内容都是自引用的。
    共识系统中的应用程序可能想要尝试对自身之外的事物进行推理。
- 包括桥接

---v

## 🦢 黑天鹅事件

<pba-flex center>

- 已知的运行范围 _被认为_ 不可能发生的事情
- 死亡螺旋

</pba-flex>

Notes:

解释 Luna 或其他系统崩溃的例子。

- 📔《黑天鹅：高度不可能事件的影响》（[The Black Swan: The Impact of the Highly Improbable](https://en.wikipedia.org/wiki/The_Black_Swan:_The_Impact_of_the_Highly_Improbable)）
- [维基百科黑天鹅理论](https://en.wikipedia.org/wiki/Black_swan_theory)

---v

## 🤯 复杂性

<pba-cols>
<pba-col>

<img rounded style="height: 700px;" src="./img/Genealogy_map_of_topics_treated_by_Nassim_Taleb.jpg" />

</pba-col>
<pba-col>

- 展示如何映射系统之间复杂的耦合和相互作用。
- * 你不需要理解这个图 😅

</pba-col>
</pba-cols>

Notes:

- 作者的精彩演讲：<https://www.youtube.com/watch?v=S3REdLZ8Xis> 参考书籍作者的演讲。

示例：非理性行为者可以在一个非常简单的模型中被表示为完全随机的行为，或者与理性行为者相反的行为。
如果你对系统进行“模糊测试”，你可能会发现系统对非理性行为的脆弱性，而这些行为可能会破坏你的系统。
也许经历黑天鹅事件比最初看起来要容易得多，也更有可能。

- 图片[来源](https://en.wikipedia.org/wiki/Nassim_Nicholas_Taleb#/media/File:Genealogy_map_of_topics_treated_by_Nassim_Taleb.jpg) - 描述了各种不确定性、认识论限制和统计主题，涉及塔勒布的黑天鹅/反脆弱性等思想。

---v

## 👪 依赖关系

<pba-cols>
<pba-col>

<img rounded style="width: 500px" src="./img/3.4-xkcd-dependency.png" />

</pba-col>
<pba-col>

- [混淆](https://secureteam.co.uk/2021/02/24/what-is-a-dependency-confusion-attack/)
- [劫持](https://blog.sonatype.com/bladabindi-njrat-rat-in-jdb.js-npm-malware)
- [硬件侧信道攻击](https://hackaday.com/2019/09/13/side-channel-attack-shows-vulnerabilities-of-cryptocurrency-wallets/)

</pba-col>
</pba-cols>

Notes:

- 是的，在软件和硬件中，你都有受到来自被污染的依赖项攻击的风险，这些依赖项可能由于缺乏维护，甚至是有针对性的利用而存在问题。
  一种缓解措施是使用供应商提供的依赖项，需要有系统来进行监控。
  Dependabot 是不够的。
- 还有对特定操作环境的依赖。
  例如，运行节点软件是否合法。

图片来源：<https://xkcd.com/2347/>

---v

## 🦸 Polkadot 中的依赖关系

<pba-cols>
<pba-col>

<img rounded style="width: 500px" src="./img/dependency-polkadot-js.png" />

</pba-col>
<pba-col>

> 对 Polkadot 生态系统至关重要！

</pba-col>
</pba-cols>

Notes:

- [Jaco](https://github.com/jacogr) 实际上是几乎所有与 Substrate 节点通信的唯一维护者！
- [Capi](https://github.com/paritytech/capi) 正在开发中，但刚刚起步。

---v

## 🙈 未知的未知

<img rounded style="width: 800px" src="./img/DiveEdge.gif" />

Notes:

在系统本身之外，我们无法保证/证明我们的模型和系统设计考虑到了所有可能的情况。
我们必须预计到系统及其模型之外的力量可能会以意想不到的方式相互作用。
必须严格评估关于上下文的假设（例如，在这个模块或合约所在的链中，最终性意味着什么？）

---

<!-- .slide: data-background-color="#4A2439" -->
# 网络层面
---v
## 🕸️ 对等网络
<img style="width: 1000px" src="./img/3.4-network-topologies.png" />
---v
## 网络攻击
<pba-flex center>
- 入口/引导节点与节点发现
- 数据中心故障
- 流量分析与针对性攻击
- 日食攻击
</pba-flex>
Notes:
网络课程会涵盖这些内容，这里只是提醒一下，网络并不受共识系统的直接控制，所以它构成了一种威胁！
- 安全与稳定性
- 隐私！链上可能采用零知识证明（ZK），但节点间的信息传播和远程过程调用（RPC）的隐私性如何呢？
引导节点通常是“硬编码”的，用于“引导”并启动节点发现过程。引导节点可以决定宣传哪些节点，或者可能无法访问。常见的数据中心（如AWS、GCP等）可能会出现故障或进行审查，这可能导致大量节点无法使用。很难隐藏！与Web2流量相比，大多数对等网络流量很容易被识别。
---v
## 节点查询
运行一个节点难度较大，大多数人选择外包。
<img style="width: 1000px" src="./img/3.4-node-queries.png" />
这些服务可能存在欺骗、审查和监控的情况。
---v
## 多链应用
如果运行一个节点都负担沉重，试试运行多个节点。
<img style="width: 700px" src="./img/3.4-multi-chain-apps.png" />
---v
## 无需信任的消息传递
为了在无需信任的情况下处理消息，系统之间必须有共同的最终性保证。
当`B`的交易被回滚而`A`没有时，`A`不应处理来自`B`的消息。
---v
## 关于同步性的说明
在单一链（如以太坊）上的智能合约，由于对最终性有共同的认知，因此可以进行无需信任的交互。
异步系统也可以共享最终性，也就是说，它们可以属于同一个共识系统。
---v
<!-- .slide: data-background-color="#4A2439" -->
# 讨论
**最小可行去中心化**
> 应该考虑哪些关键方面？
Notes:
- 定量方面：所需节点数量（用于什么目的）、激励措施等。FIXME TODO
- 定性方面：社会规范等。FIXME TODO
---
<!-- .slide: data-background-color="#4A2439" -->
# 共识
---v
## 矿池
工作量证明的授权集合没有固定的界限。
但人们喜欢组织起来。
[合作 | 勾结] 的授权集合会带来风险。
Notes:
需要指出的是，提名池是存在的，后续的NPoS课程会讨论相关内容。虽然存在类似的问题，但提名池的情况相对更有限。
---v
## 矿池
<img rounded style="width: 1000px" src="./img/mining-pools.png" />
Notes:
来源：[Buy Bitcoin Worldwide](https://buybitcoinworldwide.com/pages/mining/pools/img/pool-graph.png)
---v
## 安全稀释
安全始终是一种有限的资源：
<pba-flex center>
- 中心化：腐败/影响的成本
- 工作量证明：世界上CPU的数量
- 权益证明：价值（根据定义，是有限的）
</pba-flex>
---v
## 安全稀释
共识系统会为争夺安全资源而竞争，并且它们有理由相互攻击。
一些晦涩小众的“X 证明”算法的出现，对抵御攻击的作用有限。
---v
## ⚔ 区块链战争
高安全性的系统有动机去攻击那些它们视为竞争对手的低安全性系统。
> 为了乐趣和利益。
Notes:
“在这个（并非遥不可及的）全球共识世界里……”
---v
## ⚔ 工作量证明之战
<img rounded style="width: 1300px;" src="./img/3.4-51-percent-cost.png" />
> 成功发动攻击可能需要付出什么代价？
Notes:
- 对于工作量证明，相同算法的哈希算力可能受到攻击！购买哈希算力是可行的：
- 大多数GPU矿工使用类似<https://www.nicehash.com/>这样的软件，切换任务去挖掘收益最高（相对于某种基础货币）的链。
- ASIC矿机灵活性较差，但也会选择挖掘收益最高的加密货币。
- 示例：[以太坊经典遭遇深度重组](https://coingeek.com/ethereum-classic-experiences-51-attack-and-3000-block-reorg/)
---v
## 权益证明中的……无风险投票
对分叉进行投票是“免费”的……那就全都投！
（前提是最终不会被惩罚！）
> 成功发动攻击可能需要付出什么代价？
Notes:
- 与工作量证明不同，在工作量证明中对一条链投票需要消耗系统外部的资源，而权益证明中只有用于核算共识规则的内部衡量机制。
- **关键**：这是早期简单的权益证明实现中的一个问题。现代权益证明方案通过引入保证金和对恶意行为（如发表矛盾信息）的惩罚机制（后续会讲到）来避免这个特定问题。
- 解释得很好的文章及图片来源：<https://golden.com/wiki/Nothing-at-stake_problem-639PVZA>
---v
## 权益证明中的……相对无风险投票
攻击的风险回报比与质押资产的估值相关。
理性行为者在计算最高回报时会考虑外部激励因素。
> 成功发动攻击可能需要付出什么代价？
Notes:
- 同样，权益证明只有用于核算共识规则的内部衡量机制，但系统并非孤立存在：需要考虑所涉资产的相对估值。
---v
## 验证者整合
一个系统需要多少个验证者？
验证者数量增加应该会降低实体之间勾结的可能性。
但验证者在经济和计算方面成本高昂。
Notes:
这又是一个需要考虑的“N引理”问题。
---v
## 权益证明的经济安全性
论点：权益证明中经济安全的上限与可保障的相对估值相关，这与网络的市值相关。
> 市值是指单个公司/链/代币所固有的所有资产的总市场价值。
Notes:
- 这里的市值可以是公司股票、现存的以太坊总量，或者与特定智能合约或平行链相关的X代币总量。
---v
## ⚔ 权益证明经济安全之战
<img rounded style="width: 1150px;" src="./img/market-cap-pos.png" />
Notes:
在这里，和工作量证明一样，网络存在相对安全性，但无法从一条链“跳到”另一条链，所以竞争仍在于相对安全性，不过一个质押不能直接攻击另一个独立共识系统中的质押……那么共识内的价值体系又如何呢？
---v
## DApp的权益证明经济安全
<img rounded style="width: 1200px;" src="./img/market-cap-polkadot-chains.png" />
Notes:
需要注意：这些说明过于简化！我们可能会在NPoS课程中进一步讨论这类问题（至少Nuke是这么认为的）。正式分析的细节超出了本课程的范围。
论点：应用程序资产（智能合约上的代币或平行链）的总估值是有限的，并且该限制与它们所在的共识系统的总经济安全性相关。
在Polkadot的中继链模型中，Nuke认为从高价值资产中提取价值的攻击，其收益可能超过获取执行攻击所需的“拜占庭式质押水平”的成本。因此，所有平行链的市值总和也是有限的，因为控制相同水平的质押可以接管其上的所有链。Nuke认为以太坊上所有合约的总估值也是如此。
---v
## 验证者不当行为
<pba-flex center>
- 发表矛盾信息
  - 出块方面：提出相互排斥的链
  - 最终性方面：对相互排斥的链投票使其成为最终链
- 提供无效信息
- 可用性缺失
- **故意滥用协议**（[自私挖矿](https://golden.com/wiki/Selfish_mining_attack-39PMNNA)）
</pba-flex>
Notes:
我们已经讨论过共识故障，但滥用协议是一个新问题。Nuke认为这里所说的“滥用”并非机制设计的初衷，并且对系统健康不利。自私挖矿就是一个很好的例子，在这种攻击中，难以证明出块者通过在网络其他节点之前挖矿并扣留有效区块来“作弊”。其他参与者是否也可能滥用协议呢？
---v
## 验证者的责任
拥有权力就应承担责任。
无论如何设计验证者选择机制，总会有人在其中占据特权地位。
那些选择成为验证者的人应对自己的行为负责。
---v
## 可证明性与发表矛盾信息
有些类型的不当行为比其他行为更难证明。
**发表矛盾信息**很容易证明：有人可以直接出示两条签名消息作为加密证明。
其他不当行为则依赖于挑战 - 响应机制和争议解决机制。
No te s
“无风险”解决方案，可能存在远程攻击的问题。
[弱主观性](https://blog.ethereum.org/2014/11/25/proof-stake-learned-love-weak-subjectivity) 仍有可能以一种更难组织的方式导致相同的行为，不良行为者可能已经释放了他们的质押，从而产生一个有效的、最终确定的分叉。
---v
## Po l ka do t的设计考量
- 更多的验证者可以提高网络（平行链）的状态转换吞吐量。
- 作为更大共识系统的成员，各个分片拥有完全的经济自由。
- 超线性惩罚机制会让勾结的验证者面临生存风险，而善意的验证者则无需担忧。
Notes:
Polkadot架构中有一些有趣的设计决策。
我们之后会更深入地讲解Polkadot！
---v
## 交易审查与排序
区块创建者可以选择包含哪些交易以及交易的顺序。
<pba-flex center>
- 审查攻击
- “最大可提取价值”（MEV）
</pba-flex>
---v
## Web3的目标：无审查
系统用户数量远远多于系统验证者数量。
然而，每笔交易都必须由验证者打包进区块。
如果没有验证者愿意打包某个用户的交易，那么该用户就无法获得无许可访问。
_只要有任何一个验证者（出块者）决定不进行审查，该交易就**可能**被打包进区块。_
Notes:
目前大多数系统都没有惩罚审查行为的机制，更棘手的问题是，根据涉及的参与者不同，可能根本无法发现网络中正在发生这种审查行为。
---v

## 最大可提取价值（MEV）
最大可提取价值是衡量区块创建者基于对未确认交易的了解以及对其排序的能力所能提取的价值。
<pba-flex center style="margin-left: -90px">
- 抢先交易
- 后置交易
- 夹心交易
</pba-flex>
> <https://www.mev.wiki/>
Notes:
这是一种衍生行为。在它悄然成为常态之前，很多人都没有意识到这种可能性。
---v
## 最大可提取价值
> 在这样一个环境中，一旦被检测到就意味着死路一条……
> ……确定某人的位置就如同直接摧毁他们。
> 
> -- [以太坊是一片黑暗森林](https://www.paradigm.xyz/2020/08/ethereum-is-a-dark-forest)
Notes:
讲讲这篇文章的故事，大致是有白帽黑客试图通过设计混淆手段从一个存在漏洞的合约中转移资金，结果有人破解后发现了可提取的价值，并抢先进行了交易。
至少在以太坊上，这如今已成为常态，而且进一步 “制度化” 了。
<!-- FIXME TODO 以太坊测试网上的黑暗森林游戏……或者其他零知识证明游戏？ -->
---v
## 👼 Flashbots
> Flashbots是一个研发组织，旨在减轻最大可提取价值（MEV）给有状态区块链带来的负面外部性，从以太坊开始着手。
> 
> -- [Flashbots](https://www.flashbots.net/)
Notes:
这可能具有误导性，因为他们通过让MEV变得更有效和制度化而获利！
---v
## Flashbots 😈
- **Flashbots Auction**：一个交易排序市场，包括Flashbots中继和MEV - Geth。
- **MEV - Boost**：以太坊权益证明中提议者 - 构建者分离（PBS）的协议外实现。
- **Flashbots Protect**：一个任何人都可以使用的rpc端点，用于防止抢先交易和交易失败。
- **Flashbots Data**：用于提高以太坊上MEV活动和Flashbots Auction透明度的工具和仪表盘。
Notes:
由于信息不对称通常会导致MEV领域的垄断，所以Flashbots是一股中心化力量。这方面存在竞争格局，值得称赞的是，Flashbots似乎真心想通过去中心化来助力以太坊的健康发展……
（但首先需要进行讨论！）
尤其是鉴于最近美国外国资产控制办公室（OFAC）的压力揭示了系统的脆弱性……
---v
<!-- .slide: data-background-color="#4A2439" -->
# 讨论
抢先交易即服务（FaaS）和MEV拍卖（MEVA）
是解决方案还是权宜之计？
Notes:
- Flashbots及相关项目
---v
## 合规性
<img rounded style="width: 1000px" src="./img/tornado-ofac.png" />
Notes:
<https://cryptoslate.com/op-ed-is-ethereum-now-under-u-s-control-99-of-latest-relay-blocks-are-censoring-the-network/>
- 代码不停机，但平台 **可以** 进行审查。
  有能力就意味着有责任（我们之后可能会进一步讨论这一点）
---v
## 社会背景
社会系统和规范可以帮助掩盖协议中的弱点。
> 用于谴责OFAC审查行为的公开监测平台：
> <https://www.mevwatch.info/>
Notes:
- 通过打破规范来自同伴的压力，甚至可能因此失去在共识中的权力。
  计算机网络中的同伴声誉，在这里也适用于人类社会。
- 社会压力有时对系统有益，有时则有害，这取决于观点和受益者！
- 在门罗币中，“运行自己的节点” 文化有助于保持其去中心化。
  比特币的大区块之争表明，社会压力有助于决定规范的分叉。
- 最坏的情况是，为了中间商的利益将MEV常态化，提供价值榨取服务。
---v
## 功能拆分
<img style="width: 1200px" src="./img/3.4-web3-stack.png" />
Notes:
这张图之前出现过，这里要指出的是，功能拆分正变得越来越精细，早期比特币等由单一参与者完成所有操作的情况，正越来越多地发生改变。
- 特别是如果像MEV这样的更多事物能借此得到增强。
- 这会引入更多复杂性和可能存在弱点的接口（尤其是在需要网络支持的情况下！）
---v
## 功能拆分
### “区块空间” 的愿景越来越促使达成这一目的
---v
## 多样性
<img style="width: 1200px" src="./img/eth-client-diversity.png" />
Notes:
- Parity拯救了以太坊（替代客户端及多样性的作用）
  - 链上运行时相同，不可能有替代运行时。
- 都在谷歌云平台（GCP）上运行
- 都运行相同的硬件（CPU等）
- ……
- 图片来源及相关参考：<https://mirror.xyz/jmcook.eth/S7ONEka_0RgtKTZ3-dakPmAHQNPvuj15nh0YGKPFriA>
---
## 总结思考
- 复杂性通常会增加失败的风险，但我们不应畏惧它。
  $~~~$_假设：这通常会使系统更加脆弱。_
- 可观察到的行为比模型和理论更重要。
  $~~~$_复杂系统并非直观易懂，可能会表明你的假设和模型是错误的！_
- 这节课只是一个开始……
  $~~~$_我们鼓励你继续深入探索！_
Notes:
- 在很多情况下，风险和未知的未知会呈指数级增长。
- 像MEV中OFAC的主导地位以及Babe共识机制的备用模式主导等可观察到的例子。
- 期待一起探索Web3中广阔的未知领域！
---
## 🤝 一起，深入探索
<img rounded style="width: 800px" src="./img/DiveEdge.gif" />
---
<!-- .slide: data-background-color="#4A2439" -->
# 问题
---
<!-- .slide: data-background-color="#000000" -->
# 补充幻灯片
Notes:
主要供参考，不在正式课堂时间讲解 😀
---
## 治理……不停机？
<iframe width="1120" height="630" src="https://www.youtube-nocookie.com/embed/Q6euy5W1js4" title="YouTube视频播放器" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
[不停机的代码：不能与不愿的区别](https://www.youtube-nocookie.com/embed/Q6euy5W1js4)
Notes:
课后观看！
或许可以非正式地安排大家在接下来几天观看。
---v
## 不停机的代码
> 它从占主导地位的权力形式中夺取权力：政府、企业、国家、社团、文化、宗教。
> 它从这些庞然大物手中夺取权力，并将其赋予小人物。
> 迟早，那些正在失去不应得且被滥用权力的人会开始反击。
> 到那时，我们就会开始了解我们的代码到底有多不停机。
> 
> -- 安德烈亚斯·安东诺普洛斯（Andreas Antonopoulos）
---v
## 不能与不愿
> 当你从 “不能” 跨越到 “不愿” 的那一刻，原本的能力就变成了责任。
> 然后，如果你声称自己不再具备这种能力，那么这种责任就变成了疏忽，甚至是刑事疏忽。
> 
> -- 安德烈亚斯·安东诺普洛斯（Andreas Antonopoulos）
Notes:
- 区别是什么？
- 丝绸之路创始人被判处两个无期徒刑外加40年监禁。
- 道德相对主义 -> “谁的法律？”
- 别把你的 “意外条款” 设得太窄。
---
## 去中心化自治组织（DAOs）
去中心化自治组织（[DAOs](https://www.investopedia.com/tech/what-dao/)）。
> 一种 **协调** 机制。
---v
## 民主系统
Democratic Mediums是一个关于决策、审议和舆情处理模式的目录。
<pba-flex center>
- <https://medlabboulder.gitlab.io/democraticmediums/>
- <https://metagov.org/> -- 每周研讨会
- <https://www.smartcontractresearch.org/>
</pba-flex>
Notes:
强烈建议课后探索！这个维基页面中有许多新颖且精妙的定义。
---
## 行为建模
> [代币工程](https://tokenengineeringcommunity.github.io/website/)
> {尤其是学院课程和cadCAD Edu项目}
No te s
主要是免费的教育资源和工具，可用于更深入地研究代币经济学。
记住，这些通常是理想化系统的模型，现实世界的情况会有所不同！ 

