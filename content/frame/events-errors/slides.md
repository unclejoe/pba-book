---
title: Events and Errors
description: FRAME Events and Errors for web3 builders.
duration: 1 hour
---

# 事件与错误

---

## 事件与错误

在本次演示中，我们将介绍在开发FRAME模块（Pallet）时，你可以使用的两种工具，用以说明你的运行时调用是如何执行的。

---

# 错误

---

## 错误简介

并非所有的外部调用（extrinsics）都是有效的。原因可能有多种：

- 外部调用本身的格式有误。（参数错误、编码错误等）
- 状态转换函数不允许该调用。
  - 可能存在时间问题。
  - 用户可能缺少资源。
  - 状态转换可能正在等待其他数据或进程。
  - 等等。

---

## 调度结果

所有模块调用最终都会返回一个 `DispatchResult`。

来源：`substrate/frame/support/src/dispatch.rs`

```rust
pub type DispatchResult = Result<(), sp_runtime::DispatchError>;
```

因此，一个函数可以返回 `Ok(())` 或者某个 `DispatchError`。

---

## 调度错误

来源：`substrate/primitives/runtime/src/lib.rs`

```rust [0|6-10|13-14|15-16]
/// 调度调用失败的原因。
#[derive(Eq, Clone, Copy, Encode, Decode, Debug, TypeInfo, PartialEq, MaxEncodedLen)]
#[cfg_attr(feature = "serde", derive(Serialize, Deserialize))]
pub enum DispatchError {
	/// 发生了某个错误。
	Other(
		#[codec(skip)]
		#[cfg_attr(feature = "serde", serde(skip_deserializing))]
		&'static str,
	),
	/// 未能查找某些数据。
	CannotLookup,
	/// 错误的调用来源。
	BadOrigin,
	/// 模块中的自定义错误。
	Module(ModuleError),
	/// 至少还有一个消费者，因此账户无法被销毁。
	ConsumerRemaining,
	/// 没有提供者，因此账户无法创建。
	NoProviders,
	/// 消费者过多，因此账户无法创建。
	TooManyConsumers,
	/// 与代币有关的错误。
	Token(TokenError),
	/// 算术错误。
	Arithmetic(ArithmeticError),
	/// 已达到事务层的数量上限，或者我们不在事务层中。
	Transactional(TransactionalError),
	/// 资源耗尽，例如尝试读取或写入的数据量过大，无法处理。
	Exhausted,
	/// 状态已损坏；通常这种情况无法自行修复。
	Corruption,
	/// 某些资源（例如预映象）目前不可用。这种情况可能会在稍后自行解决。
	Unavailable,
	/// 不允许使用根来源。
	RootNotAllowed,
}
```

---

## 模块错误

来源：`substrate/primitives/runtime/src/lib.rs`

```rust
/// 在 [`ModuleError`] 中定义的模块特定 `error` 字段的字节数。
/// 在FRAME中，这是模块错误类型的最大编码大小。
pub const MAX_MODULE_ERROR_ENCODED_SIZE: usize = 4;

/// 模块调用失败的原因。
#[derive(Eq, Clone, Copy, Encode, Decode, Debug, TypeInfo, MaxEncodedLen)]
#[cfg_attr(feature = "serde", derive(Serialize, Deserialize))]
pub struct ModuleError {
	/// 模块索引，与元数据模块索引匹配。
	pub index: u8,
	/// 模块特定的错误值。
	pub error: [u8; MAX_MODULE_ERROR_ENCODED_SIZE],
	/// 可选的错误消息。
	#[codec(skip)]
	#[cfg_attr(feature = "serde", serde(skip_deserializing))]
	pub message: Option<&'static str>,
}
```

因此，一个错误最多只占5个字节。

---

## 声明错误

