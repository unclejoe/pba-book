---
title: Migrations and Try Runtime
description: Runtime upgrades and how to survive them
---

# 迁移与尝试运行时

---

## 运行时升级...

### _以及如何应对这些升级_

---

### _在本讲座结束时，你将能够：_

- 说明何时需要运行时迁移。
- 编写一个完整的包含迁移的运行时升级，端到端。
- 在网络上执行之前，使用 `try-runtime` 和 `remote-externalities` 测试运行时升级。

---

## 何时需要迁移？

---v

### 何时需要迁移？

- 在典型的运行时升级中，你通常只替换 `:code:`。这就是 _**运行时升级**_。
- 如果你改变了 _存储布局_，那么这也是 _**运行时迁移**_。

> 任何改变 **编码** 的操作都是迁移！

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, Foo>;
```

```rust
// 旧
pub struct Foo(u32)
// 新
pub struct Foo(u64)
```

- 一个明显的迁移。

<!-- .element: class="fragment" -->

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, Foo>;
```

```rust
// 旧
pub struct Foo(u32)
// 新
pub struct Foo(i32)
// 或者
pub struct Foo(u16, u16)
```

- 数据仍然 _适配_，但 _解释_ 几乎肯定不同！

<!-- .element: class="fragment" -->

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, Foo>;
```

```rust
// 旧
pub struct Foo { a: u32, b: u32 }
// 新
pub struct Foo { a: u32, b: u32, c: u32 }
```

- 这仍然是一个迁移，因为 `Foo` 的解码发生了变化。

<!-- .element: class="fragment" -->

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, Foo>;
```

```rust
// 旧
pub struct Foo { a: u32, b: u32 }
// 新
pub struct Foo { a: u32, b: u32, c: PhantomData<_> }
```

- 如果由于某种原因 `c` 的类型其编码类似于 `()`，那么这将可行。

<!-- .element: class="fragment" -->

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, Foo>;
```

```rust
// 旧
pub enum Foo { A(u32), B(u32) }
// 新
pub enum Foo { A(u32), B(u32), C(u128) }
```

- 扩展枚举更有趣，因为如果你将变体添加到末尾，则不需要迁移。

<!-- .element: class="fragment" -->

- 假设没有值初始化为 `C`，这 _不是_ 一个迁移。

<!-- .element: class="fragment" -->

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, Foo>;
```

```rust
// 旧
pub enum Foo { A(u32), B(u32) }
// 新
pub enum Foo { A(u32), C(u128), B(u32) }
```

- 你可能 _永远_ 都不想这么做，但这是一个迁移。

<!-- .element: class="fragment" -->

---v

### 🦀 Rust 回顾 🦀

枚举被编码为变体枚举，后跟内部数据：

- 顺序很重要！在 `struct` 和 `enum` 中都是如此。
- 实现 `Encode` 的枚举不能有超过 255 个变体。

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, u32>;
```

```rust
// 新
#[pallet::storage]
pub type BarValue = StorageValue<_, u32>;
```

- 到目前为止，一切都在改变 _值_ 的格式。<br />

<div>

- _键_ 的改变也是一个迁移！

</div>

<!-- .element: class="fragment" -->

---v

### 何时需要迁移？

```rust
#[pallet::storage]
pub type FooValue = StorageValue<_, u32>;
```

```rust
// 新
#[pallet::storage_prefix = "FooValue"]
#[pallet::storage]
pub type I_can_NOW_BE_renamEd_hahAA = StorageValue<_, u32>;
```

- 如果你必须重命名存储类型，这是一个方便的宏。<br />
- 这 _不需要_ 迁移。

<!-- .element: class="fragment" -->

---

## 编写运行时迁移

- 现在我们知道了如何检测存储更改是否为 **迁移**，让我们看看如何编写一个迁移。

---v

### 编写运行时迁移

- 一旦你升级了运行时，代码期望数据采用新格式。
- 任何 `on_initialize` 或事务可能会在解码数据时失败，并可能导致 `panic!`

---v

### 编写运行时迁移

- 我们需要一个 **_钩子_**，它作为新运行时的一部分 **只执行一次**...
- 但要在使用新运行时的 **任何** 其他代码（`on_initialize`、任何事务）迁移之前执行。

> 这就是 `OnRuntimeUpgrade`。

<!-- .element: class="fragment" -->

---v

### 编写运行时迁移

- 可选活动：进入 `executive` 和 `system`，找出 `OnRuntimeUpgrade` 是如何仅在代码更改时调用的！

---

## 模块内部迁移

---v

### 模块内部迁移

编写迁移的一种方法是在模块内部编写。

```rust
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  fn on_runtime_upgrade() -> Weight {
    migrate_stuff_and_things_here_and_there<T>();
  }
}
```

> 这种方法可能会被弃用，并且 Parity 也不再使用。

<!-- .element: class="fragment" -->

---v

### 模块内部迁移

```rust [4-8]
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  fn on_runtime_upgrade() -> Weight {
    if guard_that_stuff_has_not_been_migrated() {
      migrate_stuff_and_things_here_and_there<T>();
    } else {
      // 无操作
    }
  }
}
```

- 如果你执行 `migrate_stuff_and_things_here_and_there` 两次，那你就惨了 😫。

---v

### 模块内部迁移

**从历史上看**，使用的是类似这样的代码：

```rust [1-7|9-19]
#[derive(Encode, Decode, ...)]
enum StorageVersion {
  V1, V2, V3, // 每个版本添加一个新的变体
}

