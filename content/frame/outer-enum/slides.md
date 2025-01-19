---
title: Outer Enum
description: FRAME Outer Enum web3 builders.
duration: 1 hour
---

# 外部枚举

---

## 外部枚举

在本次演示中，你将了解到 FRAME 中常用的一种模式，该模式将许多单独的类型抽象为一个统一的类型，供运行时使用。

这也被称为“聚合”类型。

---

## FRAME 中的枚举

在 FRAME 开发过程中，你会遇到 4 个主要的枚举：

- The Call Enum
- The Event Enum
- The Error Enum
- The Origin Enum

所有这些枚举在各个模块（pallet）中都有某种表示形式，同时也存在于你开发的最终 FRAME 运行时中。

---

### 分解（不涉及 Substrate）

```rust [0|10 - 30|32 - 52|54 - 80|82 - 97|99|102 - 107|109 - 114|116 - 140]
#![allow(non_camel_case_types)]
#![allow(dead_code)]

use parity_scale_codec::Encode;

pub type AccountId = u16;
pub type Balance = u32;
pub type Hash = [u8; 32];

mod balances {
    use crate::*;

    #[derive(Encode)]
    pub enum Call {
        transfer { to: AccountId, amount: Balance },
        transfer_all { to: AccountId },
    }

    #[derive(Encode)]
    pub enum Error {
        InsufficientBalance,
        ExistentialDeposit,
        KeepAlive,
    }

    #[derive(Encode)]
    pub enum Event {
        Transfer { from: AccountId, to: AccountId, amount: Balance },
    }
}

mod democracy {
    use crate::*;

    #[derive(Encode)]
    pub enum Call {
        propose { proposal_hash: Hash },
        vote { proposal_id: u32, aye: bool },
    }

    #[derive(Encode)]
    pub enum Error {
        DuplicateProposal,
    }

    #[derive(Encode)]
    pub enum Event {
        Proposed { proposal_index: Hash },
        Passed { proposal_index: Hash },
        NotPassed { proposal_index: Hash },
    }
}

mod staking {
    use crate::*;

    #[derive(Encode)]
    pub enum Call {
        unstake,
        stake { nominate: Vec<AccountId>, amount: Balance },
    }

    #[derive(Encode)]
    pub enum Error {
        TooManyTargets,
        EmptyTargets,
        AlreadyBonded,
    }

    impl Into<DispatchError> for Error {
        fn into(self) -> DispatchError {
            DispatchError::Module(
                ModuleError {
                    pallet: runtime::Runtime::Staking as u8,
                    error: self as u8,
                }
            )
        }
    }
}

// 类似于 `sp - runtime`
mod runtime_primitives {
    use crate::*;

    #[derive(Encode)]
    pub struct ModuleError {
        pub pallet: u8,
        pub error: u8,
    }

    #[derive(Encode)]
    pub enum DispatchError {
        BadOrigin,
        Module(ModuleError),
    }
}

mod runtime {
    use crate::*;

    #[derive(Encode)]
    pub enum PalletIndex {
        Balances = 0,
        Democracy = 1,
        Staking = 2,
    }

    #[derive(Encode)]
    pub enum RuntimeCall {
        BalancesCall(balances::Call),
        DemocracyCall(democracy::Call),
        StakingCall(staking::Call),
    }

    #[derive(Encode)]
    pub enum RuntimeEvent {
        BalancesEvent(balances::Event),
        DemocracyEvent(democracy::Event),
        // 没有质押事件……甚至不在枚举中。
    }

    // 想象一下上面所有可能的类型……
    impl Into<RuntimeEvent> for balances::Event {
        fn into(self) -> RuntimeEvent {
            RuntimeEvent::BalancesEvent(self)
        }
    }

    // 想象一下上面所有可能的类型……
    impl TryFrom<RuntimeEvent> for balances::Event {
        type Error = ();

        fn try_from(outer: RuntimeEvent) -> Result<Self, ()> {
            match outer {
                Event::BalancesEvent(event) => Ok(event),
                _ => Err(())
            }
        }
    }
}

use runtime_primitives::*;

fn main() {
    let democracy_call = democracy::Call::propose { proposal_hash: [7u8; 32] };
    println!("模块调用:   {:?}", democracy_call.encode());
    let runtime_call = runtime::RuntimeCall::Democracy(democracy_call);
    println!("运行时调用:  {:?}", runtime_call.encode());
    let staking_error = staking::Error::AlreadyBonded;
    println!("模块错误:  {:?}", staking_error.encode());
    let runtime_error: DispatchError = staking_error.into();
    println!("运行时错误: {:?}", runtime_error.encode());
    let balances_event = balances::Event::Transfer { from: 1, to: 2, amount: 3 };
    println!("模块事件:  {:?}", balances_event.encode());
    let runtime_event: runtime::RuntimeEvent = balances_event.into();
    println!("运行时事件: {:?}", runtime_event.encode());
}
```

