# Summary

[🏠 起点](README.md)
[🪄 如何使用本书](./contribute/how-to/page.md)
[📒 本书概览](overview.md)

# 🧰 完整内容

---

- [🔐 加密学](./cryptography/index.md)

  - [加密学简介](./cryptography/intro/page.md)
  - [地址和密钥](./cryptography/addresses/page.md)
    - [`subkey` 演示](./cryptography/_materials/subkey-demo.md)
  - [哈希函数](./cryptography/hashes/page.md)
  - [破解多次密码本](./cryptography/_materials/many-time-pad.md)
  - [加密算法](./cryptography/encryption/page.md)
  - [数字签名基础](./cryptography/basic-signatures/page.md)
  - [数字签名进阶](./cryptography/advanced-signatures/page.md)
  - [基于哈希的数据结构](./cryptography/hash-based-data-structures/page.md)
  - [奇异原语](./cryptography/exotic-primitives/page.md)
  - [现实环境中的加密学](./cryptography/in-context/page.md)

- [🪙 经济学与博弈论](./economics/index.md)

  - [基础知识](./economics/basics/page.md)
  - [博弈论](./economics/game-theory/page.md)
  - [价格发现机制](./economics/price-finding-mechanisms/page.md)
  - [集体决策](./economics/collective-decision-making/page.md)
  - [Polkadot 经济学](./economics/economics-of-polkadot/page.md)

- [⛓️ 区块链和智能合约](./blockchain-contracts/index.md)

  - [概览](./blockchain-contracts/overview/page.md)
  - [Rocket Cash](./blockchain-contracts/_materials/rocket-cash.md)
  - [数字服务即状态机](./blockchain-contracts/services-as-state-machines/page.md)
  - [从零开始构建区块链](./blockchain-contracts/_materials/from-scratch.md)
  - [点对点网络 (P2P)](./blockchain-contracts/p2p/page.md)
  - [从零开始构建区块链](./blockchain-contracts/_materials/from-scratch.md)
  - [平台无关字节码](./blockchain-contracts/bytecode/page.md)
  - [Wasm 执行器](./blockchain-contracts/_materials/wasm-executor.md)
  - [区块链的结构](./blockchain-contracts/structure/page.md)
  - [从零开始构建区块链](./blockchain-contracts/_materials/from-scratch.md)
  - [共识出块](./blockchain-contracts/authoring/page.md)
  - [手动共识 (aka BitStory)](./blockchain-contracts/_materials/manual-consensus-bitstory.md)
  - [区块链经济学与博弈论](./blockchain-contracts/econ-game-theory/page.md)
  - [从零开始构建区块链](./blockchain-contracts/_materials/from-scratch.md)
  - [不停机的应用程序](./blockchain-contracts/unstoppable-apps/page.md)
  - [共识确定性](./blockchain-contracts/finality/page.md)
  - [Grandpa 棋盘游戏](./blockchain-contracts/_materials/grandpa-board-game.md)
  - [设计基于有向无环图的共识结构](./blockchain-contracts/dag/page.md)
  - [区块链的账户模型与用户抽象](./blockchain-contracts/accounting/page.md)
  - [为 UTXO 模型添加隐私性](./blockchain-contracts/utxo-privacy/page.md)
  - [轻客户端和跨链桥](./blockchain-contracts/light-clients-bridges/page.md)
  - [EVM, Solidity, and Vyper](./blockchain-contracts/languages/page.md)
  - [编写合约 Workshop](./blockchain-contracts/_materials/contract-workshop.md)
  - [启动你的区块链](./blockchain-contracts/_materials/start-your-own-blockchain.md)
  - [链分叉](./blockchain-contracts/forks/page.md)
  - [ink! Workshop](./blockchain-contracts/ink-workshop/page.md)
  - [附加课程](./additional.md)
    - [Web3的协调机制与信任](./blockchain-contracts/coordination-trust/page.md)
    - [资源，费率，排序](./blockchain-contracts/resources-fees-ordering/page.md)
    - [基础设施的探求](./blockchain-contracts/infrastructure/page.md)
    - [Ink! --- Wasm智能合约 ](./blockchain-contracts/ink/page.md)

- [🧬 Substrate](./substrate/index.md)

  - [概述](./substrate/intro/page.md)
  - [Wasm 元协议](./substrate/wasm/page.md)
  - [代码演示](./substrate/code/page.md)
  - [与 Substrate 区块链交互](./substrate/interact/page.md)
  - [交易池及其 Runtime API](./substrate/txpool-api/page.md)
  - [SCALE 编解码](./substrate/scale/page.md)
  - [Substrate and FRAME 技巧与窍门](./substrate/tips-tricks/page.md)
  - [Merklized 存储](./substrate/storage/page.md)

