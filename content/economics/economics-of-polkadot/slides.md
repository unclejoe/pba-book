---
title: The Economics of Polkadot
description: Tokenomics of Relay Chains and more
duration: 2 hour
---

# Polkadot的经济学
---
## 概述
<ul>
<li class="fragment">哪些经济要素构建了Polkadot网络？</li>
<li class="fragment">它们的机制和激励措施是什么？</li>
<li class="fragment">这些要素如何相互关联？</li>
</ul>
<p class="fragment">备注：目前有许多已规划的变动</p>
---
<img rounded style="width: 1200px; margin-right: 50px;" src="./img/polkadot_pieces.svg" />
---
# 代币经济学
---
## DOT代币
<ul>
<li class="fragment">Polkadot网络的原生代币。</li>
<li class="fragment">1 DOT = \(1\mathrm{e}{10}\) 普朗克</li>
<li class="fragment">
    普朗克是最小的记账单位。
    <ul>
        <li class="fragment">参考普朗克长度，即物理学中可能的最小距离。</li>
    </ul>
</li>
<li class="fragment">
    具有多种用途的 <strong>实用型代币</strong>：
    <ul>
        <li class="fragment">治理（去中心化）</li>
        <li class="fragment">插槽拍卖中的质押（实用性）</li>
        <li class="fragment">质押（安全性）</li>
        <li class="fragment">消息传递（例如转账）</li>
    </ul>
</li>
</ul>
---
## 通胀模型
<ul>
<li class="fragment">代币供应的扩张。</li>
<li class="fragment">凭空铸造代币。
  <ul>
  <li class="fragment">用于支付验证者和提名者的质押奖励。</li>
  <li class="fragment">（间接）为国库提供资金。</li>
  </ul>
</li>
<li class="fragment">该模型的核心经济变量为：</li>
<li class="fragment"><strong>外生变量</strong>：
  <ul>
  <li class="fragment">质押率（质押的DOT总量/ DOT总量）。</li>
  </ul>
</li>
<li class="fragment"><strong>内生变量</strong>：
  <ul>
  <li class="fragment">最优质押率（为验证者提供合理安全性的充足支持）。</li>
  </ul>
</li>
<li class="fragment">总通胀率（10%）。</li>
</ul>
---
## 通胀模型
<ul>
<li class="fragment">DOT的不同 <strong>状态</strong>：
  <ul>
  <li class="fragment"><strong>流动状态</strong>：用于消息传递和市场流动性。</li>
  <li class="fragment"><strong>质押绑定状态</strong>：保障网络安全的经济力量。</li>
  <li class="fragment"><strong>平行链绑定状态</strong>：平行链对DOT代币的需求。</li>
  </ul>
</li>
<li class="fragment">目标是在这三种代币状态之间达成（某种）<strong>合理的比例</strong>。</li>
</ul>
---
## 通胀模型
<div style="display: flex; justify-content: center; align-items: center;">
    <div style="flex: 1; text-align: center;">
        <img rounded style="width: 650px; margin-right: 50px;" src="./img/current_inflation_model_polkadot.png" />
    </div>
    <div style="flex: 1;">
        <ul>
            <li class="fragment"><strong>核心变量</strong>：理想质押率（目前约为53.5%）。</li>
            <li class="fragment">在理想质押率下，质押奖励最高。</li>
            <li class="fragment">当质押率低于（高于）最优水平时，有激励措施来（提高）降低质押率。</li>
            <li class="fragment">质押效率低下的部分进入国库。</li>
            <li class="fragment">理想质押率会随着活跃平行链数量的变化而调整（每条平行链使理想质押率降低0.5%）。</li>
        </ul>
    </div>
</div>
---
## 通胀
<ul>
    <li class="fragment">在法定货币世界中，通胀带有负面含义。</li>
    <li class="fragment">这是经济学中的常见讨论话题。</li>
    <li class="fragment">我对此的看法是：</li>
    <ul>
        <li class="fragment">可预测的（最高限度的）通胀是有益的。</li>
        <li class="fragment">它激励人们使用代币（例如为优质平行链质押，用于消息传递）。</li>
        <li class="fragment">通缩可能导致经济活动停滞，因为人们会开始囤积代币。</li>
    </ul>
