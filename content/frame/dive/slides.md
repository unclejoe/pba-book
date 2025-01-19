---
title: FRAME Deep Dive
description: FRAME, Pallets, System Pallet, Executive, Runtime Amalgamator.
duration: 1 hour
---

# FRAME 深度剖析

---v

## 议程

回想下面这张图：

<img style="height: 600px" src="./img/frame0.svg" />

Notes:

如果没有 FRAME，就只有运行时和客户端，以及介于两者之间的 API。

---v

## 议程

在本讲座结束时，你将完全理解这张图。

<img style="height: 600px" src="../intro/img/frame1.svg" />

---

## 扩展一个 Pallet

- 拿一个简单的 Pallet 代码，然后对其进行扩展。

- `Pallet` 将交易实现为公共函数。
- `Pallet` 实现了 `Hooks`，以及一些类似 `OnInitialize` 的等效功能。
- `enum Call` 本身只是交易数据的一种编码。

- 并且实现了 `UnfilteredDispatchable`（它只是将调用转发回 `Pallet`）

---v

### 扩展一个 Pallet

- 确保你明白为什么这三个是相同的！

```rust
let origin = ..;

// 函数调用
Pallet::<T>::set_value(origin, 10);

// 调度
Call::<T>::set_value(10).dispatch_bypass_filter(origin);

// 完全限定语法。
<Call<T> as UnfilteredDispatch>::dispatch_bypass_filter(Call::<T>::set_value(10), origin);
```

---

## `construct_runtime!` 和 Runtime Amalgamator。

- 现在，让我们来看一个最小化的运行时合并器。

---v

### `construct_runtime!` 和 Runtime Amalgamator。

- 结构体 `Runtime`
- 实现所有 Pallet 的 `Config` 特征。
- 将所有运行时 API 实现为函数。
- `type System`，`type SimplePallet`。
- `AllPalletsWithSystem` 等等。
  - 并且回想一下，所有 Pallet 都实现了诸如 `Hooks`、`OnInitialize` 之类的功能，并且所有这些特征都是可组合成元组的。
- 枚举 `RuntimeCall`
- 枚举 `RuntimeEvent`、`GenesisConfig` 等等，但这里我们没有涉及。

---

## Executive

- 这部分提前了解的话有点可选性，但我希望你一周后再回顾它，然后完全理解它。

- 我向你介绍 Executive 结构体：

```rust
pub struct Executive<
  System,
  Block,
  Context,
  UnsignedValidator,
  AllPalletsWithSystem,
  OnRuntimeUpgrade = (),
>(..);
```

---v

#### 扩展泛型类型。

```rust
impl<
    // 系统配置，我们现在知道这个了。
    System: frame_system::Config,
    // 块类型。
    Block: sp_runtime::traits::Block<Header = System::Header, Hash = System::Hash>,
    // 包含所有钩子的东西。这里我们对 Pallet 其他方面并不了解。
    AllPalletsWithSystem: OnRuntimeUpgrade
      + OnInitialize<System::BlockNumber>
      + OnIdle<System::BlockNumber>
      + OnFinalize<System::BlockNumber>
      + OffchainWorker<System::BlockNumber>,
    COnRuntimeUpgrade: OnRuntimeUpgrade,
  > Executive<System, Block, Context, UnsignedValidator, AllPalletsWithSystem, COnRuntimeUpgrade>
where
  // 这是关键部分，我们需要学习更多 sp_runtime 特征才能继续。
  Block::Extrinsic: Checkable,
  <Block::Extrinsic as Checkable>::Checked: Applyable
  <<Block::Extrinsic as Checkable>::Checked as Applyable>::Call: Dispatchable<_>,
{...}
```

---v

#### `Block::Extrinsic: Checkable`

- 谁实现了 `Checkable`？
- 没错，就是我们在顶层运行时中确实用作 `Block::Extrinsic` 的 `generic::UncheckedExtrinsic`。回想一下：

```rust
type UncheckedExtrinsic = generic::UncheckedExtrinsic<_, _, _, _>;
type Header = ..
type Block = generic::Block<Header, UncheckedExtrinsic>;
type Executive = frame_executive::Executive<_, Block, ...>;
```

---v

#### `Checkable<_>` 是做什么的？

- 签名验证！

```rust
impl Checkable<_> for UncheckedExtrinsic<_, _, _, _> {
  // 这是输出类型。
  type Checked = CheckedExtrinsic<AccountId, Call, Extra>;

  fn check(self, lookup: &Lookup) -> Result<Self::Checked, TransactionValidityError> {
    ..
  }
}
```

---v

#### `<Block::Extrinsic as Checkable>::Checked: Applyable`

- `UncheckedExtrinsic::Checked` 是 `CheckedExtrinsic`。
- 并且它确实实现了 `Applyable`。

---v

#### `Applyable<_>` 是做什么的？

- 简而言之：`Ok(self.call.dispatch(maybe_who.into()))`

---v

#### 最后：`<<Block::Extrinsic as Checkable>::Checked as Applyable>::Call: Dispatchable`

- 猜猜谁实现了 `Dispatchable`，我们之前已经看过了！
- 就是我们在扩展文件中有的 `enum Call`！

---v

### 回顾一下...

所以，总结一下：

```rust
struct Runtime;

impl frame_system::Config for Runtime {}
impl simple_pallet::Config for Runtime {}

enum Call {
  System(frame_system::Call<Runtime>),
  SimplePallet(simple_pallet::Call<Runtime>),
}

impl Dispatchable for Call {
  fn dispatch(self, origin: _) -> Result<_, _> {
    match self {
      Call::System(system_call) => system_call.dispatch(),
      Call::SimplePallet(simple_pallet_call) => system_pallet_call.dispatch(),
    }
  }
}

struct UncheckedExtrinsic {
  function: Call,
  signature: Option<_>,
}

type Executive = Executive<_, UncheckedExtrinsic, ...>;

//
let unchecked = UncheckedExtrinsic::new();
let checked = unchecked.check();
let _ = checked.apply();
```

