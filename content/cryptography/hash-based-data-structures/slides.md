---
title: Hash Based Data Structures
description: Hash functions in Substrate
duration: 1 hour
---

# 基于哈希的数据结构

---

## 与基于指针的数据结构的比较

- 哈希引用的是某些数据的内容；
- 指针告诉你在哪里可以找到它；
- 我们不能有哈希循环。

---

## 哈希链

<img style="width: 800px" src="./img/Hash-Chains.png" />

哈希链是使用哈希来连接节点的链表。

Notes:

每个块都有前一个块的哈希。

---

## 默克尔树

<img style="width: 800px" src="./img/Merkle-tree-all-purple.png" />

二叉默克尔树是使用哈希来连接节点的二叉树。

Notes:

Ralph Merkle 是伯克利的校友！

---

## 证明

- 根哈希或头哈希是对整个数据结构的承诺。
- 通过扩展一些但不是全部哈希来生成证明。

_这对于去中心化加密数据系统的无信任性质至关重要！_

---

## 证明：默克尔共路

<img style="width: 800px" src="./img/Merkle-Copaths.png" />

Notes:

给定一个节点的子节点，我们可以计算出该节点。
给定紫色节点和白色叶子，我们可以自下而上计算出白色节点。
如果我们计算出正确的根，这就证明了叶子在树中。

---

## 安全性

抗碰撞性：我们合理地假设每个哈希只有一个原像，因此使数据结构的链接持久耐用（直到密码学被攻破😥）。

Notes:

解释如果这一点失败会发生什么。

---

## 证明大小

叶子的证明大小为 $O(\log n)$，叶子更新的证明大小也为 $O(\log n)$。

---

## 键值数据库和字典树

---

## 键值数据库

数据结构存储一个映射 `key -> value`。我们应该能够：

<pba-flex center>

- `put(key, value)`
- `get(key)`
- `delete(key)`

</pba-flex>

---

## 键值数据库中的可证明性

对于一个可证明的键值数据库，我们还应该能够执行以下操作：

1. 对于任何键，如果 `<key,value>` 在数据库中，我们可以证明它。
1. 如果一个键没有关联的值，我们需要能够证明这一点。

---

## 数据结构类型

- **树** 是有根的、有向无环图，其中每个子节点只有一个父节点。
- **默克尔树** 是使用哈希作为链接的树。
- **字典树** 是一类树，其中：
  - 给定特定的数据，它总是在特定的路径上。
- **基数树** 是一类字典树，其中：
  - 值的位置是由路径上的数字逐个确定的。
- **帕特里夏树** 是优化后的基数树，确保孤独的节点路径被合并到单个节点中。

Notes:

这只是本课程将涵盖的一部分。

---

## 基数字典树

_单词：_ to, tea, ted, ten, inn, A.

<img style="width: 800px" src="./img/Trie.png" />

每个节点在基数 $r$ 上分割下一个数字。

Notes:

在这个图像中，$r$ 是 52（26 个小写字母 + 26 个大写字母）。

---

## 帕特里夏字典树

_单词：_ to, tea, ted, ten, inn, A.

<img style="width: 700px" src="./img/Patricia-Trie.png" />

如果一个序列只有一个选项，我们将它们合并。

<!-- TODO 也许可以添加一些带有扩展节点等的代码。 -->

---

## 帕特里夏字典树结构

```rust
pub enum Node {
  Leaf {
    partial_path: Slice<RADIX>,
    value: Value
  },
  Branch {
    partial_path: Slice<RADIX>,
    children: [Option<Hash>; RADIX],
    value: Option<Value>,
  },
}
```

Notes:

当前的实现实际上使用了专用的“扩展”节点，而不是持有部分路径的分支节点。[这里](https://ethereum.stackexchange.com/questions/39915/ethereum-merkle-patricia-trie-extension-node)有一个很好的解释。

此外，如果值的大小特别大，它将被替换为其值的哈希。

<!-- TODO: 添加一个类似于 Shawn 的 dev-trie-backend-walk 的漂亮插图。 -->

---

## 哈希字典树

- 将任意（或更糟的是，用户确定的）键插入帕特里夏树可能导致高度不平衡的分支，从而增大证明大小和查找时间。
- 解决方案：在插入之前对数据进行预哈希，以使键随机化。
- **抵抗部分碰撞非常重要。**
- 可以是默克尔字典树或常规字典树。

---

## 计算和存储权衡

什么基数 $r$ 是最好的？

- 叶子的证明大小为 $r \log_r n$
  - $r=2$ 给出了一个叶子的最小证明

但是：

- 树的高层分支越多，可以提供更小的批量证明。
- 对于存储，最好读取连续的数据，因此高 $r$ 更好。

---

## 默克尔山脉

- 高效的哈希链证明和更新
- 仅追加数据结构
- 按编号查找元素

---

## 默克尔山脉

<img style="width: 800px" src="./img/U-MMR-13.png" />

Notes:

我们有几个大小为 2 的幂的默克尔树。
这里的树对应于 13 的二进制数字 1。

---

## 默克尔山脉

<img style="width: 800px" src="./img/U-MMR-14.png" />

---

## 默克尔山脉

<img style="width: 800px" src="./img/MMR-13.png" />

Notes:

- 不像二叉树那样平衡，但很接近
- 可以仅在链上更新峰值节点

---

<!--.slide: data-background-color="#4A2439" -->

# 问题