---

## 外部枚举编码

现在解释了所有不同的运行时类型通常是如何编码的！

```rust [2,4,6,8,10,12]
fn main() {
    let democracy_call = democracy::Call::propose { proposal_hash: [7u8; 32] };
    println!("模块调用:   {:?}", democracy_call.encode());
    let runtime_call = runtime::RuntimeCall::Democracy(democracy_call);
    println!("运行时调用:  {:?}", runtime_call.encode());
    let staking_error = staking::Error::AlreadyBonded;
    println!("模块错误:  {:?}", staking_error.encode());
    let runtime_error: DispatchError = staking_error.into();
    println!("运行时错误: {:?}", runtime_error.encode());
    let balances_event = balances::Event::Transfer { from: 1, to: 2, amount: 3 };
    println!("模块事件:  {:?}", balances_event.encode());
    let runtime_event: runtime::RuntimeEvent = balances_event.into();
    println!("运行时事件: {:?}", runtime_event.encode());
}
```

```sh
模块调用:   [0, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7]
运行时调用:  [1, 0, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7]
模块错误:  [2]
运行时错误: [1, 2, 2]
模块事件:  [0, 1, 0, 2, 0, 3, 0, 0, 0]
运行时事件: [0, 0, 1, 0, 2, 0, 3, 0, 0, 0]
```

---

## 真实运行时

这直接添加到了 `substrate/bin/node - template/runtime/src/lib.rs` 中：

```rust [7,9,11,13,15,17]
#[test]
fn outer_enum_tests() {
    use sp_runtime::{DispatchError, MultiAddress};
    use sp_core::crypto::AccountId32;
    use codec::Encode;

    let balances_call = pallet_balances::Call::<Runtime>::transfer { dest: MultiAddress::Address32([1u8; 32]), value: 12345 };
    println!("模块调用:   {:?}", balances_call.encode());
    let runtime_call = crate::RuntimeCall::Balances(balances_call);
    println!("运行时调用:  {:?}", runtime_call.encode());
    let balances_error = pallet_balances::Error::<Runtime>::InsufficientBalance;
    println!("模块错误:  {:?}", balances_error.encode());
    let runtime_error: DispatchError = balances_error.into();
    println!("运行时错误: {:?}", runtime_error.encode());
    let balances_event = pallet_balances::Event::<Runtime>::Transfer { from: AccountId32::new([2u8; 32]), to: AccountId32::new([3u8; 32]), amount: 12345 };
    println!("模块事件:  {:?}", balances_event.encode());
    let runtime_event: crate::RuntimeEvent = balances_event.into();
    println!("运行时事件: {:?}", runtime_event.encode());
}
```

---

## 真实运行时输出

```sh
模块调用:   [0, 3, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 229, 192]
运行时调用:  [5, 0, 3, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 229, 192]
模块错误:  [2]
运行时错误: [3, 5, 2, 0, 0, 0]
模块事件:  [2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 57, 48, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
运行时事件: [5, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 57, 48, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

一切都和我们无 FRAME 的模拟类似，但类型更加复杂。

---

## 使用外部枚举

使用外部枚举的路径可能有点令人困惑。

- 构成外部枚举的类型来自模块（pallet）。

- 它们在运行时中被聚合。

- 它们可以通过关联类型返回到模块中，并在模块逻辑中使用。

---

### 系统聚合关联类型

你可以看到这些“聚合”类型是 FRAME 系统中的关联类型。

```rust
/// 系统配置特征。由运行时实现。
#[pallet::config]
#[pallet::disable_frame_system_supertrait_check]
pub trait Config: 'static + Eq + Clone {
    /// 可调度调用使用的 `RuntimeOrigin` 类型。
    type RuntimeOrigin: Into<Result<RawOrigin<Self::AccountId>, Self::RuntimeOrigin>> + From<RawOrigin<Self::AccountId>> + Clone + OriginTrait<Call = Self::RuntimeCall>;

    /// 聚合的 `RuntimeCall` 类型。
    type RuntimeCall: Parameter + Dispatchable<RuntimeOrigin = Self::RuntimeOrigin> + Debug + From<Call<Self>>;

    /// 运行时的聚合事件类型。
    type RuntimeEvent: Parameter + Member + From<Event<Self>> + Debug + IsType<<Self as frame_system::Config>::RuntimeEvent>;

    // -- 省略 --
}
```

---

## 模块事件

现在明白为什么我们会在每个使用events的pallet里面添加`Event` 关联类型：

```rust
/// Configure the pallet by specifying the parameters and types on which it depends.
#[pallet::config]
pub trait Config: frame_system::Config {
	/// Because this pallet emits events, it depends on the runtime's definition of an event.
	type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;
}
```

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题