#[pallet::storage]
pub type Version = StorageValue<_, StorageVersion>;

#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  fn on_runtime_upgrade() -> Weight {
    if let StorageVersion::V2 = Version::<T>::get() {
      // 执行迁移
      Version::<T>::put(StorageVersion::V3);
    } else {
      // 无操作
    }
  }
}
```

---v

### 模块内部迁移

- FRAME 引入了宏来管理迁移：`#[pallet::storage_version]`。

```rust
// 你当前的存储版本。
const STORAGE_VERSION: StorageVersion = StorageVersion::new(2);

#[pallet::pallet]
#[pallet::storage_version(STORAGE_VERSION)]
pub struct Pallet<T>(_);
```

- 这为 `Pallet<_>` 结构体添加了两个函数：

```rust
// 读取当前版本，编码在代码中。
let current = Pallet::<T>::current_storage_version();
// 读取链上编码的版本。
Pallet::<T>::on_chain_storage_version();
// 同步这两个版本。
current.put::<Pallet<T>>();
```

---v

### 模块内部迁移

```rust
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  fn on_runtime_upgrade() -> Weight {
    let current = Pallet::<T>::current_storage_version();
    let onchain = Pallet::<T>::on_chain_storage_version();

    if current == 1 && onchain == 0 {
      // 执行操作
      current.put::<Pallet<T>>();
    } else {
    }
  }
}
```

