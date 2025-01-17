---
title: Econ & Game Theory in Blockchain
description: Blockchain applications of Econ & Game Theory
duration: 60 minutes
---

# **区块链中的经济学与博弈论**

Notes:

- 演讲者介绍
- 时间线说明（模块2和3之后）
- 今天我们将进行一场关于区块链中的经济学与博弈论的全新讲座

---

# 目标是什么？

Notes:

本次讲座的目标是什么？

---v

## 目标是什么？

<pba-cols>
    <pba-col>
        <img style="width: 500px" src="./img/Econ_Box_Open.svg" alt="打开的经济学盒子" />
    </pba-col>
    <pba-col>
        <ul>
            <li> 需求与供给 </li>
            <li> 市场 </li>
            <li> 纳什均衡 </li>
            <li> 谢林点 </li>
            <li> <strong>...</strong> </li>
        </ul>
    </pba-col>
</pba-cols>

Notes:

到现在为止，你们都应该熟悉经济学和博弈论模块中的所有术语和技术，比如需求与供给或纳什均衡。

---v

## 目标是什么？

<pba-cols>
    <pba-col>
        <img style="width: 280px; margin-top: 190px" src="./img/Econ_Box_Closed.svg"
            alt="经济学盒子" />
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/Blockchain_Box_Open.svg" alt="打开的区块链盒子" />
    </pba-col>
    <pba-col>
        <ul>
            <li> 共识 </li>
            <li> 协议 </li>
            <li> 代币 </li>
            <li> 状态转换函数 </li>
            <li> <strong>...</strong> </li>
        </ul>
    </pba-col>
</pba-cols>

Notes:

而且你们刚刚也学完了区块链的基础知识，所以你们都应该熟悉状态转换函数，并且对共识协议有一定的了解。

---v

## 目标是什么？

<img style="width: 800px; margin-top: 130px" src="./img/Econ_Handshake_Blockchain.svg"
    alt="经济学与区块链握手" />

Notes:

考虑到以上所有内容，本次讲座的目标是将这两者结合起来，看看经济学和博弈论如何应用于区块链。
为了做到这一点，我们将提供一些示例用例和案例研究。

---

# 全景概述

Notes:

但首先，让我们快速总结一下我们将如何弥合这两个模块之间的差距。

---v

## 全景概述

<pba-cols>
    <pba-col>
        <ul>
            <li> 系统即游戏 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/game.svg" alt="游戏手柄" />
    </pba-col>
</pba-cols>

Notes:

首先，我们将把各种区块链系统看作是独立的游戏。

---v

## 全景概述

<pba-cols>
    <pba-col>
        <ul>
            <li> 系统即游戏 </li>
            <li> 节点/矿工/验证者即玩家 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/player.svg" alt="欢呼的笔记本电脑用户" />
    </pba-col>
</pba-cols>

Notes:

这些游戏的参与者可以是矿工，比如在比特币协议中；也可能是波卡协议中的验证者；或者只是智能合约的一些用户。

---v

## 全景概述

<pba-cols>
    <pba-col>
        <ul>
            <li> 系统即游戏 </li>
            <li> 节点/矿工/验证者即玩家 </li>
            <li> 协议即游戏规则</li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 300px" src="./img/assumptions.drawio.svg" alt="带要点的清单" />
    </pba-col>
</pba-cols>

Notes:

协议本质上是一组规则，将定义游戏本身。
这就是我们要分析的内容。

---v

## 全景概述

<pba-cols>
    <pba-col>
        <ul>
            <li> 系统即游戏 </li>
            <li> 节点/矿工/验证者即玩家 </li>
            <li> 协议即游戏规则</li>
            <li> 加密货币即点数 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 400px" src="./img/bitcoin.svg" alt="比特币符号" />
    </pba-col>
</pba-cols>

Notes:

为了正确分析这些游戏，我们需要有某种价值表示，在我们的例子中，通常是加密货币。

---v

## 全景概述

<pba-cols>
    <pba-col>
        <ul>
            <li> 系统即游戏 </li>
            <li> 节点/矿工/验证者即玩家 </li>
            <li> 协议即游戏规则</li>
            <li> 加密货币即点数 </li>
            <li> 奖励与惩罚即激励措施</li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/gavel.svg" alt="法槌" />
    </pba-col>
</pba-cols>

Notes:

最后，我们将有各种奖励和惩罚机制，这些机制将塑造玩家的激励措施。
现在我们已经有了定义区块链游戏的所有元素，我们可以寻找更多的相似之处。

---v

# 市场的出现

Notes:

首先，让我们来看看一些大家可能很熟悉的东西：市场

---v

## 市场的出现

_"市场是由系统、机构、程序、社会关系或基础设施组成的，各方通过这些进行交换。"_

Notes:

所以，市场是一个由各方进行交换的系统组成的。
这是最简单的解释方式。

市场是我们经济的基石，它们无处不在，这也意味着它们已经被深入研究过。
这对我们来说非常幸运。

现在，让我们来看看一些大家可能也很熟悉的东西……

---v

## 市场的出现

#### _费用市场_

<img style="width: 1200px" src="./img/fees.png"
    alt="比特币交易费用随时间变化的图表" />

Notes:

费用市场。
这是比特币网络中平均交易费用随时间变化的图表。
正如你所看到的，价格一直在波动。

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 费用/小费 </strong>
        <ul>
            <li> 用户用费用进行竞价 </li>
            <li> 矿工选择费用最高的交易 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/scales.svg" alt="天平图标" />
    </pba-col>
</pba-cols>

Notes:

让我们深入了解一下。
用户根据他们愿意支付的费用进行竞价，而矿工则选择最有利可图的交易。
如果矿工已经包含了所有最好的交易，并且没有太多新的交易进来，他们就需要开始接受费用较低的交易，所以价格就会下降。
但是等等……？

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 产品是什么？ </strong>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/blockspace.drawio.png" alt="波卡中的区块空间" />
    </pba-col>
</pba-cols>

Notes:

这里实际交换的产品是什么？在原油市场中，交换的商品是明确界定的。
是原油桶。
这里实际交易的是什么呢？

有没有人知道可能是什么？

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 产品是什么？ </strong>
        <ul>
            <li> 被交换的是区块空间 </li>
            <li> 矿工生产区块空间，并有效地将其拍卖给用户 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/blockspace.drawio.png" alt="波卡中的区块空间" />
    </pba-col>
</pba-cols>

Notes:

是区块空间。
矿工确保并生产区块空间，并将其拍卖给各种用户。
将区块空间作为一种产品来考虑实际上是一种全新的思考区块链的方式，所以我强烈建议查看笔记中链接的文章。

- <https://www.polkadot.network/blog/blockspace-blockspace-ecosystems-how-polkadot-is-unlocking-the-full-potential-of-web3/>

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 市场均衡 </strong>
        <ul>
            <li> 成功进入区块的交易的费用代表了当前的市场均衡 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/market_eq.drawio.svg" alt="显示市场均衡的图表" />
    </pba-col>
</pba-cols>

Notes:

