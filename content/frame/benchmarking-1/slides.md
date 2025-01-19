---
title: FRAME Benchmarking 1
description: How to benchmark Pallets in FRAME.
duration: 1 hours
---

---
标题：FRAME 基准测试 1
描述：如何在 FRAME 中进行基准测试。
时长：1 小时
---

# FRAME 基准测试

## 第一课

---

## 概述

- 权重快速回顾
- 深入了解基准测试

---

## 区块链是有限的

区块链系统是极其受限的环境。

受限因素包括：

- 执行时间 / 区块时间
- 可用存储
- 可用内存
- 网络带宽
- 等等……

---

## 性能与中心化

节点应是去中心化和分布式的。

提高系统要求可能会导致中心化，即只有那些有能力运行该硬件的人才能参与，并且这些硬件可能只能在特定地点获得。

---

## 我们为什么需要基准测试？

基准测试可确保当用户与我们的区块链进行交互时，他们不会使用超出我们网络可用和预期的资源。

---

## 什么是权重？

权重是一个用于跟踪有限区块链资源消耗的通用概念。

---

## Substrate 中的权重是什么？

我们目前主要跟踪两个限制：

- 在“参考硬件”上的执行时间
- 创建默克尔证明所需的数据大小

```rust
pub struct Weight {
	/// 基于某些参考硬件使用的计算时间的权重。
	ref_time: u64,
	/// 有效性证明所使用的存储空间的权重。
	proof_size: u64,
}
```

这已经扩展过一次，未来可能还会扩展。

---

## 权重限制因区块链而异。

- 不同的计算机在 1 秒的计算时间内能够完成的计算量不同。
- 你的区块链的权重会随着时间的推移而变化。
- 更高的硬件要求将带来性能更高的区块链（即每秒交易数），但会限制能够安全参与你网络的验证者类型。
- 证明大小限制对于平行链可能相关，但对于独立链可以忽略。

---

## 哪些因素会影响相对权重？

<pba-cols>

<pba-col>

- 处理器
- 内存
- 硬盘
  - 机械硬盘（HDD）与固态硬盘（SSD）与非易失性内存主机控制器接口（NVME）
- 操作系统
- 驱动程序
- Rust 编译器

</pba-col>
<pba-col>

- 运行时执行引擎
  - 编译型与解释型
- 数据库
  - RocksDB 与 ParityDB 与其他？
- 默克尔树 / 存储格式
- 等等！

</pba-col>
</pba-cols>

---

## 区块导入权重细分

<img style="height: 500px;" src="./img/block-import.svg" />

---

# 基准测试框架

---

## 基准测试计划

<div class="flex-container">
<div class="left-large">

- 使用运行时的经验测量来确定执行外部函数和其他运行时逻辑所需的时间和空间。
- 在最坏情况条件下运行基准测试。
  - 主要目标是确保运行时的安全性。
  - 次要目标是尽可能准确以最大化吞吐量。

</div>
<div class="right">

<img style="height: 600px;" src="./img/benchmarking.svg" />

</div>
</div>

---

## `#[benchmarks]` 宏

```rust
#[benchmarks]
mod benchmarks {
	use super::*;

	#[benchmark]
	fn benchmark_name() {
		/* 设置初始状态 */

		/* 执行外部函数或函数 */
		#[extrinsic_call]
		extrinsic_name();

		/* 验证最终状态 */
		assert!(true)
	}
}
```

---

## 多元线性回归分析

<div class="flex-container">
<div class="left-small">

<img style="height: 600px;" src="./img/linear-regression.svg" />

</div>
<div class="right">

- 我们要求 Substrate 中的任何函数都不能有超线性复杂度。
- 普通最小二乘线性回归。
  - linregress 库
- 支持多个线性系数。
  - Y = Ax + By + Cz + k
- 对于常数时间函数，我们只使用中位数。

</div>
</div>

---

## `benchmark` 命令行工具

使用 `--features runtime-benchmarks` 编译你的节点。

```sh
➜  ~ substrate benchmark --help
与基准测试相关的子命令。
Pallet 基准测试已移至 `pallet` 子命令。

用法: polkadot benchmark <COMMAND>

命令:
  pallet     对 FRAME Pallet 的外部函数权重进行基准测试
  storage    对链快照的存储速度进行基准测试
  overhead   对每个区块和每个外部函数的执行开销进行基准测试
  block      对历史区块的执行时间进行基准测试
  machine    用于对硬件进行基准测试的命令
  extrinsic  对不同外部函数的执行时间进行基准测试
  help       打印此消息或给定子命令的帮助信息

选项:
  -h, --help     打印帮助信息
  -V, --version  打印版本信息
```

