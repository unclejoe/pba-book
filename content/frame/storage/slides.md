---
title: FRAME Storage
description: Deep dive into FRAME Storage
duration: 1 hour
---

# FRAME 存储

---

## FRAME 存储

在本次演示中，我们将更深入地探讨 Substrate 存储的概念，并了解 FRAME 为你提供了哪些存储原语，以使 Pallet 开发更加轻松。

<div class="flex-container">

<div class="left-small">
	<table class="storage-layer-table">
	<tr><td class="ends">开发者</td></tr>
	<tr><td style="background-color: pink;">运行时存储 API</td></tr>
	<tr><td style="background-color: pink;">存储Overlay</td></tr>
	<tr><td>帕特里夏 - 默克尔树</td></tr>
	<tr><td>键值数据库</td></tr>
	<tr><td class="ends">计算机</td></tr>
	</table>
</div>
<div class="right">

---

### 存储层

正如我们所知，Substrate 的存储系统有四个核心层。

今天我们将重点关注最上面的两层：运行时存储 API 和存储Overlay，FRAME 使用它们来提升开发者体验。

</div>
</div>

---

### Overlay深入探讨

<br />
<div class="flex-container">
<div class="left-small" style="font-size:0.9em">

Overlay对底层数据库的更改进行暂存。

</div>

<div class="right">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td>Alice: 10</td>
	<td>Bob: 20</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

---

### Overlay：账户转账

<div class="flex-container">
<div class="left-small" style="font-size:0.9em">

1. 运行时逻辑启动。
1. 调用运行时存储 API。
1. 首先，我们查询Overlay变更集。
   - 不幸的是，数据不在那里。
1. 然后，我们查询底层数据库。
   - 如你所知，这非常慢。

</div>

<div class="right">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td>Alice: 10</td>
	<td>Bob: 20</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

---

### Overlay：账户转账

<br />
<div class="flex-container">
<div class="left-small">

- 当我们将数据返回给运行时，我们将这些值缓存在Overlay中。
- 后续的读写操作将在Overlay中进行，因为数据已经在那里了。

</div>
<div class="right">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td>Alice: 10</td>
	<td>Bob: 20</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td>Alice: 10</td>
	<td>Bob: 20</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

---

### Overlay：账户转账

<br />
<div class="flex-container">
<div class="left-small">

- 实际的转移逻辑发生在运行时内存中。
- 在某个时刻，运行时逻辑将新的余额写入存储，这会更新Overlay缓存。
- 底层数据库尚未更新。

</div>
<div class="right">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td style="color: yellow;">Alice: 15</td>
	<td style="color: yellow;">Bob: 15</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td>Alice: 10</td>
	<td>Bob: 20</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

---

### Overlay：账户转账

<br />
<div class="flex-container">
<div class="left-small">

- 在区块结束时，暂存的更改会一次性提交到数据库。
- 然后，会为最终的区块状态重新计算一次存储根。

</div>
<div class="right">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td style="color: lightgreen;">Alice: 15</td>
	<td style="color: lightgreen;">Bob: 15</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

---

### Overlay：影响

<br />
<div class="flex-container">
<div class="left-small">

- 第二次或更多次读取相同的存储比初次读取要快（但不是免费的）。
- 多次写入相同的值很快（但不是免费的），并且最终只会对数据库进行一次写入。

</div>
<div class="right">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td>Alice: 15</td>
	<td>Bob: 15</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

Notes:

这也意味着 Substrate/Polkadot 的跨实现可能很难确保确定性（下一张幻灯片也是如此）。

---

### 附加存储Overlay（事务性）

<br />
<div class="flex-container">
<div class="left-small text-small">

- 运行时具有生成额外存储层的能力，称为“事务性层”。
- 这允许你通过运行时存储 API 提交更改，但在更改到达Overlay变更集之前，如果你愿意，可以撤销这些更改。
- 运行时可以生成多个事务性层，每个层在不同的时间生成，允许运行时开发人员在逻辑上分离他们想要提交或回滚更改的时间。

</div>
<div class="right text-small">

