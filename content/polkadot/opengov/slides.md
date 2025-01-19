---
title: OpenGov
description: The Polkadot ecosystem on-chain governance solution
duration: 1 hour
---

---
title: OpenGov
description:  Polkadot 生态系统的链上治理解决方案
duration: 1小时
---

# OpenGov

Notes:

你好！

我是布拉德利·奥尔森。

曾是剑桥第一学院的学生。

目前是Parity公司的平行链核心团队成员。

要使 Polkadot 真正实现去中心化，就需要一个强大、灵活且民主的治理系统。

加文在过去一年左右的时间里投入了大量精力，精心打造了一个能体现这些理念的系统，即OpenGov。

我在这里为你概述OpenGov的相关情况，并提供一些关于它在Kusama上运行情况的新信息。

那就开始吧。

---

## 概述

<pba-flex center>

- 为什么需要区块链治理？
- 为什么要进行链上治理？
- 链上治理的目标是什么？
- 初始解决方案：治理V1
- 改进方案：OpenGov
- 运行情况如何？用数据说话。
- OpenGov与你

</pba-flex>

---

## 区块链治理的原因

<pba-flex center>

- 软件作为执行部门
  - 以预先定义的方式应用现有法律（代码）
  - 协议安全性确保严格遵守这些法律
- 但不断发展的协议需要一个立法部门
  - 用于更新法律（代码）
  - 纠正法律条文与精神不符的情况（漏洞）
  - 触发系统中未按固定时间表执行的部分
  - 支出国库资金
- 立法部门可以是链上或链下的

</pba-flex>

---

## 为什么要进行链上治理？

<pba-cols style="font-size:smaller">
<pba-col center>

- 链下治理

  - 开发团队提出正式提案
  - 进行讨论、辩论和媒体宣传活动
  - 硬分叉

- 存在的问题
  - 中心化
  - 吞吐量低
  - 决策周期长
  - 可访问性差

</pba-col>
<pba-col center>

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 300px" src="./img/Bitcoin.png" />

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 200px" src="./img/Ethereum.png" />

</pba-col>
</pba-cols>

---

## 链上治理的目标

<pba-flex center>

- **透明度**：由谁做出决策？依据什么规则？
- **去中心化**：权力分散，仅根据承诺和信念进行加权
- **安全性**：有害提案不会通过或其影响范围有限
- **可访问性**：易于起草提案、接收投票、进行投票以及委托投票
- **并发性**：在安全允许的范围内，最大限度地同时进行公投
- **速度**：每个公投在安全允许的范围内尽快完成
- **灵活性**：速度根据支持度/争议程度做出响应

</pba-flex>

---

## 治理V1

<pba-cols>
<pba-col center>

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 300px" src="./img/Polkadot.png" />

</pba-col>
<pba-col center style="font-size:smaller">

- 三权分立系统：公投、理事会和技术委员会
- 单轨制
- 每次只进行一项公投
- 根起源（无限权力！）
- 公投期为28天
- 最短实施期为1个月
- 紧急情况由技术委员会处理
- 理事会和技术委员会有权取消提案
- 大多数提案由理事会发起
- 完全由理事会控制的角色，如打赏者

</pba-col>
</pba-cols>

---

## 治理V1：有待改进之处

<pba-cols>
<pba-col left>

优点：

- 安全性
- 透明度

缺点：

- 去中心化程度不足
- 并发性差
- 速度慢
- 灵活性不足

</pba-col>
<pba-col center>

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 500px" src="./img/Snail.jpeg" />

</pba-col>
</pba-cols>

---

## OpenGov概述

<pba-cols>
<pba-col center>

- 起源和轨道
- 公投的生命周期
- 支持度和批准门槛曲线
-  Polkadot 联谊会
- 按轨道进行的投票委托
- OpenGov与治理目标

</pba-col>
<pba-col center>

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 500px" src="./img/overview.png" />

</pba-col>
</pba-cols>

---

## 起源

<pba-flex center>

- 代码执行的权限级别
- 类似于Unix系统中的用户
- 提案包含两部分
  - 操作：应该发生什么
  - 起源：谁授权进行操作
- 许多操作需要特定的起源

