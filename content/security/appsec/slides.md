---
title: Application Security
description: Application Security session slides
duration: 60 min
---

# 应用程序安全

---

安全是一个平衡的问题，不多也不少。只有**适当的安全**。

<img rounded style="height: 600px" src="./img/adequate-security.jpg" />

---

安全关乎你的**残余风险**，而不是你已经防范了什么。

<img rounded style="height: 600px" src="./img/residual-risk.jpg" />

---

## 概述

<pba-flex center>

1. [保障软件开发生命周期（SDLC）的安全](#securing-sdlc)
1. [应用程序安全设计原则](#appsec-design-principles)
1. [应用程序安全的组成部分](#components-of-appsec)
1. [已知的攻击面和攻击向量](#known-attack-surfaces-and-vectors)

</pba-flex>

---

# 保障软件开发生命周期（SDLC）的安全

---

## 应用程序安全的全局视角

<img rounded style="height: 600px" src="./img/ssdlc.png" />

我们将多次回顾这张图。

---

## 通过控制措施实施安全

控制措施必须：

<pba-flex center>

- 设计
- 开发
- 实施
- 配置
- 运行
- 监控
- 改进

</pba-flex>

---

## 我们如何确定控制措施？

威胁利用漏洞从而对资产造成损害的可能性。

<img rounded style="height: 600px" src="./img/risk-risk-everywhere.jpg" />

---

## 如果你错过了：CIA 三元组

<img rounded style="height: 600px" src="./img/cia-triad.png" />

---

## 需要确保的事项

- **保密性**：确保只有授权人员能够访问其被授权的实体。
- **完整性**：确保只有授权实体进行授权的更改。
- **可用性**：确保在需要时数据始终可用。

---

## AAA + NR

- **身份验证**：你是谁
- **授权**：你被允许做什么
- **问责制**：谁负责
- **不可否认性**：不能否认你的参与

---

# 应用程序安全设计原则

_简要介绍_

---

## 足够好的安全

不要花 10000 美元买一个保险箱来保护一张 20 美元的钞票。

<img rounded style="height: 600px" src="./img/good-enough-security.png" />

---

## 最小权限原则

不要把你保险箱的钥匙给每个人，只给他们需要的。

<img rounded style="height: 600px" src="./img/least-privilege.jpg" />

---

## 职责分离

不要把创建发票、批准发票和汇款的权力交给一个人。

<img rounded style="height: 600px" src="./img/separation-of-duties.png" />

---

## 纵深防御

一座城堡有护城河、厚厚的城墙、受限的入口、防御制高点、内部的多个检查站等；你有什么？

<img rounded style="height: 600px" src="./img/defense-in-depth.png" />

---

## 故障安全

任何未被明确授权的功能默认被拒绝。

<img rounded style="height: 600px" src="./img/fail-safe.jpg" />

---

## 机制经济性

安全本身就是一个复杂的话题，不要让它变得更复杂（保持简单）。

<img rounded style="height: 600px" src="./img/economy-of-mechanism.png" />

---

## 完全仲裁

每一个关键操作在任何时候都必须进行验证。

<img rounded style="height: 600px" src="./img/complete-mediation.png" />

---

## 开放设计

别白费力气了：安全不能依赖于保密性。

<img rounded style="height: 600px" src="./img/open-design.png" />

---

## 最少公共机制

就像最稀有的钥匙，只能打开特定的锁，不常用，但一旦使用可能会造成重大损害。

<img rounded style="height: 600px" src="./img/least-common-mechanism.png" />

---

## 心理可接受性

如果用户不能无缝地使用你的安全控制措施，那就没有意义了。

<img rounded style="height: 600px" src="./img/psychological-acceptability.png" />

---

## 最薄弱环节

链条的强度取决于其最薄弱的环节。

<img rounded style="height: 600px" src="./img/weakest-link.png" />

---

## 利用现有组件

组件越少，攻击面越小，而且……

<img rounded style="height: 600px" src="./img/leverage-existing-components.png" />

---

## 单点故障

如果单点故障出现问题，意味着整个系统都会失败。

<img rounded style="height: 600px" src="./img/spof.png" />

---

## 保障软件安全其实很简单（！？）

---

<pba-flex center>

- **识别攻击面**
  你有哪些潜在的攻击面？
- **识别攻击向量**
  你有哪些潜在的攻击向量？
- **分配安全控制措施**
  基于风险的方法 + 安全控制措施

</pba-flex>

---

## 安全控制措施很简单（！？）

---

## 安全控制措施可以是：

- **指令性（防护措施 [主动型] - 即在事件发生之前）** * 政策就是一个例子。这规定了你可以做什么，不可以做什么。

<img rounded style="height: 600px" src="./img/directive.png" />

---

## 安全控制措施可以是：

- **威慑性（防护措施 [主动型] - 即在事件发生之前）**
  - 劝阻某人不要做坏事。例如，用监控摄像头监视人们。一旦他们知道自己被监视，就会有所顾忌。

---

### 威慑性

<img rounded style="height: 600px" src="./img/deterrent.png" />

---

## 安全控制措施可以是：

- **预防性（防护措施 [主动型] - 即在事件发生之前）** * 试图阻止某人做坏事。例如，密码就是一种预防性控制措施。

<img rounded style="height: 600px" src="./img/prevention.png" />

---

## 安全控制措施可以是：

- **检测性（应对措施 [反应型] - 即在事件发生时或之后）** * 试图检测事件。例如，日志。

<img rounded style="height: 600px" src="./img/reading-toilet.png" />

---

## 安全控制措施可以是：

- **纠正性（应对措施 [反应型] - 即在事件发生之后）** * 试图在事件发生后重新建立控制并纠正即时问题。

<img rounded style="height: 600px" src="./img/corrective.png" />

---

## 安全控制措施可以是：

- **恢复/复原性（应对措施 [反应型] - 即在事件发生之后）** * 试图重建并恢复正常。

<img rounded style="height: 600px" src="./img/restoration.png" />

---

## 实施起来很困难，抱歉

<pba-flex center>

- 安全编码实践
- 环境分离
- 适当的测试
- 验证和发现
- 缓解措施
- 根本原因分析
- 每个步骤都要有文档记录

</pba-flex>

---

# 应用程序安全的组成部分

---

<pba-flex center>

- **威胁建模**：手动或自动化
- **安全测试**：静态应用程序安全测试（SAST）、动态应用程序安全测试（DAST）、交互式应用程序安全测试（IAST）、软件成分分析（SCA）、运行时应用程序自我保护（RASP）
- **漏洞收集与优先级排序**：Jira、Asana

</pba-flex>

---

会有麻烦（**风险**），你需要应对这些麻烦。但该怎么做呢？

<img rounded style="height: 600px" src="./img/there-will-be-blood.png" />

---

## 风险管理，但该怎么做呢？

- **风险规避**：这种方法通过避免可能对组织产生负面影响的活动来减轻风险。

---

## 风险管理，但该怎么做呢？

- **风险降低**：这种风险管理方法旨在限制损失，而不是完全消除损失。它接受风险，但努力控制潜在损失并防止其扩散。

---

## 风险管理，但该怎么做呢？

- **风险分担**：在这种情况下，潜在损失的风险由一组人分担，而不是由个人承担。

---

## 风险管理，但该怎么做呢？

- **风险转移**：这涉及通过合同将风险转移给第三方。例如，为财产损坏或伤害投保将相关风险从财产所有者转移到保险公司。

---

## 风险管理，但该怎么做呢？

- **风险接受与保留**：在应用风险分担、风险转移和风险降低措施后，不可避免地仍会存在一些风险，因为几乎不可能消除所有风险。这种剩余的风险被称为残余风险。

---

## 漏洞披露计划与漏洞赏金计划

---

## 左移与右移

<img rounded style="height: 600px" src="./img/left-vs-right.png" />

---

# 已知的攻击面和攻击向量

---

## 已知的 Rust 漏洞

<pba-flex center>

1. Rust 特定问题
2. 不安全代码
3. 加密错误

</pba-flex>

---

## 已知的 Substrate 漏洞

<pba-flex center>

1. 测试不充分
2. 中心化漏洞
3. 特定模块漏洞

</pba-flex>

---

## 已知的 ink! 漏洞

<pba-flex center>

1. 访问控制不正确
2. 拒绝服务（DoS）
3. 时间戳依赖
4. 版本过时

</pba-flex>

---

**总结**：做好该死的输入验证，就没问题了！

<img rounded style="height: 600px" src="./img/input-validation.png" />

---

**问题**：如果没有城堡要保卫，你该如何保卫城堡？
