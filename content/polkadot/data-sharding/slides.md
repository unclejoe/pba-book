---
title: Data Availability and Sharding
description: Data Availability Problem, Erasure coding, Data sharding.
duration: 30-45 mins
---

# 数据可用性和分片

---

### 大纲

<pba-flex center>

1. [数据可用性问题](#数据可用性问题)
1. [纠删编码](#纠删编码)
1. [数据可用性采样](#数据可用性采样)
1. [参考文献](#参考文献)

</pba-flex>

---

## 数据可用性问题

我们如何确保在不将某条数据永久存储在每个节点上的情况下，该数据仍然是可检索的呢？

错误可以被证明（欺诈证明），但数据不可用却无法被证明。

---v

### 数据可用性问题：平行链

想象一下，一个平行链收集人产生了一个区块，但只将其发送给中继链验证者进行验证。

这样的收集人可能会做什么呢？

<pba-flex center>

- 阻止节点和用户了解平行链的状态
- 阻止其他收集人创建区块

</pba-flex>

我们希望其他收集人能够从中继链中重构该区块。

---v

### 数据可用性问题：中继链

如果该区块的证明（PoV）仅由少数验证者存储，那么如果这些验证者离线或行为异常会怎样呢？

<pba-flex center>

- 诚实的批准检查者无法验证其有效性

</pba-flex>

Notes:

这可太糟糕了。
这意味着我们可能会最终确认一个无效的平行链区块。

---

## 问题

<img rounded style="width: 800px" src="./img/comic.png" />

Notes:

我真的很喜欢范式（Paradigm）关于数据可用性采样的文章中的这幅漫画。但它也适用于我们的数据分片情况。

---

## 纠删编码

目标：

<pba-flex center>

- 将 K 个数据块编码为更大的 N 个数据块
- N 个数据块中的任意 K 个子集都可以用于恢复数据

</pba-flex>

<img rounded style="width: 1000px" src="./img/erasure-coding-1.png" />

---v

### 代码实现

```rust
type Data = Vec<u8>;

pub struct Chunk {
    pub index: usize,
    pub bytes: Vec<u8>,
}

pub fn encode(_input: &Data) -> Vec<Chunk> {
    todo!()
}

pub fn reconstruct(_chunks: impl Iterator<Item = Chunk>) -> Result<Data, Error> {
    todo!()
}
```

---

### 多项式

<img rounded style="width: 1000px" src="./img/line.svg" />

---v

### 多项式：直线

<img rounded style="width: 1000px" src="./img/line-extra.svg" />

---v

### 更多多项式

<img rounded style="width: 1000px" src="./img/polynomial-2.png" />

---v

### 我们需要的多项式

<img rounded style="width: 800px" src="./img/reed-solomon.png" />

我们想要一个多项式，使得：

$$ p(x_i) = y_i$$

Notes:

问题：就我们的数据而言，$x_i$ 和 $y_i$ 分别是什么？

---

### 拉格朗日插值多项式

<!-- prettier-ignore -->
$$ \ell_j(x) = \frac{(x - x_0)}{(x_j - x_0)} \cdots \frac{(x - x_{j - 1})}{(x_j - x_{j - 1})} \frac{(x - x_{j + 1})}{(x_j - x_{j + 1})} \cdots \frac{(x - x_k)}{(x_j - x_k)} $$

<!-- prettier-ignore -->
$$ L(x) = \sum_{j = 0}^{k} y_j \ell_j(x) $$

---

### 里德 - 所罗门码

恭喜！你几乎已经学会了里德 - 所罗门编码。

实际的里德 - 所罗门码是在有限域上定义的。

它可以检测并纠正错误和擦除的组合。

Notes:

有限域的最简单例子是模素数运算。
计算机在进行素数除法时表现不佳。
里德 - 所罗门码用于 CD、DVD、二维码和 RAID 6 中。

---v

### 带有拉格朗日插值的里德 - 所罗门码

1. 将数据划分为大小为 $P$ 位的元素。
1. 将这些字节解释为模 $P$ 的（大）数。
1. 每个元素的索引是 $x_i$，元素本身是 $y_i$。
1. 构造插值多项式 $p(x)$ 并在另外的 $n - k$ 个点上进行求值。
1. 编码为 $(y_0, ..., y_{k - 1}, p(k), ... p(n - 1))$ 以及相应的索引。

Notes:

我们该如何进行重构呢？

---

## Polkadot:的数据可用性协议

<pba-flex center>

- 每个证明（PoV）被分为 $N_{validator}$ 个数据块
- 索引为 i 的验证者获取索引相同的数据块
- 验证者在收到其数据块时签署声明
- 一旦我们有了 $\frac{2}{3} + 1$ 个已签名的声明，
  则证明（PoV）被认为是可用的
- 任何 $\frac{1}{3} + 1$ 个数据块的子集都可以恢复数据

</pba-flex>

Notes:

所有验证者存储的数据总量为 PoV × 3。
对于 5MB 的 PoV 和 1000 个验证者，每个验证者每个 PoV 仅存储 15KB。
通过这个协议，我们一举两得！

---

### 候选者已包含

<img rounded style="width: 1000px" src="./img/candidate-included.png" />

---

### 可用性位字段

<img rounded style="width: 600px" src="./img/availability-bitfields.png" />

Notes:

每个验证者实际上是为每个中继链区块签署一个声明，而不是为每个 PoV 签署声明，以减少签名数量。
这些声明在链下进行传播，并包含在一个平行链固有数据（ParachainsInherent）的区块中。

---

### 挑战 1

验证者如何知道一个数据块是否与已提交的数据相对应呢？

<img rounded style="width: 600px" src="./img/not-that-merkel.jpg" />

---v

### 不是那个默克尔！

<img rounded style="width: 600px" src="./img/Ralph_Merkle.png" />

---

### 挑战 2

我们如何知道从数据块中重构出来的数据与使用里德 - 所罗门码编码的数据是相同的呢？

<pba-flex center>

- Polkadot:使用批准投票/争议机制来解决这个问题
- Celestia 使用欺诈证明
- Danksharding 使用 KZG 承诺

</pba-flex>

---

## 数据可用性采样

以太坊（Danksharding）和 Celestia 采用了数据可用性采样的方法，其中每个轻客户端通过采样和分发一些随机数据块来自行判断数据的可用性。

这种方法可以消除诚实多数假设！

这种方法以高概率保证至少有一个诚实的完整节点拥有这些数据。

<br />

> <https://arxiv.org/abs/1809.09044>

---

## Polkadot:协议的安全性

如果在 $3f + 1$ 个验证者中，最多有 $f$ 个恶意或离线的验证者，那么如果数据被标记为可用，它**可以**被恢复。

如果这个假设被打破会怎样呢？

如果有 $2f + 1$ 个验证者是恶意的，那么任何权益证明（PoS）系统都注定要失败。

Notes:

我们将在下一课中看到，即使有超过 $f$ 个恶意节点，批准投票如何防止不可用的区块被最终确认。

---

## 二维里德 - 所罗门编码

<img rounded style="width: 800px" src="./img/2d-reed-solomon.png" />

Notes:

二维里德 - 所罗门编码的方法可以减少 Celestia 使用的欺诈证明的大小。
但它会在计算和数据存储量方面增加开销。

---

## 与其他方法的比较

- Danksharding 和 Celestia 都使用二维编码和数据可用性采样（DAS）
- Celestia 没有实现数据分片
- 数据可用性只是确保有效性的一部分
- Polkadot:的数据可用性协议能够每秒处理数十 MB 的数据

Notes:

Danksharding 的目标是 1.3 MB/s，而 Celestia 小于 1 MB/s。

---

## 待探索的想法

<pba-flex center>

- 针对平行链轻客户端和完整节点的数据可用性采样
- 考虑使用 KZG 承诺
- 减少需要验证的签名数量
- 一个数据可用性平行链

</pba-flex>

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题

---

### 额外内容

<pba-flex center>

- Polkadot:使用大小为 $2^{16}$ 的域进行高效算术运算
- Polkadot:使用基于快速傅里叶变换（FFT）的里德 - 所罗门算法（不使用拉格朗日方法）

</pba-flex>

> <https://github.com/paritytech/reed-solomon-novelpoly>

---

## 参考文献

1. <https://www.youtube.com/watch?v=1pQJkt7-R4Q>
1. <https://notes.ethereum.org/@vbuterin/proto_danksharding_faq>
1. <https://www.paradigm.xyz/2022/08/das>
