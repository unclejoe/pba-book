---
title: Substrate Merklized Storage
duration: 60mins
---

---
title: Substrate 默克尔化存储
duration: 60分钟
---

# Substrate 存储

---

## 目前我们所了解的

<img style="width: 900px;" src="./img/dev-storage-1.svg" />

---v

### 目前我们所了解的

- 回想一下，在 `sp_io` 层，你有**不透明的键和值**。

- `sp_io::storage::get(vec![8, 2])`;
  - `vec![8, 2]` 是一个“存储键”。
- `sp_io::storage::set(vec![2, 9], vec![42, 33])`;

---v

### 目前我们所了解的

术语（有一些简化）：

> 提供主机函数（主要是存储函数）的环境：“`Externalities` 环境”。

Notes:

- 在 Substrate 中，一种类型需要提供主机函数可在其中执行的环境。
- 我们称之为“外部环境”，由 [`trait Externalities`](https://paritytech.github.io/substrate/master/sp_externalities/trait.Externalities.html) 表示。
- 按照惯例，一个外部环境有一个负责处理存储的“**后端**”。

---v

### 目前我们所了解的

```rust
sp_io::TestExternalities::new_empty().execute_with(|| {
  sp_io::storage::get(..);
});
```

---v

### 目前我们所了解的

<img style="width: 900px;" src="./img/dev-storage-2.svg" />

---

## 键值

- 一个键值存储外部环境怎么样？为什么不呢？🙈

---v

### 键值

<img style="width: 1200px;" src="./img/dev-kv-backend.svg" />

---v

### 键值

- “_存储键_”（你传递给 `sp_io::storage` 的任何内容）直接映射到“_数据库键_”。
- O(1) 读写。
- 对所有数据进行哈希运算以得到根。

Notes:

现在是时候明确一下你所说的存储键和数据库键的含义了。

可以想象，在 `sp_io::storage::set` 的实现中，我们将其写入一个键值数据库。

---v

### 键值

- 如果爱丽丝只有这个根，我该如何向她证明她有多少余额呢？

把整个数据库发给她 😱。

<!-- .element: class="fragment" -->

Notes:

爱丽丝代表一个轻客户端，我代表一个全节点。

---v

### 键值

- 此外，如果你更改一个键值对，我们需要再次对整个内容进行哈希运算以得到更新后的状态根 🤦。

---

## Substrate 存储：默克尔化

- 这又让我们回到了为什么基于区块链的系统倾向于对其存储进行“默克尔化”的问题。

---v

### 默克尔化

> Substrate 使用一个基数为 16 的（帕特里夏）基数默克尔树。

---v

<pba-cols>
<pba-col>

<diagram class="mermaid">
%%{init: {'theme': 'dark', 'themeVariables': { 'darkMode': true }}}%%
flowchart TD
  A["A \n 值: Hash(B|C)"] --> B["B \n 值: Hash(B|E)"]
  A --> C["C \n 值: Hash(F) \n"]
  B --> D["D \n 值: 0x12"]
  B --> E["E \n 值: 0x23"]
  C --> F["F \n 值: 0x34"]
</diagram>

</pba-col>
<pba-col>

- 默克尔树。
- 通常在叶子节点包含值。

</pba-col>
</pba-cols>

---v

<pba-cols>
<pba-col>

<diagram class="mermaid">
%%{init: {'theme': 'dark', 'themeVariables': { 'darkMode': true }}}%%
flowchart TD
  A --b--> C["C \n Hash(F) \n"]
  A["A \n 值: Hash(B|C)"] -- a --> B["B \n 值: Hash(B|E)"]
  B --c--> D["D \n 值: 0x12"]
  B --d--> E["E \n 值: 0x23"]
  C --e--> F["F \n 值: 0x34"]
</diagram>

</pba-col>
<pba-col>

- 树状结构（Trie）。
- 假设只有叶子节点有数据，编码如下：

<table>
<tr>
  <td> "ac" => 0x12</td>
</tr>
<tr>
  <td> "ad" => 0x23</td>
</tr>
<tr>
  <td> "be" => 0x34</td>
</tr>
</table>

</pba-col>
</pba-cols>

Notes:

这就是我们如何在树状结构中编码基于键值的数据。

---v

<pba-cols>
<pba-col>

<diagram class="mermaid">
%%{init: {'theme': 'dark', 'themeVariables': { 'darkMode': true }}}%%
flowchart TD
  A["A \n Hash(B|C)"] -- a --> B["B \n Hash(B|E)"]
  A --be--> F["F \n 值: 0x34"]
  B --c--> D["D \n 值: 0x12"]
  B --d--> E["E \n 值: 0x23"]
</diagram>

</pba-col>
<pba-col>

- 基数树。
- 用更少的节点来编码相同的数据。

<table>
<tr>
  <td> "ac" => 0x1234</td>
</tr>
<tr>
  <td> "ad" => 0x1234</td>
</tr>
<tr>
  <td> "be" => 0x1234</td>
</tr>
</table>

</pba-col>
</pba-cols>

Notes:

更多资源：

- <https://en.wikipedia.org/wiki/Merkle_tree>
- <https://en.wikipedia.org/wiki/Radix_tree>
- <https://en.wikipedia.org/wiki/Trie>

具体来说：

> 这种数据结构是由唐纳德·R·莫里森于 1968 年发明的，主要与他相关，还有格诺特·格韦亨伯格。

> 唐纳德·克努特在《计算机程序设计艺术》第三卷的第 498 - 500 页中将这些称为“帕特里夏树”，大概是根据莫里森论文的标题中的首字母缩写：“PATRICIA - 实用的字母数字编码信息检索算法”。今天，帕特里夏树被视为基数为 2 的基数树，这意味着键的每一位都被单独比较，每个节点是一个双向（即左或右）分支。
> ---v

### 默克尔化

> Substrate 使用一个基数为 16 的（帕特里夏）基数默克尔树。

---v

### 默克尔化

- Substrate 实际上在底层使用了一个基于键值的数据库。
- 为了存储树节点，而不是直接存储键！

<br />

<div>

- 我们使用**存储键**，并将其作为树的路径。

</div>
<!-- .element: class="fragment" -->

<div>

- 然后我们将**树节点**（通过**它们的哈希值**引用）存储在主数据库中。

</div>
<!-- .element: class="fragment" -->

---v

### 默克尔化

<img style="width: 1400px;" src="./img/trie-backend-simple-simple.svg" />

Notes:

想象一下：

sp_io::storage::get(b"ad")

---v

### 默克尔化

<img style="width: 1400px;" src="./img/dev-trie-backend-simple.svg" />

Notes:

实际上，存储键类似于 `(system_)16`，但为了简化，我在这里使用了字符串。

---

## 树遍历示例

- 我们知道给定块 `n` 的状态根。
- 假设这是一个基数为 26 的帕特里夏树。
  - 英文字母是键的范围。
- 让我们看看从存储中读取 `balances_alice` 所需的步骤。

---v

<img style="width: 1400px;" src="./img/dev-trie-backend-walk-m1.svg" />

---v

<img style="width: 1400px;" src="./img/dev-trie-backend-walk-0.svg" />

---v

<img style="width: 1400px;" src="./img/dev-trie-backend-walk-1.svg" />

---v

<img style="width: 1400px;" src="./img/dev-trie-backend-walk-2.svg" />

---v

<img style="width: 1400px;" src="./img/dev-trie-backend-walk-full.svg" />

---

## 默克尔化：证明

- 如果爱丽丝只有这个根，我该如何向她证明她有多少余额呢？

---v

<img style="width: 1400px;" src="./img/dev-trie-backend-proof.svg" />

Notes:

重要的一点是，例如，`_system` 下的所有数据并不是隐藏在一个哈希值后面。

深蓝色部分是证明，浅蓝色部分的哈希值是存在的。

接收方将对根节点进行哈希运算，并将其与公开已知的存储根进行比较。

这与代码中实际的证明生成方式略有不同。

一般来说，你需要进行权衡：发送更多数据，但要求爱丽丝进行更少的哈希运算，或者相反（这就是我们所说的“紧凑证明”）。

---v

### 默克尔化：证明

- 🏆 小证明尺寸对于轻客户端和 **Polkadot** 来说是一个重大优势。

---

## 默克尔化：回顾

<div>

- 存储键（你传递给 `sp_io` 的任何内容）是树的路径。

</div>
<!-- .element: class="fragment" -->

<div>

- 存储键的长度是任意的。

</div>
<!-- .element: class="fragment" -->

<div>

- 中间（分支）节点可以包含值。
  - `:code` 包含一些值，`:code:more` 也可以包含值。

</div>

<!-- .element: class="fragment" -->

<div>

- 存储键 != 数据库键。
- 1 次存储访问 = 多次数据库访问。

</div>
<!-- .element: class="fragment" -->

Notes:

你认为会有多少次数据库访问呢？

我们将在接下来的几张幻灯片中解释，但假设是一个阶数为 `N` 的树，并且假设它是平衡的，那么将是 `O(LOG_n)`。

---

## 基数 2、基数 16、基数 26？

- 我们不使用字母表，而是使用所有内容的基数 16 表示。

> 基数 16 的（帕特里夏）默克尔树。

- `System` -> `73797374656d`
- `:code` -> `3a636f646500`

---v

### 基数 2、基数 16、基数 26？

<img style="width: 1400px;" src="./img/dev-trie-backend-16.svg" />
<!-- TODO: 更新图表以表示节点大小。 -->

权衡：“_IO 次数与节点大小_”

<!-- .element: class="fragment" -->

在轻客户端和全节点之间，哪一个更关心哪一个方面呢？

<!-- .element: class="fragment" -->

Notes:

轻客户端更关心节点大小。
当发送证明时，没有 IO 操作。

乍一看，基数为 8 的树似乎更好：通常你需要更少的数据库访问来访问一个键。
例如，对于二进制树，通过 3 次 IO 操作，我们只能访问 8 个项目，但对于基数为 8 的树，可以访问 512 个项目。

那么为什么不选择一个非常宽的树呢？
因为树越宽，每个节点就越大，因为它必须存储更多的哈希值。
在某个点上，这会影响证明大小以及读取/写入/编码/解码所有这些节点的成本。

---v

### 基数 2、基数 16、基数 26？

<img style="width: 1400px;" src="./img/dev-trie-backend-16-with-size.svg" />

注意：

这里有一种不同的表示方法；基数为 16 的树中的节点更大。

---v

### 基数 2、基数 16、基数 26？

- 基数 2：证明小，节点多。
- 基数 8：证明大，节点少。

✅ 16 已经在多年前经过基准测试和研究，被认为是一个很好的折衷选择。

Notes:

任何对区块链和研究感兴趣的人都应该关注这一点。

---

### 不平衡树

<img style="width: 800px;" src="./img/dev-trie-backend-unbalanced.svg" />

---v

### 不平衡树

- 不平衡树意味着性能不平衡。
  - 如果操作得当，这是一个攻击向量。
- 在 FRAME 存储中会有更多关于这方面的内容，以及如何在那里防止这种情况。

Notes:
- 低估权重/Gas消耗等。
- 作为运行时开发者，你必须确保使用正确的键。
- 如果终端用户能够控制他们在树中插入数据的位置，这将是一个特别严重的问题！
- 主要的预防措施是在框架端使用加密安全的哈希函数。
---
## 等一下……🤔
- 默克尔证明不太适用的两种常见场景：
  - 父节点之一包含大量数据。
  - 你想要证明叶节点的删除/不存在。
---v
<img style="width:1400px" src="./img/dev-trie-backend-proof-fat.svg" />
---v
## 等一下……🤔
新的“树格式”🌈：
- 所有超过32字节的数据都将被其哈希值取代。
- （超过32字节的）值本身将以该哈希值为键存储在数据库中。
```rust
struct RuntimeVersion {
  ...
  state_version: 0,
}
```
<!-- .element: class="fragment" -->
---v
<img style="width:1400px" src="./img/dev-trie-backend-proof-fat-fix.svg" />
<!-- TODO:更新图表 -->
这对全节点和轻客户端有什么影响？
Notes:
现在读和写都多了一个步骤，但证明过程更简单了。
埃默里克（Emeric）注释：绿色节点实际上并不是一个“真正的”节点，它只是存储在数据库中的`{ value: BIG_STUFF }`。为了简单起见，我将略过这个细节。可以把绿色节点看作树中的其他节点一样。
---
## Substrate存储：最新情况
<img style="width:1000px" src="./img/dev-storage-3.svg" />
---
## 等一下……🤔
- 在一个区块结束前，我们很少会关注状态根以及所有与树相关的复杂操作……
> 一个针对存储的区块范围缓存。
<!-- .element: class="fragment" -->
Notes:
换句话说，在区块仍在执行时，是否真的需要过于关注更新“树”及其所有哈希计算细节呢？这些都可以延迟处理。
---
## 覆盖层（Overlay）
- 是运行时之外的一个缓存层。
- 它基于键值对工作，而不是树格式。
---v
### 覆盖层（Overlay）
- 其语义与CPU缓存几乎相同：
  - <!-- .element: class="fragment" --> 一旦读取一个值，该值会留在缓存中，后续再次读取成本较低。
  - <!-- .element: class="fragment" --> 一旦写入一个值，只会在缓存中写入。后续可以低成本读取该值。
  - <!-- .element: class="fragment" --> 在运行时API调用结束时，所有写入操作都会被刷新。
- <!-- .element: class="fragment" --> 由于运行时是单线程的，所以不存在竞争条件。
---v
<img style="width:1400px" src="./img/dev-overlay.svg" />
---v
<img style="width:1400px" src="./img/dev-overlay-1.svg" />
---v
<img style="width:1400px" src="./img/dev-overlay-2.svg" />
---v
<img style="width:1400px" src="./img/dev-overlay-3.svg" />
---v
<img style="width:1400px" src="./img/dev-overlay-4.svg" />
---v
<img style="width:1400px" src="./img/dev-overlay-5.svg" />
---v
### 覆盖层（Overlay）
<pba-cols>
<pba-col>
- 低成本 ≠ 无成本
</pba-col>
<pba-col>
<img style="width:700px" src="./img/dev-4-3-io.svg" />
</pba-col>
</pba-cols>
Notes:
- 在代码中，你通常可以选择在栈变量间传递数据，或者从`sp-io`重新读取代码。多数情况下，这是一个微小的优化，影响不大，但一般来说你应该知道前者性能更高，因为不会访问主机。
- 删除操作本质上就是写入`null`。
---v
### 覆盖层（Overlay）
- 覆盖层还能够生成子覆盖层，也称为“_存储层_”。
- 对于实现事务性代码块很有用。
```rust
// 生成一个新层。
with_storage_layer(|| {
    let foo = sp_io::storage::read(b"foo");
    sp_io::storage::set(b"bar", foo);
    if cond {
        Err("this will be reverted")
    } else {
        Ok("This will be commit to the top overlay")
    }
})
```
<!-- .element: class="fragment" -->
Notes:
- 实现为零拷贝。所以，值的大小不是很重要，数量更关键。
---v
<img style="width:1400px" src="./img/dev-overlay-nested.svg" />
---v
<img style="width:1400px" src="./img/dev-overlay-nested-1.svg" />
---v
### 覆盖层（Overlay）
- 可生成的嵌套层数是有限制的。
- 这不是无成本的，因此存在被攻击的风险。
```rust
with_storage_layer(|| {
    let foo = sp_io::storage::read(b"foo");
    with_storage_layer(|| {
        sp_io::storage::set(b"foo", b"foo");
        with_storage_layer(|| {
            sp_io::storage::set(b"bar", foo);
            with_storage_layer(|| {
                sp_io::storage::set(b"foo", "damn");
                Err("damn")
            })
            Ok("what")
        })
        Err("the")
    });
    Ok("hell")
})
```
---v
### 覆盖层（Overlay）
- 如果我在区块执行中间调用`sp_io::storage::root()`会怎样？
- 覆盖层能响应这个调用吗？
Notes:
不能！
覆盖层基于键值对工作，它对树节点一无所知，而要计算根节点，我们必须进入树层，从磁盘拉取大量数据并构建所有节点等。
---v
### 覆盖层：更多缓存
- 树层中也有更多缓存。
但这超出了本次讲座的范围。
```bash
./substrate --help | grep cache
```
Notes:
<https://www.youtube.com/embed/OoMPlJKUULY>
---
### Substrate存储：最终图示
<img style="width:1000px" src="./img/dev-storage-full.svg" />
---v
### Substrate存储
`Externalities`有多种实现：
- [`TestExternalities`](https://paritytech.github.io/substrate/master/sp_state_machine/struct.TestExternalities.html)：
  - 覆盖层（Overlay）
  - 带有`InMemoryBackend`的`TrieDb`
- [`Ext`](https://paritytech.github.io/substrate/master/sp_state_machine/struct.Ext.html)（实际使用的版本 🫡）
  - 覆盖层（Overlay）
  - 以真实数据库为后端的`TrieDb`
---v
### Substrate存储
- 回顾：任何访问主机函数的代码都需要包装在实现`Externalities`的对象中。
```rust
// ❌
let x = sp_io::storage::get(b"foo");
// 错误信息：
// thread '..' panicked at '`get_version_1` called outside of an Externalities-provided environment.'
```
```rust
// ✅
SomeExternalities.execute_with(|| {
  let x = sp_io::storage::get(b"foo");
});
```
---
## 状态修剪
- 每个运行时都会认为通过`sp_io::storage`可以访问其完整状态。
- 那么客户端会为每个区块存储一个完整的树吗？
当然不会。
<!-- .element: class="fragment" -->
Notes:
- 只有在不同区块间发生更新的树节点才会作为新的数据库键被创建。
- 对于未更改的节点，我们只引用现有的节点。
---v
### 状态修剪
<img style="width:1400px" src="./img/dev-4-3-pruning-1.svg" />
---v
### 状态修剪
<img style="width:1400px" src="./img/dev-4-3-pruning-2.svg" />
---v
### 状态修剪
<img style="width:1400px" src="./img/dev-4-3-pruning-3.svg" />
---v
### 状态修剪
<img style="width:1400px" src="./img/dev-4-3-pruning-4.svg" />
---v
## 状态修剪
- 🧠 存储在链上但很少更改的数据？无关紧要。
- 状态修剪完全是客户端侧的优化。
---
## 子树
<img style="width:1400px" src="./img/dev-4-3-child.svg" />
---v
### 子树
- 存储在不同的数据库列（支持类似异步批量删除操作）。
- 最重要的是，可采用不同的树格式。
---
## 树格式很重要！
- 回想在“树遍历”中，我们获取状态根，并从数据库获取根节点。
- 任何基于Substrate的链，包括Polkadot，其状态根都是“树节点”的哈希值。
> 树格式很重要！因此它是[Polkadot规范](https://spec.polkadot.network)的一部分。
Notes:
这意味着，如果另一个客户端想要同步Polkadot，就需要了解树格式的细节。
---
#### 讲座总结/回顾：
<pba-cols>
<pba-col>
- 基于键值对的存储
- 默克尔化存储及证明
- 大型节点
- 基数顺序的影响
- 不平衡树
- 状态修剪
</pba-col>
<pba-col>
<img src="./img/dev-storage-full.svg" />
</pba-col>
</pba-cols>
---
## 更多资源！😋
> 查看演讲者备注（点击“s” 😉）
<img width="300px" rounded src="../scale/img/thats_all_folks.png" />
Notes:
- Shawn的深度剖析：<https://www.shawntabrizi.com/substrate/substrate-storage-deep-dive/>
- Basti关于树缓存的演讲：<https://www.youtube.com/watch?v=OoMPlJKUULY>
- 关于状态版本：
  - <https://github.com/paritytech/substrate/pull/9732>
  - <https://github.com/paritytech/substrate/discussions/11824>
- 一篇关于以太坊树的“经典”文章：<https://medium.com/shyft-network/understanding-trie-databases-in-ethereum-9f03d2c3325d>
- 关于优化Substrate存储证明：<https://github.com/paritytech/substrate/issues/3782>
- Parity维护的底层树库：<https://github.com/paritytech/trie>
- <https://github.com/paritytech/trie/>
- <https://spec.polkadot.network/chap-state#sect-state-storage>
- <https://research.polytope.technology/state-(machine)-proofs>
- 一个有趣但不合理的想法：区块N的运行时能否访问区块N - 1的状态？绝对不行。这听起来可能会让人想问“为什么不行”，但它打破了关于状态转换的所有假设。运行时就是状态转换函数。回想一下其公式，你就会明白为什么不允许这样做。
### 课后反馈
仔细检查`BIG_STUFF`节点的相关描述和示例。如果能有一个示例或练习就好了，让学生调用一系列`sp_io`函数，可视化树结构，并调用证明记录器，查看树的哪些部分确切地包含在证明中。 