<table class="overlay-table">
<tr><td style="background-color: red;">运行时逻辑</td></tr>
<tr><td style="background-color: darkred;">运行时内存</td></tr>
</table>

<br />
<table class="overlay-table" style="background-color: green;">
<tr><td>运行时存储 API</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>事务性层</td></tr>
<tr>
	<td style="color: yellow;">Alice: 25</td>
	<td>&nbsp;</td>
	<td style="color: yellow;">Cindy: 20</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>Overlay变更集</td></tr>
<tr>
	<td>Alice: 15</td>
	<td>&nbsp;</td>
	<td>Cindy: 30</td>
	<td>&nbsp;</td>
</tr>
</table>

<br />
<table class="overlay-table" style="background-color: blue;">
<tr><td>内存 / 数据库接口</td></tr>
</table>

<br />
<table class="overlay-table">
<tr><td colspan=4>数据库</td></tr>
<tr>
	<td>Alice: 15</td>
	<td>Bob: 15</td>
	<td>Cindy: 30</td>
	<td>Dave: 40</td>
</tr>
</table>

</div>

---

### 事务性实现细节

- 非零开销（但非常小）
  - 每个存储层中，每个写入的键有 0.15% 的开销。
- 值不会在层之间复制。
  - 值存储在堆中，我们只是移动指针。
  - 因此，开销与存储大小无关，只与层中的存储项数量有关。
- 存储层使用客户端内存，因此实际上没有上限。

Notes:

更多详细信息请参阅：

- <https://github.com/paritytech/substrate/pull/10413>
- <https://github.com/paritytech/substrate/pull/10809>

在模块 6 中，我们可以更仔细地研究此功能在 FRAME 中是如何公开的。

请参阅：<https://github.com/paritytech/substrate/pull/11431>

---

## 默认存储层

所有外在函数（extrinsics）都在事务性存储层中执行。

这意味着，如果你的外在函数返回一个 `Error`，则该外在函数对存储所做的所有更改都将被回滚。

这与以太坊等智能合约环境中的行为相同。

---

### 事务性层攻击

事务性层可用于攻击你的链：

<br />

- 允许用户生成大量事务性层。
- 在顶层进行大量更改。
- 所有这些更改每次都需要向下传播。

**解决方案：**

- 不要允许用户在你的运行时逻辑中创建无限制数量的层。

---

# 运行时存储API
## Patricia Trie
 <img style="height: 500px;" src="./img/patricia-trie.svg" />
## 存储键
 <img style="height: 500px;" src="./img/navigate-storage-2.svg" />

---

## FRAME存储键
我们遵循一个简单的模式：
```
hash(name) ++ hash(name2) ++ hash(name3) ++ hash(name4) ...
```
例如：
```
twox128(pallet_name) ++ twox128(storage_name) ++ ...
```
在研究具体的存储原语时，我们会深入探讨更多细节。
## 模块名称
模块名称来自`construct_runtime!`宏。
```rust
// 配置一个模拟运行时来测试模块
frame_support::construct_runtime!(
    pub enum Test {
        System: frame_system::{Pallet, Call, Config, Storage, Event<T>},
        Example: pallet_template,
    }
);
```
这意味着在此处更改模块名称是一个**破坏性**的变更，因为它会改变你的存储键。

---

## FRAME存储原语
 - `StorageValue`
 - `StorageMap`
 - `CountedStorageMap`
 - `StorageDoubleMap`
 - `StorageNMap`
我们将逐一介绍它们，以及过程中的许多重要且微妙的细节。

---

## 存储值
将单个项放入运行时存储中。
```rust
pub struct StorageValue<Prefix, Value, QueryKind = OptionQuery, OnEmpty = GetDefault>(_);
```
---

# 存储键：
```rust
Twox128(Prefix::pallet_prefix()) ++ Twox128(Prefix::STORAGE_PREFIX)
```
---

## 存储值：示例
```rust
#[pallet::storage]
pub type Item1<T> = StorageValue<_, u32>;
```
```rust
#[test]
fn storage_value() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        assert_eq!(Item1::<T>::get(), None);
        Item1::<T>::put(10u32);
        assert_eq!(Item1::<T>::get(), Some(10u32));
    });
}
```
---

