---
title: FRAME Pallet Coupling
description: A look into how multiple pallets interact.
duration: 1 hour
---

# 模块耦合

---

## 概述

Substrate 致力于构建模块化和可组合的区块链运行时。

FRAME 运行时的构建块是模块（Pallets）。

模块耦合将教你如何配置多个模块以使其相互交互。

---

## 耦合类型

- 紧密耦合的模块

  - 直接相互连接的模块。
  - 你必须使用完全紧密耦合的模块来构建运行时。

- 松散耦合的模块

  - 通过特性（trait）/接口“松散”连接的模块。
  - 你可以使用任何满足所需接口的模块来构建运行时。

---

## 紧密耦合的模块

紧密耦合通常是一种让两个模块相互交互的更简单但灵活性较低的方式。

它看起来像这样：

```rust [2]
#[pallet::config]
pub trait Config: frame_system::Config + pallet_treasury::Config {
	// -- snip --
}
```

请注意，所有内容都与 `frame_system` 紧密耦合！

---

## 这意味着什么？

如果模块 A 与模块 B 紧密耦合，那么基本上意味着：

> 这个模块 A 需要一个也配置了模块 B 的运行时。

你不一定需要模块 A 才能使用模块 B，但如果你使用模块 A，则始终需要模块 B。

---

## 示例：国库模块

国库模块（Treasury Pallet）是一个独立的模块，用于控制一笔资金，这些资金可以由链的治理机制进行分配。

还有另外两个与国库模块紧密耦合的模块：小费模块（Tips）和赏金模块（Bounties）。

你可以把它们想象成“模块扩展”。

---

## 国库、小费、赏金

`pallet_treasury`

```rust
#[pallet::config]
pub trait Config<I: 'static = ()>: frame_system::Config { ... }
```

`pallet_tips` 和 `pallet_bounties`

```rust
#[pallet::config]
pub trait Config<I: 'static = ()>: frame_system::Config + pallet_treasury::Config<I> { ... }
```

---

## 紧密耦合错误

当你尝试使用紧密耦合的模块而没有配置适当的模块依赖项时，会看到以下类型的错误：

```rust
error[E0277]: the trait bound `Test: pallet_treasury::Config` is not satisfied
   --> frame/sudo/src/mock.rs:149:17
    |
149 | impl Config for Test {
    |                 ^^^^ the trait `pallet_treasury::Config` is not implemented for `Test`
    |
n o t e: required by a bound in `pallet::Config`
   --> frame/sudo/src/lib.rs:125:43
    |
125 |     pub trait Config: frame_system::Config + pallet_treasury::Config{
    |                                              ^^^^^^^^^^^^^^^^^^^^^^^ required by this bound in `Config`

For more information about this error, try `rustc --explain E0277`.
error: could not compile `pallet-sudo` due to previous error
warning: build failed, waiting for other jobs to finish...
```

---

## 紧密耦合的优势

通过紧密耦合，你可以直接访问另一个模块的所有公共函数和接口。就像直接使用一个 crate / 模块一样。

示例：

```rust
// 从 `frame_system` 获取块号
frame_system::Pallet::<T>::block_number()
```

```rust
// 使用另一个模块中定义的类型配置。
let who: T::AccountId = ensure_signed(origin)?;
```

```rust
// 抛出另一个模块中定义的错误。
ensure!(
	bounty.value <= max_amount,
	pallet_treasury::Error::<T, I>::InsufficientPermission
);
```

---

## 何时使用紧密耦合

当试图将一个“大型”模块拆分成更小但完全依赖的部分时，紧密耦合是很有意义的。

如前所述，你可以把它们看作是“扩展”。

由于配置紧密耦合的模块的方式灵活性较低，因此配置它们时出错的可能性也较小。

---

## 松散耦合的模块

松散耦合是构建模块的“首选”方式，因为它强调了模块开发的模块化特性。

它看起来像这样：

```rust [3]
#[pallet::config]
pub trait Config<I: 'static = ()>: frame_system::Config {
	type NativeBalance: fungible::Inspect<Self::AccountId> + fungible::Mutate<Self::AccountId>;

	// -- snip --
}
```

在这里，你可以看到这个模块需要配置一个关联类型 `NativeBalance`，该类型要实现一些特性 `fungible::Inspect` 和 `fungible::Mutate`，但是对于该类型如何或在哪里配置没有要求。

---

## 特性定义

要开始进行松散耦合，你需要定义一个可以被提供和依赖的特性（trait）/接口。一个非常常见的例子是 `fungible::*` 特性，它通常由 `pallet_balances` 实现。

```rust
/// 用于提供对可替代资产的余额检查访问的特性。
pub trait Inspect<AccountId>: Sized {
	/// 用于表示账户余额的标量类型。
	type Balance: Balance;

	/// 系统中的总发行量。
	fn total_issuance() -> Self::Balance;

	/// 系统中排除由系统控制的部分后的总发行量。
	fn active_issuance() -> Self::Balance {
		Self::total_issuance()
	}

	// -- snip --
}
```

`frame/support/src/traits/tokens/fungible/regular.rs`

---

## 特性实现

然后，这个特性可以由一个模块来实现，例如 `pallet_balances`。

```rust
impl<T: Config<I>, I: 'static> fungible::Inspect<T::AccountId> for Pallet<T, I> {
	type Balance = T::Balance;

	fn total_issuance() -> Self::Balance {
		TotalIssuance::<T, I>::get()
	}
	fn active_issuance() -> Self::Balance {
		TotalIssuance::<T, I>::get().saturating_sub(InactiveIssuance::<T, I>::get())

	// -- snip --
}
```

`frame/balances/src/impl_fungible.rs`

任何模块，甚至是你自己编写的模块，都可以实现这个特性。

---

## 特性依赖

另一个模块可以单独依赖这个特性。

```rust [3]
#[pallet::config]
pub trait Config: frame_system::Config {
	type NativeBalance: fungible::Inspect<Self::AccountId> + fungible::Mutate<Self::AccountId>;
}
```

并且可以在整个模块中使用这个特性：

```rust [4-5]
#[pallet::weight(0)]
pub fn transfer_all(origin: OriginFor<T>, to: T::AccountId) -> DispatchResult {
	let from = ensure_signed(origin)?;
	let amount = T::NativeBalance::balance(&from);
	T::NativeBalance::transfer(&from, &to, amount, Expendable)
}
```

---

## 运行时实现

最后，在运行时配置中，我们具体定义哪个模块实现了该特性。

```rust
/// 使用 `fungible::*` 特性的模块的配置。
impl pallet_voting::Config for Runtime {
	type RuntimeEvent = RuntimeEvent;
	type NativeBalance = pallet_balances::Pallet<Runtime>;
}
```

这里的事情就不再是“松散”定义的了。

---

## 松散耦合的挑战

松散耦合更具挑战性，因为你需要提前考虑开发一个灵活的 API，使其对潜在的多种实现都有意义。

你需要尽量不让实现细节影响 API，为这些特性的用户和提供者提供最大的灵活性。

如果做得好，它会非常强大；就像 ERC20 代币格式一样。

---

## 泛型类型的挑战

许多新的模块开发者也觉得松散耦合具有挑战性，因为关联类型是有意不具体定义的。

例如，注意 `fungible::*` 特性有一个泛型 `Balances` 类型。

这允许模块开发者将大多数无符号整数类型（`u32`、`u64`、`u128`）配置为其链的 `Balance` 类型，然而，这也意味着在对这些泛型类型进行数学运算或其他操作时，你需要更加巧妙。

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题

接下来，我们将查看常见的模块和特性，并将亲眼看到许多模块耦合模式。
```
