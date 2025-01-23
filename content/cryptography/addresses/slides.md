---
title: Addresses and Keys
description: Addresses and keys in cryptography
duration: 30 min
---

# 地址和密钥

---

# 大纲

<pba-flex center>

1. 二进制格式
1. 种子创建
1. 分层确定性密钥派生

</pba-flex>

---

## 二进制显示格式

在表示二进制数据时，有几种不同的显示格式你应该熟悉 

十六进制：0-9，a-f

Base64：A-Z，a-z，0-9，+，/

Base58：Base64 去掉 0/O，I/l，+，和 /

**Notes:**

这些都是通过文本来传输二进制数据的显示格式 相同的数据可以用这些格式中的任何一种进行编码，重要的是要知道你正在使用哪种格式来解码 数据通常不会以这些格式存储，除非它必须通过文本来传输 

---

## 二进制显示格式示例

每个十六进制字符是 4 位 
每个 Base64 字符是 6 位 
Base58 字符通常是 _大约_ 6 位 

```text
binary: 10011111 00001010 10011110 10011000 01001100 11010011 10110010 00000101
hex:    9   f    0   a    9   e    9   8    4    c   d   3    b   2    0   5
base64: n     w      q      e      m     E      z      T      s     g      U=
base58: T     b      u      H      z     e      3      c      t     k      c

hex:    9f0a9e984cd3b205
base64: nwqemEzTsgU=
base58: TbuHze3ctkc
```

**Notes:**

事实证明，从十六进制/Base64 转换为 Base58 在理论上可能需要 n^2 时间！

---

# 助记词和种子创建

Notes:
这些都是秘密的不同表现形式 从根本上来说，并没有真正改变任何东西 

---

## 种子是秘密

回想一下 对称和非对称密码学都需要一个秘密 

---

## 助记词

许多钱包使用一个单词字典给人们提供短语，通常是 12 或 24 个单词，因为比字节数组更容易备份/恢复 

**Notes:**

- 需要高熵 
- 人们不擅长随机 
- 有些人创建自己的短语...这通常是愚蠢的 

---

## 字典
<pba-cols>
<pba-col>

有一些标准字典来定义在生成短语时包含哪些单词（和字符集） Substrate 使用 BIP39 中的字典 

</pba-col>
<pba-col>

| No. | word    |
| --- | ------- |
| 1   | abandon |
| 2   | ability |
| 3   | able    |
| 4   | about   |
| 5   | above   |

<pba-flex style="font-size:.6em;" center>

_BIP39 英语字典的前 5 个单词_

</pba-col>
</pba-cols>

---

## 助记词到密钥

当然，密钥是椭圆曲线上的一个点，而不是一个短语 

BIP39 应用 2048 轮 SHA-512 哈希函数把助记词派生成 64 字节的密钥 

Substrate 使用来自助记词的熵字节数组 

---

## 可移植性

不同的密钥派生函数会影响在多个钱包中使用相同助记词的能力，因为不同的钱包可能使用不同的函数来从助记词派生密钥 

---

## 密码学类型

通常，你会在大多数系统中遇到三种不同的现代密码学类型 

<pba-flex center>

- Ed25519
- Sr25519
- ECDSA

</pba-flex>

我们将在未来的讲座中更深入地探讨这些！

**Notes:**

你可能在学校里学过 RSA 它现在已经过时了，并且需要 _巨大数量_ 的密钥 

---

## 什么是地址？

地址是公钥的一种表示，可能包含额外的上下文信息 

**Notes:**

对于对称密码学，拥有一个地址实际上没有任何意义，因为对称密钥没有公开信息 

---

## 地址格式

地址通常包括一个校验和，这样一个小错误并不会使一个有效的地址变成另一个 

```text
Valid address:   5GEkFD1WxzmfasT7yMUERDprkEueFEDrSojE3ajwxXvfYYaF
Invalid address: 5GEkFD1WxzmfasT7yMUERDprk3ueFEDrSojE3ajwxXvfYYaF
                                          ^
                                          E changed to 3
```

**Notes:**

还没有涉及到，但有些地址甚至更 _花哨_，在地址中包含一个错误纠正码 

---

## SS58 地址格式

SS58 是 Substrate 中使用的格式 

它是 base58 编码的，包括一个校验和和一些上下文信息 
几乎总是 2 字节的上下文和 2 字节的校验和 

```text
base58Encode( context | public key | checksum )
```

**Notes:**
`|` 在这里表示连接 

对于 ECDSA，公钥是 33 字节，所以我们使用它的哈希值来代替公钥 

这里有很多变体，但这是迄今为止最常见的一种 

[参考](https://docs.substrate.io/reference/address-formats/)

---

# HDKD

分级确定性密钥派生

<img style="width: 1100px;" src="./img/HD-Deterministic-Wallet.png" />

---

## 硬派生与软派生

密钥派生允许从一个“父”密钥派生出（实际上是无限的）子密钥 

派生可以是“硬性”的或“软性”的 

---

## 硬派生与软派生

<img style="width: 1200px;" src="./img/soft-vs-hard-derivation.png" />

---

## 硬派生

硬派生在原始密钥上派生出新的子密钥 

典型的“安全操作”应该优先考虑使用硬派生而不是软派生，因为硬派生避免了泄露一族密钥里的其他密钥，除非原始密钥被泄露 

总是优先使用硬路径派生，然后考虑软路径派生 

---

## 硬派生在钱包中的应用

钱包可以在不同的共识系统中使用同一个密钥，而只需要备份一个秘密和一个用于子派生的模式 

<img style="width: 1000px;" src="./img/Hard-Derivation-in-Wallets.png" />

---

## 硬派生在钱包中的应用

假设我们想在多个网络上使用同一个私钥，但我们不想使用相同的公钥 

<img style="width: 1000px;" src="./img/Hard-Derivation-in-Wallets.png" />

---

<!--.slide: data-background-color="#4A2439" -->

# 子密钥演示

## 硬派生

**Notes:**

硬密钥：取一个 _路径_（如名称/索引），将其与原始密钥连接，然后哈希以获得新密钥 
它们不会泄露关于它们上面的密钥的任何信息，并且只有通过 _路径_ 才能恢复它们和它们的子密钥 

---

## 软派生

软派生允许从公钥创建派生地址，而不需要私钥 
<br />
与硬派生不同，_所有_ 密钥都是相关的 

**Notes:**

- 使用任何密钥和到子密钥和/或父密钥的路径，可以恢复公钥和私钥 
- 软派生可能会破坏一些基于特殊需求的区块链协议，但我们的 sr25519 crate 避免支持与软派生冲突的协议 

---

## 软派生

- 请注意，这些生成新的地址使用的是相同的密钥种子 
- 我们也可以使用相同的路径，但只使用从 `//polkadot` 派生来的账户 ID 它生成相同的地址！

---

## 软派生在钱包中的应用

钱包可以使用软派生将所有由单个私钥控制的支付链接起来，而不需要暴露用于地址派生的私钥 

**用例：** _一家企业希望为每笔支付生成一个新地址，能够在不需要私钥所有者派生新子密钥的情况下自动向客户提供新地址_

**Notes:**

在这个用例中，在不同的地址接收每笔支付可以帮助使支付与客户的关联更加模糊 

参见：<https://wiki.polkadot.network/docs/learn-accounts#soft-vs-hard-derivation>

---

<!--.slide: data-background-color="#4A2439" -->

# 子密钥演示

## 软派生

**Notes:**

请参阅本课的 Jupyter 笔记本和/或 HackMD 备忘单 
提到这些派生创建了全新的密钥种子 

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题