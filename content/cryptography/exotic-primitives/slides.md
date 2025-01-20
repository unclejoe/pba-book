---
title: Exotic Primitives
description: More cutting edge cryptography used in blockchain
duration: 1 hour
---

# 奇异原语

---

# 大纲

<pba-flex center>

1. [可验证随机函数 (VRFs)](#verifiable-random-functionsvrfs)
1. [擦除编码](#erasure-coding)
1. [Shamir 秘密共享](#shamir-secret-sharing)
1. [代理重加密](#proxy-reencryption)
1. [ZK 证明](#zk-proofs)

</pba-flex>

---

## 可验证随机函数（VRFs）

<widget-center>

- 用于获取**私有的随机数**，并且该随机数是**可公开验证**的。
- 是签名方案的一种变体：
  - 仍然有公私钥对，输入作为消息。
  - 除了签名，我们还得到一个输出。

---

### VRF 接口

- `sign(sk, input) -> signature`
- `verify(pk, signature) -> option output`
- `eval(sk,input) -> output`

Notes:

验证作为选项的输出表示签名无效的可能性。

---

### VRF 输出属性

- 输出是**密钥**和**输入**的确定性函数。
- 它应该是伪随机的。
- 但是，在 VRF 被揭示之前，只有私钥持有者知道输出。
- 揭示输出不会泄露私钥。

---

### VRF 使用

- 在选择输入后选择密钥，这样密钥持有者就无法影响输出。
- 然后，输出实际上是一个只有密钥持有者知道的随机数。
- 但是，他们可以通过发布 VRF 证明（签名）来稍后揭示它。

Notes:

签名证明这是与其输入和公钥相关联的输出。

---

## VRF 示例

- 以分布式和无需信任的方式玩纸牌游戏
- 当轮到玩家A抽一张牌，所有玩家同意一个新的随机数x
- A的牌是由
'eval（sk_A， x）mod 52'决定的
- 为了出牌，A公布签名

---

### VRF 扩展

- 阈值 VRFs / 共同硬币：如果$t$个人中的$n$个人参与，则生成相同的随机数。
- RingVRFs：VRF 输出可以来自一组公钥中的任何一个。

---

## 擦除编码

_神奇的数据扩展_

- 将数据转换为片段（具有一定的冗余），以便即使某些片段丢失也可以重建数据。
- 一个由$k$个符号组成的消息被转换为一个由$n$个符号组成的编码消息，并且可以从这$n$个符号中的任意$k$个符号中恢复。

---

### 擦除编码感知

- 擦除编码依赖于双方对有效消息的共同理解。这使得错误能够被注意到并得到纠正。
- 例如，在餐厅点餐时，即使你说话含糊不清，他们也可能理解你的意思。

Notes:

消息子集有效的概念在现实生活中非常普遍，并且无处不在。
在餐馆，当他们问你要汤还是沙拉时，即使你喃喃自语，他们也可能会理解你。

---

### 擦除编码直觉

- 如何对以下单词进行分类？
<span style="color: red;">file</span> pile pale tale tall rule tail rail rain <span style="color: blue;">ruin</span>

---v

### 擦除编码直觉

- 如何对以下单词进行分类？
<span style="color: red;">file pile pale tale tall</span> <span style="color: purple;">rule</span> <span style="color: blue;"> tail rail rain ruin</span>

- 你可以根据它们与有效输入的接近程度对它们进行分类。这也意味着我们可以发现这些消息中的错误。

Notes:

没有完美的方法来区分这些，但是一个非常合理的方法是根据接收到的单词的编辑距离和你可以接收到的任何有效消息来做这件事。

---v

### 擦除编码直觉

- 现在，你正在接收可能是`msg1`或`msg2`的消息。你能应用同样的技术吗？是否容易区分接收到的消息？
- 如果你收到`msg3`会怎样？

如果你收到“msg3”怎么办？

Notes:

如果消息相距不远，很多情况下是无法区分的，两种可能性之间没有足够的“距离”。

---v

### 擦除编码直觉

通过擦除编码，我们神奇地扩展了每个消息，使它们即使在消息相似的情况下也非常不同。发送方和接收方知道相同的编码过程。这些扩展将非常不同，即使消息相似。

`msg1`<span style="color: red;"> `jdf`</span> and `msg2`<span style="color: red;"> `ajk`</span>

Notes:

实际上，总是可以将额外的魔法附加到消息中。这称为_systematic encoding_。

对于那些对“魔法”如何运作感到好奇的人：

这里的神奇之处在于多项式，事实上，$n$度的多项式完全由$n+1$点决定。网上有很多很好的解释。

---

## 擦除编码

<img style="width: 1000px;" src="./img/erasure-code.svg" />

---

## 擦除编码的经典用途

- 用于有噪声的信道
- 如果编码数据的一些比特被随机翻转，我们仍然可以恢复原始数据
- 通常 $n$ 不比 $k$ 大很多

---

## 在去中心化系统中的应用

- 我们有数据想要公开保存
  - 但不想让每个人都存储
  - 而且我们不信任所有存储数据的人
- 通常我们使用 $n$ 比 $k$ 大很多

---
## Shamir 秘密共享

_为你的秘密提供冗余_

- 将数据（通常是一个秘密）分成若干部分，以便可以从这些部分的某个子集中重建原始数据。

- 一个秘密被分成 $n$ 个份额，并且可以通过任意 $k$ 个份额恢复。$k-1$ 个份额一起不会泄露关于秘密的任何信息。

---

## Shamir 秘密共享

<img style="height: 600px" src="./img/shamir-secret-sharing.png" />

Notes:

图片来源: <https://medium.com/clavestone/bitcoin-multisig-vs-shamirs-secret-sharing-scheme-ea83a888f033>

---

## 优缺点

- 如果你丢失了秘密，可以重建它。
- 同样，收集到足够份额的其他人也可以。

---

## 代理重加密

- 生成密钥，允许第三方转换加密数据，以便其他人可以读取，而不向第三方透露数据。

---

## 代理重加密

<img rounded style="height: 600px" src="./img/proxy-reencryption.png" />

Notes:

[img source](https://scrapbox.io/layerx/Proxy_Re-Encryption%28PRE%29%E3%81%A8NuCypher)

---

### 代理重加密 API

- `fn encrypt(pk, msg) -> ciphertext;`：使用公钥和消息进行加密，返回密文。
- `fn decrypt(sk, ciphertext) -> msg;`：使用私钥和解密密文，返回消息。
- `fn get_reencryption_key(sk, pk) -> rk;`：使用私钥和接收者的公钥获取重加密密钥。
- `fn reencrypt(rk, old_ciphertext) -> new_ciphertext;`：使用重加密密钥转换密文，使其可以被新的接收者解密。

---

## ZK 证明

- 如何在公共区块链上进行私有操作，并让每个人都知道它们是正确执行的？

Notes:

（我们正在研究相应的substrate支持，并将它们用于协议）

---

### 什么是 ZK 证明？

- 证明者想要说服验证者某件事是真实的，而不透露为什么它是真实的。
- 它们可以是交互式协议，但我们主要处理非交互式类型。

---

### 我们可以展示什么？

- NP 关系：`function(statement, witness) -> bool`
- 证明者知道一个声明的证人：
  - 他们想表明他们知道它（_知识证明_）
  -... 而不透露任何关于证人的信息（_零知识_）
- 具有小证明，即使证人很大（_简洁性_）

---

### ZK 证明接口

- NP 关系：`function(statement, witness) -> bool`
- `prove(statement, witness) -> proof`
- `verify(statement, proof) -> bool`

---

### ZK 证明示例

- 示例：Schnorr 签名是 ZK 证明
- 它们表明证明者知道私钥（公钥的离散对数），而不透露任何关于它的信息。
- 声明是公钥，证人是私钥。

---

### zk-SNARK

**Z**ero-**K**nowledge **S**uccinct **N**on-interactive **Ar**gument of **K**nowledge

- **零知识**：证明不透露证人的任何信息，除了声明本身所揭示的。
- **简洁**：证明很小。
- **知识证明**：如果你能计算出正确的声明证明，你应该能够计算出证人。

---

### 我们可以展示什么？

- 有许多方案可以为每个 NP 关系生成简洁的零知识证明（_ZK-SNARKs_）

---

## ZK 证明扩展

少量数据、ZK 证明和执行时间可用于展示大量数据集的属性，而验证者不需要知道这些数据。

---

## 通过区块链中的 ZK 证明扩展

- 大量数据 - 区块链
- 验证者例如是移动电话上的应用程序

Notes:

例如，Mina 使用递归 SNARK 进行区块链的常量大小证明（执行和共识的正确性）。

---

## 在区块链中通过 ZK 证明进行扩展

- 验证者是区块链：数据和计算成本非常昂贵。
- 使用 ZK 汇总的第 2 层

Notes:

其中以太坊有很多，ZKsync，ZKEVM 等。
Polkadot 已经更好地扩展了！

---

## 隐私

<pba-flex center>

用户拥有私人数据，但我们可以公开证明这些私人数据被正确使用。
一个例子是私人加密货币：

- 保密谁支付给谁
- 保密金额，但显示它们是正数！

</pba-flex>

Notes:

你可以在没有 ZK-SNARK 的情况下对金额进行部分保密，但正数部分很难。
要做好一切，例如 ZCash 及其许多衍生产品（如 Manta）需要 ZK-SNARK。

---

### 实际考虑

- 非常强大的原语。
- 对扩容和隐私都很有用。
- 可以设计许多使用 ZK 证明的协议，否则这些协议是不可能的。

---

### 缺点

- 通用计算的证明生成时间慢。
- 要快速，需要手动优化。
- 非常奇怪的计算模型：非确定性算术电路。

---

### 缺点结论？

- 如果你想在组件中使用这个，预计需要一个熟练的团队至少工作一年。
- 但是如果你在 5 年后看到这个，人们已经构建了工具来减轻痛苦。

---

## 使用密码学进行简洁证明？

<pba-flex center>

- ZK 友好哈希
- 非哈希基础数据结构
  - RSA 累加器
  - 基于多项式承诺的<br />
    （Verkle 树）

</pba-flex>

---

## 总结

- VRF：稍后可公开验证的私有随机性
- 擦除编码：通过冗余使数据对损失具有鲁棒性
- Shamir 秘密共享：为你的秘密提供冗余
- 代理重加密：允许通过密码学访问你的数据
- ZK 证明：只是魔法，但很昂贵的魔法

---

<!--.slide: data-background-color="#4A2439" -->

# 问题