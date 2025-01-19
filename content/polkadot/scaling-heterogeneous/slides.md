---
title: "Blockchains Scaling 2: Modular and Heterogeneous"
duration: 45 mins
---

# 区块链扩容

## 模块化与异构化

---

## 模块化：解耦第一层（L1）的角色

- 排序：将操作按顺序排列作为状态转换函数（STF）的输入
  - 必要的，但总是在链下进行
- 对状态转换进行排序
  - 区块链的定义
  - 对于第二层（L2）的情况：将其他链的状态转换（通常是状态根）的承诺存储在 L1 链上
- 执行状态转换
  - 根据定义，L2 将执行过程移至链下
- 数据可用性
  - 将数据可用性（DA）与排序解耦通常被称为“模块化区块链”（如 Celestia）

---

## L2 的分类

<img rounded style="width: 550px" src="./img/L2Beat.png" />

Notes:

- <https://l2beat.com/scaling/summary>

---

## L2 的分类

- 侧链
  - 从 L1 继承排序
  - 诚实多数桥（例如 8 个多重签名中的 5 个）

---

## L2 的分类

- 智能合约Rollup 
  - 从 L1 继承排序和可用性
  - “最小信任”桥：
    - 状态转换函数（STF）的正确性通过有效性证明或欺诈证明来保证
    - 收件箱：通过基础层提出交易的选项，以避免排序器/提议者的审查

---

## L2 的分类

- 有效性Rollup 
  - 从 L1 继承排序
  - 最小信任桥
  - 链下数据可用性

Notes:

https://www.starknet.io/en/posts/developers/rollup-validium-volition-where-is-your-data-stored

---

## L2 的分类

- 主权Rollup 
  - 从 L1（甚至比特币，哈哈）继承排序和可用性
  - 没有最小信任桥：正确性和抗审查性完全在链下

Notes:

- <https://celestia.org/learn/sovereign-rollups/an-introduction/>
- <https://rollkit.dev/blog/sovereign-rollups-on-bitcoin-with-rollkit>

---

## 结算？

- 以太坊社区的许多人通过桥来定义Rollup 
  - 如果目的是扩展以太坊交易，这是有道理的
- Rollup 节点可以分叉Rollup 并指向不同的桥
  - L1 原生代币仍然被锁定
  - L2 原生代币保留价值
- 一些模块化共识层不允许结算： Polkadot（Polkadot）和 Celestia

Notes:

- <https://dba.mirror.xyz/LYUb_Y2huJhNUw_z8ltqui2d6KY8Fc3t_cnSE9rDL_o>

---

## 数据可用性

---

## 数据可用性

- 不是数据存储。仅在有限时间内可用。
- 两个目的：
  - 安全性：在乐观系统中验证状态转换函数（STF）的正确性（乐观Rollup 中为一周， Polkadot中约为 30 秒）
  - 活性：供排序器下载以便在此基础上构建。必须比 STF 正确性验证的时间稍长（ Polkadot中约为 1 天，danksharding 中为 30 天）
- 不能使用欺诈证明（渔夫困境）
- 最简单的选项是发布到 L1（以太坊调用数据）

---

## 数据可用性委员会（DAC）

- 每个节点冗余地保存数据
- 可以在链下或使用 L1 验证者委员会
- 向 L1 发布阈值签名以证明数据可用性
- 对于Rollup 用户来说，协调可能很昂贵：我的数据在哪个分片上？

---

## 数据可用性采样（DAS）

- 数据进行纠删编码
- 轻客户端可以通过随机采样节点来验证数据可用性
- 例如：Celestia（独立的数据可用性层）、Danksharding（以太坊路线图）、Polygon Avail（基于 Substrate 构建）、ZKPorter、Eigenlayer

Notes:

- <https://arxiv.org/abs/1809.09044>
- <https://github.com/availproject/data-availability/blob/master/reference%20document/Data%20Availability%20-%20Reference%20Document.pdf>

---

## 如何确保编码正确完成？

- 零知识简洁非交互式知识论证（SNARKs）：成本太高
- 欺诈证明：需要二维编码才能高效
- KZG 承诺：还允许分布式重建（分块）

---

## 二维里德 - 所罗门码

<pba-cols>
<pba-col>

<img rounded style="width: 400px" src="./img/2dReedSolomon.png" />

</pba-col>
<pba-col>

- 计算行和列的默克尔根
- 需要存储 2$\sqrt{n}$ 个状态根，而不是一个
- 允许 O($\sqrt{n}$) 的编码欺诈证明

</pba-col>
</pba-cols>

---

## Celestia 中的数据可用性

- 全节点在链下冗余地保存纠删编码的数据
- 轻客户端采样 50% 并参与共识
- 可能的激励问题：扩展数据比扩展执行更容易，因此独立的数据可用性层更容易受到打压

Notes:

- <https://docs.celestia.org/learn/how-celestia-works/data-availability-layer>

---

## Danksharding 中的数据可用性

