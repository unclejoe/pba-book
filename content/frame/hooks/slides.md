---
title: FRAME/Pallet Hooks
description: FRAME/Pallet Hooks
duration: 1 hour
---

# 🪝 FRAME/Pallet Hooks 🪝

---

## Hooks: All In One

- 链上 / STF
  - `on_runtime_upgrade`
  - `on_initialize`
  - `poll` (开发中)
  - `on_finalize`
  - `on_idle`
- 链下：
  - `genesis_build`
  - `offchain_worker`
  - `integrity_test`
  - `try_state`

Notes:

<https://paritytech.github.io/substrate/master/frame_support/traits/trait.Hooks.html>

---v

### Hooks: All In One

```rust
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  fn on_runtime_upgrade() -> Weight {}
  fn on_initialize() -> Weight {}
  fn on_finalize() {}
  fn on_idle(remaining_weight: Weight) -> Weight {}
  fn offchain_worker() {}
  fn integrity_test() {}
  #[cfg(feature = "try-runtime")]
  fn try_state() -> Result<(), &'static str> {}
}

#[pallet::genesis_build]
impl<T: Config> BuildGenesisConfig for GenesisConfig<T> {
	fn build(&self) {}
}
```

Notes:

这些函数中的许多都将块号作为参数接收，但可以很容易地从 `frame_system::Pallet::<T>::block_number()` 获取。

---

## Hooks: `on_runtime_upgrade`

- 每当 `spec_version`/`spec_name` 升级时调用。
- 你为什么可能会对实现这个函数感兴趣呢？

Notes:

因为运行时升级通常需要伴随某种状态迁移。
有专门的课程讲解，更多内容在那里。

---

## Hooks: `on_initialize`

- 对于任何类型的**自动**操作都很有用。
- 你返回的权重被解释为 `DispatchClass::Mandatory`。

---v

### Hooks: `On_Initialize`

- `Mandatory` Hooks 应该真正轻量级且可预测，具有有界的复杂度。

```rust
fn on_initialize() -> Weight {
  // 任何用户都可以在 `MyMap` 中创建一个条目 😱🔫。
  <MyMap<T>>::iter().for_each(do_stuff);
}
```

<!-- .element: class="fragment" -->

---v

### Hooks: `On_Initialize`

- &shy;<!-- .element: class="fragment" --> 问题：如果你有3个pallet，它们的 `on_initialize` 函数按什么顺序调用？
- &shy;<!-- .element: class="fragment" --> 问题：如果你的运行时在 `on_initialize` 时发生恐慌，你该如何恢复？
- &shy;<!-- .element: class="fragment" --> 问题：如果你的 `on_initialize` 消耗的权重超过了最大块权重会怎样？

Notes:

- 顺序来自 `construct_runtime!` 宏。
- 强制性钩子中的恐慌是致命错误。基本上就没救了。
- 使用强制性钩子的超重块，只有在单链的上下文中才有可能。这样的块生成时间会更长，但最终还是会生成。如果你想成为平行链开发者，你应该把超重块也视为致命错误。

---

## Hooks: `on_finalize`

- 是 `on_initialize` 的扩展，但在块结束时执行。
- 它的权重需要提前知道。因此，与 `on_initialize` 相比，不太推荐使用。

```rust
fn on_finalize() {} // ✅
fn on_finalize() -> Weight {} // ❌
```

<!-- .element: class="fragment" -->

- 与共识上下文中的_最终性_无关。

<!-- .element: class="fragment" -->

---v

### Hooks: `on_finalize`

> 一般来说，除非在块结束时真的需要做些什么，否则避免使用它。

Notes:

有时候，与其想着“在第N个块结束时”，不如考虑编写“在第N + 1个块开始时”的代码。

---

## Hooks: `poll`

- `on_initialize` 的非强制性版本。
- 正在开发中 👷

Notes:

参见：<https://github.com/paritytech/substrate/pull/14279> 及相关的PR

---

## Hooks: `on_idle`

- `on_finalize` 的**可选**变体，也在块结束时执行。
- 小的语义区别：每个块随机执行一个pallet的钩子，而不是所有pallet的钩子。