## 存储“前缀”
任何FRAME存储中的第一个泛型参数是`Prefix`，用于生成存储键。`Prefix`实现了`StorageInstance`，该trait定义如下：
```rust
pub trait StorageInstance {
    const STORAGE_PREFIX: &'static str;
    fn pallet_prefix() -> &'static str;
}
```
`STORAGE_PREFIX`是存储的名称，`pallet_prefix()`是该存储所在模块的名称。这得益于FRAME的宏魔法得以填充。
Notes:<https://substrate.stackexchange.com/questions/476/storage-definition-syntax/478#478>

---

## 存储值键
```rust
use sp_core::hexdisplay::HexDisplay;
println!("{}", HexDisplay::from(&Item1::<T>::hashed_key()));
```
这当然取决于你的模块名称...
```sh
e375d60f814d02157aaaa18f3639a254c64445c290236a18189385ed9853fb1e
```
```sh
e375d60f814d02157aaaa18f3639a254 ++ c64445c290236a18189385ed9853fb1e
```
```sh
twox128("Example") = e375d60f814d02157aaaa18f3639a254
twox128("Item1") = c64445c290236a18189385ed9853fb1e
```

---

## 揭开FRAME存储的神秘面纱
这大致展示了你使用模块存储时发生的情况。并不完全准确，但应该有一定的启发性。如你所见，它其实并没有那么复杂。
```rust
struct Prefix;
impl StorageInstance for Prefix {
    const STORAGE_PREFIX: &'static str = "MyStorage";
    fn pallet_prefix() -> &'static str {
        "MyPallet"
    };
    fn prefix() -> Vec<u8> {
        twox_128(Self::pallet_prefix()) ++ twox_128(STORAGE_PREFIX)
    }
}
type MyStorage = StorageValue<Prefix, Type>;
trait StorageValue<Prefix, Type> {
    fn get() -> Option<Type> {
        sp_io::storage::get(Prefix::prefix())
    }
    fn set(value: Type) {
        sp_io::storage::set(Prefix::prefix(), value)
    }
    fn kill() {
        sp_io::storage::clear(Prefix::prefix())
    }
    // 等等...
}
```

---

## 所有存储都是可选的
 - 在运行时存储API层面，一个存储键要么有值，要么没有值。
 - 如果没有值，从后端进行的任何查询结果都将是`None`。
 - 如果有值，查询结果将是`Some(value)`。
 - 不过，我们也可以用一个默认值来隐藏这种情况。

---

## 查询存储是否实际存在
有一些API可以让你知道某个值是否实际存在于数据库中。
```rust
#[pallet::storage]
pub type Item1<T> = StorageValue<_, u32>;
```
```rust
#[test]
fn storage_value() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        // 此时实际上没有任何数据
        assert_eq!(Item1::<T>::exists(), false);
        assert_eq!(Item1::<T>::try_get().ok(), None);
    });
}
```

---

## 查询类型
 - `OptionQuery`：默认选择，代表数据库的实际状态。
 - `ValueQuery`：当值为`None`时返回一个值。（可以是默认值或可配置的值）
```rust
#[pallet::storage]
pub type Item2<T> = StorageValue<_, u32, ValueQuery>;
```
```rust
#[test]
fn value_query() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        // `0u32`是`u32`的默认值
        assert_eq!(Item2::<T>::get(), 0u32);
        Item2::<T>::put(10u32);
        assert_eq!(Item2::<T>::get(), 10u32);
    });
}
```
请记住，在第一次查询时，0实际上并不在存储中。
## 空值处理
你可以通过以下方式控制`OnEmpty`的值：
```rust
#[pallet::type_value]
pub fn MyDefault<T: Config>() -> u32 { 42u32 }
#[pallet::storage]
pub type Item3<T> = StorageValue<_, u32, ValueQuery, MyDefault<T>>;
```
```rust
#[test]
fn my_default() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        // `42u32`是配置的`OnEmpty`值
        assert_eq!(Item3::<T>::get(), 42u32);
        Item3::<T>::put(10u32);
        assert_eq!(Item3::<T>::get(), 10u32);
    });
}
```
请记住，在第一次查询时，42实际上并不在存储中。

