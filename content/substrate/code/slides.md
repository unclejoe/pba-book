---
title: Substrate; Show Me The Code
description: A hands-on dive into practical matters of substrate, such as docs, CLI and folder structure.
duration: 60 minutes
---


# Substrate; 请展示代码 👨‍💻

---

## Substrate; 请展示代码 👨‍💻

上一讲都是关于高层次的信息；现在我们要将其与实际代码联系起来。

---

## 关于以前的版本

- 这是一个全新的讲座，取代了两个旧讲座，更加关注Rust文档。
- 由于这是第一次，我保留了旧版本供你参考。

> Cambridge-y (adj) - 总体质量不错，但有一些粗糙的边缘或缺陷。特别是与PBA内容相关时。

<!-- .element: class="fragment" -->

---v

### 关于Rust考试

上一批学员的两个主要收获：

- 多写Rust文档，预计会有人阅读。
- 广泛使用类型系统，以便你更好地为FRAME做准备。

Notes:

我个人会尽力让考试有一定难度，但又合理，这样可以让你为深入开发Substrate做好最好的准备。

---v

### 互动环节

- 本讲座将是互动式的。
- 尝试学习技术，而不是具体的主题。 <!-- .element: class="fragment" -->
- 稍后尝试重复这个过程。 <!-- .element: class="fragment" -->

Notes:

我在这里想做的是教你如何种树，而不是给你苹果。

---

## 文档资源

核心资源

- paritytech.github.io
  - `substrate` crate
  - 正在进行中：`frame`、`cumulus`和`polkadot` crate。
- Github
- Substrate/Polkadot StackExchange

高层次资源

- `substrate.io`*
- Discord、Telegram等。

---

## 探索`substrate` crate。

<https://paritytech.github.io/substrate/master/substrate/index.html>

---v

### 从内部看Substrate

从内部看Substrate的划分：

1. `sp`
2. `sc`
3. `frame/pallet/runtime`

Notes:

这部分应该会涵盖。

---v

### Substrate二进制文件

Notes:

另一种方法是在所有toml文件中搜索`[[bin]]`。

---v

### 二进制crate的结构

一个典型的基于Substrate的项目的划分：

1. `node`
   1. 包含一个`main.rs`
   2. `service.rs`
   3. 还有更多！
2. `runtime`
   1. 包含一个`/src/lib.rs`（“_runtime amalgamator_”）
3. 还有更多！

Notes:

`node`是客户端的入口点，`runtime amalgamator for the runtime`。

- 看`node-template`，它只有这两个。
- `node`还有更多内容。
- `polkadot`还有更多内容。

---v

### Substrate命令行界面（CLI）

在文档中学习：

- `--dev`
- `--chain`
- `--tmp`、`--base-path`、`purge-chain`。

Notes:

所有命令：<https://paritytech.github.io/substrate/master/sc_cli/commands/index.html>
一个典型运行命令的所有参数 <https://paritytech.github.io/substrate/master/sc_cli/commands/struct.RunCmd.html>

但每个节点可以决定选择这些命令的哪个子集，以及如何实现它们。

<https://paritytech.github.io/substrate/master/node_template/cli/enum.Subcommand.html>
<https://paritytech.github.io/substrate/master/node_cli/enum.Subcommand.html>

- 执行策略
- 数据库类型
- 日志
- 远程过程调用（RPC）
- 修剪
- 同步模式

---v

## Wasm构建 + `std`特性。

- 如何编译为Wasm？`build.rs`！
- 请确保你的`std`特性设置正确！

Notes:

<https://crates.io/crates/substrate-wasm-builder>（查看环境变量，非常有用！）
<https://docs.substrate.io/build/build-process/>

---v

## 链规范

Notes:

原始的与非原始的

---

## 有史以来最有用的Rust文档技巧 #1

- 搜索特性（trait），找到其实现。
- 示例：`trait Block`、`trait Extrinsic`、`trait Header`。

```rust
trait Config {
  type Foo: SomeTrait<Bar>
}
```

<!-- .element: class="fragment" -->

Notes:

特别是在FRAME中，你经常需要使用上述模式来参数化你的pallet。
只需在Rust文档中搜索该特性，然后找到其实现者！

---

## 额外资源！😋

> 查看演讲者笔记（点击“s” 😉）

<img width="300px" rounded src="../scale/img/thats_all_folks.png" />

Notes:

对于基于Substrate的链来说，一个重要但这里有点缺失的概念是`chain-spec`。一定要在Substrate文档中阅读相关内容。

```