```rust [0|23-30|47-48]
#![cfg_attr(not(feature = "std"), no_std)]

pub use pallet::*;

#[frame_support::pallet]
pub mod pallet {
	use frame_support::pallet_prelude::*;
	use frame_system::pallet_prelude::*;

	/// 通过指定模块所依赖的参数和类型来配置模块。
	#[pallet::config]
	pub trait Config: frame_system::Config {
		/// 由于此模块会发出事件，因此它依赖于运行时对事件的定义。
		type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;
	}

	#[pallet::pallet]
	pub struct Pallet<T>(_);

	#[pallet::storage]
	pub type CurrentOwner<T: Config> = StorageValue<_, T::AccountId>;

	// 错误用于告知用户发生了问题。
	#[pallet::error]
	pub enum Error<T> {
		/// 当前没有设置所有者。
		NoOwner,
		/// 调用用户无权进行此调用。
		NotAuthorized,
	}

	#[pallet::event]
	#[pallet::generate_deposit(pub(super) fn deposit_event)]
	pub enum Event<T: Config> {
		/// 所有者已更新。
		OwnerChanged,
	}

	#[pallet::call]
	impl<T: Config> Pallet<T> {
		/// 此函数允许当前所有者设置新的所有者。
		/// 如果没有所有者，此函数将返回错误。
		#[pallet::weight(u64::default())]
		#[pallet::call_index(0)]
		pub fn change_ownership(origin: OriginFor<T>, new: T::AccountId) -> DispatchResult {
			let who = ensure_signed(origin)?;
			let current_owner = CurrentOwner::<T>::get().ok_or(Error::<T>::NoOwner)?;
			ensure!(current_owner == who, Error::<T>::NotAuthorized);
			CurrentOwner::<T>::put(new);
			Self::deposit_event(Event::<T>::OwnerChanged);
			Ok(())
		}
	}
}
```

---

## 使用错误

在编写测试时，你可以使用错误来确保你的函数按预期执行。

```rust
#[test]
fn errors_example() {
	new_test_ext().execute_with(|| {
		assert_noop!(TemplateModule::change_ownership(Origin::signed(1), 2), Error::<T>::NoOwner);
		CurrentOwner::<T>::put(1);
		assert_ok!(TemplateModule::change_ownership(Origin::signed(1), 2));
		assert_noop!(TemplateModule::change_ownership(Origin::signed(1), 2), Error::<T>::NotAuthorized);
	});
}
```

---

## 编码错误

所有错误最终都会变成 `DispatchError`，这是运行时返回的最终类型。

```rust
println!("{:?}", Error::<T>::NoOwner.encode());
println!("{:?}", Error::<T>::NotAuthorized.encode());
let dispatch_error1: DispatchError = Error::<T>::NoOwner.into();
let dispatch_error2: DispatchError = Error::<T>::NotAuthorized.into();
println!("{:?}", dispatch_error1.encode());
println!("{:?}", dispatch_error2.encode());
```

```sh
[0]
[1]
[3, 1, 0, 0, 0, 0]
[3, 1, 1, 0, 0, 0]
```

---

## 调度错误编码

<table>
<tr>
	<td>3</td>
	<td>1</td>
	<td>1</td>
	<td>0</td>
	<td>0</td>
	<td>0</td>
</tr>
<tr>
	<td>DispatchError::Module</td>
	<td>模块 #2</td>
	<td>错误 #2</td>
	<td>(未使用)</td>
	<td>(未使用)</td>
	<td>(未使用)</td>
</tr>
</table>

基于配置的编码：

<div class="flex-container">
<div class="left" style="max-width: 700px;">

```rust
// 配置一个模拟运行时来测试模块。
frame_support::construct_runtime!(
	pub struct Test {
		System: frame_system::{Pallet, Call, Config, Storage, Event<T>},
		TemplateModule: pallet_template,
	}
);
```

</div>
<div class="right" style="margin-left: 10px; max-width: 700px;">

```rust
// 错误用于告知用户发生了问题。
#[pallet::error]
pub enum Error<T> {
	/// 当前没有设置所有者。
	NoOwner,
	/// 调用用户无权进行此调用。
	NotAuthorized,
	}
```

</div>
</div>
---

## 嵌套错误

错误最多支持5个字节，这允许你创建嵌套错误，或者使用 `PalletError` 派生宏插入其他最小数据。

```rust
#[derive(Encode, Decode, PalletError, TypeInfo)]
pub enum SubError {
	SubError1,
	SubError2,
	SubError3,
}

use frame_system::pallet::Error as SystemError;

// 错误用于告知用户发生了问题。
#[pallet::error]
pub enum Error<T> {
	/// 当前没有设置所有者。
	NoOwner,
	/// 调用用户无权进行此调用。
	NotAuthorized,
	/// 来自其他地方的错误。
	SubError(SubError),
	/// 来自其他地方的错误。
	SystemError(SystemError<T>),
	/// 带有最小数据的某个错误
	DataError(u16),
}
```

Notes:

