---
title: Introduction to FRAME
description: An introduction into FRAME, a framework for building Substrate runtimes.
duration: 1 hour
---

# FRAME 简介

---
## 课程计划

<table class="no-bullet-padding">
<tr>
  <td>周一</td>
  <td>周二</td>
  <td>周三</td>
  <td>周四</td>
  <td>周五</td>
  <td>周末</td>
</tr>
<tr class="text-small">
<td>

- Substrate 讲座

</td>
<td>

- Substrate 讲座

</td>
<td>

- FRAME 入门
- 👩‍💻 练习：存在性证明运行时
- 宣布 FRAME 作业

</td>
<td>

- 模块耦合
- FRAME 公共知识（模块与特征）
- FRAME 存储

</td>
<td>

- 事件与错误
- 调用
- 起源
- 外部枚举
- 钩子

</td>
<td>

- 完成无 FRAME 练习

</td>
</tr>
<tr class="text-small">
<td>

- 构建运行时 + 测试
- 👨‍💻 练习：测试
- FRAME 基准测试
- 👨🏾‍💻 练习：基准测试

</td>
<td>

- FRAME 底层原理
  - 深入探讨
  - 执行器
- 签名扩展
- 迁移与尝试运行时

</td>
<td>

- 剩余内容 + 现场编码
- Polkadot 讲座

</td>

<td>

- Polkadot 讲座

</td>
<td>

- Polkadot 讲座

</td>
<td>

- 完成 FRAME 练习

</td>
</tr>
</table>

---

# FRAME 入门

---

## 什么是 FRAME？

FRAME 是一个 Rust 框架，用于更轻松地构建 Substrate 运行时。

---

## 简明解释 FRAME

<pba-flex center>

编写 Sudo 模块：

不使用 FRAME：2210 行代码。

使用 FRAME：310 行代码。

缩小了 7 倍。

</pba-flex>

---

## FRAME 的目标

- 让开发者能够轻松、简洁地进行开发。
- 为模块开发者提供最大的灵活性和兼容性。
- 为运行时开发者提供最大的模块化。
- 尽可能地与原生 Rust 相似。

---

## FRAME 的构建模块

- FRAME 开发
  - 模块
  - 宏
- FRAME 协调
  - FRAME 系统
  - FRAME 执行器
  - 构建运行时

---

## 模块

FRAME 认为区块链运行时应该由各个独立的模块组成。我们将这些模块称为 Pallets。

<img style="height: 600px" src="./img/frame1.svg" />

---

## 模块的构建模块

模块由运行时开发中常见的多个部分组成：

- 可调度的外部函数
- 存储项
- 钩子，用于：
  - 块初始化，
  - 块最终确定（_!= 块确定性，即 GRANDPA_）

---

## 模块的更多构建模块

还有一些不太重要的部分：

- 事件
- 错误
- 与交易池的自定义验证/通信
- 链下工作者
- 还有很多！但你稍后会了解到它们。

---

### “外壳”模块

```rust
pub use pallet::*;

#[frame_support::pallet]
pub mod pallet {
  use frame_support::pallet_prelude::*;
  use frame_system::pallet_prelude::*;

  #[pallet::pallet]
  #[pallet::generate_store(pub(super) trait Store)]
  pub struct Pallet<T>(_);

  #[pallet::config]  // 省略部分
  #[pallet::event]   // 省略部分
  #[pallet::error]   // 省略部分
  #[pallet::storage] // 省略部分
  #[pallet::call]    // 省略部分
}
```

---

## FRAME 宏

Rust 允许你编写宏，宏是一种生成代码的代码。

FRAME 使用宏来简化模块的开发，同时保留使用 Rust 的所有优点。

在本模块中，我们将更详细地研究每个属性。

---

## 亲自试试

- `wc -l` 将会显示一个文件的行数。
- `cargo expand` 将会把宏展开为“纯” Rust 代码。

```sh
➜  substrate git:(master) ✗ wc -l frame/sudo/src/lib.rs
    310 frame/sudo/src/lib.rs

➜  substrate git:(master) ✗ cargo expand -p pallet-sudo | wc -l
    2210
```

---

## FRAME 系统

FRAME 系统是一个模块，在使用 FRAME 时，它被假定始终存在。你可以在每个模块的 `Config` 中看到这一点：

```rust
#[pallet::config]
pub trait Config: frame_system::Config { ... }
```

它包含区块链系统所需的所有最基本的函数和类型。还包含许多低级外部函数，用于直接管理你的链。

<div class="flex-container">
<div class="left-small">

- 块编号
- 账户
- 哈希
- 等等...

</div>
<div class="right text-small">

- `BlockNumberFor<T>`
- `frame_system::Pallet::<T>::block_number()`
- `T::AccountId`
- `T::Hash`
- `T::Hashing::hash(&bytes)`

</div>
</div>

---

## FRAME 执行器

FRAME 执行器是一个“协调器”，定义了基于 FRAME 的运行时的执行顺序。

```rust
/// 实际执行 `block` 的所有转换。
pub fn execute_block(block: Block) { ... }
```

- 初始化块
  - `on_runtime_upgrade` 和 `on_initialize` 钩子
- 初始检查
- 签名验证
- 执行外部函数
  - `on_idle` 和 `on_finalize` 钩子
- 最终检查

---

## 构建运行时

你的最终运行时由模块组成，这些模块通过 `construct_runtime!` 宏组合在一起。

```rust
// 通过组合之前配置的 FRAME 模块来创建运行时。
construct_runtime!(
	pub struct Runtime {
		System: frame_system,
		RandomnessCollectiveFlip: pallet_randomness_collective_flip,
		Timestamp: pallet_timestamp,
		Aura: pallet_aura,
		Grandpa: pallet_grandpa,
		Balances: pallet_balances,
		TransactionPayment: pallet_transaction_payment,
		Sudo: pallet_sudo,
		// 将 pallet-template 中的自定义逻辑包含在运行时中。
		TemplateModule: pallet_template,
	}
);
```

---

## 模块配置

在你将一个模块添加到最终运行时之前，需要按照 `Config` 中的定义对其进行配置。

<div class="flex-container text-small">
<div class="left" style="max-width: 50%;">

在模块中：

```rust
/// 时间戳模块配置特征。
#[pallet::config]
pub trait Config: frame_system::Config {
  type Moment: Parameter + Default + AtLeast32Bit + Scale<Self::BlockNumber, Output = Self::Moment> + Copy + MaxEncodedLen + scale_info::StaticTypeInfo;

  type OnTimestampSet: OnTimestampSet<Self::Moment>;

  #[pallet::constant]
  type MinimumPeriod: Get<Self::Moment>;

  type WeightInfo: WeightInfo;
}
```

</div>

<div class="right" style="max-width: 50%; padding-left: 10px;">

在运行时中：

```rust
/// 时间戳模块配置。

impl pallet_timestamp::Config for Runtime {
  type Moment = u64;

  type OnTimestampSet = Aura;


  type MinimumPeriod = ConstU64<{ SLOT_DURATION / 2 }>;

  type WeightInfo = ();
}
```

</div>
</div>

```