</ul>
备注：
问题：你对通胀有何看法？
---
## 即将到来的潜在变化
<ul>
    <li class="fragment">当前系统激励将质押率调整至理想水平。</li>
    <li class="fragment">届时，国库的流入将为0 DOT。</li>
    <li class="fragment">这是不可持续的。</li>
    <li class="fragment">提议的变更：将质押者的通胀与总通胀分离，直接将剩余部分转入国库。</li>
</ul>
---
## 质押：概念
<ul>
    <li class="fragment"><strong>提名权益证明（NPoS）</strong>。</li>
    <li class="fragment"><strong>验证者</strong>和 <strong>提名者</strong> 的经济激励与网络的激励一致。</li>
    <ul>
        <li class="fragment">良好行为会获得质押奖励。</li>
        <li class="fragment">恶意/疏忽行为将受到惩罚（被扣除质押）。</li>
    </ul>
    <li class="fragment">目前，最低总质押量约为160万DOT。</li>
    <li class="fragment">系统中的总质押量直接转化为其所提供的 <strong>经济安全性</strong>。</li>
    <li class="fragment">总质押量由验证者（自质押）及其提名者（提名质押）共同提供。</li>
    <ul>
        <li class="fragment">高度包容性</li>
        <li class="fragment">高度安全性</li>
        <li class="fragment">目标是让参与者尽可能多地承担风险。</li>
    </ul>
</ul>
---
## 验证者
<ul>
    <li class="fragment">验证者具备弹性的因素：</li>
    <ul>
        <li class="fragment">自质押</li>
        <li class="fragment">声誉（身份）</li>
        <li class="fragment">未来的高额回报（自质押 + 佣金）</li>
    </ul>
</ul>
---
## 提名者
<ul>
    <li class="fragment">为他们认为值得信赖的最多16个验证者质押代币。</li>
    <li class="fragment">他们有动力寻找最符合自身偏好的验证者。</li>
    <li class="fragment">他们的任务是共同管理活跃验证者集合。</li>
</ul>
---
## 奖励
<pba-cols>
<pba-col>
<pba-flex center>
<img rounded style="width: 700px;" src="./img/rewards_flow.png" />
</pba-flex>
</pba-col>
<pba-col>
<ul>
    <li class="fragment"><em>质押奖励的用途是什么？</em></li>
    <li class="fragment"><strong>验证者</strong>：用于硬件、网络和维护成本，增强 <strong>弹性</strong>。</li>
    <li class="fragment"><strong>提名者</strong>：管理活跃验证者集合，筛选出优质验证者（无形之手）。</li>
</ul>
</pba-col>
</pba-cols>
---
## 验证者选择
<ul>
    <li class="fragment">提名者的工作是寻找并挑选合适的验证者。</li>
    <li class="fragment">提名者在选择验证者时面临多种权衡：</li>
    <ul>
        <li class="fragment">安全性、性能、去中心化程度</li>
        <li class="fragment">理想情况下，要考虑这些变量的历史时间序列。</li>
    </ul>
    <li class="fragment">经济背景：</li>
    <ul>
        <li class="fragment">自质押是参与者承担风险的主要指标。</li>
        <li class="fragment">在其他条件相同的情况下，更高的佣金会给予验证者更多合规行为的激励。</li>
    </ul>
    <li class="fragment">多种信任来源</li>
    <li class="fragment">高效的验证者推荐是我的研究课题之一。</li>
</ul>
---
# 平行链
---
## 什么是平行链？
<ul>
    <li class="fragment">平行链（或核心链）是协议的第一层部分。</li>
    <li class="fragment">可并行运行的独立区块链。</li>
    <ul>
        <li class="fragment">高度特定于领域，在架构上具有高度灵活性。</li>
        <li class="fragment">共享相同的消息传递标准，通过中继链实现互操作和消息交换。</li>
    </ul>
    <li class="fragment">Polkadot有43条平行链，Kusama有46条平行链。</li>
    <li class="fragment">它们的状态转换函数（STF）在中继链上注册。</li>
    <ul>
        <li class="fragment">验证者无需了解平行链上的所有数据即可验证状态转换。</li>
        <li class="fragment">收集者维持平行链的运行（但对安全性并非必需）。</li>
    </ul>
    <li class="fragment">为网络提供实用功能。</li>