---

## `pallet` 子命令

- 对 Pallet 内的函数权重进行基准测试。
  - 任何任意代码都可以进行基准测试。
- 输出生成的权重文件。

```rust
pub trait WeightInfo {
   fn transfer() -> Weight;
   fn transfer_keep_alive() -> Weight;
   fn set_balance_creating() -> Weight;
   fn set_balance_killing() -> Weight;
   fn force_transfer() -> Weight;
}
```

---

# 深入探究

让我们逐步了解基准测试的步骤！

参考：`frame/benchmarking/src/lib.rs`

```rust
-> fn run_benchmark(...)
```

---

## 基准测试过程

对于每个组件，重复以下步骤：

1. 选择要进行基准测试的组件
1. 生成要测试的值的范围（步骤）
1. 白名单已知的数据库键
1. 设置基准测试状态
1. 将状态提交到数据库，清除缓存
1. 获取系统时间（开始）
1. 执行外部函数 / 基准测试函数
1. 获取系统时间（结束）
1. 统计数据库的读写次数
1. 记录数据

---

## 基准测试组件

- 假设有一个包含 3 个组件的函数
  - let x in 1..2;
  - let y in 0..5;
  - let z in 0..10;
- 我们将步骤数设置为 3。
- 每次只改变一个组件，其他组件选择高值。

<!-- prettier-ignore -->
|   | Δx | Δy | Δy | Δz | Δz | max |
| - | -- | -- | -- | -- | -- | --- |
| x | 1  | 2  | 2  | 2  | 2  | 2   |
| y | 5  | 0  | 2  | 5  | 5  | 5   |
| z | 10 | 10 | 10 | 0  | 5  | 10  |

---

## 基于组件评估的基准测试

<img style="height: 600px;" src="./img/components.svg" />

---

## 白名单数据库键

```rust [3]
/// 当前正在处理的区块号。由 `execute_block` 设置。
#[pallet::storage]
#[pallet::whitelist_storage]
#[pallet::getter(fn block_number)]
pub(super) type Number<T: Config> = StorageValue<_, BlockNumberFor<T>, ValueQuery>;
```

- 某些键在每个区块中都会被访问：
  - 区块号
  - 事件
  - 总发行量
  - 等等……
- 我们不希望在基准测试结果中统计这些读写操作。
- 应用于所有正在运行的基准测试。
- 这包括 FRAME 提供的“白名单账户”。

---

## 示例基准测试

身份 Pallet

<img style="height: 500px;" src="./img/identity-icon.svg" />

---

## 身份 Pallet

- 身份可以包含可变数量的信息
  - 姓名
  - 电子邮件
  - 推特
  - 等等……
- 身份可以由可变数量的注册机构进行评判。
- 身份可以与“子身份”建立双向链接
  - 其他账户继承“超级身份”的身份状态

---

## 外部函数：删除身份

```rust
pub fn kill_identity(
	origin: OriginFor<T>,
	target: AccountIdLookupOf<T>,
) -> DispatchResultWithPostInfo {
	T::ForceOrigin::ensure_origin(origin)?;

	// 确定我们要清除的对象。
	let target = T::Lookup::lookup(target)?;

	// 获取他们的押金（并检查他们是否有押金）。
	let (subs_deposit, sub_ids) = <SubsOf<T>>::take(&target);
	let id = <IdentityOf<T>>::take(&target).ok_or(Error::<T>::NotNamed)?;
	let deposit = id.total_deposit() + subs_deposit;
	for sub in sub_ids.iter() { <SuperOf<T>>::remove(sub); }

	// 从他们那里扣除押金。
	T::Slashed::on_unbalanced(T::Currency::slash_reserved(&target, deposit).0);
	Self::deposit_event(Event::IdentityKilled { who: target, deposit });
	Ok(())
}
```

---

## 处理配置

- `kill_identity` 只有在 `ForceOrigin` 调用时才会执行。

```rust
T::ForceOrigin::ensure_origin(origin)?;
```