区块空间的价格由供需的市场均衡决定。
需求方是愿意进行交易的用户，供应方是生产区块空间的矿工。
实际被包含的交易代表了当前的均衡。

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 供给与需求 </strong>
        <ul>
            <li> 在极端情况下，需求可能会大幅增加，以至于费用会让网络不堪重负 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/rising.svg" alt="上升的图表图标" />
    </pba-col>
</pba-cols>

Notes:

我们都知道，交易费用有时会高得离谱。
在许多用户想要进行交易的情况下，需求可能会急剧上升。
我们看到的是价格均衡也随之上升。

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 供给与需求 </strong>
        <ul>
            <li> 在极端情况下，需求可能会大幅增加，以至于费用会让网络不堪重负 </li>
            <li> 大多数经济市场可以通过增加供应来应对不断增长的需求，但在大多数区块链中，这是无法直接实现的 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 300px" src="./img/goods.svg" alt="传送带上的一堆货物" />
    </pba-col>
</pba-cols>

Notes:

现在让我们想想正常市场是怎么做的。
如果我们的需求和价格极高，供应商就会有动力生产更多。
但在区块链中，这是无法直接实现的。
更多的矿工往往并不意味着更多的区块空间，而是更安全的区块空间。
这意味着质量更好的区块空间。

所以，供应往往是固定的，而需求是波动的。
这通常是费用市场波动剧烈的原因。

---v

## 市场的出现

#### _费用市场_

<pba-cols>
    <pba-col>
        <strong> 供给与需求 </strong>
        <ul>
            <li> 在极端情况下，需求可能会大幅增加，以至于费用会让网络不堪重负 </li>
            <li> 大多数经济市场可以通过增加供应来应对不断增长的需求，但在大多数区块链中，这是无法直接实现的 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/kitty.png" alt="加密猫标志" />
    </pba-col>
</pba-cols>

Notes:

根据这一理论，一些大型区块链实际上试图实施可变大小的区块来应对需求，但这是一项相当复杂的任务，并且需要做出很大的权衡。

你们中有些人可能认出这张图片是以太坊上的加密猫游戏。
这是一个可以购买和繁殖数字猫的游戏。
它非常受欢迎，以至于实际上堵塞了以太坊网络，使正常交易几乎无法进行。
也许现在你对那里实际发生的事情有了一些额外的见解。

=5秒停顿=

---

# 纳什均衡
Notes:
现在，我们来看一些能让我们预测用户行为的不同内容……即纳什均衡。
---v
## 纳什均衡
- 没有玩家想要偏离的策略
- 但比特币挖矿的策略是什么呢？
Notes:
纳什均衡指的是没有玩家想要偏离的策略。道理很简单，可比特币挖矿的策略是什么呢？我们如何运用这一知识呢？
---v
## 纳什均衡
<img style="width: 500px" src="./img/honest_mining_meme.jpg" alt="诚实挖矿梗图"/>
Notes:
更重要的是……答案是诚实挖矿，对吧？
---v
## 纳什均衡
#### _比特币挖矿_
<img style="width: 400px" src="./img/bitcoin.svg" alt="比特币标志" />
Notes:
让我们深入探讨，看看答案究竟是什么。比特币挖矿的纳什均衡是什么？是诚实挖矿还是不诚实挖矿？
---v
## 纳什均衡
<pba-cols>
    <pba-col>
        <strong>假设：</strong>
        <ul>
            <li> 只有2个矿工 </li>
            <li> 区块奖励 = 2 </li>
            <li> 难度随诚实矿工数量变化 </li>
            <li> 矿工是理性行动者 </li>
            <li> 不诚实的矿工之间不合作 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/nash.drawio.svg" alt="收益表" />
    </pba-col>
</pba-cols>
Notes:
这里我们有几个假设，主要的假设如下：
- 假设我们只有2个矿工
- 区块奖励为2
- 矿工可以选择诚实地遵循协议，或者试图作弊，发布无效区块（比如在区块中给自己999枚比特币）
让我们来计算一下他们平均每人能挖到多少比特币。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (1).svg" alt="收益表" />
Notes:
如果两个矿工都不诚实挖矿，那么他们都无法产出有效的比特币区块……也就没有实际的比特币被挖到。记住，他们不会合作，而是会尝试发布给自己账户9999枚比特币的区块。所以双方的收益均为0。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (2).svg" alt="收益表" />
Notes:
如果两人都诚实地工作并产出有效区块，他们有同等机会挖到一个区块并获得奖励。其他节点会接受这些区块，但这两个矿工竞争同一资源，所以平均每人能获得1枚比特币（因为挖矿奖励是2枚）。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (3).svg" alt="收益表" />
Notes:
如果其中一人不诚实，另一人仍可以诚实地工作，由于没有竞争，他能获得更大的奖励。在这种情况下，诚实的一方获得2枚比特币。
现在我们知道了每种情况下谁能挖到多少比特币，我们需要将注意力转移到另一个重要因素上。像Twitter、Facebook或比特币这类事物，往往因网络效应而被认为具有价值。
---v
## 纳什均衡
<br />
#### _网络效应_
“网络效应是一种商业原理，表明当更多人使用某种产品或服务时，其价值会增加。”
Notes:
一般来说，使用某个平台或相信某种事物的人越多，它就越有价值。这是一个相当简单的概念，但在区块链领域非常重要。对比特币来说，许多人珍视它，是因为很多人相信它，并且参与挖矿和交易。在区块链中，使用你的链的人越多，它就越有价值。
---v
## 纳什均衡
<br />
#### _网络效应_
<br />
**重新审视假设：**
<ul>
    <li style="color: grey;"> 只有2个矿工 </li>
    <li style="color: grey;"> 区块奖励 = 2 </li>
    <li style="color: grey;"> 难度随诚实矿工数量线性变化 </li>
    <li style="color: grey;"> 矿工是理性行动者 </li>
    <li style="color: grey;"> 不诚实的矿工之间不合作 </li>
    <li> 代币价格随诚实矿工数量呈二次方比例变化
        <ul>
            <li>1个诚实矿工 -> 1美元</li>
            <li>2个诚实矿工 -> 4美元</li>
    </li>