---

## 并非神奇魔法
这些“额外功能”只是简化代码的方式。即使不借助这些特殊功能，你也能实现相同效果：
```rust
let value = Item1::<T>::try_get().unwrap_or(42u32);
```
但你肯定不想每次都这么写 。
## set与put的区别
 - `pub fn set(val: QueryKind::Query)`
 - `pub fn put<Arg: EncodeLike<Value>>(val: Arg)`
例如：
```rust
#[pallet::storage]
pub type Item1<T> = StorageValue<_, u32>;
```
```rust
Item1::<T>::set(Some(42u32));
Item1::<T>::put(42u32);
```

---

## 不要将Option类型作为存储值
这基本上是一种反模式，这么做没什么实际意义。
```rust
#[pallet::storage]
pub type Item4<T> = StorageValue<_, Option<u32>>;
#[pallet::storage]
pub type Item5<T> = StorageValue<_, Option<u32>, ValueQuery>;
```
```rust
#[test]
fn nonsense() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        assert_eq!(Item4::<T>::exists(), false);
        assert_eq!(Item5::<T>::exists(), false);
        Item4::<T>::put(None::<u32>);
        Item5::<T>::put(None::<u32>);
        assert_eq!(Item4::<T>::exists(), true);
        assert_eq!(Item5::<T>::exists(), true);
    });
}
```

---

## 用单元类型替代布尔类型
你可能想在存储中简单表示某个布尔值……为节省字节，可以使用单元类型。
```rust
#[pallet::storage]
pub type Item6<T> = StorageValue<_, ()>;
```
```rust
#[test]
fn better_bool() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        // 表示false的情况
        assert_eq!(Item6::<T>::exists(), false);
        Item6::<T>::put(());
        // 表示true的情况
        assert_eq!(Item6::<T>::exists(), true);
    });
}
```

---

## 删除存储项
使用`kill()`或`take()`从数据库中移除存储项。
```rust
#[pallet::type_value]
pub fn MyDefault<T: Config>() -> u32 { 42u32 }
#[pallet::storage]
pub type Item3<T> = StorageValue<_, u32, ValueQuery, MyDefault<T>>;
```
```rust
#[test]
fn kill() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        assert_eq!(Item3::<T>::get(), 42u32);
        Item3::<T>::put(10u32);
        assert_eq!(Item3::<T>::get(), 10u32);
        //Item3::<T>::kill();
        let old_value = Item3::<T>::take();
        assert_eq!(Item3::<T>::get(), 42u32);
        assert_eq!(old_value, 10u32);
    });
}
```

---

## 变更存储项
对存储项执行闭包操作。
```rust
#[test]
fn mutate() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        Item2::<T>::put(42u32);
        Item2::<T>::mutate(|x| {
            if *x % 2 == 0 {
                *x = *x / 2;
            }
        });
        assert_eq!(Item2::<T>::get(), 21);
    });
}
```
---

## 尝试变更存储项
对存储项执行闭包操作，但仅当闭包返回`Ok`时才写入。
```rust
#[test]
fn try_mutate() {
	sp_io::TestExternalities::new_empty().execute_with(|| {
		Item2::<T>::put(42u32);
		assert_noop!(Item2::<T>::try_mutate(|x| -> Result<(), ()> {
			*x = *x / 2;
			if *x % 2 == 0 {
				Ok(())
			} else {
				Err(())
			}
		}), ());
		// Nothing written
		assert_eq!(Item2::<T>::get(), 42);
	});
}
```

---

## 断言无操作

你可能已经注意到我们使用了 `assert_noop!` 而不是 `assert_err!`。

```rust
/// 评估一个表达式，断言它返回一个预期的 `Err` 值，并且运行时存储未被修改（即表达式是一个无操作）。
/// 使用方式为 `assert_noop(要断言的表达式, 预期错误表达式)`。
#[macro_export]
macro_rules! assert_noop {
    (
        $x:expr,
        $y:expr $(,)?
    ) => {
        let h = $crate::storage_root($crate::StateVersion::V1);
        $crate::assert_err!($x, $y);
        assert_eq!(h, $crate::storage_root($crate::StateVersion::V1), "存储已被修改");
    };
}
```
还有 `assert_storage_noop!`，它不关心返回的内容，只关心存储是否被修改。

