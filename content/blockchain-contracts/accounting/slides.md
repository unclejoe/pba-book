---
title: Accounting Models & User Abstractions in Blockchains
---

# 区块链中的账户模型与用户抽象

---

## 概述

- 密码学、签名、哈希函数、基于哈希的数据结构
  <!-- .element: class="fragment" data-fragment-index="2" -->
- 经济学/博弈论
  <!-- .element: class="fragment" data-fragment-index="3" -->
- 区块链结构
  <!-- .element: class="fragment" data-fragment-index="4" -->

---

## 我们接下来要做什么？

- 我们有了一些基本元素、想法和概念
  <!-- .element: class="fragment" data-fragment-index="2" -->
- 现在，让我们把它们组合成一些很酷的东西……
  <!-- .element: class="fragment" data-fragment-index="3" -->

---

## 我们在讨论什么？

<pba-cols>
<pba-col style="font-size:smaller">

- 现在我们有了这个结构化的去中心化防篡改状态机……
- 让我们想想如何从表示用户的角度来构建一个状态和状态转换

</pba-col>

<img rounded style="width: 400px;float:right; margin-right:5px" src="./img/3.2-thinking-cartoon.png" />

---

## 状态用户模型

<img rounded style="width: 1200px;" src="./img/utxo_state_1.svg" />

---

## 状态用户模型

<img rounded style="width: 1200px;" src="./img/utxo_state_2.svg" />

---

## 如何表示乔希和安德鲁？

<img rounded style="width: 1200px;" src="./img/utxo_state_1.svg" />

---

## 用户表示

<img rounded style="width: 1200px;" src="./img/utxo_state_3.svg" />

---

## 如何从乔希转账给安德鲁？你需要什么？

<img rounded style="width: 1200px;" src="./img/utxo_state_4.svg" />

备注：

如果我们弄错了会有什么灾难性后果？

---

## 如果我们想花这笔钱怎么办？

<img rounded style="width: 1200px;" src="./img/utxo_state_5.svg" />

备注：

为什么我们在这里说“花费”而不是“修改”？

---

## 输入

<img rounded style="width: 1200px;" src="./img/utxo_transaction_1.svg" />

---

## 交易

<img rounded style="width: 1200px;" src="./img/utxo_transaction_2.svg" />

备注：

为什么我们不把 70 全部转给安德鲁？

---

## 如何验证这个状态变化是有效的？

- 我们实际上可以通过签名验证来花费这个东西！
- 输入的总和 >= 输出的总和
- 没有价值为 0 的硬币
- 这个东西之前已经被花费过了吗？

备注：

我忘了哪一个？

---

## 我们的新状态

<img rounded style="width: 1200px;" src="./img/utxo_state_6.svg" />

---

## 我们如何超越货币进行泛化？

<img rounded style="width: 1200px;" src="./img/utxo_state_7.svg" />

---

## 我们如何超越货币进行泛化？

<img rounded style="width: 1200px;" src="./img/utxo_state_8.svg" />

备注：

现在我们要如何验证状态转换是有效的？

---

## 交易

<img rounded style="width: 1200px;" src="./img/utxo_transaction_3.svg" />

---

## 交易

<img rounded style="width: 1200px;" src="./img/utxo_transaction_4.svg" />

---

## 这是一个好模型吗？为什么？让我们讨论一下

- 可扩展性
- 隐私性
- 通用计算

---

## 有没有其他方法？

<img rounded style="width: 1200px;" src="./img/utxo_state_7.svg" />

备注：

现在引导他们想到账户的解决方案

---

## 账户

<img rounded style="width: 1200px;" src="./img/accounts_state_1.svg" />

备注：

现在引导他们想到账户的解决方案

---

## 账户状态转换

<img rounded style="width: 1200px;" src="./img/accounts_transaction_1.svg" />

---

## 账户状态转换

<img rounded style="width: 1200px;" src="./img/accounts_transaction_2.svg" />

---

## 我们如何验证和处理这笔交易？

- 验证乔希的账户中有足够的资金
- 验证这笔金额加上安德鲁的金额不超过最大值
- 检查交易的随机数
- 进行输出值的实际计算

备注：

我有没有遗漏什么？

---

## 账户状态转换

<img rounded style="width: 1200px;" src="./img/accounts_transaction_3.svg" />

---

## 与 UTXO 模型相比，我们在账户模型中做了哪些不同的处理？

备注：

进行验证而不是确定结果。
在交易中不提交输出状态

---

## 账户任意数据

<img rounded style="width: 1200px;" src="./img/accounts_state_2.svg" />

---

## 这是一个好模型吗？为什么？让我们讨论一下

- 可扩展性
- 隐私性
- 通用计算

备注：

并行处理？存储空间、隐私解决方案？

---

## 小推荐……燕尾服 👔

> <https://github.com/Off-Narrative-Labs/Tuxedo>

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题