</pba-flex>

---

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 300px" src="./img/rail_road_tracks.jpeg" />

## 起源和轨道

- 每个起源都有一个对应的公投轨道
- 一个轨道可以服务于多个起源
- 这些轨道彼此完全独立
- 轨道示例：根、平行链管理员、大支出者、打赏者
- 紧急轨道：公投取消者、公投终止者

---

## 轨道参数

<pba-cols>
<pba-col>

参数使我们能够在安全性和吞吐量之间找到最佳平衡。

打赏者轨道的安全需求与根轨道的安全需求非常不同。

</pba-col>
<pba-col>
<pba-flex center>

- 前置期时长
- 决策期时长
- 确认期时长
- 最短实施期
- 并发性，即该轨道一次可以同时进行多少公投
- 支持度和批准门槛曲线

</pba-flex>
</pba-col>
</pba-cols>

---

<pba-cols>
<pba-col>

### OpenGov轨道

</pba-col>
<pba-col>

<img rounded style="width: 600px" src="./img/TracksTable.webp" />

</pba-col>
</pba-cols>

Notes:

突出白名单调用者轨道和根轨道参数之间的差异

---

## 提案通过的标准

- 批准率：赞成票/总投票数，按信念加权
  - 信念：锁定代币的时间越长，投票影响力越大，最长可达6倍，锁定期限为896天
- 支持率：赞成票/总可能投票池，不考虑信念

---

## 决策期和确认期

- 如果批准率和支持率达到门槛，确认期开始
- 在整个确认期内，批准率和支持率必须保持在各自的门槛之上
- 确认期结束 -> 提案提前批准
- 决策期到期 -> 提案被否决
- 只有一个决策期，在此期间，如果门槛未能持续达到，提案可能会多次进入和退出确认期

---

## 公投的生命周期

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 1000px" src="./img/lifecycle_of_a_referendum.png" />

Notes:

步骤顺序：**提案、前置期、决策期、确认期、实施期**

---

## 支持度和批准门槛曲线

<pba-flex center>

- 我们希望具备灵活性
  - 得到广泛支持的提案能够快速通过
  - 有争议的提案需要更多的审议
- 通过随时间变化的曲线来实现
  - 支持率门槛
    - 起始约为50%
    - 结束于该轨道的最低安全参与率（例如：大支出者轨道结束于0 + 极小值%）
  - 批准率门槛
    - 起始为100%
    - 结束于50 + 极小值%
- 以各轨道特定的安全需求所确定的速率单调递减

</pab-flex>

---

## 支持度和批准率曲线示例

<img rounded style="width: 1400px" src="./img/support_and_approval_curves.png" />

Notes:

来源于资源中的PolkaWorld文章

---

## 投票委托

<pba-cols style="font-size:smaller">
<pba-col center>

- 传统委托：你将所有事项的投票权委托给一个第三方
- 按轨道委托：你可以按轨道将投票权委托给一个或多个第三方
- 示例：将打赏者轨道的投票权委托给当地大使，将白名单调用者轨道的投票权委托给Parity Technologies，其他轨道的投票权自行保留
- 这可能是首创！

</pba-col>
<pba-col center>

<img rounded style="width: 500px" src="./img/vote.jpeg" />

</pba-col>
</pba-cols>

---

<img rounded style="width: 400px" src="../async-backing-deep/img/stopwatch.png" />

## OpenGov在压力下的运作

通往安全的典型途径：降低吞吐量并限制起源

但在紧急情况下，我们可能需要通过既需要根起源又具有时间紧迫性的提案！

解决方案：某种能够提供专家信息的预言机

---

## 专家信息的预言机化

<pba-flex center>

1. 跟踪每个人的专业水平
1. 允许专家表达观点
1. 根据专业水平汇总意见

</pba-flex>

> 但这些步骤是如何实现的呢？

---

<!-- .slide: data-background-color="#4A2439" -->

## 有请……<br /><br /> Polkadot 联谊会

---

一个纯粹的链上会员组织，旨在认可和奖励所有掌握并运用 Polkadot 专业知识且符合其广泛利益和理念的个人。

