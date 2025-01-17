---
title: Blockchain Forks
description: Detailed Classification for Blockchain Forks Types
duration: 60 minutes
---

# 区块链分叉

---

# 概况

---v

## 概况

#### 理想世界

在理想世界中，区块链应该是这样的：
<br /><br />

<img style="width: 800px" src="./img/no_fork.drawio.svg" />

---v

## 概况

#### 现实世界

事情并不总是按计划进行：

<br />

<img style="width: 800px" src="./img/fork_small.drawio.svg" />

---v

## 概况

#### 混乱的现实世界

有时情况会变得格外混乱：

<br />

<img style="width: 800px" src="./img/fork_chaos.drawio.svg" />

---

# 目标是什么？

---v

## 目标是什么？

#### _分叉识别_

<img style="width: 500px" src="./img/forks_and_boxes.drawio.svg" />

Notes:

存在不同的分叉，它们可能有不同的形态和原因。
我们将尝试识别一些示例。

---v

## 目标是什么？

#### _分叉分类_

<img style="width: 800px" src="./img/forks_in_boxes.drawio.svg" />

Notes:

为什么要分类？
同一类别的分叉将表现出相似的行为，需要类似的处理方式。
这样，在进行更改时，我们可以轻松确定更改属于哪个类别，并做出相应的反应。

值得指出的是，整个Web3领域仍然非常年轻，我们仍在摸索如何恰当地命名事物。
关于分叉类型肯定还存在很多困惑，我今天将使用的命名惯例是基于麻省理工学院数字货币倡议提出的命名。
它将涵盖大多数常见术语，希望不会像社区中使用的某些术语那样自相矛盾。

---v

## 目标是什么？

#### _分叉的困惑_

<br />
BABE（有时）：
<br /><br />
<img style="width: 800px" src="./img/transitory_fork_unresolved.drawio.svg" />

Notes:

为了说明这种困惑，我们可以想想BABE。
BABE可能会让多个区块作者同时创建区块，然后链就会分叉。
这是什么类型的分叉呢？
你们中有些人可能听说过软分叉和硬分叉，有没有人知道这是哪一种？

---

# 分叉分类

---v

## 分叉分类

#### _分叉家族树_

<br />
<img style="width: 800px" src="./img/fork_family.drawio.svg" />

Notes:

这是我们今天要带大家了解的分叉的核心分类。
你不需要一下子理解整棵树，我们将逐步进行讲解。
现在，让我们回到BABE的例子，把它放在这个分类图中。

---

# 临时分叉

Notes:

为此，我们要谈谈临时分叉。

---v

## 分叉分类

#### _临时分叉_

<br />
<img style="width: 800px" src="./img/fork_family_transitory.drawio.svg" />

Notes:

这是最简单的分叉之一，很少被提及，所以这个名字可能不太熟悉。
它们通常也被称为短暂分叉或临时分叉。

---v

## 临时分叉

<br />
<img style="width: 800px" src="./img/transitory_forks.drawio.svg" />

Notes:

它们通常是由基本的协议不确定性或网络延迟引起的，但幸运的是，网络本身通常会很快解决这些问题。
例如，在BABE中，即使所有节点都运行相同的软件，当两个节点的随机数足够低时，仍然可能会出现分叉。
在比特币中，两个矿工可能几乎同时挖出一个区块。
随着时间的推移，由于一些启发式规则（如最长链规则），其中一个区块会成为获胜者。
这些分叉通常不是问题，只会在短时间内存在。

---

# 共识分叉

Notes:

现在，让我们来看看更有趣的东西。
共识分叉。

---v

## 分叉分类

#### _共识分叉_

<br />
<img style="width: 800px" src="./img/fork_family_consensus.drawio.svg" />

Notes:

通常，当你听到关于分叉的讨论时，听到的就是这些。
它们是另一个分支，也有很多不同的类型，我们一会儿会详细讨论。

---v

# 共识分叉

## _有效集_

Notes:

但在理解共识分叉的复杂性之前，我们需要先理解有效集的概念以及它与协议的关系。

---v

## 共识分叉

#### _有效集_

<br />
<img style="width: 800px" src="./img/BTC_block.drawio.svg" />

Notes:

通过一个例子来理解是最好的，让我们看看比特币的区块。
你不需要理解其中的所有字段，现在先看看区块大小字段以及区块头本身。

#### _有效集_

<br />
<img style="width: 300px" src="./img/BTC_header.drawio.svg" />

---v

## 共识分叉

#### _有效集_

<br />
<img style="width: 500px" src="./img/BTC_header_constraints.drawio.svg" />

---v

## 共识分叉

#### _有效集_