---v

## 未来：远离强制性钩子

- `on_initialize` -> `poll`
- `on_finalize` -> `on_idle`
- 用于多块迁移的新原语
- 通过外部调用进行可选服务工作的新原语。

Notes:

这都是Parity的FRAME团队2023年的议程。

<https://github.com/paritytech/polkadot-sdk/issues/206>
<https://github.com/paritytech/polkadot-sdk/issues/198>

---

## 回顾：链上/STF Hooks

<diagram class="mermaid">
%%{init: {'theme': 'dark', 'themeVariables': { 'darkMode': true }}}%%
graph LR
    subgraph AfterTransactions
        direction LR
        OnIdle --> OnFinalize
    end

    subgraph OnChain
        direction LR
        Optional --> BeforeExtrinsics
        BeforeExtrinsics --> Inherents
        Inherents --> Poll
        Poll --> Transactions
        Transactions --> AfterTransactions
    end

    subgraph Optional

OnRuntimeUpgrade
end

    subgraph BeforeExtrinsics
        OnInitialize
    end

    subgraph Transactions
        Transaction1 --> UnsignedTransaction2 --> Transaction3
    end

    subgraph Inherents
        Inherent1 --> Inherent2
    end

</diagram>

Notes:

这里隐含的是：

固有函数（Inherents）总是最先执行，相关讨论见：<https://github.com/polkadot-fellows/RFCs/pull/13>

---

## Hooks: `genesis_build`

- 每个pallet在创世时指定一个 $f(input): state$ 的方法。
- 这仅在你**创建新链**时由客户端调用一次。
  - &shy;<!-- .element: class="fragment" --> 每次运行 `cargo run` 时都会调用这个函数吗？
- `#[pallet::genesis_build]`。

---v

### Hooks: `genesis_build`

```rust
#[pallet::genesis_build]
pub struct GenesisConfig<T: Config> {
  pub foo: Option<u32>,
  pub bar: Vec<u8>,
}
```

<!-- .element: class="fragment" -->

```rust
impl<T: Config> Default for GenesisConfig<T> {
  fn default() -> Self {
    // 省略部分代码
  }
}
```

<!-- .element: class="fragment" -->

```rust
#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig<T> {
  fn build(&self) {
    // 使用 self.foo, self.bar 等
  }
}
```

<!-- .element: class="fragment" -->

---v

### Hooks: `genesis_build`

- `GenesisConfig` 是运行时顶层的一个复合/合并项。

```rust
construct_runtime!(
  pub enum Runtime where {
    System: frame_system,
    Balances: pallet_balances,
  }
);
```

```rust
struct RuntimeGenesisConfig {
  SystemConfig: pallet_system::GenesisConfig,
  PalletAConfig: pallet_a::GenesisConfig,
}
```

<!-- .element: class="fragment" -->

Notes:

<https://paritytech.github.io/substrate/master/node_template_runtime/struct.RuntimeGenesisConfig.html>

---v

### Hooks: `genesis_build`

- 最近的更改将 `genesis_build` 改为通过运行时API使用，而不是原生运行时。
- pallet中的 `#[cfg(feature = "std")]` 将不再使用。

Notes:

<https://github.com/paritytech/polkadot-sdk/issues/25>

---

## Hooks: `offchain_worker`

**完全链下应用**：

- 通过RPC读取链状态。
- 将期望的副作用作为交易提交回链上。

**运行时链下工作者**：

- &shy;<!-- .element: class="fragment" --> 代码存在于链上，只能与整个运行时同步升级 👎
- &shy;<!-- .element: class="fragment" --> 符合人体工程学且快速的状态访问 👍
- &shy;<!-- .element: class="fragment" --> 状态写入被忽略 🤷
- &shy;<!-- .element: class="fragment" --> 也可以将交易提交回链上 ✅
- &shy;<!-- .element: class="fragment" --> 容易引起很多困惑！

Notes:

人们常常认为他们可以用链下工作者（OCW）做神奇的事情，请不要这样做。给创始人一个大大的警告，使用时要小心！

<https://paritytech.github.io/substrate/master/pallet_examples/index.html>

---v