<https://github.com/paritytech/substrate/pull/10242>

---

# 事件

---

## 事件简介

当一个外部调用成功完成时，通常你希望向外界公开一些关于执行过程中具体发生了什么的元数据。

例如，一个外部调用可能有多种不同的成功完成方式，你希望用户知道发生了什么。

或者可能有一些重要的状态转换，你知道用户

---

## 声明和发出事件

```rust [10-15|32-37|50]
#![cfg_attr(not(feature = "std"), no_std)]

pub use pallet::*;

#[frame_support::pallet]
pub mod pallet {
	use frame_support::pallet_prelude::*;
	use frame_system::pallet_prelude::*;

	/// 通过指定模块所依赖的参数和类型来配置模块。
	#[pallet::config]
	pub trait Config: frame_system::Config {
		/// 由于此模块会发出事件，因此它依赖于运行时对事件的定义。
		type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;
	}

	#[pallet::pallet]
	pub struct Pallet<T>(_);

	#[pallet::storage]
	pub type CurrentOwner<T: Config> = StorageValue<_, T::AccountId>;

	// 错误用于告知用户发生了问题。
	#[pallet::error]
	pub enum Error<T> {
		/// 当前没有设置所有者。
		NoOwner,
		/// 调用用户无权进行此调用。
		NotAuthorized,
	}

	#[pallet::event]
	#[pallet::generate_deposit(pub(super) fn deposit_event)]
	pub enum Event<T: Config> {
		/// 所有者已更新。
		OwnerChanged,
	}

	#[pallet::call]
	impl<T: Config> Pallet<T> {
		/// 此函数允许当前所有者设置新的所有者。
		/// 如果没有所有者，此函数将返回错误。
		#[pallet::weight(u64::default())]
		#[pallet::call_index(0)]
		pub fn change_ownership(origin: OriginFor<T>, new: T::AccountId) -> DispatchResult {
			let who = ensure_signed(origin)?;
			let current_owner = CurrentOwner::<T>::get().ok_or(Error::<T>::NoOwner)?;
			ensure!(current_owner == who, Error::<T>::NotAuthorized);
			CurrentOwner::<T>::put(new);
			Self::deposit_event(Event::<T>::OwnerChanged);
			Ok(())
		}
	}
}
```

---

## 存入事件

```rust
#[pallet::generate_deposit(pub(super) fn deposit_event)]
```

简单地生成：

```rust
impl<T: Config> Pallet<T> {
	pub(super) fn deposit_event(event: Event<T>) {
		let event = <<T as Config>::Event as From<Event<T>>>::from(event);
		let event =
			<<T as Config>::Event as Into<<T as frame_system::Config>::Event>>::into(event);
		<frame_system::Pallet<T>>::deposit_event(event)
	}
}
```

<br />

`frame/support/procedural/src/pallet/expand/event.rs`

---

## 系统中的存入事件

在FRAME系统中，事件只是一个存储项。

`frame/system/src/lib.rs`

```rust
/// 当前块中存入的事件。
///
/// 注意：该项是无界的，因此绝不应在链上读取。
/// 否则，它可能会增加块的证明大小（PoV size）。
///
/// 事件在内存中占用的空间很大。将事件装箱，以防有人在运行时内仍然读取它们，从而避免内存溢出。
#[pallet::storage]
pub(super) type Events<T: Config> =
	StorageValue<_, Vec<Box<EventRecord<T::RuntimeEvent, T::Hash>>>, ValueQuery>;

/// `Events<T>` 列表中的事件数量。
#[pallet::storage]
#[pallet::getter(fn event_count)]
pub(super) type EventCount<T: Config> = StorageValue<_, EventIndex, ValueQuery>;
```

---

## 系统中的存入事件

在系统中存入事件最终只是将一个新事件追加到这个存储中。

`frame/system/src/lib.rs`