- 然而，这是由 Pallet 开发者配置的。
- 我们的基准测试需要始终独立于配置工作。
- 我们在特性标志后面添加了一个特殊函数：

```rust
/// 返回一个能够通过 `try_origin` 检查的外部来源。
///
/// ** 仅应用于基准测试！！！ **
#[cfg(feature = "runtime-benchmarks")]
fn successful_origin() -> OuterOrigin;
```

---

## 外部逻辑 / 钩子

```rust
// 确定我们要清除的对象。
let target = T::Lookup::lookup(target)?;
```

- 一般来说，像这样的钩子在运行时是可配置的。
- 每个区块链都有自己的逻辑，因此有自己的权重。
- 我们针对真实的运行时运行基准测试，因此可以得到真实的结果。
- **重要！** 你需要确保 Pallet 开发者和你的 Pallet 用户充分了解这些钩子的限制，否则你的基准测试将不准确。

---

## 确定性存储读写

```rust
// 获取他们的押金（并检查他们是否有押金）。
let (subs_deposit, sub_ids) = <SubsOf<T>>::take(&target);
let id = <IdentityOf<T>>::take(&target).ok_or(Error::<T>::NotNamed)?;
```

- 2 次存储读写操作。
- 这些存储项的大小取决于：
  - 注册机构的数量
  - 附加字段的数量

---

## 可变存储读写

```rust
for sub in sub_ids.iter() { <SuperOf<T>>::remove(sub); }
```

enchmarkinghere you store balances!

- 扣除的资金的处理方式也是可配置的！

---

## 白名单存储

```rust
Self::deposit_event(Event::IdentityKilled { who: target, deposit });
```

- 我们将对事件存储项的更改列入白名单，因此通常除了计算和内存中数据库的权重外，这是“免费”的。

---

## 准备编写你的基准测试

- 3 个组件

  - `R` - 注册机构的数量
  - `S` - 子账户的数量
  - `X` - 附加字段的数量

- 需要做的事情：
  - 为账户设置资金。
  - 注册一个带有附加字段的身份。
  - 为注册机构和子账户设置最坏情况。
  - 考虑 `ForceOrigin` 来进行调用。

---

## 删除身份基准测试

```rust
#[benchmark]
fn kill_identity(
	r: Linear<1, T::MaxRegistrars::get()>,
	s: Linear<0, T::MaxSubAccounts::get()>,
	x: Linear<0, T::MaxAdditionalFields::get()>,
) -> Result<(), BenchmarkError> {
	add_registrars::<T>(r)?;

	let target: T::AccountId = account("target", 0, SEED);
	let target_origin: <T as frame_system::Config>::RuntimeOrigin = RawOrigin::Signed(target.clone()).into();
	let target_lookup = T::Lookup::unlookup(target.clone());
	let _ = T::Currency::make_free_balance_be(&target, BalanceOf::<T>::max_value());

	let info = create_identity_info::<T>(x);
	Identity::<T>::set_identity(target_origin.clone(), Box::new(info.clone()))?;
	let _ = add_sub_accounts::<T>(&target, s)?;

	// 用户请求所有注册机构进行评判，并且他们都批准了
	for i in 0..r {
		let registrar: T::AccountId = account("registrar", i, SEED);
		let balance_to_use =  T::Currency::minimum_balance() * 10u32.into();
		let _ = T::Currency::make_free_balance_be(&registrar, balance_to_use);

		Identity::<T>::request_judgement(target_origin.clone(), i, 10u32.into())?;
		Identity::<T>::provide_judgement( RawOrigin::Signed(registrar).into(), i, target_lookup.clone(), Judgement::Reasonable, T::Hashing::hash_of(&info),
		)?;
	}
	ensure!(IdentityOf::<T>::contains_key(&target), "Identity not set");
	let origin = T::ForceOrigin::successful_origin();

	#[extrinsic_call]
	kill_identity<T::RuntimeOrigin>(origin, target_lookup)

	ensure!(!IdentityOf::<T>::contains_key(&target), "Identity not removed");
	Ok(())
}
```

---

---
## 基准测试组件

```rust
fn kill_identity(
    r: Linear<1, T::MaxRegistrars::get()>,
    s: Linear<0, T::MaxSubAccounts::get()>,
    x: Linear<0, T::MaxAdditionalFields::get()>,
) -> Result<(), BenchmarkError> {... }
```

