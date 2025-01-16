---
title: Cryptography In Context
description: Real-world considerations around cryptography
duration: 1 hour
---

好的，这是一个关于密码学的幻灯片，内容包括密码学的实际应用考虑、如何保持秘密、安全和可用性等方面。以下是翻译后的文本：

---

# 密码学在实际应用中的考虑

---

# 大纲

<pba-flex center>

1. 保持秘密
1. 安全和可用性

</pba-flex>

---

## 秘密

在密码学中，秘密是什么？

数据，你知道，但没有其他人知道。

---

## 如何保持秘密

为了保持密码学秘密的机密性，唯一可以透露的是已知的、安全的密码学操作的输出。

- 一个（足够随机的）秘密可以被哈希，并且哈希值可以被透露。
- 私钥可以用来生成公钥，并且公钥可以被透露。
- 私钥可以用来生成签名，并且签名可以被透露。

---

## 秘密是如何泄露的

1. 在正常操作过程中无意中泄露了关于秘密的信息。
2. 数字或物理安全受到损害，导致私钥丢失。

### Notes:

让我们依次讨论这些问题。

---

## 随机数质量差

一些算法需要随机数。如果随机数被泄露，私钥或加密信息可能会被暴露。

### 来源：

[one source](https://learn.saylor.org/mod/book/view.php?id=36341&chapterid=18921)

---

## 侧信道攻击

侧信道攻击是指对密码系统进行攻击，攻击者拥有系统输出的另一个信息源。

---

## 定时攻击

如果以下任何一项取决于秘密的内容，则可能发生定时攻击：

<pba-flex center>

- 执行哪些指令
- 分支（if 语句）
- 内存访问模式

</pba-flex>

### Notes:

有许多疯狂的侧信道攻击形式，但主要的是定时攻击。定时攻击也是唯一可靠地通过长距离发送回的攻击。

---

## 示例

想象一下，这是一个密码检查器的源代码：

```rust
fn verify_password(actual: &[u8], entered: &[u8]) -> bool {
 if actual.len()!= entered.len() {
    return false;
 }

 for i in 0..actual.len() {
  if entered.get(i)!= actual.get(i) {
   return false;
  }
 }
 true
}
```

问题是什么？

### Notes:

想象一下，你编译了这个小二进制文件，并且能够反复运行它。当向这个程序发送猜测时，你会得到什么信息？

一个布尔值，以及从发送密码到收到响应的时间。

问题是，响应时间揭示了关于密码的信息。攻击者可以反复发送猜测，并根据响应时间的长短来判断猜测是否正确。

---

## 示例（续）

如果我们将代码改为如下所示，是否安全？

```rust
fn verify_password(actual: &[u8], entered: &[u8]) -> bool {
 actual == entered
}
```

### Notes:

现在，我们没有看到任何代码行数或循环的差异，对吧？

---

## 示例（续）

源代码看起来像这样：

```rust[0|1|7-9|15]
// Use memcmp for bytewise equality when the types allow
impl<A, B> SlicePartialEq<B> for [A]
where
    A: BytewiseEq<B>,
{
    fn equal(&self, other: &[B]) -> bool {
        if self.len()!= other.len() {
            return false;
        }

        // SAFETY: `self` and `other` are references and are thus guaranteed to be valid.
        // The two slices have been checked to have the same size above.
        unsafe {
            let size = mem::size_of_val(self);
            memcmp(self.as_ptr() as *const u8, other.as_ptr() as *const u8, size) == 0
        }
    }
}
```

这安全吗？

### Notes:

好的，仍然不安全。看起来现在攻击者仍然可以根据早期返回的时间来判断密码的长度。但是如果我们确保所有密码都是 16 字节长。现在我们只使用一个系统调用。这样安全吗？

---

## 示例（续）

让我们检查一下 `memcmp`。

```text
memcmp(3) — Linux manual page

/* snip */

NOTES
       Do not use memcmp() to compare security critical data, such as
       cryptographic secrets, because the required CPU time depends on
       the number of equal bytes.  Instead, a function that performs
       comparisons in constant time is required.  Some operating systems
       provide such a function (e.g., NetBSD's consttime_memequal()),
       but no such function is specified in POSIX.  On Linux, it may be
       necessary to implement such a function oneself.
```

---

## 那么我们该怎么做？

这是来自 `subtle`  crate 的代码，它提供了常量时间的相等性比较。

```rust[0|14-15|20-28]
impl<T: ConstantTimeEq> ConstantTimeEq for [T] {
    /// Check whether two slices of `ConstantTimeEq` types are equal.
    ///
    /// # Note
    ///
    /// This function short-circuits if the lengths of the input slices
    /// are different.  Otherwise, it should execute in time independent
    /// of the slice contents.
    /* snip */
    #[inline]
    fn ct_eq(&self, _rhs: &[T]) -> Choice {
        let len = self.len();

        // Short-circuit on the *lengths* of the slices, not their
        // contents.
        if len!= _rhs.len() {
            return Choice::from(0);
        }

        // This loop shouldn't be shortcircuitable, since the compiler
        // shouldn't be able to reason about the value of the `u8`
        // unwrapped from the `ct_eq` result.
        let mut x = 1u8;
        for (ai, bi) in self.iter().zip(_rhs.iter()) {
            x &= ai.ct_eq(bi).unwrap_u8();
        }

        x.into()
    }
}
```

### Notes:

现在我们已经看到了阻止一个非常简单的定时信息泄露是多么困难。让我们看看一个实际的密码学库是如何关注这些问题的。

---

## Ed25519 的保证

这是来自 [ed25519](https://ed25519.cr.yp.to/) 描述的节选。

- **无懈可击的会话密钥**。签名是确定性生成的；密钥生成消耗新的随机数，但新的签名不会。这不仅是一个速度特性，也是一个安全特性。
- **抗碰撞性**。哈希函数的碰撞不会破坏这个系统。这增加了一层防御，防止所选哈希函数可能存在的弱点。

---

## Ed25519 的保证（续）

- **没有秘密数组索引**。软件从不从 RAM 中的秘密地址读取或写入数据；地址的模式是完全可预测的。因此，软件不受依赖于通过 CPU 缓存泄露地址的缓存定时攻击、超线程攻击和其他侧信道攻击的影响。
- **没有秘密分支条件**。软件从不基于秘密数据执行条件分支；跳转的模式是完全可预测的。因此，软件不受依赖于通过分支预测单元泄露信息的侧信道攻击的影响。

---

## 总结

防止侧信道攻击是 _困难的_！注意到侧信道攻击更是难上加难！

### 不要自己实现密码学

### Notes:

在处理涉及秘密的任何事情时，请务必非常小心。这包括任何涉及秘密的操作，或在任何地方读取/写入它。

必要时，请咨询安全专家或密码学家。

---

## 安全使用密码学库

- 保持在抽象屏障之上
- 在组合原语时验证每个原语的假设
- 使用你能找到的最可靠的库
- 意识到何时需要认真考虑
  - 一些可能令人害怕的术语：曲线点、填充方案、IV、扭曲曲线、配对、ElGamal

### Notes:

库的可靠性是以下因素的某种组合：

- 被许多人使用
- 经过安全审计
- 可靠的密码学文献
- 最小的外部依赖
- 密码学家推荐

如果你在密码学库中看到这些术语的引用，而不仅仅是在描述中，那么你可能太低级了。

---

# 恐怖故事

### Notes:

仅根据兴趣和剩余时间浏览其中的一些故事。

---v

## PS3 秘密密钥泄露

**问题**：随机数质量差

**描述**：PS3 开发者在使用需要随机数的算法时没有使用随机数。

**后果**：每个 PS3 都被硬编码为信任该密钥。当黑客获得密钥时，他们就能够冒充索尼并编写任何在 PS3 上运行的软件。实际上，这使得运行盗版游戏变得轻而易举。

### 来源：

[source](https://www.engadget.com/2010-12-29-hackers-obtain-ps3-private-cryptography-key-due-to-epic-programm.html)

---v

## IOTA 的新型哈希函数

**问题**：自己实现密码学

**描述**：IOTA 是一种加密货币，在当时价值 19 亿美元。他们编写了自己的哈希函数，研究人员发现了严重的漏洞。

**后果**：善良的安全研究人员直接向开发者报告了该漏洞。他们不得不暂停区块链 3 天，为所有账户生成新地址，并切换到 KECCAK。

### Notes:

IOTA 最初自己实现哈希函数是为了抵御量子计算的攻击。

一些哈希函数的弱点是微弱的。这不是。概念验证漏洞利用实际上发现了两个哈希值，分别对应于区块链上发送少量货币的消息，以及另一个对应于发送大量货币的消息。

[漏洞利用 POC](https://github.com/mit-dci/tangled-curl/blob/master/vuln-iota.md)
[关闭来源](https://www.bitfinex.com/posts/215)

---v

## NSA 如何多年来窃听所有手机通话

**问题**：密钥空间小/秘密技术

**描述**：直到 2000 年代中后期，手机通话的标准（GSM A5/1）使用 54 位密钥，并且该方法是保密的。它没有保持秘密，并且变得非常容易破解。

**后果**：情报机构可以并且确实轻松窃听通话。对密钥空间进行了多次暴力攻击。

### 来源：

当标准化过程开始时，教授们提议使用 128 位密钥。西欧（尤其是英国）的情报机构希望降低安全性。斯诺登最终透露，NSA 可以轻松读取 A5/1 通话。

[文章来源](https://goodenoughsecurity.blogspot.com/2011/10/gsm-a51-substandard-security-pt2.html)
[削弱来源](https://www.aftenposten.no/verden/i/Olkl/sources-we-were-pressured-to-weaken-the-mobile-security-in-the-80s)

---v

## 为什么 HTTPS 不像你希望的那样安全

**问题**：密码学原语假设未得到维护

**描述**：加密通常不会隐藏底层消息的长度。HTTPS 经常在加密前使用压缩。压缩使得重复的字符串更小。

**后果**：一种名为 BREACH 的漏洞可以在不到 30 秒的时间内从受 HTTPS 保护的网站上窃取秘密。所有网站都不得不添加缓解措施来抵消这种攻击。

### 来源：

缓解措施包括：

- 随机化压缩后响应内容的大小
- 将秘密与用户输入分开
- 禁用 HTTP 压缩（这很昂贵）
- 为每个请求随机化秘密

[来源](https://arstechnica.com/information-technology/2013/08/gone-in-30-seconds-new-attack-plucks-secrets-from-https-protected-pages/)

---

## 物理安全

<img style="width: 900px;" src="./img/xkcd-physical-security.png" />

### Notes:

物理安全是密码学中一个重要但经常被忽视的方面，也是个经典的xkcd漫画。

---

好的，以下是翻译后的文本：

---

## 物理安全

对运行中的计算机的完全物理访问通常可以让攻击者付出足够的努力就能完全访问你的秘密。

一些可能的手段：

- 扫描所有磁盘存储
- 取出内存并将其换到另一台计算机上读取（冷启动攻击）
- 近距离侧信道攻击
  - 射频辐射
  - 功耗
  - 计算机运行的声音

### Notes:

有关外来攻击的来源：

- [EM 侧信道攻击的一般调查](https://arxiv.org/abs/1903.07703)
- [基于声音的攻击](https://www.iacr.org/archive/crypto2014/86160149/86160149.pdf)
- [仅 500 次跟踪的 15m 处的 EM 侧信道攻击](https://www.diva-portal.org/smash/record.jsf?pid=diva2%3A1648290&dswid=6646)

---

## HSMs

HSM 是 **h**ardware **s**ecurity **m**odule 的缩写。HSM 可以使窃取加密密钥变得更加困难甚至不可能。HSM 将保存加密密钥，并对其执行操作。

### Notes:

我们没有过多涉及这方面的内容，因为有许多关于物理安全和 HSM 的可用资源。这只是在上下文中提出这些想法，即什么使加密秘密真正成为 **秘密**。

---

## 安全与可用性

秘密的可访问性通常与安全性成反比。

提高秘密的安全性通常是不切实际的，这取决于使用情况。

### Notes:

这在所有情况下都不是绝对正确的，但它是一个很好的经验法则。此外，请注意，不切实际并不等于不可能。

---

## 思想实验

假设我给你一个太长而无法记住的秘密。

在一年结束时，如果没有其他人知道这个秘密，我会给你一百万美元。

你会怎么做？

---

## 思想实验

假设我给你一个太长而无法记住的秘密。

在一年结束时，如果没有其他人知道这个秘密 **并且你向我展示这个秘密**，我会给你一百万美元。

你会怎么做？

### 销毁它

---

## 思想实验

假设我给你一个太长而无法记住的秘密。

在一年结束时，如果没有其他人知道这个秘密 **并且你每月向我展示一次这个秘密**，我会给你一百万美元。

你会怎么做？

---

## 思想实验

假设我给你一个太长而无法记住的秘密。

在一年结束时，如果没有其他人知道这个秘密 **并且你每天向我展示这个秘密**，我会给你一百万美元。

你会怎么做？

### 把它藏在一个安全的地方

Notes:比如银行金库，埋在树林里的盒子等

---

## 思想实验

假设我给你一个太长而无法记住的秘密。

一年后，如果没有人其他人知道这个秘密，**而你每个月必须告诉我一次这个秘密**，我就给你一百万美元。

你会怎么做？

---

## 思想实验

假设我给你一个太长而无法记住的秘密。

一年后，如果没有其他人知道这个秘密，**而你每天必须告诉我一次这个秘密**，我就给你一百万美元。

你会怎么做？

---

## 加密秘密的应用

加密秘密很容易拥有多个。

所以不要让用户对所有事情都使用同一个！

尽可能地，一个根秘密不应该既被经常使用，又具有极高的价值。

---

<!--.slide: data-background-color="#4A2439" -->

# 问题