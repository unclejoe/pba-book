---
title: Encryption
description: A lesson on symmetric and asymmetric encryption.
duration: 1 hour
---

---
title: 加密
description: 关于对称和非对称加密的课程。
duration: 1 小时
---

# 加密

---

## 本课目标

<pba-flex center>

- 了解对称和非对称加密的区别。

</pba-flex>

---

## 对称密码学

对称加密假设所有参与方都从一些共享的秘密信息开始，这是一个潜在的非常困难的要求。然后，共享的秘密可以用来保护进一步的通信，使其免受不知道这个秘密的其他人的攻击。

从本质上讲，它提供了一种随着时间的推移扩展共享秘密的方法。

---

## 对称加密

<img style="width: 1100px" src="./img/Symmetric-Cryptography.png" />

例子：ChaCha20，Twofish，Serpent，Blowfish，XOR，DES，AES

---

## 对称加密 API

对称加密库通常应该公开一些基本功能：

- `fn generate_key(r) -> k;` <br /> 从某个输入 `r` 生成一个 `k`（密钥）。
- `fn encrypt(k, msg) -> ciphertext;` <br /> 接受 `k` 和一个消息；返回密文。
- `fn decrypt(k, ciphertext) -> msg;` <br /> 接受 `k` 和一个密文；返回原始消息。

总是有 `decrypt(k, encrypt(k, msg)) == msg`。

Notes:

输入 `r` 通常是一个随机源，例如鼠标的移动模式。

---

## 对称加密保证

提供：

- 机密性
- 真实性\*

不提供：

- 完整性\*
- 不可否认性

Notes:

- 真实性：消息只能由知道共享密钥的人发送。在大多数情况下，这在功能上是对接收方的认证。
- 完整性：没有适当的完整性检查，但是如果消息被更改，更改的部分将是乱码。检测乱码可以作为一种形式的完整性检查。

---

## 对称加密的不可否认性

存在加密证明，表明秘密密钥的生产者知道该秘密。

然而，秘密的知识并不限于一方：在对称加密通信中的所有各方（或所有各方）都知道这个秘密。此外，为了向任何人证明这一点，他们还必须获得这个秘密的知识。

Notes:

仅由纯对称密码学提供的不可否认程度不是很有用。

---

## 对称加密

#### _示例：XOR 密码_

<pba-cols>
<pba-col>

加密和解密函数是相同的：使用密钥进行按位异或操作。

</pba-col>
<pba-col style="padding-right: 100px">

```text
明文：1010  --> 密文：0110
密钥：1100  |          1100
       ----  |          ----
       0110--^          1010
```

Notes:

通过使用密钥进行按位异或操作，可以将明文转换为密文，反之亦然。

</pba-col>
</pba-cols>

---

## 对称加密

#### ⚠ 警告 ⚠