会员持有表示其经同行认可的专业水平和承诺的等级，对于更高等级的会员，还需通过全民公投认可。

---

##  Polkadot 联谊会的成员构成？

<pba-col center>

-  Polkadot 核心协议的专家，他们保持着持续的积极贡献
- 值得注意的是，这并不包括独立平行链协议的核心开发者，这些协议应根据需要开发自己特定的联谊会。
- 发展轨迹
  - 目前：不到100名核心开发者，大多来自Parity或Web3基金会
  - 未来一两年：数百名
  - 理想的遥远未来：数万名，且独立于任何中心化实体
-  Polkadot 和Kusama只有一个联谊会

</pba-col>

---

##  Polkadot 联谊会的职能

<pba-col center>

- 白名单调用者轨道
  - 根权限
  - 更灵活
  - 通过联谊会维持合理的安全性
- 白名单提案必须通过两轮投票
  - 通过第二个公投模块实例进行的基于专业知识加权的联谊会投票
  - 与其他轨道相同的全民公投，仍需DOT持有者的多数投票
- 只是一个预言机！
- 次要目的是在Parity之外培养长期的 Polkadot 核心开发者基础

</pba-col>

Notes:

强调作为一个预言机，联谊会本身不能采取任何行动。任何列入白名单的调用仍需要大量DOT持有者的支持。

---

## OpenGov与治理目标

<pba-col center>

- 开源 + 单一流程 + 轨道抽象 -> 透明度

<!-- .element: class="fragment" data-fragment-index="1" -->

- 自由提案创建 + 更高的吞吐量 + 按轨道委托 -> 可访问性

<!-- .element: class="fragment" data-fragment-index="2" -->

- 可访问性 + 无特殊机构 -> 去中心化

<!-- .element: class="fragment" data-fragment-index="3" -->

- 有限的起源 + 紧急轨道 + 白名单 -> 安全性

<!-- .element: class="fragment" data-fragment-index="4" -->

- 多个轨道 + 低风险轨道 -> 并发性

<!-- .element: class="fragment" data-fragment-index="5" -->

- 低风险轨道 + 提前确认 -> 速度

<!-- .element: class="fragment" data-fragment-index="6" -->

- 支持度和批准门槛曲线 + 白名单 -> 灵活性

<!-- .element: class="fragment" data-fragment-index="7" -->

</pba-col>

---

<!-- .slide: data-background-color="#000" -->

# OpenGov<br /><br />用数据说话

---

## 治理活动

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 900px" src="./img/proposals_per_day.png" />

> 每日治理活动增加了5.5倍

---

## 提案起源

<!-- 设置高度*宽度（单位：像素），全屏为1920*1080 -->
<img rounded style="width: 900px" src="./img/proposals_from_democracy.png" />

> 现在的提案主要通过民主方式提出

---

## 国库资金使用情况

<img rounded style="width: 900px" src="./img/treasury_waste_since_opengov.png" />

> 国库资金使用更加高效

---

## OpenGov与你

<pba-col center>

- 参与 Polkadot 和Kusama上的OpenGov和 Polkadot 联谊会
- 可以为每个平行链定制OpenGov实例
- 每个平行链可以有自定义的联谊会
- 有可能创建非技术类联谊会，例如品牌大使联谊会

</pba-col>

---

## 资源
<pba-col center>
1. [PolkaWorld的OpenGov实践指南](https://polkaworld.medium.com/a-hands-on-guide-for-kusamas-open-gov-governance-system-98277629b0c5)
1. [Moonbeam团队的OpenGov文章](https://moonbeam.network/blog/opengov/)
1. [加文在2022年波卡解码大会上的演讲](https://www.youtube.com/watch?v=EF93ZM_P_Oc)
1. [Gov（OpenGov和V1）跟踪](https://polkadot.polkassembly.io/opengov)
1. [OpenGov跟踪](https://kusama.subsquare.io/)
</pba-col>

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题
---
## OpenGov的实际操作
<pba-flex center>
- 投票 - <https://kusama.subsquare.io/referenda>
- 委托投票 - <https://kusama.subsquare.io/referenda>
- 提交提案 - <https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fkusama-rpc.dwellir.com#/referenda>
</pba-flex>