<br />
<img style="width: 500px" src="./img/validity_set.drawio.svg" />

Notes:

所以，有效集是协议可能产生的所有假设区块的集合。
它是在这些规则下所有有效区块的集合。

例如，如果有一个区块D，它太大了，其区块大小超过了允许的大小……

---v

## 共识分叉

#### _有效集_

<br />
<img style="width: 500px" src="./img/universal_set.drawio.svg" />

Notes:

那么它就会从有效集中排除，进入所有可能的数据块的通用集合。
只有其中一些数据块是有效区块。

---v

## 共识分叉

#### _有效集_

<br />
<img style="width: 500px" src="./img/validity_set_old.drawio.svg" />

Notes:

让我们看一个例子。
假设这是比特币的有效集，我们可以看到其中的一些区块。
顶部的数字是代表这些区块的哈希值的前几位数字。

想象一下，所有比特币节点突然决定，他们真的不喜欢哈希值的第一个数字是奇数的情况。
他们只喜欢第一个数字是偶数的情况，所以他们联合起来修改协议，只接受哈希值第一个数字是偶数的区块。

---v

## 共识分叉

#### _有效集_

<br />
<img style="width: 500px" src="./img/validity_set_new.drawio.svg" />

Notes:

协议的这种变化会缩小有效集。
它将比以前受到更多的限制。
一些以前有效的区块在新规则下将不再有效。
在这种情况下会发生什么？我们能预测吗？

---v

## 共识分叉

#### _有效集_

<pba-cols>
    <pba-col>
		<img style="width: 500px" src="./img/validity_set_new.drawio.svg" />
    </pba-col>
    <pba-col>
		<img style="width: 300px" src="./img/venn_soft.drawio.svg" />
		<div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
</pba-cols>

Notes:

为了更简单地表示同样的概念，我们将使用右边的简化表示。
其中新集合N包含在旧集合O中。
底部的花哨符号表达的也是同样的意思，即N包含于O。

---

# 软分叉

为了理解刚才的例子，我们将深入探讨软分叉。

---v

## 分叉分类

#### _软分叉_

<br />
<img style="width: 800px" src="./img/fork_family_soft.drawio.svg" />

Notes:

首先，软分叉是一种共识分叉，它是协议变更的结果，因此会影响有效集。

---v

## 分叉分类

#### _软分叉_

<pba-cols>
    <pba-col>
		<img style="width: 300px" src="./img/venn_soft.drawio.svg" />
		<div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
		<ul>
			<li>向后兼容</li>
			<li>通过使共识规则更加严格，有效区块的集合变小。</li>
			<li>不是所有（通常是没有）在旧规则下产生的区块都会被新节点接受。</li>
		</ul>
    </pba-col>
</pba-cols>

Notes:

根据旁边的维恩图，我们可以看到，新的共识规则更加严格，因为有效集缩小了。

新节点产生的区块总是会被旧节点接受。
旧节点通常不会产生被新节点接受的区块。

在我们进行演示之前，请问减小或增加区块大小是软分叉吗？

---v

## 分叉分类

#### _软分叉_

<pba-cols>
    <pba-col>
		<img style="width: 300px" src="./img/venn_soft.drawio.svg" />
		<div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
		<strong>示例：</strong>
		<br /><br />
		<ul>
			<li>减小区块大小</li>
			<li>只接受哈希值为偶数/奇数的区块</li>
			<li>禁止使用某些交易类型</li>
		</ul>
    </pba-col>
</pba-cols>

Notes:

减小区块大小会限制可以构建的不同区块的数量，从而使集合变小。
这是一种软分叉。
我们刚才提到的偶数哈希值的例子也是一种软分叉，因为它在以前的协议规则上增加了另一个限制，使其更加严格。
另一个很好的例子是禁止使用某些交易类型。

现在，让我们看看分叉在实际中是如何工作的，以及它们如何根据支持协议变更的哈希算力或权益比例而变化。

---v

## 分叉分类

#### _软分叉_

<pba-cols>
    <pba-col>
		<img style="width: 300px" src="./img/venn_soft.drawio.svg" />
		<div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
		<img style="width: 800px" src="./img/soft_forks_s50.drawio.svg" />
    </pba-col>
</pba-cols>

Notes:

在这种情况下，我们将看看如果哈希算力或权益比例低于50%的节点想要进行软分叉会发生什么。
记住，软分叉只是让共识规则更加严格。

在这种情况下，新节点产生的区块用N标记，它们会被旧链接受，但旧链的挖矿速度更快，所以它们并不关心新节点。
旧节点产生的区块不会被新节点接受，所以对于新节点来说，最长的链是只有N区块的短链。
这实际上是一个永久性的分叉。