</ul>
Notes:
让我们在模型中加入这个额外的假设。如果有更多矿工保障网络安全，那么代币本身会更有价值。我们在此使用一个非常简单的模型，即代币价格是矿工数量的平方。由于我们研究的是小型系统，这个模型的近似度足够高。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (4).svg" alt="收益表" />
Notes:
我们可以通过改变对矿工代币的估值来应用这些变化。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (4b).svg" alt="收益表" />
Notes:
如果两个矿工都诚实地保障网络安全，代币价格为4美元；如果只有一个诚实矿工，价格为1美元。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (5).svg" alt="收益表" />
Notes:
现在我们可以专注于寻找纳什均衡了，那就用最简单的方法吧。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (6).svg" alt="收益表" />
Notes:
假设矿工B诚实挖矿，矿工A需要在收益4和收益0之间做出选择。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (7).svg" alt="收益表" />
Notes:
他当然会选择4，因为4更大。我们暂时用红圈标记这个选择。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (8).svg" alt="收益表" />
Notes:
当B不诚实时，同理，A会选择2而不是0。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (9).svg" alt="收益表" />
Notes:
现在我们反转假设，假设A诚实挖矿，那么B需要在4和0之间做出选择。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (10).svg" alt="收益表" />
Notes:
和之前一样，他应该选择4，因为4更大。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (10b).svg" alt="收益表" />
Notes:
最后剩下的选项结果是圈出的2。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (12).svg" alt="收益表" />
Notes:
现在，所有选项都被圈出的方格就是纳什均衡。在我们的例子中，纳什均衡似乎是诚实挖矿。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (13).svg" alt="收益表" />
Notes:
这并不奇怪，不诚实挖矿会让你一无所获，这在意料之中。但如果我们稍微改变一下假设呢？
---v
## 纳什均衡
<br />
**不诚实**究竟意味着什么？
<br />
<br />
<div>
存在一些主流规则（协议），如果单个矿工违反这些规则，这看起来像是孤立的错误或作弊企图。
</div>
<!-- .element: class="fragment" data-fragment-index="1" -->
<br />
<br />
如果多个矿工**以相同方式**违反协议，这可以被视为一种偏离主流协议的新协议。
<!-- .element: class="fragment" data-fragment-index="2" -->
Notes:
我们之前假设矿工要么诚实，要么不诚实，但不诚实实际上意味着什么呢？
=fragment=
存在一些主流规则（协议），如果单个矿工违反这些规则，这看起来像是孤立的错误或作弊企图。这是我们之前分析的情况。
=fragment=
但如果多个矿工以相同方式违反协议呢？这可以被视为一种偏离主流协议的新协议。这就是我们接下来要探讨的内容，即不诚实的矿工进行合作。
---v
## 纳什均衡
<pba-cols>
    <pba-col>
        <strong>假设：</strong>
        <ul>
            <li> 只有2个矿工 </li>
            <li> 区块奖励 = 2 </li>
            <li> 难度随诚实矿工数量变化 </li>
            <li> 代币价格随诚实矿工数量呈二次方比例变化 </li>
            <li> 矿工是理性行动者 </li>
            <li> 选择遵循哪种协议 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/nash.drawio (14).svg" alt="收益表" />
    </pba-col>
</pba-cols>
Notes:
假设基本相同，但这次不诚实的矿工将进行合作。实际上，他们将遵循一种不同的修改后的协议。所以我们不再认为他们不诚实，只是他们遵循了不同的规则。现在矿工需要选择是为比特币BTC协议挖矿，还是为比特币现金BCH协议挖矿。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (15).svg" alt="收益表" />
Notes:
让我们快速按照之前的规则填充这个表格。只是这次如果两个矿工都遵循BCH协议，他们当然会获得BCH代币作为奖励。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (16).svg" alt="收益表" />
Notes:
和之前一样，我们进一步应用网络效应。如果两个矿工都保障网络安全，价格为4；如果只有一个，价格为1。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (17).svg" alt="收益表" />
Notes:
现在我们已经调整了所有价格。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (18).svg" alt="收益表" />
Notes:
现在让我们看看如果矿工B挖掘BTC会发生什么。矿工A会更倾向于也挖掘比特币。所以他们都会挖掘同一种币。
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (19).svg" alt="收益表" />
Notes:
另一方面，如果矿工B挖掘BCH，那么矿工A似乎更倾向于挖掘BCH……
---v
## 纳什均衡
<img style="width: 700px" src="./img/nash.drawio (20).svg" alt="收益表" />
Notes:
实际上，我们在这里得到了两个不同的纳什均衡！
---v
## 纳什均衡
那么诚实是最佳策略吗？
<br />
<br />
并非总是如此。如果大多数人诚实，那么诚实会带来回报。如果大多数人以相同方式**不诚实**，那么你也应和他们一样**不诚实**。
<!-- .element: class="fragment" data-fragment-index="1" -->
<br />
<br />
<div>
事实上，已经证明在工作量证明（PoW）中，跟随多数人是真正的纳什均衡，无论他们采用何种策略或协议，只要该策略或协议具有一致性。
</div>
<!-- .element: class="fragment" data-fragment-index="2" -->
Notes:
那么诚实是最佳策略吗？
=fragment=
并非总是如此。如果大多数人诚实，那么诚实会带来回报。如果大多数人以相同方式不诚实，那么你也应和他们一样不诚实。
=fragment=
事实上，已经证明在工作量证明（PoW）中，跟随多数人是真正的纳什均衡，无论他们采用何种策略或协议，只要该策略或协议具有一致性。所以比特币挖矿实际上是一个大型的协调博弈，这就是为什么诚实（即遵循BTC协议）通常会有回报。
- 更复杂的例子将在周五关于分叉的讲座中探讨
- 研究工作量证明纳什均衡和跟随多数人的论文：
  <https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=7bf78054192d98e999edcdf08971a5eed42518d2>

---

# 谢林点
Notes:
说到这个话题……如果存在多个纳什均衡会怎样呢？谢林点常常能解决这一问题。
---v
## 谢林点
- 人们倾向于选择的解决方案（最容易达成协调的方案）
- 通常也是一种纳什均衡
- 它在区块链中究竟能有什么用处呢？
<!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
首先，谢林点是人们倾向于选择的一种策略，它通常也是一种纳什均衡。
=fragment=
但它在区块链领域究竟对我们有什么用处呢？
---v
## 谢林点
#### 侦探游戏
<pba-cols>
    <pba-col>
        <ul>
            <li> 两个犯罪同伙 </li>
            <li> 侦探分别审讯他们 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 700px" src="./img/detective.drawio.png" alt="侦探和两个劫匪" />
    </pba-col>
</pba-cols>
Notes:
在我们全面解答这个问题之前，让我们先来探讨另一个能展示谢林点如何起作用的游戏。我保证这与区块链相关。故事是这样的，有两个银行劫匪和一名侦探，侦探分别审讯这两个劫匪，试图让他们坦白或抓住他们说谎的漏洞。
---v
## 谢林点
#### _侦探游戏_
<img style="width: 550px" src="./img/interrogation_small.drawio.svg" alt="审讯游戏收益表" />
Notes:
如果任何一个劫匪泄密并说出真相，他们俩都会输掉这场“游戏”并入狱。如果他们都撒谎，就能逃脱惩罚。这看起来非常简单，显然他们俩只需撒谎就行。这似乎是个稳妥的策略，对吧？但要记住，他们俩都目睹了抢劫的真实经过……
---v
## 谢林点
#### _侦探游戏_
<img style="width: 700px" src="./img/interrogation_large.drawio.svg" alt="审讯游戏详细收益表" />
Notes:
然而，他们能编造出多种谎言。如果他们的说法不一致，侦探就会识破他们。所以现在他们需要就某个特定的谎言达成一致，并且所有细节都必须完全相同，否则就会败露。相比之下，说实话就容易多了。他们俩都目睹了真相，所以一致地陈述事实是轻而易举的事。
---v
## 谢林点
#### _侦探游戏_
如实回答是最容易达成协调的策略之一，因此它们就是谢林点。
Notes:
这就是为什么我们可以说，如实回答是最容易达成协调的策略之一，因此它们就是谢林点。真相本身就是一个谢林点。这个概念对区块链中的某样东西至关重要，那就是……
---v
## 谢林点
#### _预言机_
预言机是一种区块链实体，负责将外部世界的信息提供给区块链。
<br />
<br />
<img style="width: 700px" src="./img/oracles.drawio.svg" alt="预言机从现实世界获取信息" />
Notes:
预言机。首先，在座的各位有谁听说过预言机？举手示意一下。预言机非常有趣，因为它们试图实现的是将外部世界的信息引入区块链，而这是一项极其艰巨的任务。
---v
## 谢林点
#### _预言机_
预言机是一种区块链实体，负责将外部世界的信息提供给区块链。
<br />
<br />
<div>
    <strong>外部信息示例：</strong>
    <ul>
        <li> 伯克利的气温是多少？ </li>
        <li> 谁赢得了选举？ </li>
        <li> 美元对比特币的汇率是多少？ </li>
    </ul>