```rust [0|1-4|34|13-16]
// 将一个事件存入此块的事件记录中。
pub fn deposit_event(event: impl Into<T::RuntimeEvent>) {
    Self::deposit_event_indexed(&[], event.into());
}

// 将一个事件存入此块的事件记录中，并将此事件添加到相应的主题索引中。
// 这将更新与指定主题对应的存储项。预计轻客户端可以订阅这些主题。
pub fn deposit_event_indexed(topics: &[T::Hash], event: T::RuntimeEvent) {
    let block_number = Self::block_number();
    // 在创世块上不填充事件。
    if block_number.is_zero() {
        return
    }

    let phase = ExecutionPhase::<T>::get().unwrap_or_default();
    let event = EventRecord { phase, event, topics: topics.to_vec() };

    // 要添加的事件的索引。
    let event_idx = {
        let old_event_count = EventCount::<T>::get();
        let new_event_count = match old_event_count.checked_add(1) {
            // 当达到此块中事件的最大数量时，不做任何操作并保持事件计数不变。
            None => return,
            Some(nc) => nc,
        };
        EventCount::<T>::put(new_event_count);
        old_event_count
    };

    Events::<T>::append(event);

    for topic in topics {
        <EventTopics<T>>::append(topic, &(block_number, event_idx));
    }
}
```

---

## 你不能读取事件

- 事件存储是由你的模块发出的单个事件的无界向量。
- 如果你读取这个存储，你会将整个存储内容引入存储证明中！
- 永远不要编写从事件读取或依赖事件的运行时逻辑。
- 测试中可以这样做。

---

## 你不能读取事件

`frame/system/src/lib.rs`

```rust
// 获取运行时存入的当前事件。
// 只有在你清楚自己在做什么并且不在运行时块执行中时才应调用此函数，否则它可能会对块的PoV大小产生很大影响。
pub fn read_events_no_consensus(
) -> impl sp_std::iter::Iterator<Item = Box<EventRecord<T::RuntimeEvent, T::Hash>>> {
    Events::<T>::stream_iter()
}

// 获取运行时存入的当前事件。
// 注意：此函数仅应在测试中使用。从运行时读取事件可能会对块的PoV大小产生很大影响。用户对于此类行为应使用替代的有界存储项。
// 注意：创世块中未注册的事件将被悄悄忽略。
#[cfg(any(feature = "std", feature = "runtime-benchmarks", test))]
pub fn events() -> Vec<EventRecord<T::RuntimeEvent, T::Hash>> {
    debug_assert!(
       !Self::block_number().is_zero(),
        "创世块中未注册事件"
    );
    // 在此处解引用事件是可以的，因为我们不在受内存限制的运行时中。
    Self::read_events_no_consensus().map(|e| *e).collect()
}
```

---

## 测试事件

记住将块号设置为大于零的值！

FRAME系统中的一些工具：

`frame/system/src/lib.rs`

```rust
// 将块号设置为特定值。对于不需要处理其他环境项的测试，可作为 `initialize` 的替代方法。
#[cfg(any(feature = "std", feature = "runtime-benchmarks", test))]
pub fn set_block_number(n: BlockNumberFor<T>) {
    <Number<T>>::put(n);
}

// 断言给定的 `event` 存在。
#[cfg(any(feature = "std", feature = "runtime-benchmarks", test))]
pub fn assert_has_event(event: T::RuntimeEvent) {
    assert!(Self::events().iter().any(|record| record.event == event))
}

// 断言最后一个事件等于给定的 `event`。
#[cfg(any(feature = "std", feature = "runtime-benchmarks", test))]
pub fn assert_last_event(event: T::RuntimeEvent) {
    assert_eq!(Self::events().last().expect("期望有事件").event, event);
}
```

---

## 在测试中使用事件

```rust
#[test]
fn events_example() {
    new_test_ext().execute_with(|| {
        frame_system::Pallet::<T>::set_block_number(1);
        CurrentOwner::<T>::put(1);
        assert_ok!(TemplateModule::change_ownership(Origin::signed(1), 2));
        assert_ok!(TemplateModule::change_ownership(Origin::signed(2), 3));
        assert_ok!(TemplateModule::change_ownership(Origin::signed(3), 4));

        let events = frame_system::Pallet::<T>::events();
        assert_eq!(events.len(), 3);
        frame_system::Pallet::<T>::assert_has_event(crate::Event::<T>::OwnerChanged { old: 1, new: 2}.into());
        frame_system::Pallet::<T>::assert_last_event(crate::Event::<T>::OwnerChanged { old: 3, new: 4}.into());
    });
}
```

记住，其他模块也可以存入事件！

---

## 总结

- 事件和错误是当用户发起外部调用时，你可以向用户传达正在发生什么的两种方式。
- 事件通常表示某些操作成功完成。
- 错误表示出现问题（并且所有更改都已回滚）。
- 发生时，最终用户都可以访问这两者。