---

## 向量技巧

你可以使用 `decode_len()` 和 `append()` 来操作 `Vec` 而无需解码所有元素。

```rust
#[pallet::storage]
#[pallet::unbounded]
pub type Item7<T> = StorageValue<_, Vec<u8>, ValueQuery>;
```

```rust
#[test]
fn vec_tricks() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        assert_eq!(Item7::<T>::decode_len(), None);
        Item7::<T>::put(vec![0u8]);
        assert_eq!(Item7::<T>::decode_len(), Some(1));
        Item7::<T>::append(1u8);
        Item7::<T>::append(2u8);
        assert_eq!(Item7::<T>::get(), vec![0u8, 1u8, 2u8]);
        assert_eq!(Item7::<T>::decode_len(), Some(3));
    });
}
```

---

## 有界存储

你可能已经注意到上一张幻灯片中存储项上的 `#[pallet::unbounded]`。
请记住，区块链受到以下限制：
- 计算时间
- 内存限制
- 存储大小/证明大小

通常，FRAME 中的每个存储项的大小都应该是**有界的**。我们在讨论性能测试时会对此进行更深入的探讨。

---

## 有界向量

我们有一些无界项（如 `Vec`、`BTreeSet` 等）的有界版本。

```rust
#[pallet::storage]
pub type Item8<T> = StorageValue<_, BoundedVec<u8, ConstU32<100>>, ValueQuery>;
```
使用第二个 `Get<u32>` 类型来指定元素的最大数量。

```rust
#[test]
fn bounded_vec() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        for i in 0u8..100u8 {
            assert_ok!(Item8::<T>::try_append(i));
        }
        // 最多支持 100 个元素。
        assert_noop!(Item8::<T>::try_append(100), ());
    });
}
```

---

## 存储映射

将元素存储为键值对映射。

```rust
pub struct StorageMap<Prefix, Hasher, Key, Value, QueryKind = OptionQuery, OnEmpty = GetDefault, MaxValues = GetDefault>(_);
```

存储键：
```rust
Twox128(Prefix::pallet_prefix()) ++ Twox128(Prefix::STORAGE_PREFIX) ++ Hasher1(encode(key))
```

---

## 存储映射：示例

```rust
#[pallet::storage]
pub type Item9<T: Config> = StorageMap<_, Blake2_128, u32, u32>;
```

```rust
#[test]
fn storage_map() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        Item9::<T>::insert(0, 100);
        assert_eq!(Item9::<T>::get(0), Some(100));
        assert_eq!(Item9::<T>::get(1), None);
    });
}
```

---

## 存储映射键

使用存储映射，你可以引入任意类型的“键”和“值”。

```rust
pub struct StorageMap<Prefix, Hasher, Key, Value,...>(_);
```
映射的存储键使用键的哈希。你可以选择存储哈希函数，以下是当前实现的一些哈希函数：
- Identity（完全不进行哈希）
- Blake2_128
- Blake2_256
- Twox128
- Twox256
- Twox64Concat（特殊）
- Blake2_128Concat（特殊）

---

## 值查询：余额

```rust
#[pallet::storage]
pub type Item10<T: Config> = StorageMap<_, Blake2_128, T::AccountId, Balance, ValueQuery>;
```

```rust
#[test]
fn balance_map() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        // 通常是 32 字节的地址
        let alice = 0u64;
        let bob = 1u64;
        Item10::<T>::insert(alice, 100);

        let transfer = |from: u64, to: u64, amount: u128| -> Result<(), &'static str> {
            Item10::<T>::try_mutate(from, |from_balance| -> Result<(), &'static str> {
                Item10::<T>::try_mutate(to, |to_balance| -> Result<(), &'static str> {
                    *to_balance = to_balance.checked_add(amount).ok_or("溢出")?;
                    *from_balance = from_balance.checked_sub(amount).ok_or("余额不足")?;
                    Ok(())
                })
            })
        };

        assert_noop!(transfer(bob, alice, 10), "余额不足");
        assert_ok!(transfer(alice, bob, 10));
        assert_noop!(transfer(alice, bob, 100), "余额不足");

        assert_eq!(Item10::<T>::get(alice), 90);
        assert_eq!(Item10::<T>::get(bob), 10);
    });
}
```