</div>
Notes:
它们能提供的信息示例包括伯克利当前的气温是多少、谁赢得了选举、美元对比特币的汇率是多少等等。让我们通过气温这个稍微简化的例子，实际看看它们可能是如何工作的。
---v
## 谢林点
#### _预言机_
<br />
<br />
<img style="width: 900px" src="./img/temp1.drawio.svg" alt="温度数轴" />
Notes:
那么伯克利的气温是多少呢？假设你在伯克利有个花园，并且你购买了链上保险，一旦气温过高你就能获得赔付。所以你想知道伯克利的气温。我们知道答案在这条数轴上的某个位置，但具体是多少呢？
---v
## 谢林点
#### _预言机_
<br />
<br />
<img style="width: 900px" src="./img/temp2.drawio.svg" alt="带有部分测量值的温度数轴" />
Notes:
我们可以让一些用户提交他们认为的气温数值。他们中有些人会自己去查看，有些人会使用天气应用程序，还有些人会直接猜测。
---v
## 谢林点
#### _预言机_
<img style="width: 900px" src="./img/temp3.drawio.svg" alt="带有一组测量值聚集区的温度数轴" />
Notes:
我们期望这里看到的这组投票结果能围绕实际气温。最好的方法是查看中位数，这是为什么呢？
---v
## 谢林点
#### _预言机_
<pba-cols>
    <pba-col>
        <ul>
            <li> 诚实的参与者自然会达成一致 </li>
            <li> 攻击者可能试图串通并撒谎 </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/temp3.drawio.svg" alt="带有一组测量值聚集区的温度数轴" />
    </pba-col>
</pba-cols>
Notes:
诚实的投票者自然会达成一致。他们会查看气温并诚实地投票，误差在很小的范围内。那些为了歪曲结果而撒谎、提交随机数值的人，通常不会像诚实的投票者那样聚集在某个数值附近。要发动更具威胁性的攻击，他们需要有策略地就某个特定数值达成一致并集体撒谎，这比直接查看室外气温要难得多。在这里，提交真实数据就是谢林点，这使得诚实变得容易。
---v
## 谢林点
#### _预言机_
<pba-cols>
    <pba-col>
        如何应对攻击者？
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/temp3.drawio.svg" alt="带有一组测量值聚集区的温度数轴" />
    </pba-col>
</pba-cols>
Notes:
但如果存在攻击者该怎么办呢？我们能采取什么措施？
---v
## 谢林点
#### _预言机_
<pba-cols>
    <pba-col>
        如何应对攻击者？
        <br /><br />
        如果不惩罚他们，他们会不断发动攻击直至成功
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/temp3.drawio.svg" alt="带有一组测量值聚集区的温度数轴" />
    </pba-col>
</pba-cols>
Notes:
如果我们从不惩罚他们，他们会不断发动攻击直至得手，这可不是好事。
---v
## 谢林点
#### _预言机_
<pba-cols>
    <pba-col>
        如何应对攻击者？
        <br /><br />
        如果不惩罚他们，他们会不断发动攻击直至成功
        <br /><br />
        更糟糕的是，他们可能创建一百万个虚假身份，大量发送错误投票
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/temp3.drawio.svg" alt="带有一组测量值聚集区的温度数轴" />
    </pba-col>
</pba-cols>
Notes:
更糟糕的是，他们可能创建一百万个虚假身份，大量发送错误投票。所以我们必须惩罚他们。但这已经不属于谢林点要解决的问题了，谢林点已经发挥了它的作用。我们现在讨论的是…… 

---

# 激励机制
Notes:
激励机制。这是我们接下来要探讨的重要话题，其在区块链领域至关重要。
---v
## 激励机制
“激励是指能够鼓励一个人去做某事的事物。”
<br /><br />
<div>在我们的情境中，我们希望构建激励机制，促使用户提交真实数据。</div>
<!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
激励是能鼓励人们行动的事物。
=fragment=
在我们的情境中，我们希望构建激励机制，促使用户提交真实数据。我们需要以一种引导用户诚实行为的方式来构建激励机制。
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        如何应对攻击者？
        <br /><br />
        如果不惩罚他们，他们会不断发动攻击直至成功
        <br /><br />
        更糟糕的是，他们可能创建一百万个虚假身份，大量发送错误投票
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/temp3.drawio.svg" alt="带有一组测量值聚集区的温度数轴" />
    </pba-col>
</pba-cols>
Notes:
回到我们的预言机问题，我们该如何应对攻击者呢？
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <div style="color: grey;">如何应对攻击者？
        <br /><br />
        如果不惩罚他们，他们会不断发动攻击直至成功
        <br /><br /></div>
        <strong>更糟糕的是，他们可能创建一百万个虚假身份，大量发送错误投票</strong>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/mask.svg" alt="面具" />
    </pba-col>
</pba-cols>
Notes:
我们先聚焦于虚假身份这第二个问题。如何防止这种情况发生呢？
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <strong>女巫攻击 </strong>
        <br /><br />
        区块链中的常见问题。
        <br /><br />
        <div>如果在链上部署，一个简单的解决方案是让用户锁定部分资金。</div>
        <!-- .element: class="fragment" data-fragment-index="1" -->
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/mask.svg" alt="面具" />
    </pba-col>
</pba-cols>
Notes:
单个实体创建多个虚假身份的攻击被称为女巫攻击，这在区块链中极为常见。在区块链上部署项目时，你必须时刻问自己：我构建的东西是否能抵御女巫攻击？
=fragment=
一个常用且简单的现成解决方案是让用户锁定部分资金。只有锁定了资金的用户才能投票，投票权重与质押金额成正比，因此创建一百万个账户毫无意义。这是个简单的解决方案，但并非在所有情况下都适用。
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <div style="color: grey;">如何应对攻击者？ </div>
        <br />
        <strong>如果不惩罚他们，他们会不断发动攻击直至成功</strong>
        <br /><br />
        <div style="color: grey;">更糟糕的是，他们可能创建一百万个虚假身份，大量发送错误投票 </div>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/gavel.svg" alt="法槌" />
    </pba-col>