我们通常期望对称加密能保留很少的原始明文信息。然而，我们提醒，即使使用安全的原语构建这些协议仍然很微妙，两个经典的例子是未加盐的密码和 [ECB 企鹅](https://tonybox.net/posts/ecb-penguin/)。

---

### ECB 企鹅

<pba-cols>
<pba-col>

<img style="width: 300px" src="./img/ECB-Penguin.png" />

_原始图像_

</pba-col>
<pba-col>

<img style="width: 300px" src="./img/ECB-Penguin-Encrypted.png" />

_加密图像_

（按块）

</pba-col>
<pba-col>

<img style="width: 300px" src="./img/ECB-Penguin-Secure.png" />

_加密图像_

（一次全部）

</pba-col>
</pba-cols>

Notes:

ECB 企鹅展示了当你加密一小块数据，并多次使用相同的密钥进行加密，而不是一次加密所有数据时，可能会出现的问题。

图片来源：<https://github.com/robertdavidgraham/ecb-penguin/blob/master/Tux.png> 和 <https://github.com/robertdavidgraham/ecb-penguin/blob/master/Tux.ecb.png> 和 <https://upload.wikimedia.org/wikipedia/commons/5/58/Tux_secure.png>

---

## 非对称加密

- 假设发送者不知道接收者的秘密“密钥”🎉😎
- 发送者只知道这个秘密的一个特殊标识符
- 用这个特殊标识符加密的消息只能用知道这个秘密的人才能解密
- 知道这个标识符并不意味着知道这个秘密，因此不能用来解密用它加密的消息
- 出于这个原因，这个标识符可以公开共享，被称为_公钥_。

---

## 非对称加密

<img style="height: 600px" src="./img/asymmetric-crypto-flow.svg" />

---

## 为什么“非对称”？

_仅使用公钥_，信息可以被转换（“加密”），使得只有那些知道这个秘密的人才能逆向并恢复原始信息。

即，公钥用于加密，但必须使用不同的、_秘密的_密钥来解密。

---

## 非对称加密 API

非对称加密库通常应该公开一些基本功能：

- `fn generate_key(r) -> sk;` <br /> 从某个输入 `r` 生成一个 `sk`（私钥）。
- `fn public_key(sk) -> pk;` <br /> 从私钥 `sk` 生成一个 `pk`（公钥）。
- `fn encrypt(pk, msg) -> ciphertext;` <br /> 接受公钥和一个消息；返回密文。
- `fn decrypt(sk, ciphertext) -> msg;` <br /> 接受私钥和一个密文；返回原始消息。

总是有 `decrypt(sk, encrypt(public_key(sk), msg)) == msg`。

Notes:

输入 `r` 通常是一个随机源，例如鼠标的移动模式。

---

## 非对称加密保证

提供：

- 机密性

不提供：

- 完整性\*
- 真实性
- 不可否认性

Notes:

- 真实性：消息只能由知道共享密钥的人发送。在大多数情况下，这在功能上是对接收方的认证。
- 完整性：没有适当的完整性检查，但是如果消息被更改，更改的部分将是乱码。检测乱码可以作为一种形式的完整性检查。

---

## Diffie-Hellman 密钥交换

<img style="height: 500px" src="./img/Diffie-Hellman_Key_Exchange_horizontal.svg" />

混合油漆可视化

Notes:

混合油漆示例。
图片来源：<https://upload.wikimedia.org/wikipedia/commons/4/46/Diffie-Hellman_Key_Exchange.svg>

---

## 认证加密

认证加密添加了一个**消息认证码**（MAC），以额外提供加密数据的_真实性_和_完整性_保证。

读者可以检查 MAC 以确保消息是由知道这个秘密的人构建的。

Notes:

具体来说，这种真实性表明，_任何不知道发送者秘密的人_都不可能构建这个消息。

通常，这会为每个加密消息增加约 16-32 字节的开销。

---

## AEAD（**认证加密附加数据**）

AEAD 是通过一些未加密但具有完整性和真实性保证的额外数据进行认证的。

Notes:

认证加密和 AEAD 可以与对称和非对称密码学一起使用。

---

## AEAD 示例

想象一下，在一个表中存储了加密的医疗记录，其中数据是使用 AEAD 存储的。这种方案有什么优点？

```text
用户 ID -> 数据（加密），用户 ID（附加数据）
```

Notes:
通过使用这种方案，数据总是与用户 ID 相关联。攻击者无法将该条目放入另一个用户的条目中。

---

## 混合加密

混合加密结合了所有加密世界的最佳方案。非对称加密在发送者和特定公钥之间建立一个共享秘密，然后使用对称加密来加密实际消息。它也可以进行认证。

Notes:

在实践中，非对称加密_几乎总是_混合加密。

---

## 加密属性

| 属性        | 对称   | 非对称   | 认证   | 混合+认证   |
| --------------- | --------- | ---------- | ------------- | ---------------------- |
| 机密性        | 是       | 是        | 是           | 是                    |
| 真实性        | 是\*     | 否         | 是\*         | 是                    |
| 完整性        | 否\*      | 否\*       | 是           | 是                    |
| 不可否认性    | 否        | 否\*       | 否            | 否\*                   |

Notes:

- 对称-认证和认证-真实性：消息只能由知道共享密钥的人发送。在大多数情况下，这在功能上是对接收方的认证。
- 对称-完整性和非对称-完整性：没有适当的完整性检查，但是如果消息被更改，更改的部分将是乱码。检测乱码可以作为一种形式的完整性检查。
- 不可否认性：尽管这些原语本身都不提供不可否认性，但通过签名可以很容易地将不可否认性添加到非对称和混合方案中。
- 请注意，加密还最重要的是使数据对所有应该访问的人都可用。

---

<!-- .slide: data-background-color="#4A2439" -->

# Questions