- [🧱 FRAME](./frame/index.md)

  - [概述](./frame/intro/page.md)
  - [存在性证明 Runtime](./frame/_materials/proof-of-existence.md)
  - [Pallet 解耦](./frame/coupling/page.md)
  - [Pallets & Traits](./frame/traits/page.md)
  - [FRAME 存储](./frame/storage/page.md)
  - [事件和错误处理](./frame/events-errors/page.md)
  - [调用 Calls](./frame/calls/page.md)
  - [钩子函数 Hooks](./frame/hooks/page.md)
  - [调用发起者 Origin](./frame/origin/page.md)
  - [外部枚举](./frame/outer-enum/page.md)
  - [Runtime 构建](./frame/construct-runtime/page.md)
  - [基准测试 1](./frame/benchmarking-1/page.md)
  - [基准测试 2](./frame/benchmarking-2/page.md)
  - [基准测试 活动](./frame/_materials/benchmarking-activity.md)
  - [深入学习](./frame/dive/page.md)
  - [签名扩展](./frame/extensions/page.md)
  - [移植 及 Try Runtime](./frame/migrations/page.md)
  - [其他](./frame/extras/page.md)

- [🟣 Polkadot](./polkadot/index.md)

  - [概述](./polkadot/intro/page.md)
  - [Polkadot 决策](./polkadot/decisions/page.md)
  - [什么是共享安全?](./polkadot/shared-security/page.md)
  - [Polkadot 运行分片](./polkadot/execution-sharding/page.md)
  - [数据可用性与分片](./polkadot/data-sharding/page.md)
  - [Cumulus](./polkadot/cumulus/page.md)
  - [生态系统与经济学](./polkadot/ecosystem-economy/page.md)
  - [注册一条平行链](./polkadot/_materials/regiser.md)
  - [跨链消息传递 (XCMP)](./polkadot/xcmp/page.md)
  - [Zombienet](./polkadot/zombienet/page.md)
  - [浅析异步支持](./polkadot/async-backing-shallow/page.md)
  - [轻客户端与不可停机应用](./polkadot/light-clients/page.md)
  - [区块链扩展 - 单体式与同构式](./polkadot/scaling-homogeneous/page.md)
  - [Blockchain Scaling - 模块化与异构化](./polkadot/scaling-heterogeneous/page.md)
  - [附加课程](./additional.md)
    - [深入探讨异步支持](./polkadot/async-backing-deep/page.md)
    - [区块空间: Polkadot的产物](./polkadot/blockspace/page.md)
    - [构建一条平行链](./polkadot/build-a-parachain/page.md)
    - [核心可用性](./polkadot/availability-cores/page.md)
    - [Polkadot Fellowship](./polkadot/fellowship/page.md)
    - [提名质押权益证明](./polkadot/npos/page.md)
    - [OpenGov](./polkadot/opengov/page.md)

- [💱 XCM](./xcm/index.md)

  - [跨共识消息概述 (XCM)](./xcm/intro/page.md)
  - [XCVM虚拟机](./xcm/xcvm/page.md)
  - [XCM Pallet](./xcm/pallet/page.md)
  - [平行链 XCM 配置](./xcm/config/page.md)
  - [附加课程](./additional.md)
    - [XCM 活动](./xcm/_materials/activities.md)
    - [XCM in Polkadot](./xcm/polkadot/page.md)
    - [XCM in Use](./xcm/in-use/page.md)
    - [除资产转账以外的内容](./xcm/beyond/page.md)

# 🍰 附加内容

---

- [🕵️ 安全性的实际应用](./security/index.md)

  - [网络安全概览](./security/overview/page.md)
  - [Web3 中的安全意识](./security/awareness/page.md)
  - [以用户为中心的安全性](./security/user-centric/page.md)
  - [基础设施安全](./security/infrastucutre/page.md)
  - [应用程序安全](./security/appsec/page.md)
  - [Polkadot SDK 开发中的常见安全风险](./security/risks/page.md)

- [𝞨 Rust形式化方法入门](./formal-methods/intro/page.md)

# 👪 贡献

---

- [🙋 指引](./contribute/index.md)
  - [🤝 行为准则](./CODE-OF-CONDUCT.md)
  - [🌀 课程模板](./contribute/template/page.md)
  - [📋 PPT 模板](./contribute/copy-paste-slides/page.md)

[🏷️ License](./LICENSE.md)