</pba-cols>
Notes:
现在回到第一个问题，即未受惩罚的攻击者。我们该如何应对，才能让他们不再继续攻击我们呢？
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <strong>惩罚措施 </strong>
        <br /><br />
        我们已经为惩罚措施奠定了基础。
        <br /><br />
        <div>我们解决女巫攻击的方法是让用户锁定资金。
        <br /><br />
        若这类用户投票错误，我们可以扣除他们的资金。
        </div>
        <!-- .element: class="fragment" data-fragment-index="1" -->
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/gavel.svg" alt="法槌" />
    </pba-col>
</pba-cols>
Notes:
有趣的是，我们已经为防御措施奠定了基础。投票者在系统中锁定了部分资金，因此他们自身也有利益关联。
=fragment=
若他们投票错误，我们可以扣除他们的资金。这在区块链中是一种常见的解决方案。这里的投票错误指投票结果与中位数相差甚远。我们设计了一些保护性的激励措施，现在系统似乎是安全的。
---v
## 激励机制
#### _预言机_
<br />
我们是不是遗漏了什么？
<br /><br />
**为什么会有人参与这个系统？**
<!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
但我们是不是遗漏了什么？有人能想到是什么吗？我们忽略了什么呢？
=fragment=
为什么会有人参与这个系统？为什么会有人投票？为什么会有人锁定资金并承担风险呢？
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <strong>空城现象 </strong>
        <br /><br />
        没有用户愿意参与
        <br /><br />
        从现实世界获取信息需要付出努力，投票者是在为协议提供服务
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/ghost.svg" alt="幽灵" />
    </pba-col>
</pba-cols>
Notes:
从现实世界获取信息需要付出努力，投票者是在为协议提供服务。因此，我们需要激励他们参与。我们需要制定奖励机制，否则网络将无人参与。
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <strong>奖励机制 </strong>
        <br /><br />
        如果用户为协议提供了服务，就需要得到奖励
        <br /><br />
        <div>一种方法是为表现良好的投票者铸造一些代币奖励</div>
        <!-- .element: class="fragment" data-fragment-index="1" -->
        <br />
        <div>或者从之前积累的奖励池中进行分配</div>
        <!-- .element: class="fragment" data-fragment-index="2" -->
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/rewards.svg" alt="接收金钱的手" />
    </pba-col>
</pba-cols>
Notes:
如果用户为协议提供了服务，就需要得到奖励。
=fragment=
一种方法是为表现良好的投票者铸造一些代币奖励。
=fragment=
或者从之前积累的奖励池中进行分配。
但关键在于，只有有足够多的投票者参与，协议才会安全可靠，所以激励机制的设计需要鼓励用户参与。更准确地说，激励措施应大致与攻击者通过破坏系统可能获得的价值成正比。低风险的预言机不需要过于激进的激励措施。
---v
## 激励机制
#### _预言机_
<pba-cols>
    <pba-col>
        <strong>奖励机制问题</strong>
        <br /><br />
        <div>
        我们能否为正确投票分配固定金额的奖励？
        <br />例如每次正确投票奖励10美元
        </div>
        <br /><br />
        <div>
         不行。
         我们应该根据投票者的质押金额来确定奖励。
        </div>
        <!-- .element: class="fragment" data-fragment-index="1" -->
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/questions.svg" alt="举手" />
    </pba-col>
</pba-cols>
Notes:
我们快速思考一个问题。能否为正确投票分配固定金额的奖励？比如每次正确投票奖励10美元。
=提问环节=
=fragment=
不行。我们应该根据投票者的质押金额来确定奖励。否则，系统将易受女巫攻击。如果你有一百万个虚假身份，就可以投票一百万次并获得一百万次奖励。所以，奖励应该与质押金额成正比。
---v
## 激励机制
**总结：**
- 让诚实用户受益，增加攻击者作恶难度
<!-- .element: class="fragment" data-fragment-index="1" -->
- 为协议提供服务应得到奖励
<!-- .element: class="fragment" data-fragment-index="2" -->
- 破坏或干扰行为需受到惩罚
<!-- .element: class="fragment" data-fragment-index="3" -->
- 防止女巫攻击有助于抵御垃圾信息
<!-- .element: class="fragment" data-fragment-index="4" -->
Notes:
现在总结要点。
我们需要让诚实节点受益，增加攻击者作恶难度。谢林点作为设计的基础部分，帮助我们解决了这一问题。
=fragment=
为协议提供服务应得到奖励。我们需要激励用户参与，以确保获得可靠的结果。
=fragment=
破坏或干扰行为需受到惩罚。我们需要抑制不良行为。在我们的例子中，采取了扣除资金的惩罚方式。
=fragment=
防止女巫攻击有助于抵御垃圾信息。
具备这些条件后，我们的系统应该能得到恰当的激励且安全……那么，当激励机制…… 

---

# 激励错位
Notes:
激励错位指的是激励机制促使人们做出对网络不利的行为。
---v
## 激励错位
#### _以太坊状态存储问题_
<img style="width: 200px" src="./img/eth.svg" alt="以太坊标志" />
Notes:
我们来看看以太坊。以太坊是一个拥有大量智能合约的区块链。智能合约本质上是运行在区块链上的程序，它们存储在链上且任何人都可以执行。为了让智能合约运行，需要在链上部署大量代码。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>状态存储复制 </strong>
        <br /><br />
        每当我们在链上存储数据（比如智能合约）时，这些数据至少需要在节点间部分复制。
        <br /><br />
        <div>多个节点存储相同的数据。</div>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/replication.drawio.svg" alt="数据复制示意图" />
    </pba-col>
</pba-cols>
Notes:
此外，每当我们在链上存储数据（如智能合约）时，这些数据至少需要在节点间部分复制。成千上万的节点存储相同的数据，这并不是非常高效。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>状态存储复制成本 </strong>
        <br /><br />
        以太坊通过对提交大容量数据收取更多的Gas费用来应对数据复制带来的负担。
        <br /><br />
        而这还叠加在任何计算所需的Gas费用之上。
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/storage_costs.drawio.svg" alt="数据块旁的一堆硬币" />
    </pba-col>
</pba-cols>
Notes:
以太坊试图通过引入规模费用来解决这个问题。你在状态中存储的数据越多，需要支付的费用就越高。而且这是在任何计算费用之外的额外支出。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>状态存储时长</strong>
        <br /><br />
        状态的这一特定部分可能与未来的状态转换相关，因此节点不能简单地丢弃它。
        <br /><br />
        <div>全节点需要保存所有数据。</div>
    </pba-col>
    <pba-col>
        <img style="width: 500px" src="./img/data_no_changes.drawio.svg" alt="数据随时间不变" />
    </pba-col>
