---
title: Cybersecurity Overview
description: "Key drivers around Cyber Security, Introduction to the other modules
  Security Awareness (40/50mn)
  User Centric Security in Web3 (40/50mn)
  Infrastructure Security (40/50mn)
  Application Security (1h)"
duration: 30 minutes
---

# 网络安全概述

---

## 大纲

<pba-flex center>

1. 威胁态势
2. 风险管理
3. 发展
4. 结论
5. 问答

</pba-flex>

Notes:

- 威胁态势
  - 主要威胁行为者
  - 最大的加密货币盗窃案
  - 加密货币事件
- 风险管理
  - 固有风险与剩余风险
  - 攻击的关键步骤
  - 文化的重要性
- 发展
  - 发展及重点关注领域
  - 持续集成/持续交付（CI/CD）
- 结论
- 问答

---

#### 网络威胁 - 六大主要行为者

<img rounded style="width: 1300px" src="./img/2-landscape.png" />

Notes:

不同的行为者有不同的动机，但作案手法有共性。

---

#### 最大的加密货币损失

<img rounded style="width: 900px" src="./img/3-loss.png" />

> 有些是庞氏骗局，大多数是漏洞/攻击事件

Notes:

在加密货币生态系统中已经发生了许多网络事件！
<https://medium.com/ngrave/the-history-of-crypto-hacks-top-10-biggest-heists-that-shocked-the-crypto-industry-828a12495e76>

---

#### 更多近期的加密货币事件

<img rounded style="width: 1300px" src="./img/4-incidents.png" />

> 强大的网络控制基础可以降低事件发生的风险。

Notes:

- <https://www.forbes.com/sites/ninabambysheva/2022/12/28/over-3-billion-stolen-in-crypto-heists-here-are-the-eight-biggest/?sh=5d411c13699f>
- <https://www.zdnet.com/article/iota-cryptocurrency-shuts-down-entire-network-after-wallet-hack/>
- <https://news.bitcoin.com/kucoin-boss-on-strategy-after-hack-we-chose-to-act/>
- <https://www.halborn.com/blog/post/explained-the-ronin-hack-march-2022>
- <https://www.bitdefender.com/blog/hotforsecurity/cryptocurrency-monero-website-hacked-original-binaries-replaced/>
- <https://www.coindesk.com/markets/2020/02/10/new-crypto-exchange-altsbit-says-it-will-close-following-hack/>
- <https://peckshield.medium.com/akropolis-incident-root-cause-analysis-c11ee59e05d4>
- <https://www.coindesk.com/markets/2020/12/21/bitgrail-operator-may-have-hacked-own-exchange-to-steal-120m-police-allege/>
- <https://coinmarketcap.com/academy/article/bitgrail-hack-one-of-the-largest-crypto-hacks-in-history>
- <https://www.coindesk.com/tech/2020/04/08/hacker-exploits-flaw-in-decentralized-bitcoin-exchange-bisq-to-steal-250k/>
- <https://www.coindesk.com/policy/2021/02/20/cryptopia-exchange-currently-in-liquidation-gets-hacked-again-report/>
- <https://bravenewcoin.com/insights/cryptopia-exchange-liquidator-releases-third-report>
- <https://www.elementus.io/blog-post/cryptopia-hack-transparency>
- <https://blog.merklescience.com/hacktrack/hack-track-eterbase-cryptocurrency-exchange>
- <https://www.reuters.com/investigates/special-report/fintech-crypto-binance-dirtymoney/>

---

#### 信息安全与网络风险 - 分类法

<img rounded style="width: 1300px" src="./img/5-taxonomy.png" />

Notes:

当威胁利用漏洞时，就会产生风险。
通常，威胁是无法被影响的，而漏洞是可以被影响的。
威胁和漏洞都会随着时间的推移，基于多种因素而演变，因此部署控制措施来识别、预防、检测以及应对和恢复这些威胁和漏洞非常重要（美国国家标准与技术研究院，NIST）。