</ul>
---
## 平行链插槽
<ul>
    <li class="fragment">对网络的访问被抽象为 “插槽” 这一概念。</li>
    <ul>
        <li class="fragment">在Polkadot上的租赁期约为2年（在Kusama上约为1年）。</li>
        <li class="fragment">可用插槽数量有限（受网络限制）。</li>
        <li class="fragment">插槽通过蜡烛拍卖分配。</li>
    </ul>
    <li class="fragment">绑定的代币（无信任地）托管在中继链上。</li>
    <li class="fragment">插槽到期后，代币将被退还。</li>
</ul>
---
## 经济逻辑
<ul>
    <li class="fragment">代币无法用于其他用途（质押、交易、流动性、治理）。</li>
    <ul>
        <li class="fragment">这意味着被锁定的代币会产生机会成本。</li>
        <li class="fragment">一个近似值是质押的无风险回报率。</li>
    </ul>
    <li class="fragment">平行链需要与这些成本竞争，并产生超过这些机会成本的收益。</li>
    <ul>
        <li class="fragment">获得足够的众贷奖励。</li>
        <li class="fragment">链上有足够的经济活动以证明续约的合理性。</li>
    </ul>
    <li class="fragment">插槽机制创造了对DOT代币的持续需求。</li>
    <li class="fragment">成为并维持平行链的地位成本高昂。</li>
    <ul>
        <li class="fragment">这是一种筛选有用平行链的自然选择机制。</li>
        <li class="fragment">持续面临筹集资金以延长插槽租期的压力。</li>
    </ul>
</ul>
---
## 平行链获得了什么？
<ul>
    <li class="fragment"><strong>平行链为安全付费</strong>。</li>
    <ul>
        <li class="fragment">每条平行链都与中继链一样安全。</li>
        <li class="fragment">Polkadot是一个具有网络效应的安全联盟。</li>
        <li class="fragment">不仅实现了交易数量的扩展，还实现了安全性的提升。</li>
    </ul>
    <li class="fragment">由于金融资源有限，安全保障如同一块大小有限的蛋糕。</li>
    <li class="fragment">每条自我保障的链都需从蛋糕中切取一块，这就减少了留给其他链的份额（零和博弈）。</li>
    <li class="fragment">共享安全协议能够使蛋糕保持完整，并将其分配给所有参与者。</li>
    <li class="fragment">共享安全是一种扩展机制，因为保障100个分片所需支付给质押者的质押量，少于保障100条独立链所需的质押量。</li>
</ul>
---
## Polkadot 2.0展望
<ul>
    <li class="fragment">基于Gav在2023年Polkadot解码大会上的主题演讲。</li>
    <li class="fragment">Polkadot整个系统的全新理念。</li>
    <li class="fragment">我们不再将平行链视为独立的个体，而是将Polkadot看作全球分布式计算机。</li>
    <li class="fragment">关注的重点是空间和应用程序，而非链。</li>
    <li class="fragment">这台计算机拥有计算核心，可灵活分配给有需求的应用程序。</li>
    <li class="fragment">核心时间可购买、共享和转售。</li>
</ul>
---
## 区块空间的核心属性
<ul>
    <li class="fragment"><strong>安全性</strong>：区块链中最稀缺的资源，对于防止可能危及交易的共识故障或51%攻击至关重要。</li>
    <li class="fragment"><strong>可用性</strong>：确保区块空间可随时使用，且不会出现长时间等待或成本不确定的情况，以实现去中心化生态系统内的顺畅交互。</li>
    <li class="fragment"><strong>灵活性</strong>：区块空间能够由用户根据特定用例进行微调的能力。</li>
</ul>
---
## 区块空间生态系统
<ul>
    <li class="fragment">由各个区块空间生产者（区块链）组成的网络集合，提供安全、适用、高效分配且成本效益高的区块空间。</li>
    <li class="fragment">区块空间生态系统的一个重要价值在于其共享安全和可组合性的连接架构。</li>
    <li class="fragment">Dapp开发者或区块空间提供者可以专注于自身独特功能，复用生态系统内现有的能力。</li>
    <li class="fragment">例如，一个供应链追溯应用程序可以使用不同类型的区块空间进行身份验证、资产代币化和来源追溯。</li>