</pba-cols>
Notes:
需要注意的是，一旦我们将某些数据存入状态，在再次使用它之前，这些数据几乎会无限期地保留在那里。因为谁也不知道，它可能与未来的某些状态转换相关。所以节点不能简单地丢弃它。现在我们来看一个例子。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>认识一下鲍勃 </strong>
        <br /><br />
        鲍勃愉快地在以太坊上部署了他很棒的智能合约。
        他支付了高昂的Gas费用，但也只好如此。
        <br /><br />
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/smart_contract.drawio.svg" alt="在链上部署智能合约" />
    </pba-col>
</pba-cols>
Notes:
我们来认识一下鲍勃。鲍勃是一名开发者，他愉快地在以太坊上部署了自己很棒的智能合约。他支付了高昂的Gas费用，但也只能接受。他的代码被添加到状态中，现在许多节点都保存了一份副本。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>问题所在 </strong>
        <br /><br />
        鲍勃决定成为一名音乐家，或者只是不再喜欢编程了。
        <br /><br />
        他不再关心自己的智能合约了。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/smart_contract_deployed.drawio.svg" alt="已部署在链上的智能合约" />
    </pba-col>
</pba-cols>
Notes:
但想象一下，有一天鲍勃决定成为一名音乐家，或者只是不再喜欢编程了。他不再关心自己的智能合约。但区块链并不知道这一点，他的代码仍然存在于状态中，并且必须持续被复制和维护。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>问题加剧 </strong>
        <br /><br />
        许多像鲍勃这样的人纷纷效仿。
        <br /><br />
        他们中的一些人继续开发，但 <strong>何必费劲</strong> 删除旧数据呢？他们已经为此付过费了。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/smart_contract_many.drawio.svg" alt="链上部署了许多智能合约" />
    </pba-col>
</pba-cols>
Notes:
现在想象有数百个像鲍勃这样的人。他们中的一些人甚至还在继续开发，但他们心想“何必费劲”删除旧数据呢？他们已经为此付过费了。而另一些人则完全不在乎了。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>“何必费劲？” </strong>
        <br /><br />
        将数据存储到链上成本很高，但却没有清理数据的激励。
        <br /><br />
        这是导致以太坊状态规模失控增长的核心激励错位问题。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/smart_contract_many.drawio.svg" alt="链上部署了许多智能合约" />
    </pba-col>
</pba-cols>
Notes:
我们需要关注“何必费劲”这部分。这是一个激励错位的核心例子，它导致了以太坊状态规模失控增长。将数据存入状态确实成本高昂，但一旦存入……为什么要清理呢？没有这样做的激励。所以区块链被垃圾数据淹没了。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>目标 </strong>
        <br /><br />
        设计新的协议规则，引导用户行为，使他们开始清理状态。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/smart_contract_clean.drawio.svg" alt="链上清理后的智能合约" />
    </pba-col>
</pba-cols>
Notes:
那么我们能做些什么呢？目标是什么？我们需要设计新的协议规则，引导用户行为，使他们开始清理状态。希望不会产生任何副作用。
---v
## 激励错位
#### _以太坊状态存储问题_
<pba-cols>
    <pba-col>
        <br />
        <strong>解决方案 </strong>
        <br /><br />
        状态存储Gas退款机制
        <br /><br />
        在向状态中部署数据时支付高额费用，但在删除数据时可获得部分退款。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/smart_contract_burn.drawio.svg" alt="销毁智能合约" />
    </pba-col>
</pba-cols>
Notes:
提出的解决方案之一是引入Gas退款机制。在向状态中部署数据时支付高额费用，但在删除数据时可获得部分退款。这样现在就有了清理状态的激励。
---v
## 激励错位
#### _以太坊状态存储问题_
<br /><br />
<strong>之前的行为</strong>
<img style="width: 900px" src="./img/developer_musician.drawio.svg" alt="开发者成为音乐家" />
Notes:
最初的情况是，鲍勃为他的智能合约支付了费用，然后就去弹吉他了。
---v
## 激励错位
#### _以太坊状态存储问题_
<br /><br />
<strong>之后的行为</strong>
<img style="width: 900px" src="./img/developer_musician2.drawio.svg" alt="开发者删除合约后成为音乐家" />
Notes:
之后，鲍勃以同样的方式部署了他的合约，但在跑去弹吉他之前，他从状态中删除了合约并拿回了部分Gas费用。他喜欢这笔额外的钱，所以有了清理的动力。这里为了便于讲解，展示的是他全额拿回费用的情况，而实际上以太坊只退还部分Gas费用。
但是等等……如果他支付了10又拿回10，那么实际成本是多少呢？有人知道吗？成本可能不那么明显，但它是一种…… 

---

# 机会成本
Notes:
机会成本，这是经济学中一个非常重要的概念，在区块链领域同样至关重要。
---v
## 机会成本
“选择一种方案时，所放弃的其他选择可能带来的损失。”
<img style="width: 400px" src="./img/opp_cost.drawio.svg" alt="带有机会成本的多种选择" />
Notes:
一般来说，机会成本是指在做出选择时，放弃的其他选择所带来的损失。当你在10美元和30美元之间做选择时，选择30美元的机会成本就是10美元，即你所放弃的另一个选项。
---v
## 机会成本
#### _以太坊状态存储_
<pba-cols>
    <pba-col>
        <br />
        <strong>真正的成本 </strong>
        <br /><br />
        鲍勃原本可以将锁定在存储押金/退款机制中的资金投资到其他地方并获利。
        <br /><br />
        <div>仅仅把资金锁定本身就相当于一种惩罚。</div><!-- .element: class="fragment" data-fragment-index="1" -->
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/opp_cost_locking.drawio.svg" alt="锁定资金与投资对比" />
    </pba-col>
