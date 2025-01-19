---
title: Construct Runtime
description: Deep dive into the Construct Runtime macro
duration: 1 hour
---

---
# 构建运行时
---

# `construct_runtime!` 和测试 🔨

---

# 第一部分：运行时构建

---

<img style="height: 600px" src="../intro/img/frame1.svg" />

---

## 模块 <=> 运行时

一个运行时实际上是 ✌️ 两件事：

1. 一个实现了所有模块的 `Config` 的结构体。
2. 一个帮助 `Executive` 实现 `RuntimeApis` 的类型。

---v

### 模块 <=> 运行时

我们通常会使用 `construct_runtime` 构建运行时，一般会构建两次：

1. 对于每个模块，都有一个模拟运行时。
2. 另外有一个真实的运行时。

Notes:

基准测试可以使用这两种运行时。

---

## `construct_runtime`：`Runtime` 类型

```rust [1-100|2]
frame_support::construct_runtime!(
  pub struct Runtime {
    System: frame_system,
    Timestamp: pallet_timestamp,
    Balances: pallet_balances,
    Aura: pallet_aura,
    Dpos: pallet_dpos,
  }
);
```

---v

### `Runtime` 类型

- 它实现了 [很多东西](https://paritytech.github.io/substrate/master/kitchensink_runtime/struct.Runtime.html)！
- 但最重要的是，实现了你所有模块的 `Config` 特征 🫵🏻。

```rust
impl frame_system::Config for Runtime { .. }
impl pallet_timestamp::Config for Runtime { .. }
impl pallet_dpos::Config for Runtime { .. }
```

---v

### `<T: Config>` ==> `Runtime`

> 在你的模块代码中，任何出现 `<T: Config>` 的地方现在都可以替换为 `Runtime`。

```rust[1-2|3-4|5-6]
// 一个普通的公有函数定义在
frame_system::Pallet::<Runtime>::block_number();
// 一个映射的存储获取器。
frame_system::Pallet::<Runtime>::account(42u32);
// 一个存储类型。
frame_system::Account::<Runtime>::get(42u32);
```

---

## `construct_runtime`：模块列表

```rust [3-7|8|1-100]
frame_support::construct_runtime!(
  pub struct Runtime {
    System: frame_system,
    Timestamp: pallet_timestamp,
    Balances: pallet_balances,
    Aura: pallet_aura,
    Dpos: pallet_dpos,
    <NameYouChoose>: path_to_crate,
  }
);
```

---v

### 模块列表

- 关键的是，在底层，这会生成：

```rust
type System = frame_system::Pallet<Runtime>;
type Balances = pallet_balances::Pallet<Runtime>;
..
type DPos = pallet_dpos::Pallet<Runtime>;
```

- 回想一下，`Runtime` 实现了所有模块的 `<T: Config>`。

---v

### 模块列表

```rust
frame_system::Pallet::<Runtime>::block_number(); // 🤮
System::block_number(); // 🥳

frame_system::Pallet::<Runtime>::account(42u32); // 🤮
System::account(42u32); // 🥳
```

---v

### 模块列表

- 接下来生成的另一个关键信息是：

```rust
type AllPallets = (System, Balances, ..., Dpos);
```

<div>

- 这在 `Executive` 中用于调度模块钩子。

```rust
<AllPallets as OnInitialize>::on_initialize();
<AllPallets as OnInitialize>::on_finalize();
```

</div>

<!-- .element: class="fragment" -->

Notes:

问题：`fn on_initialize()` 的执行顺序是什么？
还有 `type AllPalletsWithoutSystem` 以及一些其他变体现在已经不再使用了。

---v

### 模块列表 + 外部枚举

- 生成一些外部类型：

  - `RuntimeCall`
  - `RuntimeEvent`
  - `RuntimeOrigin`
  - `RuntimeGenesisConfig`

Notes:

参见关于单个项目的讲座，以及“外部枚举”讲座。

---v

### 模块列表：`RuntimeCall` 示例

```rust
// 在你的某个模块中，名为 `my_pallet`
#[pallet::call]
impl<T: Config> Pallet<T> {
  fn transfer(origin: OriginFor<T>, from: T::AccountId, to: T::AccountId, amount: u128);
  fn update_runtime(origin: OriginFor<T>, new_code: Vec<u8>);
}
```

```rust
// 在你的模块中展开
enum Call {
  transfer { from: T::AccountId, to: T::AccountId, amount: u128 },
  update_runtime { new_code: Vec<u8> },
}
```

<!-- .element: class="fragment" -->

```rust
// 在你的外部运行时中
enum RuntimeCall {
  System(frame_system::Call),
  MyPallet(my_pallet::Call),
}
```

<!-- .element: class="fragment" -->

---v

### 模块列表：模块部分

```rust [1-100|3-5]
frame_support::construct_runtime!(
  pub struct Runtime {
    System: frame_system::{Pallet, Call, Config, Storage, Event<T>},
    Balances: pallet_balances::{Pallet, Call, Storage, Config<T>, Event<T>},
    Dpos: pallet_dpos,
  }
);
```

- 省略它们将使它们从元数据或“外部/运行时类型”中排除。

<!-- .element: class="fragment" -->

---v

### 模块列表：模块索引

```rust [3-5]
frame_support::construct_runtime!(
  pub struct Runtime {
    System: frame_system::{Pallet, Call, Config, Storage, Event<T>} = 1,
    Balances: pallet_balances = 0,
    Dpos: pallet_dpos = 2,
  }
);
```

---

## `construct_runtime`：最终思考

- `construct_runtime` 中的顺序很重要！
- 回想一下，在调用 `construct_runtime` 时会调用 `integrity_test()`。

```sh
test mock::__construct_runtime_integrity_test::runtime_integrity_tests ... ok
```

---v

### 预览

可能的新语法：

```rust
#[frame::construct_runtime]
mod runtime {
  #[frame::runtime]
  pub struct Runtime;

  #[frame::executive]
  pub struct Executive;

  #[frame::pallets]
  #[derive(RuntimeGenesisConfig, RuntimeCall, RuntimeOrigin)]
  pub type AllPallets = (
    System = frame_system = 0,
    BalancesFoo = pallet_balances = 1,
    BalancesBar = pallet_balances = 2,
    Staking = pallet_staking = 42,
  );
}
```

Notes:

参见：<https://github.com/paritytech/polkadot-sdk/issues/232>

---

# 第二部分：测试

---

## 测试和模拟

一个测试需要一个模拟运行时，所以我们需要进行完整的 `construct_runtime` 😱

.. 但幸运的是，大多数类型都可以被模拟 😮‍💨

<!-- .element: class="fragment" -->

---v

### 测试和模拟

- `u32` 类型的账户 ID。
- `u128` 类型的余额。
- `u32` 类型的区块号。
- ...

---

## 测试：`Get<_>`

- 接下来，我们想要为那些 `Get<_>` 关联类型提供一些值。

```rust
#[pallet::config]
pub trait Config: frame_system::Config {
  type MaxVoters: Get<u32>;
}
```

---v

### 测试：`Get<_>`

```rust
parameter_types! {
  pub const MyMaxVoters: u32 = 16;
}
```

```rust
impl pallet_template::Config for Runtime {
  type MaxVoters = MyMaxVoters;
}
```

<!-- .element: class="fragment" -->

---v

### 测试：`Get<_>`

- 或者，如果你的值始终是常量：

```rust
impl pallet_dpos::Config for Runtime {
  type MaxVoters = frame_support::traits::ConstU32<16>;
}
```

---v

### 测试：`Get<_>`

- 或者，如果你想折磨自己：

```rust
pub struct MyMaxVoters;
impl Get<u32> for MyMaxVoters {
  fn get() -> u32 {
    100
  }
}

impl pallet_dpos::Config for Runtime {
  type MaxVoters = MyMaxVoters;
}
```

---

## 测试：创世配置和构建器

- 接下来，如果你想将一些数据输入到你的模块的创世状态中，我们必须首先正确设置创世配置。

```rust
#[pallet::genesis_config]
#[derive(frame_support::DefaultNoBound)]
pub struct GenesisConfig<T: Config> {
	pub voters: Vec<(T::AccountId, Option<Vote>)>,
}

#[pallet::genesis_build]
impl<T: Config> BuildGenesisConfig for GenesisConfig<T> {
  fn build(&self) {
    for (voter, maybe_vote) in &self.voters {
      // 做一些事情。
    }
  }
}
```

---v

### 测试和模拟：创世配置和构建器

- 然后，我们构建一个构建器模式来构造创世配置。

```rust
#[derive(Default)]
pub struct Builder {
  pub voters: Vec<(u64, Option<Vote>)>,
}
```

```rust
impl Builder {
  pub fn add_voter(mut self, who: u64) -> Self {
    self.voters.push((who, None));
    self
  }
}
```

<!-- .element: class="fragment" -->

---v

### 测试和模拟：创世配置和构建器

- 最后：

```rust
impl Builder {
  pub fn build(self) -> TestExternalities {
    let system = frame_system::GenesisConfig::<Runtime>::default();
    let template_module = crate::GenesisConfig { voters: self.voters, ..Default::default() };
    RuntimeGenesisConfig { system, template_module }.build_storage().unwrap().into()
  }

  pub fn build_and_execute(self, f: impl FnOnce()) {
    let mut ext = self.build();
    ext.execute_with(f);
    // 任何后置检查可以放在这里。
  }
}
```

---v

### 测试和模拟

- 最后，这允许你编写这样的测试：

```rust
#[test]
fn test_stuff() {
  let mut ext = Builder::default()
    .add_voter_with_vote(2, Vote::Aye)
    .add_voter(3)
    build_and_execute(|| {
      // 做一些事情
    });
}
```

---

## 测试：静态 `parameter_types!`

- 如果你想改变 `MyMaxVoters` 怎么办？

<div>

```rust
parameter_types! {
  pub static MyMaxVoters: u32 = 100;
}
```

```rust
MyMaxVoters::set(200);
MyMaxVoters::get();
```

<!-- .element: class="fragment" -->

</div>

---

## 测试：推进区块

- 通常，在你的测试中，你想要模拟一个空区块的推进。
- 没问题！我们可以在测试中伪造一切 🤠

<!-- .element: class="fragment" -->

---v

### 推进区块

```rust
pub fn next_block() {
  let now = System::block_number();
  Dpos::on_finalize(now);
  System::on_finalize(now);

  System::set_block_number(now + 1);

  System::on_initialize(now + 1)
  Dpos::on_initialize(now + 1);
}
```

---v

### 推进区块

```rust
pub fn next_block() {
  let now = System::block_number();
  AllPallets::on_finalize(now);

  System::set_block_number(now + 1);

  AllPallets::on_initialize(now + 1)
}
```

---v

### 推进区块

````rust
```rust
#[test]
fn test() {
  let mut ext = Builder::default()
    .add_validator(1)
    .set_minimum_delegation(200)
    .build();
  ext.execute_with(|| {
    // 初始操作
    next_block();

    // 调度一些调用
    assert!(some_condition);

    next_block();

    // 重复...
  });
}
````

```
---

## 附加资源 😋

> 查看演讲笔记（按 “s” 😉）

Notes:

- 这个 PR 实际上是剑桥 PBA 的成果：<https://github.com/paritytech/substrate/pull/11932>
- <https://github.com/paritytech/substrate/pull/11818>
- <https://github.com/paritytech/substrate/pull/10043>
- 关于 Substrate 中宏的使用：<https://github.com/paritytech/substrate/issues/12331>
- 关于高级测试的讨论：<https://forum.polkadot.network/t/testing-complex-frame-pallets-discussion-tools/356>
- 预留主题：读取事件。
- 预留主题：尝试状态。

### 原始演讲脚本

这是你从模块进入运行时的桥梁。

一个运行时聚合器由以下部分组成：

1. 所有模块的 `Config` 由一个 `struct Runtime` 实现；
1. 构建 `Executive` 并使用它来实现所有运行时 API。
```