---

#### 分类法 - 威胁示例

- 网络罪犯：在过去的12个月里，网络犯罪活动增加了200%。
- 内部人员/心怀不满的员工：资源方面有了很大的变化。
- 黑客活动分子：加密货币项目和Web3有一些反对者。
- 恐怖分子：他们越来越多地将网络作为一种武器。
- 国家：中国、朝鲜、俄罗斯/乌克兰等地缘政治的演变。
- “政府”：对加密货币领域有很多监管审查。
- 媒体：Web3和加密货币的发展经常出现在媒体上。
- 竞争对手：波卡（Polkadot）的方法是一个游戏规则改变者。

---

#### 什么是网络风险管理？

<img rounded style="width: 1300px" src="./img/7-risk.png" />

---

#### 什么是网络风险管理？

<img rounded style="width: 1300px" src="./img/8-risk.png" />

---

#### 固有风险和剩余风险

<img rounded style="width: 900px" src="./img/9-inherent.png" />

> 对固有风险的可见性有助于形成对重点关注领域和优先级的共识。

Notes:

- 识别固有风险是基础。包括与资产所有者合作。特别是从影响的角度来看。

- 控制措施的关键在于：
  - 降低初始入侵的可能性。
  - 一旦攻击者站稳脚跟，限制入侵的影响。

并且要提高尽快检测到入侵的能力。

从固有风险入手是基础，因为威胁态势会不断演变，包括控制措施的有效性。

---

#### 攻击杀伤链

<img rounded style="height: 700px" src="./img/10-attack.png" />

Notes:

通常，攻击者不会直接攻击目标，而是：

1. 利用可用的数字足迹（领英个人资料、DNS记录、网站、代码仓库、第三方信息，任何公开可用的信息）收集信息。
2. 利用可用信息和漏洞创建“武器”来准备攻击。
3. 通过可用渠道传递“武器”：电子邮件（专业/个人）、USB、WhatsApp/Signal/Telegram、网页（合法或域名劫持）、代码更新等。
4. 在受害者系统上使用传递的“武器”来执行代码。
5. 在目标上获得立足点。
6. 顺利地横向移动以达到目标，包括在一段时间内保持隐藏。
7. 执行最终目标：勒索、拒绝服务、数据泄露、数据破坏、资金盗窃。

---

#### 文化的重要性

<img rounded style="width: 1300px" src="./img/11-culture.png" />

---

#### 信息安全与网络风险 - 嵌入式

在每个步骤中嵌入安全并与关键成功因素合作：

<pba-cols>
<pba-col>

- 前期威胁建模
- 同行代码审查
- 代码扫描
- 独立安全代码审查

</pba-col>
<pba-col>

- 渗透测试（pentest）
- 密钥管理
- 供应链管理
- 监控
- 操作手册

</pba-col>
</pba-cols>

---

#### 信息安全与网络风险 - 持续集成/持续交付（CI/CD）

<img rounded style="width: 11 00px" src="./img/13-cicd.png" />

Notes:

这是一个持续的过程，在每个步骤中都要进行。

---

#### 结论

<img rounded style="width: 1300px" src="./img/14-conclusion.png" />

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题

---

#### 下一次实践课程

- 安全意识（40 - 50分钟）：
  背景和对手、攻击面和社会工程学
- Web3中的以用户为中心的安全（40 - 50分钟）：
  钱包格局、密钥管理和用户设备保护
- 基础设施安全（40 - 50分钟）：
  集中化、平台下架、供应链风险、节点上的密钥管理和基础设施的密码管理
- 应用程序安全（60分钟）：
  保障软件开发生命周期（SDLC）的安全、应用程序安全的组成部分以及已知的攻击面和攻击向量

---

#### 附录 - 路灯效应

<img rounded style="width: 1100px" src="./img/18-street.png" />