</pba-cols>
Notes:
回到以太坊的话题，对鲍勃来说，真正的成本不是他为存储支付的10美元，因为这笔钱他之后可以拿回。成本在于他失去了将这笔钱投资到其他地方的机会。
=fragment=
仅仅把资金锁定本身就相当于一种惩罚，即便之后能拿回这笔钱。在通货膨胀的体系中尤其如此，而大多数体系都是通胀体系。机会成本是一种巧妙的机制，它让我们能够在不直接收费的情况下将成本考虑在内。我们也必须格外留意，以免因未考虑到外部机会成本而意外地惩罚用户。
---v
## 机会成本
#### _其他例子_
- 比特币中创建无效区块，即便该区块被网络拒绝，也不会直接受到惩罚。真正的成本是机会成本，因为矿工本可以去挖掘一个有效区块。
- 波卡的原生代币DOT具有通胀性（每年约7.5%），但可以通过质押来赚取奖励（每年约15%）。不质押DOT存在机会成本，这就激励人们质押以保障网络安全。
<!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
区块链领域有许多关于机会成本的精彩例子。例如，在比特币中创建无效区块，即便该区块被网络拒绝，也不会直接受到惩罚。真正的成本是机会成本，因为矿工本可以去挖掘一个有效区块。
=fragment=
波卡的原生代币DOT具有通胀性（每年约7.5%），但可以通过质押来赚取奖励（每年约15%）。不质押DOT存在机会成本，这就激励人们质押以保障网络安全。此外，还有许多其他质押和去中心化金融（DeFi）的例子。
---
# 外部性
Notes:
为了真正理解我们在上一部分所讲的内容，我们需要探讨一下外部性。
---v
## 外部性
“经济活动对无关第三方造成的影响。”
Notes:
外部性是经济活动对某些第三方造成的后果。例如，你开车时排放的尾气就是一种负外部性，会影响到周围所有人。相反，想象一下你仅仅因为喜欢树的样子和它能提供阴凉，就在自家花园里种了一棵树。这棵树改善了周边的空气质量，对周围的人来说就是一种正外部性。
---v
## 外部性
#### _以太坊状态存储_
无用数据堵塞区块链是一种负外部性，会影响到所有使用该区块链的用户。作为协议设计者，我们需要意识到这类外部性，并尝试通过**将其纳入定价**来限制它们的影响。
Notes:
在以太坊的例子中，你可以认为网络堵塞是单个开发者不清理自己产生的数据所带来的外部性。它影响到了所有使用该区块链的用户。区块链是公共物品的一个例子。作为协议工程师或系统设计师，你需要识别出这些外部性成本，并将其纳入定价考量。
---v
## 外部性
#### _以太坊状态存储_
<pba-cols>
    <pba-col>
        <br />
        <strong>负外部性成本 </strong>
        <br /><br />
        在以太坊状态存储问题中，我们将锁定资金的机会成本视为负外部性的定价。
    </pba-col>
    <pba-col>
        <img style="width: 700px" src="./img/opp_cost_locking.drawio.svg" alt="锁定资金与投资对比" />
    </pba-col>
</pba-cols>
Notes:
这就是我们在以太坊中对机会成本的运用。我们让造成区块链负担的人付出实际代价，使他们的激励与区块链的利益保持一致。
---v
## 外部性
#### _预言机_
<pba-cols>
    <pba-col>
        <br />
        <strong>正外部性 </strong>
        <br /><br />
        在预言机机制中提供投票服务，可以被视为对网络的一种正外部性，网络能够进一步利用这些额外信息。
        <br /><br />
        投票者为协议提供了有价值的服务。
    </pba-col>
    <pba-col>
        <img style="width: 700px" src="./img/oracles.drawio.svg" alt="外部世界信息进入区块链" />
    </pba-col>
</pba-cols>
Notes:
但并非所有外部性都是负面的。例如，整个预言机机制使区块链能够获取来自现实世界的信息。这对网络来说是一种正外部性，网络可以进一步利用这些额外信息。
---v
## 外部性
#### _预言机_
投票者为协议提供了有价值的服务。那么，如果他们通过交易在链上提交投票，是否应该支付任何费用呢？ <!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
诚实的投票者为协议提供了有价值的服务。
=fragment=
那么，考虑到这一点，他们提交投票时是否应该支付交易费用呢？
---v
## 外部性
#### _预言机_
<pba-cols>
    <pba-col>
        <br />
        <strong>有益交易 </strong>
        <br /><br />
        这样的交易可以是完全免费的。
        <br /><br />
        <div>但要确保它不会被滥发！</div><!-- .element: class="fragment" data-fragment-index="1" -->
    </pba-col>
    <pba-col>
        <img style="width: 300px" src="./img/free.svg" alt="写有“免费”的贴纸" />
    </pba-col>
</pba-cols>
Notes:
与普遍看法相反，这样的交易可以是完全免费的。
=fragment=
但要确保它不会被滥发！这一点非常重要，因为如果交易免费，就可能会被滥用。在我们的预言机系统中，我们可以确保每一份质押对应一次投票。这样既能保证安全，又能使交易免费，从而进一步激励人们参与。
---v

## 免费交易
<pba-cols>
    <pba-col>
        <br />
        还有其他并非一定属于正外部性的免费交易。
        <br /><br />
        <strong>固有交易 </strong>
        <ul>
            <li> 比特币的区块奖励 </li>
            <li> 每个区块需要执行的任何逻辑（与区块本身相关） </li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 300px" src="./img/free.svg" alt="写有“免费”的贴纸" />
    </pba-col>
</pba-cols>
Notes:
存在一些并非一定属于正外部性的免费交易。例如在比特币中，挖出一个区块后会获得奖励。这是一笔免费交易，但不属于正外部性，它只是与区块本身相关。通常，每个区块需要执行的任何逻辑（与区块本身相关的）都是免费的。
---
# 完全信息博弈与不完全信息博弈
Notes:
现在让我们来看一些截然不同的内容。我们将讨论博弈论中一个至关重要的概念，即信息。
---v
## 完全信息博弈与不完全信息博弈
玩家是否知晓游戏状态的所有信息？
玩家是否**需要**知晓游戏状态的所有信息？
Notes:
我们将探讨诸如玩家是否知晓游戏状态的所有信息、他们是否需要知晓这些信息，以及这对游戏有何影响等问题。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票（简化版）_
<img style="width: 900px" src="./img/Approval_checkers.drawio.svg" alt="五个验证者，其中三个为批准检查者" />
Notes:
为了深入研究这个话题，我们将深入探讨波卡，尤其是批准投票子系统。这是我在Parity亲自参与的工作内容，在后续模块中你们会对此有更深入的了解。现在你们需要明白的是，波卡中有一些特殊节点被称为验证者。顾名思义，他们负责验证网络中的新区块是否有效和正确。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票（简化版）_
<pba-cols>
    <pba-col>
        <br />
        <strong>批准检查者 </strong>
        <br /><br />
        在波卡中，验证新区块时，并非所有人都参与验证工作。只有一些随机挑选出的验证者——称为**批准检查者**——被选中来验证候选区块。
    </pba-col>
    <pba-col>
        <img style="width: 700px" src="./img/Approval_checkers.drawio.svg" alt="五个验证者，其中三个为批准检查者" />
    </pba-col>
</pba-cols>
Notes:
但在波卡中，并非每个验证者都承担所有工作。他们分担工作，每个区块仅由一部分验证者进行检查，这些验证者被称为批准检查者。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票（简化版）_
<pba-cols>
    <pba-col>
        <br />
        <strong>攻击者 </strong>
        <br /><br />
        我们假设攻击者可以对部分但并非所有验证者进行分布式拒绝服务（DDoS）攻击。
        <br /><br />
        遭受DDoS攻击会导致他们无法及时投票。
    </pba-col>
    <pba-col>
        <img style="width: 700px" src="./img/validators_attack.drawio.svg" alt="攻击者淘汰了部分验证者" />
    </pba-col>