- 我们的组件。
  - R = 注册商的数量
  - S = 子账户的数量
  - X = 身份上附加字段的数量。
- 请注意，所有这些都具有可配置的、在编译时已知的最大值。
  - 这是模块配置特征的一部分。
  - 运行时逻辑应该强制执行这些限制。

---

## 准备逻辑

```rust
add_registrars::<T>(r)?;

let target: T::AccountId = account("target", 0, SEED);
let target_origin: <T as frame_system::Config>::RuntimeOrigin = RawOrigin::Signed(target.clone()).into();
let target_lookup = T::Lookup::unlookup(target.clone());
let _ = T::Currency::make_free_balance_be(&target, BalanceOf::<T>::max_value());
```

- 向运行时存储中添加注册商。
- 用适当的资金设置一个账户。
- 注意，这就像编写运行时测试一样！

---

## 可重用的准备函数

```rust
let info = create_identity_info::<T>(x);
Identity::<T>::set_identity(target_origin.clone(), Box::new(info.clone()))?;
let _ = add_sub_accounts::<T>(&target, s)?;
```

- 使用基准测试文件中定义的一些自定义函数：
- 给该账户一个具有 x 个附加字段的身份。
- 给该身份添加 `s` 个子账户。

---

## 设置最坏情况

```rust
// 用户向所有注册商请求评判，并且他们都批准了
for i in 0..r {
    let registrar: T::AccountId = account("registrar", i, SEED);
    let balance_to_use =  T::Currency::minimum_balance() * 10u32.into();
    let _ = T::Currency::make_free_balance_be(&registrar, balance_to_use);

    Identity::<T>::request_judgement(target_origin.clone(), i, 10u32.into())?;
    Identity::<T>::provide_judgement( RawOrigin::Signed(registrar).into(), i, target_lookup.clone(), Judgement::Reasonable, T::Hashing::hash_of(&info),
    )?;
}
```

- 添加 `r` 个注册商。
- 让它们都对该身份进行评判。

---

## 执行并验证基准测试：

```rust
ensure!(IdentityOf::<T>::contains_key(&target), "身份未设置");
let origin = T::ForceOrigin::successful_origin();

#[extrinsic_call]
kill_identity<T::RuntimeOrigin>(origin, target_lookup)

ensure!(!IdentityOf::<T>::contains_key(&target), "身份未移除");
Ok(())
```

- 第一个 `ensure` 语句验证“之前”的状态是否符合我们的预期。
- 我们需要使用我们的自定义来源。
- 验证块确保我们的“最终”状态符合我们的预期。

---

## 执行基准测试

```sh
./target/production/substrate benchmark pallet \
    --chain=dev \                # 可配置的链规范
    --steps=50 \                # 组件范围的步数
    --repeat=20 \                # 我们重复基准测试的次数
    --pallet=pallet_identity \    # 选择模块
    --extrinsic=* \             # 选择外部函数（多个）
    --wasm-execution=compiled \   # 始终使用 `wasm-time`
    --heap-pages=4096 \          # 不是必需的，用于调整内存
    --output=./frame/identity/src/weights.rs \    # 将结果输出到一个 Rust 文件
    --header=./HEADER-APACHE2 \    # 包含模板的自定义头文件
    --template=./.maintain/frame-weight-template.hbs # 手柄模板
```

---

# 查看原始基准测试数据

---

## 结果：外部函数时间与注册商数量的关系

<img style="height: 500px;" src="./img/identity-raw-registrars.png" />

Notes:

图表来源：<https://www.shawntabrizi.com/substrate-graph-benchmarks/old/>

---

## 结果：外部函数时间与子账户数量的关系

<img style="height: 500px;" src="./img/identity-raw-sub.png" />

Notes:

图表来源：<https://www.shawntabrizi.com/substrate-graph-benchmarks/old/>

---

## 结果：外部函数时间与附加字段数量的关系

<img style="height: 500px;" src="./img/identity-raw-fields.png" />

Notes:

图表来源：<https://www.shawntabrizi.com/substrate-graph-benchmarks/old/>

---

## 结果：数据库操作与子账户的关系

<img style="height: 500px;" src="./img/identity-db-sub.png" />

Notes:

图表来源：<https://www.shawntabrizi.com/substrate-graph-benchmarks/old/>