### Hooks: `offchain_worker`

- 执行完全由客户端决定。
- 有一个与正常执行完全独立的线程池。

```
--offchain-worker <ENABLED>
    可能的值：
    - always:
    - never:
    - when-authority

--execution-offchain-worker <STRATEGY>
    可能的值：
    - native:
    - wasm:
    - both:
    - native-else-wasm:
```

---v

### Hooks: `offchain_worker`

- 线程可以**重叠**，每个线程读取其对应块的状态

<img style="height: 500px" src="./img/ocw.svg"  />

<!-- .element: class="fragment" -->

Notes:

<https://paritytech.github.io/substrate/master/sp_runtime/offchain/storage_lock/index.html>

---v

### Hooks: `offchain_worker`

- &shy;<!-- .element: class="fragment" --> 链下工作者有自己的**特殊主机函数**：http、专用存储、时间等。
- &shy;<!-- .element: class="fragment" --> 链下工作者具有与Wasm相同的**执行限制**（有限的内存、自定义分配器）。

- &shy;<!-- .element: class="fragment" --> 令人困惑的是，为什么链下工作者不能写入状态。

Notes:

这些就是困惑的来源。

关于Substrate Wasm执行中的分配器限制（可能会更改）：

- 最大单次分配有限制
- 最大总分配有限制。

---

## Hooks: `integrity_test`

- 由 `construct_runtime!` 放入测试中。

```rust
__construct_runtime_integrity_test::runtime_integrity_tests
```

<!-- .element: class="fragment" -->

```rust
fn integrity_test() {
  assert!(
    T::MyConfig::get() > 0,
    "我使用的所有泛型类型都合理吗？"
  );
  // 注意这是用于测试的，std是可用的。
  assert!(std::mem::size_of::<T::Balance>() > 4);
}
```

<!-- .element: class="fragment" -->

Notes:

我想给这个函数改名。如果你也这么想，请在这里评论。

---

## Hooks: `try_state`

- 一种在每次状态转换后确保你的 $STF$ 正确性的方法。
- &shy;<!-- .element: class="fragment" --> 完全链下，自定义运行时API，条件编译。
  - &shy;<!-- .element: class="fragment" --> 由 `try-runtime-cli` 调用，你下周会学到，或者其他人也可以调用
  - &shy;<!-- .element: class="fragment" --> 作业中有相关例子吗？

Notes:

什么是状态转换？要么是一个块，要么是单个外部调用。

---

## Hooks: 回顾

<diagram class="mermaid">
%%{init: {'theme': 'dark', 'themeVariables': { 'darkMode': true }}}%%
graph LR
    subgraph Offchain
        OffchainWorker
        TryState
    end

    subgraph Genesis
        GenesisBuild
    end

    subgraph AfterTransactions
        direction LR
        OnIdle --> OnFinalize
    end

    subgraph OnChain
        direction LR
        Optional --> BeforeExtrinsics
        BeforeExtrinsics --> Inherents
        Inherents --> Poll
        Poll --> Transactions
        Transactions --> AfterTransactions
    end

    subgraph Optional

OnRuntimeUpgrade
end

    subgraph BeforeExtrinsics
        OnInitialize
    end

    subgraph Transactions
        Transaction1 --> UnsignedTransaction2 --> Transaction3
    end

    subgraph Inherents
        Inherent1 --> Inherent2
    end

</diagram>

- 你还能想到哪些钩子？

Notes:

你还能想到哪些点子？

- 一个在pallet首次初始化时调用一次的钩子。
  <https://github.com/paritytech/polkadot-sdk/issues/109>
- 本地的Post/Pre调度钩子：<https://github.com/paritytech/polkadot-sdk/issues/261>
- 全局的Post/Pre调度钩子实际上是一个签名扩展。它必须存在于运行时中，因为你必须指定顺序。

---

## 更多资源！😋

> 查看演讲笔记（点击“s” 😉）

Notes:

## 课后笔记

关于链下工作者只能与网络同步升级的这个缺点。链下工作者，就像tx-pool api一样，只能从链下上下文调用。节点运行者可以随时使用运行时覆盖功能轻松更改其链下工作者的行为。