</ul>
---
## 批量市场
<ul>
    <li class="fragment">其运作方式尚未最终确定，但可能如下：</li>
    <ul>
        <li class="fragment">约75%的核心分配给市场。</li>
        <li class="fragment">核心由经纪人以NFT形式出售，租期为4周。</li>
        <li class="fragment">未出租的核心进入即时市场。</li>
        <li class="fragment">价格随需求上下波动。</li>
        <li class="fragment">当前租户对其租用的核心拥有优先购买权。</li>
    </ul>
</ul>
---
## 为何做出改变？
<ul>
    <li class="fragment">这使得人们能够以较低的门槛将代码部署到核心并进行测试。</li>
    <li class="fragment">这提高了区块空间的使用效率，因为并非所有团队都能或都希望每6/12秒生成一个完整区块。</li>
</ul>
---
# 国库
<ul>
    <li class="fragment">国库是一个链上资金库，持有DOT代币，由网络的所有代币持有者共同管理。</li>
    <li class="fragment">这些资金来源包括：</li>
    <ul>
        <li class="fragment">交易手续费</li>
        <li class="fragment">罚没资金</li>
        <li class="fragment">质押效率低下产生的资金（实际质押率与最优质押率的偏差部分）</li>
    </ul>
    <li class="fragment">通过治理机制，每个人都可以提交提案以启动国库资金支出。</li>
    <li class="fragment">目前国库持有约4600万DOT。</li>
    <li class="fragment">通过一种燃烧机制激励资金支出（每26天燃烧1%）。</li>
</ul>
---
## 作为去中心化自治组织（DAO）的国库
<ul>
    <li class="fragment">这是一个DAO（去中心化自治组织），能够获取资金，并能依据集体（在网络中有既得利益者）的指示做出资金决策 。</li>
    <li class="fragment">它蕴含着巨大潜力，或许尚未被人们充分认知。</li>
    <li class="fragment">这赋予了区块链自我维持和发展的资金能力，未来它将用于……</li>
    <ul>
        <li class="fragment">……支付给 <strong>核心开发者</strong> 以改进协议。</li>
        <li class="fragment">……支付给 <strong>研究人员</strong> 用于探索新方向、解决问题，并开展对网络有益的研究。</li>
        <li class="fragment">……支付用于开展 <strong>教育大众</strong> 了解该协议的活动。</li>
        <li class="fragment">……支付用于 <strong>系统平行链</strong>（开发及整理）。</li>
    </ul>
    <li class="fragment">这是一个真正去中心化且能自我维持的组织。</li>
</ul>
---
# 这些如何相互配合？
---
<div style="display: flex; justify-content: center; align-items: center;">
    <img src="./img/Polkadot-Economics-Cycle.svg" style="width: 1200px;" />
</div>
---
## 要点总结
<ul>
    <li class="fragment">Polkadot是一个提供共享安全和跨链消息传递的系统。</li>
    <li class="fragment">安全性具有扩展性，即保障100条平行链所需的质押比保障100条独立链更少。</li>
    <li class="fragment">DOT代币获取平行链提供的效用，并将其转化为安全性。</li>
    <li class="fragment">插槽机制（续约、拍卖）创造了一个市场，平行链要想可持续发展，需在这个市场中战胜机会成本（即它们需要发挥作用 ）。</li>
    <li class="fragment">Polkadot是一个能够为自身的存续和发展提供资金的DAO。</li>
    <li class="fragment">Polkadot 2.0将带来诸多变化，打造一个更加灵活的系统。</li>
</ul>
---
## 更多资源
- [敏捷核心时间RFC](https://github.com/polkadot-fellows/RFCs/pull/1)
- [关于改变通胀模型的讨论](https://forum.polkadot.network/t/adjusting-the-current-inflation-model-to-sustain-treasury-inflow/3301)
- [关于Polkadot 2.0的讨论](https://www.youtube.com/watch?v=GIB1WeVuJD0)
- [Polkadot上的提名与验证者选择](https://www.polkadot.network/blog/nominating-and-validator-selection-on-polkadot/) 