---

## 前缀树

所有模块和存储项自然形成“前缀树”。

<img style="height: 500px;" src="./img/balance-trie.svg" />

在该图中，一个名为“Balances”的模块有一个存储值“Total Issuance”，以及一个以余额作为值的“Accounts”映射。

---

## 前缀树键

现在让我们看看这些存储项的键：

```rust
use sp_core::hexdisplay::HexDisplay;
println!("{}", HexDisplay::from(&Item2::<T>::hashed_key()));
println!("{}", HexDisplay::from(&Item10::<T>::hashed_key_for(0)));
println!("{}", HexDisplay::from(&Item10::<T>::hashed_key_for(1)));
```

```sh
e375d60f814d02157aaaa18f3639a2546fe5a43b77d7334acfb711a021a514b8
e375d60f814d02157aaaa18f3639a254ca79d14bc48854f664528f3a696b6c27c804ce198ec337e3dc762bdd1a09aece
e375d60f814d02157aaaa18f3639a254ca79d14bc48854f664528f3a696b6c279ea2d098b5f70192f96c06f38d3fbc97
```

<table style="font-size: 24px">
<tr>
    <td><span style="color: red;">e375d60f814d02157aaaa18f3639a254</span></td>
    <td><span style="color: lightblue;">6fe5a43b77d7334acfb711a021a514b8</span></td>
    <td>&nbsp;</td>
</tr>
<tr>
    <td><span style="color: red;">e375d60f814d02157aaaa18f3639a254</span></td>
    <td><span style="color: lightgreen;">ca79d14bc48854f664528f3a696b6c27</span></td>
    <td><span style="color: pink;">c804ce198ec337e3dc762bdd1a09aece</span></td>
</tr>
<tr>
    <td><span style="color: red;">e375d60f814d02157aaaa18f3639a254</span></td>
    <td><span style="color: lightgreen;">ca79d14bc48854f664528f3a696b6c27</span></td>
    <td><span style="color: orange;">9ea2d098b5f70192f96c06f38d3fbc97</span></td>
</tr>
</table>

---

## 存储迭代

因为所有存储项都形成前缀树，所以你可以从任意前缀开始迭代内容。

```rust [0|6-7]
impl<T: Decode + Sized> Iterator for StorageIterator<T> {
    type Item = (Vec<u8>, T);

    fn next(&mut self) -> Option<(Vec<u8>, T)> {
        loop {
            let maybe_next = sp_io::storage::next_key(&self.previous_key)
               .filter(|n| n.starts_with(&self.prefix));
            break match maybe_next {
                Some(next) => {
                    self.previous_key = next.clone();
                    let maybe_value = frame_support::storage::unhashed::get::<T>(&next);
                    match maybe_value {
                        Some(value) => {
                            if self.drain {
                                frame_support::storage::unhashed::kill(&next);
                            }
                            Some((self.previous_key[self.prefix.len()..].to_vec(), value))
                        },
                        None => continue,
                    }
                },
                None => None,
            }
        }
    }
}
```
---

## 你可以...

- 使用前缀 `&[]` 迭代区块链上的所有存储。
- 使用前缀 `hash(pallet_name)` 迭代模块的所有存储。
- 使用前缀 `hash("Balances") ++ hash("Accounts")` 迭代所有用户的余额。

这并非固有属性！这仅仅是因为我们“巧妙地”选择了这种生成键的模式。

请注意，迭代没有“正确”的顺序。所有键都经过哈希处理，我们只是按照生成的哈希值的顺序进行迭代。

---

## 不透明存储键

但存在一个问题……