---v

## 分叉分类

#### _软分叉_

<pba-cols>
    <pba-col>
		<img style="width: 300px" src="./img/venn_soft.drawio.svg" />
		<div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
		<img style="width: 800px" src="./img/soft_forks_g50.drawio.svg" />
    </pba-col>
</pba-cols>

Notes:

在类似的例子中，当新节点控制超过50%的算力时，情况会发生巨大的变化。
新节点的挖矿速度更快，成为最长的链。
但要记住，旧节点会接受新的区块，所以如果新节点挖矿速度更快，旧节点的区块会不断被重组出去。
如果旧节点想要他们的区块被接受，就必须更新软件，否则他们将失去所有的奖励。

---

# 隐藏分叉

Notes:

现在，让我们来看看一些不太为人所知的东西。
隐藏分叉。

---v

## 分叉分类

#### _隐藏分叉_

<br />
<img style="width: 800px" src="./img/fork_family_hidden.drawio.svg" />

Notes:

软分叉的一种特殊情况。

---v

## 分叉分类
#### _隐藏分叉_
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hidden.drawio.svg" />
        <div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
        <ul>
            <li>无冲突</li>
            <li>那些现在被排除在外的旧区块，在实践中虽被允许存在，但从未被使用过。</li>
            <li>新节点理论上更为严格，但实际上会接受所有旧区块。</li>
            <li>旧节点接受新区块。</li>
        </ul>
    </pba-col>
</pba-cols>
Notes:
所以这个维恩图和普通软分叉的情况完全一样。但想象一下，橙色月牙部分，也就是我们从旧协议切换到新协议时排除的部分……实际上从未被使用过。例如，区块中有一个空字段，里面本可以包含任意数据，但大家都让它空着，也从不检查里面有什么。新协议在这个空字段中放入了有意义的内容，但并非强制要求。由于旧节点从未使用过这个字段，几乎所有旧区块在新规则下都会被接受。
简而言之，我们从有效集中移除的内容，尽管在技术上是有效的，但实际上从未被使用过。
---v
## 分叉分类
#### _隐藏分叉_
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hidden.drawio.svg" />
        <div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
        <strong>示例：</strong>
        <br /><br />
        <ul>
            <li>为空白操作码分配无冲突的用途。</li>
            <li>BTC Ordinals使用空白操作码来实现比特币NFT。</li>
        </ul>
    </pba-col>
</pba-cols>
Notes:
一个很好的例子就是为以前未使用的操作码分配新的可选用例，就像最近比特币Ordinals更新的例子一样。
---v
## 分叉分类
#### _隐藏分叉_
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hidden.drawio.svg" />
        <div style="font-size: 50px;">N ⊆ O</div>
    </pba-col>
    <pba-col>
        <img style="width: 800px" src="./img/soft_forks_hidden.drawio.svg" />
    </pba-col>
</pba-cols>
Notes:
它们之所以被称为隐藏分叉……是因为尽管共识发生了变化，但它们甚至不会表现为分叉。所有节点实际上都接受彼此的区块，因此没有冲突。
---
# 硬分叉
Notes:
---v
## 分叉分类
#### _硬分叉_
<br />
<img style="width: 800px" src="./img/fork_family_hard.drawio.svg" />
---v
## 分叉分类
#### _硬分叉_
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hard.drawio.svg" />
        <div style="font-size: 50px;">O ⊆ N</div>
    </pba-col>
    <pba-col>
        <ul>
            <li>向前兼容</li>
            <li>通过放宽共识规则，有效区块的集合变大。</li>
            <li>不是所有（通常是没有）在新规则下产生的区块都会被旧节点接受。</li>
            <li>所有在旧规则下产生的区块都会被新节点接受。</li>
        </ul>
    </pba-col>
</pba-cols>
---v

## 分叉分类
### 硬分叉
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hard.drawio.svg" />
        <div style="font-size: 50px;">O ⊆ N</div>
    </pba-col>
    <pba-col>
        <strong>示例：</strong>
        <br /><br />
        <ul>
            <li>增加区块大小</li>
            <li>最初的比特币现金分叉*</li>
            <li>添加新的交易类型</li>
            <li>增加最大随机数（nonce）值</li>
        </ul>
    </pba-col>
</pba-cols>
---v
## 分叉分类
### 硬分叉
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hard.drawio.svg" />
        <div style="font-size: 50px;">O ⊆ N</div>
    </pba-col>
    <pba-col>
        <img style="width: 800px" src="./img/hard_forks_s50.drawio.svg" />
    </pba-col>