</pba-cols>
Notes:
我们还需要对意图扰乱网络的攻击者做出一些假设。我们假设攻击者可以对部分但并非所有验证者进行DDoS攻击，遭受攻击的验证者将无法及时投票。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票_
**默认场景**
- 随机选择3个批准检查者并公布
- 被选中的批准检查者公布投票
- 如果收到的所有投票都确认该区块没问题，则该区块通过
<img style="width: 600px" src="./img/Approval_checkers.drawio.svg" alt="五个验证者，其中三个为批准检查者" />
Notes:
默认的验证场景是这样的。我们随机选择3个批准检查者并公布，被选中的批准检查者公布投票，如果收到的所有投票都确认该区块没问题，则该区块通过。但这里存在一个问题，有人知道是什么问题吗？
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票_
**默认场景**
- 随机选择3个批准检查者并公布
- 被选中的批准检查者公布投票
- **攻击者利用该信息，在批准检查者公布投票前对其进行DDoS攻击**（除了他们的内应）
- 如果收到的所有投票都确认该区块没问题，则该区块通过
<img style="width: 600px" src="./img/validators_target_attack.drawio.svg" alt="被淘汰的批准检查者" />
Notes:
如果攻击者得知指定的批准检查者是谁，他们就可以集中攻击这些目标。只淘汰相关目标，从而危及网络安全。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票_
**问题是什么？**
- 攻击者知晓了游戏的所有信息
- 攻击者能够在验证者做出反应之前利用这些信息
**如何解决？**
<!-- .element: class="fragment" data-fragment-index="1" -->
- 限制攻击者能够获取的信息，使他们无法提前规划
<!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
那么我们来思考一下问题出在哪里。攻击者知晓了游戏的所有信息，并且能够在验证者做出反应之前利用这些信息。信息成为了他们的武器。
=fragment=
如果我们能够以某种方式限制信息，游戏局面将对我们有利。我们需要将其转变为不完全信息博弈，隐藏一些信息。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票_
**改进后的场景**
- 每个验证者使用可验证随机函数（VRF）生成一个随机数
- 生成的随机数足够小的验证者将有权成为批准检查者
- 批准检查者通过展示其较小的随机数（附上VRF证明）并同时公布投票来表明身份 <!-- .element: class="fragment" data-fragment-index="2" -->
- 如果收到的所有投票都确认该区块没问题，则该区块通过 <!-- .element: class="fragment" data-fragment-index="2" -->
<br />
Notes:
- 让我们设想这个新的改进场景。每个验证者使用VRF生成一个随机数。
- 存在某个阈值，生成的随机数足够小的验证者将有权成为批准检查者。
=fragment=
- 批准检查者通过展示其较小的随机数（附上VRF证明）并同时公布投票来表明身份。如果收到的所有投票都确认该区块没问题，则该区块通过。
这种方法的优点在于，攻击者在批准检查者表明身份之前无法得知他们是谁，因此无法提前规划攻击。
---v
## 完全信息博弈与不完全信息博弈
#### _波卡批准投票_
**攻击者能做什么？**
- 他们不再知道批准检查者是谁，只能猜测
- 如果猜错，他们将受到严重惩罚
<img style="width: 700px" src="./img/validators_random_attack.drawio.svg" alt="部分验证者被淘汰" />
Notes:
那么攻击者能做什么呢？如果他们无法再进行针对性的DDoS攻击，就只能随机攻击。这极大地降低了他们成功的概率，一旦失败，他们就容易受到惩罚。这基本上就是波卡采用的方法，称为基于VRF的随机分配。这是一种不完全信息博弈。
---v
## 完全信息博弈与不完全信息博弈
#### _其他例子_
- BABE是波卡中用于选择新区块生产者的机制（在波卡模块中会进一步介绍）。它也使用类似的VRF方案来生成随机分配。
Notes:
在后续模块中，你们还会学到一些其他值得关注的例子，其中就包括波卡中用于选择新区块生产者的BABE机制。它也使用类似的VRF方案来生成随机分配。
---
# 假设条件的变化
Notes:
到目前为止，你们可能已经注意到一个规律，即我们通常会列出一系列假设条件。
---v
## 假设条件的变化
<pba-cols>
    <pba-col>
        每个游戏在进行推理之前都需要做出一些假设。
        <ul>
            <li>玩家数量</li>
            <li>可行的行动</li>
            <li>信息获取途径</li>
            <li>等等</li>
        </ul>
    </pba-col>
    <pba-col>
        <img style="width: 200px" src="./img/assumptions.drawio.svg" alt="写有假设的板子" />
    </pba-col>
</pba-cols>
Notes:
其中一些假设与玩家数量或信息获取途径有关。这些假设通常较为稳定。
---v
## 假设条件的变化
<pba-cols>
    <pba-col>
        如果任何一个假设发生变化，游戏也会随之改变。曾经合理的旧激励机制可能如今会促使玩家做出截然不同的行为。
    </pba-col>
    <pba-col>
        <img style="width: 300px" src="./img/assumptions_change.drawio.svg" alt="板子上的部分假设发生变化" />
    </pba-col>
</pba-cols>
Notes:
如果由于某种原因假设条件发生演变和变化会怎样呢？游戏也会相应改变。曾经合理的旧激励机制可能如今会促使玩家做出截然不同的行为。
---v
## 假设条件的变化
#### _重质押_
<pba-cols>
    <pba-col>
        通过EigenLayer进行的重质押激励以太坊质押者同时将相同资金质押给多个应用程序。
        <br /><br />
        激励博弈将截然不同，资金将被有效杠杆化（双倍风险和双倍回报）。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/eigen.drawio.png" alt="EigenLayer标志" />
    </pba-col>
</pba-cols>
Notes:
一个很好的例子就是以太坊近期发生的情况，我说的就是本月的事。有一个名为EigenLayer的新二层协议，它允许质押者同时将相同资金质押给多个应用程序，这就是所谓的重质押。这在以太坊原生功能中并不存在，在设计惩罚/奖励机制时也未曾考虑到这一点。
---v
## 假设条件的变化
#### _重质押_
<pba-cols>
    <pba-col>
        <strong>后果 </strong>
        <br /><br />
        重质押的后果尚未完全明晰，相关研究仍在进行中。
        <br /><br />
        可查看演讲者备注（按“S”）以获取更多阅读资料。
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/eigen.drawio.png" alt="EigenLayer标志" />
    </pba-col>
</pba-cols>
Notes:
重质押的后果尚未完全明晰，相关研究仍在进行中。我建议你们查看演讲者备注以获取更多阅读资料。整个区块链激励机制和协议设计领域仍在不断发展，存在许多未知因素，但总体而言，我希望今天介绍的所有方法能帮助你们在未来做出更明智的决策。就讲到这里……
- 播客：<https://www.youtube.com/watch?v=aP9f_1v9Ulc>
- 白皮书：<https://2039955362-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FPy2Kmkwju3mPSo9jrKKt%2Fuploads%2F9tExk4U2OdiRKGEsUWqW%2FEigenLayer_WhitePaper.pdf?alt=media&token=c20ac4bd-badd-4826-9fb6-492923741c9e>
---
## 总结
- 市场 - 费用市场
- 纳什均衡 - 比特币挖矿
- 谢林点 - 预言机
- 激励机制 - 预言机
- 机会成本 - 以太坊状态存储退款
- 外部性 - 以太坊状态存储和预言机
- 完全信息博弈与不完全信息博弈 - 波卡批准投票
- 假设条件 - 重质押
Notes:
综上所述，我们讨论了：
---
# 感谢大家！ 