假设我要迭代所有用户的余额……
- 我会得到所有的余额值。
- 我会得到所有存储键。
    - 这些键都是经过哈希处理的。
- 我不会得到拥有这些余额的实际账户！

为此，我们需要**透明存储键**。

---

## 透明哈希

- Twox64Concat
- Blake2_128Concat

基本原理如下：
```sh
最终哈希 = hash(原像) ++ 原像
```
从这种哈希中，我们总能提取出原像：
```sh
"hello" = 0x68656c6c6f
blake2_128("hello") = 0x46fb7408d4f285228f4af516ea25851b
blake2_128concat("hello") = 0x46fb7408d4f285228f4af516ea25851b68656c6c6f
```

```sh
"world" = 0x776f726c64
twox64("world") = 0xef51ee66fefb78e7
twox64concat("world") = 0xef51ee66fefb78e7776f726c64
```

---

## 更好的余额映射

<div class="text-small">
我们应该使用 `Blake2_128Concat`！

```rust
#[pallet::storage]
pub type Item11<T: Config> = StorageMap<_, Blake2_128Concat, T::AccountId, Balance, ValueQuery>;
```

```rust
#[test]
fn better_balance_map() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        for i in 0u64..10u64 {
            Item10::<T>::insert(i, u128::from(i * 100u64));
            Item11::<T>::insert(i, u128::from(i * 100u64));
        }
        // 不能调用 iter 方法，因为它无法返回键
        let all_10: Vec<_> = Item10::<T>::iter_values().collect();
        let all_11: Vec<_> = Item11::<T>::iter().collect();
        println!("{:?}\n{:?}", all_10, all_11);

        assert!(false)
    });
}
```

```sh
[600, 500, 300, 100, 800, 400, 700, 900, 0, 200]
[(6, 600), (5, 500), (3, 300), (1, 100), (8, 800), (4, 400), (7, 700), (9, 900), (0, 0), (2, 200)]
```
</div>

---

## 应使用哪种哈希函数？

既然我们知道透明哈希函数非常有用，实际上就只有 3 种选择：
- `Identity` - 完全不进行哈希
- `Twox64Concat` - 非加密且透明的哈希
- `Blake2_128Concat` - 加密且透明的哈希

---

## 非平衡树

<div class="flex-container">
<div class="left">
<img src="./img/unbalanced-tree.svg" />
</div>
<div class="right">
- 我们提到过，非平衡树有时可能很有用……
- 但在这种情况下，我们必须选择一种哈希函数，防止用户操纵前缀树的平衡。
</div>
</div>

---

## 应使用哪种哈希函数？

基本上，你应该始终使用 `Blake2_128Concat`，因为用户最难对其施加影响。执行时间上的差异可能不大（据我所知尚未进行适当的性能测试）。

一些合理的例外情况：
- 如果键已经是一个不可控的加密哈希，你可以使用 `Identity`。
- 如果键很简单且由运行时控制（如递增的计数器），`Twox64Concat` 就足够了。

更多信息请查看文档……

---

## 阅读存储映射的 API 文档

<https://crates.parity.io/frame_support/pallet_prelude/struct.StorageMap.html>

---

## 存储双映射和存储 N 映射

与 `StorageMap` 的基本思想相同，但有更多键：

```rust
pub struct StorageDoubleMap<Prefix, Hasher1, Key1, Hasher2, Key2, Value, QueryKind = OptionQuery, OnEmpty = GetDefault, MaxValues = GetDefault>(_);
```

```sh
Twox128(Prefix::pallet_prefix()) ++ Twox128(Prefix::STORAGE_PREFIX) ++ Hasher1(encode(key1)) ++ Hasher2(encode(key2))
```

```rust
pub struct StorageNMap<Prefix, Key, Value, QueryKind = OptionQuery, OnEmpty = GetDefault, MaxValues = GetDefault>(_);
```

```sh
Twox128(Prefix::pallet_prefix())
        ++ Twox128(Prefix::STORAGE_PREFIX)
        ++ Hasher1(encode(key1))
        ++ Hasher2(encode(key2))
    ++...
    ++ HasherN(encode(keyN))
```

---

## 存储 N 映射：示例

