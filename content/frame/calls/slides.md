---
title: FRAME Calls
description: FRAME Calls for web3 builders.
duration: 1 hour
---

# FRAME 调用

---

## 概述

调用允许用户与你的状态转换函数进行交互。

在本讲座中，你将学习如何使用 FRAME 为你的 Pallet 创建调用。

---

## 术语

“调用（call）”、“外部交易（extrinsic）”和“可调度（dispatchable）” 这些术语经常被混淆。

下面这句话应该有助于阐明它们之间的关系，以及为什么它们如此相似：

> 用户向区块链提交一个 **外部交易**，该交易被 **调度** 到一个 Pallet **调用**。

---

## 调用定义

下面是一个简单的 Pallet 调用。让我们来详细分析一下。

```rust
#[pallet::call(weight(<T as Config>::WeightInfo))]
impl<T: Config> Pallet<T> {
    #[pallet::call_index(0)]
    pub fn transfer(
        origin: OriginFor<T>,
        dest: AccountIdLookupOf<T>,
        #[pallet::compact] value: T::Balance,
    ) -> DispatchResult {
        let source = ensure_signed(origin)?;
        let dest = T::Lookup::lookup(dest)?;
        <Self as fungible::Mutate<_>>::transfer(&source, &dest, value, Expendable)?;
        Ok(())
    }
}
```

---

## 调用实现

调用只是在 `Pallet` 结构体上实现的函数。

你可以使用任何类型的函数来实现，不过，“FRAME 魔法” 通过 `#[pallet::call]` 宏将这些函数转换为可调度的调用。

---

## 调用来源

每个 Pallet 调用都必须有一个 `origin` 参数，该参数使用 `OriginFor<T>` 类型，该类型来自 `frame_system`。

```rust
/// 系统配置的 `Origin` 关联类型的类型别名。
pub type OriginFor<T> = <T as crate::Config>::RuntimeOrigin;
```

---

## 来源

frame 中可用的基本来源有：

```rust
/// System Pallet 的来源。
#[derive(PartialEq, Eq, Clone, RuntimeDebug, Encode, Decode, TypeInfo, MaxEncodedLen)]
pub enum RawOrigin<AccountId> {
    /// 系统本身规定此调度发生：这是最高特权级别。
    Root,
    /// 由某个公钥签名，我们提供 `AccountId`。
    Signed(AccountId),
    /// 无人签名，可以是以下两种情况之一：
    /// * 无论如何都被验证者包含并认可，
    /// * 或者是由一个 Pallet 验证的无签名交易。
    None,
}
```

我们将有另一个演示深入探讨来源。

---

## 来源检查

通常，在调用中你要做的第一件事是检查调用者的来源是否符合你的预期。

最常见的情况是检查外部交易是否为 `Signed`，即来自用户账户的交易。

```rust
let caller: T::AccountId = ensure_signed(origin)?;
```

---

## 调用参数

Pallet 调用可以有除 `origin` 之外的其他参数，允许你提交有关调用者想要执行的操作的相关数据。

所有调用参数都需要满足 `Parameter` 特征：

```rust
/// 一种可以在可调度函数中用作参数的类型。
pub trait Parameter: Codec + EncodeLike + Clone + Eq + fmt::Debug + scale_info::TypeInfo {}
impl<T> Parameter for T where T: Codec + EncodeLike + Clone + Eq + fmt::Debug + scale_info::TypeInfo {}
```

---

## 参数限制

几乎任何东西都可以用作调用参数，甚至是普通的 `Vec`，不过，FRAME 会确保编码后的块小于最大块大小，这从本质上限制了外部交易的长度。

在 Polkadot 中，目前这个大小是 5 MB。

---

## 紧凑参数

可以在调用中使用紧凑编码的参数。

```rust
pub fn transfer(
    origin: OriginFor<T>,
    dest: AccountIdLookupOf<T>,
    #[pallet::compact] value: T::Balance,
) -> DispatchResult { ... }
```

这可以节省大量字节，尤其是在像上面所示的余额这种情况下。

---

## 调用逻辑

调用中最相关的部分是“调用逻辑”。

这里并没有什么神奇的地方，只是普通的 Rust 代码。

不过，你必须遵循一个重要规则……

---

## 调用绝不能 panic

在任何情况下（除非可能是存储进入无法修复的损坏状态），这个函数都绝不能 panic。

允许调用者从调用中触发 panic 会让用户通过绕过费用或与在区块链上执行逻辑相关的其他成本来攻击你的链。

---

## 调用返回

每个调用都返回一个 `DispatchResult`：

```rust
pub type DispatchResult = Result<(), sp_runtime::DispatchError>;
```

这允许你在运行时处理错误，并且永远不要 panic！

---

## 返回错误

在你的调用逻辑中的任何时候，你都可以返回一个 `DispatchError`。

```rust
ensure!(new_balance >= min_balance, Error::<T, I>::LiquidityRestrictions);
```

当你这样做时，多亏了事务性存储层，所有修改过的状态都会被回滚。

---

## 返回成功

如果你的 Pallet 中的所有操作都成功完成，你只需返回 `Ok(())`，并且你所有的状态更改都会被提交，外部交易将被视为已成功执行。

---

## 调用索引

最好的做法是用 `call_index` 显式标记你的调用。

```rust
#[pallet::call_index(0)]
```

这可以帮助确保对你的 Pallet 所做的更改不会导致交易格式的破坏性更改。

---

## 调用编码

从高层来看，一个调用被编码为两个字节（加上任何参数）：

1. Pallet 索引
1. 调用索引

Pallet 索引来自 `construct_runtime!` 的顺序 / 显式编号。如果顺序发生变化，而没有显式标记，由钱包（如账本）生成的交易可能会出错！

Notes:

请注意，这也意味着由于 1 字节编码，每个 Pallet 最多只能有 256 个调用。

---

## 权重

每个调用还必须指定一个调用 `weight`。

我们还有另一个关于权重和基准测试的讲座，但从高层来看，这个权重函数告诉我们调用的复杂程度，以及应该向用户收取的费用。

---

## 每个调用的权重

可以为每个调用指定权重：

```rust [3]
#[pallet::call]
impl<T: Config> Pallet<T> {
    #[pallet::weight(T::WeightInfo::transfer())]
    #[pallet::call_index(0)]
    pub fn transfer(
        origin: OriginFor<T>,
        dest: AccountIdLookupOf<T>,
        #[pallet::compact] value: T::Balance,
    ) -> DispatchResult {
        let source = ensure_signed(origin)?;
        let dest = T::Lookup::lookup(dest)?;
        <Self as fungible::Mutate<_>>::transfer(&source, &dest, value, Expendable)?;
        Ok(())
    }
}
```

---

## Pallet 的权重

或者为 Pallet 中的所有调用指定权重：

```rust [1]
#[pallet::call(weight(<T as Config>::WeightInfo))]
impl<T: Config> Pallet<T> {
    #[pallet::call_index(0)]
    pub fn transfer(
        origin: OriginFor<T>,
        dest: AccountIdLookupOf<T>,
        #[pallet::compact] value: T::Balance,
    ) -> DispatchResult {
        let source = ensure_signed(origin)?;
        let dest = T::Lookup::lookup(dest)?;
        <Self as fungible::Mutate<_>>::transfer(&source, &dest, value, Expendable)?;
        Ok(())
    }
}
```

在这种情况下，假设所有调用的权重函数名称都与调用名称匹配。

Notes:

<https://github.com/paritytech/substrate/pull/13932>

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题