- 使用 KZG 多项式承诺进行二维纠删编码
  - 还提供编码证明
  - KZG 需要可信设置，仪式在今年早些时候完成
- 分布式构建：没有节点需要构建所有行和列
- 分布式重建：
  - 分块和分片类似于 Polkadot
  - 由于二维编码，阈值更高
- 允许轻客户端通过采样达成共识
- 数据在 30 天后删除

Notes:

- <https://notes.ethereum.org/@vbuterin/proto_danksharding_faq>
- <https://dankradfeist.de/ethereum/2020/06/16/kate-polynomial-commitments.html>
- <https://www.youtube.com/watch?v=4L30t_6JBAg>

---

## Rollup 安全性

---

## 用于扩容的有效性证明

- 用于恒定空间区块链的递归证明（如 Mina）
- 零知识Rollup 
  - 事务性（私有或公共）：例如 Aztec
  - 特定应用：例如 STARKDex、Loopring
  - 智能合约：例如 ZEXE（Aleo）、zkEVM（Polygon、ZKSync、Scroll）

Notes:

- <https://medium.com/hackernoon/scaling-tezo-8de241dd91bd>
- <https://eprint.iacr.org/2020/352.pdf>
- <https://github.com/barryWhiteHat/roll_up>
- <https://zkhack.dev/whiteboard/>

---

## 乐观Rollup ：欺诈证明

“兑现支票时不必去法院——只有当支票退票时才需要去。”

- 提议者向 L1 发布一个状态根并缴纳押金
- 挑战者可以在规定时间内（通常为 7 天）提交欺诈证明
  - 如果成功，将获得部分押金作为奖励
- 欺诈证明可以是交互式的（如 Arbitrum）或非交互式的（如 Optimism）

Notes:

- <https://www.usenix.org/system/files/conference/usenixsecurity18/sec18-kalodner.pdf>

---

## Rollup 训练轮次

<div style="font-size: 0.82em;">

- 第 0 阶段
  - 链上数据可用性
  - 必须有收件箱
  - 没有状态转换函数（STF）正确性
- 第 1 阶段
  - 状态转换函数（STF）正确性（欺诈证明或有效性证明）
  - 8 个多重签名中的 6 个可以覆盖智能合约的安全性
  - 智能合约升级时，要么采用相同的多重签名阈值，要么采用与挑战期相同的延迟
- 第 2 阶段
  - 仅在出现错误（两个证明者实现之间存在差异）的情况下才能覆盖安全性
  - 升级必须有超过 30 天的延迟

</div>

Notes:

- <https://ethereum-magicians.org/t/proposed-milestones-for-rollups-taking-off-training-wheels/11571>

---

## Rollup 排序器

- 目前是中心化的（不像 Polkadot的整理者）
- 共享排序：例如 Espresso、OP Superchain
- 提议者 - 构建者分离

Notes:

- <https://docs.espressosys.com/sequencer/espresso-sequencer-architecture/readme>
- <https://stack.optimism.io/docs/understand/explainer/>
- <https://ethereum.org/nl/roadmap/pbs/>

---

## 乐观Rollup ：无许可？

- 垃圾状态根会使链停滞
  - Arbitrum 允许发布多个（分叉和修剪），类似于中本聪共识
- 垃圾挑战会延迟确认
  - 它们通常必须单独且按顺序执行，以防止勾结
  - Arbitrum BOLD 允许同时执行挑战，时间限制为 7 天
- 垃圾信息需要许可的提议者/验证者集

Notes:

- <https://github.com/OffchainLabs/bold/blob/main/docs/research-specs/BOLDChallengeProtocol.pdf>
- <https://medium.com/offchainlabs/solutions-to-delay-attacks-on-rollups-434f9d05a07a>

---

## 乐观Rollup ：验证者困境

- 挑战奖励不足以激励验证所有状态根
  - 提议者在 L1 上不会面临赌徒破产的风险
  - 验证者不会因执行有效的状态转换而获得奖励
- 注意力挑战

Notes:

- <https://medium.com/offchainlabs/the-cheater-checking-problem-why-the-verifiers-dilemma-is-harder-than-you-think-9c7156505ca1>
- <https://medium.com/offchainlabs/cheater-checking-how-attention-challenges-solve-the-verifiers-dilemma-681a92d9948e>

---

## Rollup 安全假设

- 乐观Rollup 仅与其验证者一样安全
  - 通常是中心化的或小型许可集
  - 没有与 L1 验证类似的激励措施
  - 声誉损害论点
- 桥接速度慢
  - 流动性提供者（LPs）可以为小额交易提供退出流动性……
  - ……但随后少数大户会检查所有Rollup 

---

##  Polkadot与其他Rollup 协议相比如何？

<img rounded style="width: 700px" src="./img/polkadot-rollup.png" />

- 批准检查是一个去中心化的共享监视网络
-  Polkadot的价值主张是在模块化堆栈中做出一致的安全假设