```rust
#[pallet::storage]
pub type Item12<T: Config> = StorageNMap<
    _,
    (
        NMapKey<Blake2_128Concat, u8>,
        NMapKey<Blake2_128Concat, u16>,
        NMapKey<Blake2_128Concat, u32>,
    ),
    u128,
>;
```
将键视为复合键的元组。

```rust
#[test]
fn storage_n_map() {
    sp_io::TestExternalities::new_empty().execute_with(|| {
        Item12::<T>::insert((1u8, 1u16, 1u32), 1u128);
        assert_eq!(Item12::<T>::get((1u8, 1u16, 1u32)), Some(1u128));
    });
}
```

---

## 映射迭代的复杂性

- 对映射进行迭代在计算和存储证明资源方面代价极高。
- 需要进行 `N` 次树读取，实际上是 `N * log(N)` 次数据库读取。
- 占用 `32 字节/哈希 * 16 个哈希/节点 * N * log(N)` 的证明大小。

通常你不应该对映射进行迭代。如果确实需要迭代，请确保它是有界的。

---

## 删除所有项

在存储映射上实现的功能：

```rust
// 删除存储中的所有值。
pub fn remove_all(limit: Option<u32>) -> KillStorageResult
```
其中：
```rust
pub enum KillStorageResult {
    AllRemoved(u32),
    SomeRemaining(u32),
}
```
与其尝试一次性删除所有项，你可以“冻结”状态机，让用户多次调用 `remove_all` 并使用一个限制。

---

## 计数存储映射

`StorageMap` 和 `StorageValue<Value=u32>` 的包装器，用于在不迭代所有值的情况下跟踪映射中的元素数量。

```rust
pub struct CountedStorageMap<Prefix, Hasher, Key, Value, QueryKind = OptionQuery, OnEmpty = GetDefault, MaxValues = GetDefault>(_);
```
与常规存储映射相比，在操作值时，此存储项具有额外的存储读写开销。

---

## 揭开计数存储映射的神秘面纱

```rust
#[pallet::storage]
pub type Item13<T: Config> = CountedStorageMap<_, Blake2_128Concat, T::AccountId, Balance>;
```
这个 `CountedStorageMap` 与以下代码完全相同：

```rust
#[pallet::storage]
pub type Item13<T: Config> = StorageMap<_, Blake2_128Concat, T::AccountId, Balance>;

/// 计数器总是以 "CounterFor" 为前缀
#[pallet::storage]
pub type CounterForItem13<T: Config> = StorageValue<_, u32>;
```
并带有一些额外逻辑来协调这两者。

---

## 架构考虑因素

- 你知道从数据库访问大型项效率不高：
    - 像 ParityDB 这样的数据库针对小于 32 KB 的项进行了优化。
    - 解码有一定开销。
- 你知道访问映射中的大量存储项也很糟糕！
    - 频繁调用主机函数会产生大量开销。
    - 从默克尔树查找和数据库读取会产生大量开销。
    - 存储证明会产生大量**额外**开销。

那么你该如何选择呢？

---

## 视情况而定！有时两者都要！

存储的选择取决于你的逻辑将如何访问它。

<div class="text-small">
- 场景 A：我们需要管理数百万用户并支持余额转账。
    - 显然我们应该使用映射！余额转账每次仅涉及 2 个账户。2 次映射读取比读取数百万用户来转移余额要高效得多。

- 场景 B：我们需要获取下一个时期的 1000 个验证者。
    - 显然我们应该使用有界向量！我们知道验证者有上限，而且我们的逻辑需要读取所有验证者！

- 场景 C：我们需要存储每个验证者的配置元数据。
    - 我们可能应该使用映射！我们会复制上述向量中的一些数据，但我们通常是按验证者来访问配置信息。
</div>

---

### 总结
- FRAME存储只是包装底层Substrate存储API的简单宏。
- Substrate存储的原理直接决定了你在FRAME中能够进行哪些操作。
- 不能因为某些功能在FRAME中不存在，就认为无法实现它！
- 也不能因为某些功能在FRAME中存在，就不假思索地使用它！ 