</pba-cols>
Notes:
首先，我们来看一下支持度低于50%的硬分叉场景。记住，这次规则是放宽了。如果新节点接受旧区块，由于它们的算力低于50%，其产生的区块会不断被重组出去。在这种情况下不会形成永久性分叉，如果支持度有限，这种变化也无法实现。
---v
## 分叉分类
### 硬分叉
<pba-cols>
    <pba-col>
        <img style="width: 300px" src="./img/venn_hard.drawio.svg" />
        <div style="font-size: 50px;">O ⊆ N</div>
    </pba-col>
    <pba-col>
        <img style="width: 800px" src="./img/hard_forks_g50.drawio.svg" />
    </pba-col>
</pba-cols>
Notes:
当支持度超过50%时，新节点挖矿速度更快，但它们不会被旧节点接受，于是新节点继续前行。旧节点维护旧链，社区就此分裂。所以，如果有一个大多数人接受但并非所有人都认可的重大变化，就总会导致链的分叉。
---
## 小结
<pba-cols>
    <pba-col>
        <img style="width: 400px" src="./img/soft_forks_s50.drawio.svg" />
        <br />
        <img style="width: 400px" src="./img/soft_forks_g50.drawio.svg" />
    </pba-col>
    <pba-col>
        <img style="width: 400px" src="./img/hard_forks_s50.drawio.svg" />
        <br />
        <img style="width: 400px" src="./img/hard_forks_g50.drawio.svg" />
    </pba-col>
</pba-cols>
Notes:
现在我们已经了解了软分叉和硬分叉……如果我们手动增加比特币网络的挖矿难度，这会是软分叉还是硬分叉呢？是硬分叉。另外需要重申的是，只有在支持度低于50%的软分叉和支持度超过50%的硬分叉中，才会出现永久性分叉。
---
# 完全分叉
---v
## 分叉分类
### 完全分叉
<br />
<img style="width: 800px" src="./img/fork_family_full.drawio.svg" />
---v

## 分叉分类
### 完全分叉
<pba-cols>
    <pba-col>
        <img style="width: 200px" src="./img/venn_full.drawio.svg" />
        <div style="font-size: 50px;">O ∩ N = ∅</div>
    </pba-col>
    <pba-col>
        <ul>
            <li>完全不兼容</li>
            <li>兼具软分叉和硬分叉的特点</li>
            <li>通过改变共识规则，相关集合可能变得互不相交或相互重叠 。</li>
            <li>在一种规则集下产生的大多数（通常是所有）区块在另一种规则集下不被接受。</li>
        </ul>
    </pba-col>
</pba-cols>
---v
## 分叉分类
### 完全分叉
<pba-cols>
    <pba-col>
        <img style="width: 200px" src="./img/venn_full.drawio.svg" />
        <div style="font-size: 50px;">O ∩ N = ∅</div>
    </pba-col>
    <pba-col>
        <strong>示例：</strong>
        <br /><br />
        <ul>
            <li>更改哈希函数</li>
            <li>更改签名方案</li>
            <li>软分叉和硬分叉的特定组合</li>
            <li>最终的比特币现金分叉*</li>
        </ul>
    </pba-col>
</pba-cols>
---v
## 分叉分类
### 完全分叉
<pba-cols>
    <pba-col>
        <img style="width: 200px" src="./img/venn_full.drawio.svg" />
        <div style="font-size: 50px;">O ∩ N = ∅</div>
    </pba-col>
    <pba-col>
        <img style="width: 600px" src="./img/full_forks__&_50.drawio.svg" />
    </pba-col>
</pba-cols>
---
## 总结
<pba-cols>
    <pba-col>
        <img style="width: 400px" src="./img/soft_forks_s50.drawio.svg" />
        <br />
        <img style="width: 400px" src="./img/soft_forks_g50.drawio.svg" />
    </pba-col>
    <pba-col>
        <img style="width: 400px" src="./img/hard_forks_s50.drawio.svg" />
        <br />
        <img style="width: 400px" src="./img/hard_forks_g50.drawio.svg" />
    </pba-col>
    <pba-col>
        <img style="width: 400px" src="./img/full_forks__&_50.drawio.svg" />
    </pba-col>
</pba-cols>
Notes:
- 比特币现金因算力不足从硬分叉转变为完全分叉。
- 软分叉通常更受青睐，因为当算力支持超过50%时，它不会导致社区分裂（比特币社区的逻辑）。
- 硬分叉也可能被偏好，因为它们似乎能更好地代表少数群体的意见。如果一些人不同意多数人的意见，他们可以自然地分叉出去，而不会受到同伴压力而被迫跟随（以太坊社区的逻辑）。
---
# 感谢！
---
<img style="width: 1800px" src="./img/forks.drawio.svg" />