---

## 最终权重

```rust
// 存储：身份 SubsOf（读:1 写:1）
// 存储：身份 IdentityOf（读:1 写:1）
// 存储：系统账户（读:1 写:1）
// 存储：身份 SuperOf（读:0 写:100）
/// 组件 `r` 的范围是 `[1, 20]`。
/// 组件 `s` 的范围是 `[0, 100]`。
/// 组件 `x` 的范围是 `[0, 100]`。
fn kill_identity(r: u32, s: u32, x: u32, ) -> Weight {
    // 最小执行时间：68,794 纳秒。
    Weight::from_ref_time(52_114_486 as u64)
        // 标准误差：4,808
       .saturating_add(Weight::from_ref_time(153_462 as u64).saturating_mul(r as u64))
        // 标准误差：939
       .saturating_add(Weight::from_ref_time(1_084_612 as u64).saturating_mul(s as u64))
        // 标准误差：939
       .saturating_add(Weight::from_ref_time(170_112 as u64).saturating_mul(x as u64))
       .saturating_add(T::DbWeight::get().reads(3 as u64))
       .saturating_add(T::DbWeight::get().writes(3 as u64))
       .saturating_add(T::DbWeight::get().writes((1 as u64).saturating_mul(s as u64)))
}
```

---

## 权重信息生成

```rust
/// pallet_identity 所需的权重函数。
pub trait WeightInfo {
    fn add_registrar(r: u32, ) -> Weight;
    fn set_identity(r: u32, x: u32, ) -> Weight;
    fn set_subs_new(s: u32, ) -> Weight;
    fn set_subs_old(p: u32, ) -> Weight;
    fn clear_identity(r: u32, s: u32, x: u32, ) -> Weight;
    fn request_judgement(r: u32, x: u32, ) -> Weight;
    fn cancel_request(r: u32, x: u32, ) -> Weight;
    fn set_fee(r: u32, ) -> Weight;
    fn set_account_id(r: u32, ) -> Weight;
    fn set_fields(r: u32, ) -> Weight;
    fn provide_judgement(r: u32, x: u32, ) -> Weight;
    fn kill_identity(r: u32, s: u32, x: u32, ) -> Weight;
    fn add_sub(s: u32, ) -> Weight;
    fn rename_sub(s: u32, ) -> Weight;
    fn remove_sub(s: u32, ) -> Weight;
    fn quit_sub(s: u32, ) -> Weight;
}
```

---

## 权重信息集成

```rust
#[pallet::weight(T::WeightInfo::kill_identity(
    T::MaxRegistrars::get(), // R
    T::MaxSubAccounts::get(), // S
    T::MaxAdditionalFields::get(), // X
))]
pub fn kill_identity(
    origin: OriginFor<T>,
    target: AccountIdLookupOf<T>,
) -> DispatchResultWithPostInfo {

    // -- 省略 --

    Ok(Some(T::WeightInfo::kill_identity(
        id.judgements.len() as u32,      // R
        sub_ids.len() as u32,            // S
        id.info.additional.len() as u32, // X
    ))
   .into())
}
```

---

## 初始权重

```rust
#[pallet::weight(T::WeightInfo::kill_identity(
    T::MaxRegistrars::get(), // R
    T::MaxSubAccounts::get(), // S
    T::MaxAdditionalFields::get(), // X
))]
```

- 将权重信息函数用作你函数的权重定义。
- 请注意，我们从一开始就假设是绝对最坏的情况，因为在查询存储之前我们无法知道这些具体值。

---

## 最终权重（退款）

```rust
pub fn kill_identity(...) -> DispatchResultWithPostInfo {... }
```

```rust
Ok(Some(T::WeightInfo::kill_identity(
    id.judgements.len() as u32,      // R
    sub_ids.len() as u32,            // S
    id.info.additional.len() as u32, // X
))
.into())
```

- 然后我们返回最终使用的实际权重！
- 我们使用相同的权重信息公式，但使用执行外部函数时从存储中查询的值。
- 这只允许你**减少**最终权重。如果你返回的权重比初始权重大，将不会发生任何事情。

---

<!--.slide: data-background-color="#4A2439" -->

# 问题

在另一个演示中，我们将介绍我们在基准测试时学到的一些内容以及最佳实践。

---

# 基准测试练习