将版本存储为 u16 类型，存储位置为 [`twox(pallet_name) ++ twox(:__STORAGE_VERSION__:)`](https://github.com/paritytech/polkadot-sdk/blob/c7c5fc7/substrate/frame/support/src/traits/metadata.rs#L163)。

---

## 外部迁移

---v

### 外部迁移

- 在模块内部管理迁移可能会很困难。
- 特别是对于那些想要使用外部模块的人来说。

替代方案：

- 每个运行时都可以显式地将任何实现 `OnRuntimeUpgrade` 的内容传递给 `Executive`。
- 最终，`Executive` 会执行：
  - `<(COnRuntimeUpgrade, AllPalletsWithSystem) as OnRuntimeUpgrade>::on_runtime_upgrade()`。

<!-- .element: class="fragment" -->

---v

### 外部迁移

- 外部迁移的主要目的是更清晰地表明：
- “_在升级到 spec_version xxx 时，到底执行了哪些迁移_”

---v

### 外部迁移

- 将你的迁移作为一个独立的函数或实现 `OnRuntimeUpgrade` 的结构体暴露在 `pub mod v<版本号>` 中。

```rust
pub mod v3 {
  pub struct Migration;
  impl OnRuntimeUpgrade for Migration {
    fn on_runtime_upgrade() -> Weight {
      // 执行操作
    }
  }
}
```

---v

### 外部迁移

- 使用 `pallet::storage_version` 保护迁移的代码
- 别忘了编写新版本！

```rust
pub mod v3 {
  pub struct Migration;
  impl OnRuntimeUpgrade for Migration {
    fn on_runtime_upgrade() -> Weight {
      let current = Pallet::<T>::current_storage_version();
      let onchain = Pallet::<T>::on_chain_storage_version();

      if current == 1 && onchain == 0 {
        // 执行操作
        current.put::<Pallet<T>>();
      } else {
      }
    }
  }
}
```

---v

### 外部迁移

- 按版本将其传递给运行时。

```rust
pub type Executive = Executive<
  _,
  _,
  _,
  _,
  _,
  (v3::Migration, ...)
>;
```

---v

### 外部迁移

- 讨论：运行时升级脚本可以永远保留吗？还是应该在几个版本后删除？

备注：

简短的回答是可以，但这需要做很多工作。详见此处：<https://github.com/paritytech/polkadot-sdk/issues/296>

---

### `frame-support` 中的实用工具。

- `translate` 方法：
  - 适用于 `StorageValue`、`StorageMap` 等。
- <https://paritytech.github.io/substrate/master/frame_support/storage/migration/index.html>

* `#[storage_alias]` 宏用于为那些要被移除的存储类型创建存储类型。

备注：

想象一下，你想要移除一个存储映射，并且在迁移中你想要遍历它并删除所有项。你想要移除这个存储项，但在迁移代码中最后一次访问它会很方便。这就是 `#[storage_alias]` 发挥作用的地方。

---

## 案例研究

1. 我们摧毁 Polkadot 中所有余额的那一天。
1. 第一次迁移（[`pallet-elections-phragmen`](https://github.com/paritytech/substrate/pull/3948)）。
1. `pallet-elections-phragmen` 中相当独立的迁移。

---

## 测试升级

---v

### 测试升级

- `try-runtime` + `RemoteExternalities` 允许你详细检查和测试运行时，并且可以高度控制环境。

- 它旨在进行试验，并且受诸如 `TryFrom` 之类的特性启发，选择了 `TryRuntime` 这个名称。

---v

### 测试升级

回顾：

- 运行时通过主机函数与客户端通信。
- 此外，客户端通过运行时 API 与运行时通信。
- 提供这些主机函数的环境称为 `Externalities`。
- 其中一个例子是 `TestExternalities`，你已经见过了。

---v

### 测试升级：`remote-externalities`

`remote-externalities` 是一种构建器模式，它将一个实时链的状态加载到 `TestExternalities` 中。

```rust
let mut ext = Builder::<Block>::new()
 .mode(Mode::Online(OnlineConfig {
  	运输方式: "wss://rpc.polkadot.io",
  	模块: vec!["模块A", "模块B", "模块C", "随机前缀"],
  	..默认值
  }))
 .构建()
 .等待
 .unwrap();
```

通过 RPC 读取所有这些数据可能会很慢！

---v

### 测试升级：`remote-externalities`

`remote-externalities` 支持：

- 自定义前缀 -> 读取特定的模块
- 注入自定义键 -> 也读取 `:代码:`。
- 注入自定义键值对 -> 用 `0x00` 覆盖 `:代码:`。
- 读取子树数据 -> 与众筹模块等相关。
- 将所有内容缓存在磁盘中以便重复使用。

---v

### 测试升级：`remote-externalities`

`remote-externalities` 本身是一个非常有用的工具，可用于：

- 回到过去并重新运行一些代码。
- 编写基于真实链状态的单元测试。

---

## 测试升级：`try-runtime`

- `try-runtime` 是一个 CLI 命令，也是一组集成在 substrate 中的自定义运行时 API，允许你进行详细的测试。

-... 包括在真实链的数据上运行新运行时的 `OnRuntimeUpgrade` 代码。

---v

### 测试升级：`try-runtime`

- 关于它可以说很多，最好的资源是 [rust 文档](https://paritytech.github.io/substrate/master/try_runtime_cli/index.html)。

---v

### 测试升级：`try-runtime`

- 你可能会在你的运行时中找到一些使用 `#[cfg(feature = "try-runtime")]` 特性门控的代码。这些代码总是用于测试。
- `pre_upgrade` 和 `post_upgrade`：在 `on_runtime_upgrade` 之前和之后执行的钩子。
- `try_state`：在各种其他地方调用，用于检查模块的不变量。

---v

### 测试升级：`try-runtime`：现场演示。

- 让我们在简陋的 node-template 上精心设计一个迁移 😈...
- 并将余额类型从 u128 迁移到 u64。

---

## 额外资源 😋

> 查看演讲笔记（点击 "s" 😉）

Notes:

- 关于自动版本升级的额外工作：<https://github.com/paritytech/substrate/issues/13107>
- 一个关于 `try-runtime` 和进一步测试运行时的精彩演讲：<https://www.youtube.com/watch?v=a_u3KMG-n-I>

#### 参考资料：

- 📺 [Substrate 关于迁移的研讨会](https://www.youtube.com/watch?v=MQgDV37JrIY>)

Notes:

FIXME：这里将 `docs.google.com/presentation/d/1hr3fiqOI0JlXw0ISs8uV9BXiDQ5mGOQLc3b_yWK6cxU/edit#slide=id.g43d9ae013f_0_82` 列为参考资料，但它不是公开的！

#### 练习思路：

- 找到 Kusama 中提名池模块的存储版本。
- 给它们一段编写糟糕的迁移代码，并尝试修复它。它们需要修复的问题：
  - 迁移依赖于 `<T: Config>`
  - 未正确管理版本
  - 在模块中硬编码。
- 重新执行 2021 年 5 月 25 日 Polkadot 运行时发生 OOM 的那个区块。
