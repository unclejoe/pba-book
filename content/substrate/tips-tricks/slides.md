---
title: Substrate and FRAME Tips and Tricks
description: Substrate and FRAME Tips and Tricks for web3 builders
---

# Substrate和FRAME的技巧与窍门

Notes:

- 一些你可能需要了解的随机知识点。
- 这些内容与在FRAME和Substrate中进行编码相关。

---

# 第一部分 Substrate相关内容

---

## `<Type as Trait>::AssociatedType`

- 你**必须**了解的最有用的Rust语法细节。

Notes:

什么是类型？结构体是一种类型。枚举是一种类型。所有基本类型都是类型。很多东西都是类型。

---v

### `<Type as Trait>::AssociatedType`

示例：

```rust [1-4|1-8|1-12|14|1-100]
trait Config {
  type Extrinsic;
  type Header: HeaderT;
}

pub type ExtrinsicFor<C> = <C as Config>::Extrinsic;
fn process_extrinsic<C>(<C as Config>::Extrinsic) { .. }
fn process_extrinsic<C>(BlockFor<C>) { .. }

trait HeaderT {
  type Number;
}

pub type NumberFor<C> = <<C as Config>::Header as HeaderT>::Number;
```

Notes:

turbofish（尖括号语法）
完全限定语法。

---v

### 说到特性（Traits）...

- 泛型和关联类型有什么区别？

```rust
trait Block<Extrinsic> {
  fn execute(e: Extrinsic);
}
```

对比

```rust
trait Block {
  type Extrinsic;
  fn execute(e: Self::Extrinsic);
}
```

Notes:

在剑桥（的课程）中，我是这么讲的。但既然学生们现在应该已经很熟悉特性了，我就不讲这个了。

```rust
trait Engine {
    fn start() {}
}

struct BMW;
impl Engine for BMW {}

trait Brand {
    fn name() -> &'static str;
}

trait Car<E: Engine> {
    type Brand: Brand;
}

struct KianCarCo;
impl Brand for KianCarCo {
  fn name() -> &'static str {
    "KianCarCo!"
    }
}

struct MyCar;
impl<E: Engine> Car<E> for MyCar {
    type Brand = MyBrand;
}

fn main() {
    // Car<E1>, Car<E2>是不同的特性！

    // 泛型可以被限定或约束
    // impl<E: Engine> Car<E> {}
    // impl Car<BMW> {}

    // 关联类型可以：
    // 仅在定义时被限定，
    // 在实现时或使用特性时可以被约束。
    fn some_fn<E: Engine, C: Car<E, Brand = MyBrand>>(car: C) {
      // 我们知道关联类型更像是输出类型，让我们获取汽车的品牌
      let name = <<C as Car<E>>::Brand as Brand>::name();
    }
    fn other_fn<C: Car<BMW, Brand = MyBrand>>(car: C) {

    }

    // 现在，看看这个
}
```

---v

### 说到特性（Traits）...

泛型和关联类型都可以被指定，但语法略有不同。

```rust
trait Block<Extrinsic> {
  type Header;
}

fn process_block<B: Block<E1, Header = H1>>(b: B);
```

---v

### 说到特性（Traits）...

- 任何可以用关联类型表达的内容也可以用泛型表达。
- 关联类型 < 泛型（关联类型功能相对较弱）
- 关联类型通常会减少样板代码。

---

## `std`范式

- 回顾：

  - `std`是与常见操作系统抽象的接口。
  - `core`是`std`的一个子集，它不依赖于特定的操作系统。

- 一个`no_std`的crate是指依赖于`core`而非`std`的crate。

---v

### Cargo特性（Features）

- 通过标志编译不同代码的方式。
- Crate在它们的`Cargo.toml`中定义一些特性。
- Crate也可以有条件地启用其依赖项的特性。

```toml
[dependencies]
other-stuff = { version = "1.0.0" }

[features]
default = [""]
additional-features = ["other-stuff/on-steroids"]
```

Notes:

想象一下，你有一个crate，它有一些不总是需要的额外特性。你可以把这些特性放在一个名为`additional-features`的特性标志后面。

---v

### Cargo特性（Features）：Substrate Wasm Crates

```toml
[dependencies]
dep1 = { version = "1.0.0", default-features = false }
dep2 = { version = "1.0.0", default-features = false }

[features]
default = ["std"]
std = [
  "dep1/std"
  "dep2/std"
]
```

Notes:

每个crate都会有一个名为`std`的特性。这是一个标志，表示你正在使用标准库进行编译。这是默认设置。

然后，引入一个依赖项并设置`default-features = false`意味着默认情况下，不启用这个依赖项的`std`特性。

然后，在`std = ["dep/std"]`中，你是在说“如果我的`std`特性被启用，也启用我的依赖项的`std`特性”。

---v

### Cargo特性（Features）

- 名称`std`只是Rust生态系统中的一种习惯用法。
- `no_std`并不意味着Wasm！
- `std`并不意味着原生！

Notes:

但在Substrate中，它有点类似这样的含义：

std => 原生
no_std => Wasm

---v

### `std`范式

- Substrate中所有最终编译为Wasm的crate：

```rust
#![cfg_attr(not(feature = "std"), no_std)]
```

---v

### `std`范式：添加依赖项

```sh
error: duplicate lang item in crate sp_io (which frame_support depends on): panic_impl.
  |
  = Notes:


 the lang item is first defined in crate std (which serde depends on)

error: duplicate lang item in crate sp_io (which frame_support depends on): oom.
  |
  = Notes:


 the lang item is first defined in crate std (which serde depends on)
```

---v

### `std`范式

Rust标准类型的一个子集也存在于`rust core`中，这些类型从[`sp_std`](https://paritytech.github.io/substrate/master/sp_std/index.html)中重新导出。

```rust
sp_std::prelude::*;
```

Notes:

由于非确定性问题，`Hashmap`没有被导出。
浮点数可以使用，但也是非确定性的！（而且我认为它们缺少`encode`、`decode`的实现）

看看`sp_std`中的`if_std`宏会很有趣。

---

## 运行时中的日志记录和打印

- 首先，为什么要这么做呢？让我们在运行时中添加尽可能多的日志吧。

<!-- .element: class="fragment" -->

- Wasm二进制文件的大小很重要...

<!-- .element: class="fragment" -->

- 任何日志记录都会增加Wasm二进制文件的大小。**字符串字面量**会存储在你的程序中的某个地方！

<!-- .element: class="fragment" -->

---v

### 运行时中的日志记录和打印

- `wasm2wat polkadot_runtime.wasm > dump | rg stripped`

- 这应该会给你Wasm二进制文件的`.rodata`（只读数据）行，其中包含所有的日志噪音。

- 这包含来自错误、日志、元数据等的字符串字面量。

---v

### 运行时中的日志记录和打印

```rust
#[derive(sp_std::fmt::Debug)]
struct LONG_AND_BEAUTIFUL_NAME {
  plenty: u32,
  of: u32,
  fields: u32,
  with: u32,
  different: u32
  names: u32
}
```

这会给你的Wasm二进制文件添加很多字符串字面量。

---v

### 运行时中的日志记录和打印

`sp_std::fmt::Debug` 与 `sp_debug_derive::RuntimeDebug`

Notes:

<https://paritytech.github.io/substrate/master/sp_debug_derive/index.html>

---v

### 运行时中的日志记录和打印

```rust
#[derive(RuntimeDebug)]
pub struct WithDebug {
    foo: u32,
}

impl ::core::fmt::Debug for WithDebug {
    fn fmt(&self, f: &mut ::core::fmt::Formatter) -> ::core::fmt::Result {
        #[cfg(feature = "std)]
        {
          fmt.debug_struct("WithRuntimeDebug")
            .field("foo", &self.foo)
            .finish()
        }
        #[cfg(not(feature = "std))]
        {
          fmt.write("<wasm:stripped>")
        }
    }
}
```

---v

### 运行时中的日志记录和打印

一旦类型实现了`Debug`或`RuntimeDebug`，它们就可以被打印。有多种方式：

- 如果你只希望在测试、原生构建等中使用某些内容

```rust
sp_std::if_std! {
  println!("hello world!");
  dbg!(foo);
}
```

---v

### 运行时中的日志记录和打印

- 或者你可以使用常见的`log` crate

```rust
log::info!(target: "foo", "hello world!");
log::debug!(target: "bar", "hello world! ({})", 10u32);
```

---v

### 运行时中的日志记录和打印

- 但`log` crate本身并不能做太多事情！它需要两个额外的步骤才能工作：

1. `// $ RUST_LOG=foo=debug,bar=trace cargo run`
2. `sp_tracing::try_init_simple()`

Notes:

<https://paritytech.github.io/substrate/master/sp_tracing/index.html>

---v

### 运行时中的日志记录和打印

- 日志语句只有在满足相应的日志级别和目标时才会被评估。

```rust
/// 只有在`RUST_LOG=KIAN=trace`时才会执行
frame_support::log::trace!(target: "KIAN", "({:?})", (0..100000).into_iter().collect());
```

Notes:

Rust中的`log`本身不会做任何事情——它只是跟踪需要记录的内容。然后你需要一个日志记录器来实际输出它们。在Rust中，这通常是`env_logger`，在Substrate测试中则是`sp_tracing`。

在运行时中，日志消息通过主机函数发送到客户端进行打印。

如果接口是使用`disable-logging`构建的，它会忽略所有日志消息。

---

## 算术助手，以及`f32`、`f64`的故事

- 浮点数有不同的标准，并且在不同的架构和供应商上有（**略微**）不同的实现。

- 如果我的余额在一个验证者上是`10.000000000000001` DOT，而在另一个验证者上是`10.000000000000000` DOT，那么你的共识算法就完蛋了😮‍💨。

---v

### 百分比相关

```python
> .2 + .2 + .2 == .6
> false
```

```
> a = 10
> b = 0.1
> c = 0.2
> a*(b+c) == a*b + a*c
> false
```

- 搜索“weird float behavior”可以找到更多关于这个的有趣内容。

---v

### 百分比相关

- 我们在运行时中使用“定点”算术类型来存储比率等。

```rust
struct Percent(u8);

impl Percent {
  fn new(x: u8) {
    Self(x.min(100));
  }
}

impl Mul<u32> for Percent {
  ...
}
```

---v

### 百分比相关

```rust
use sp_arithmetic::Perbill;

let p = Perbill::from_part_parts(1_000_000_000u32 / 4);
let p = Perbill::from_percent(25);
let p = Perbill::from_rational(1, 4);

> p * 100u32;
> 25u32;
```

- 存在一些精度问题，但这是另一个话题了。

---v

### 定点数

`Per-thing`非常适合表示`[0, 1]`范围。

如果我们需要表示更大的范围呢？

```
100 ~ 1
200 ~ 2
300 ~ 3
350 ~ 3.5
```

---v

### 定点数

```rust
use sp_arithmetic::FixedU64;

let x = FixedU64::from_rational(5, 2);
let y = 10u32;
let z = x * y;
> 25
```

---v

### 更大的类型

- [`U256`](https://paritytech.github.io/substrate/master/sp_core/struct.U256.html)、`U512`：自以太坊时代以来经过实战检验。
- [substrate-fixed](https://github.com/encointer/substrate-fixed)：社区项目。超级强大的`PerThing`和`Fixed`。
- [`big_uint.rs`](https://paritytech.github.io/substrate/master/sp_arithmetic/biguint/index.html)（未经审计）

```rust
pub struct BigUint {
	/// 这个数字的数位（肢）（按从最高有效位到最低有效位排序）。
	pub(crate) digits: Vec<Single>,
}
```

---v

### 算术类型
- 可访问 [`sp-arithmetic`](https://paritytech.github.io/substrate/master/sp_arithmetic/index.html) 了解更多信息。

---

### 易错性：数学运算
诸如 **加法**、**乘法**、**除法** 等运算都很容易出错。

<div>

- 引发恐慌
  - `u32::MAX * 2 / 2`（在调试构建中）
  - `100 / 0`

</div>

<!--.element: class="fragment" -->

<div>

- 溢出
  - `u32::MAX * 2 / 2`（在发布构建中）

</div>

<!--.element: class="fragment" -->

---v

### 易错性：数学运算
- `Checked`（检查） -- 预防 ✋🏻

  ```
  if let Some(outcome) = a.checked_mul(b) {... } else {... }
  ```

- `Saturating`（饱和） -- 静默恢复 🤫

  ```
  let certain_output = a.saturating_mul(b);
  ```

Notes:
为什么要使用饱和运算呢？只有在你知道数字是否溢出，且系统的其他方面已经严重受损，进行任何类型的恢复都无济于事的情况下才会使用。

在所有 Rust 基本类型上还有 `wrapping_op` 和 `carrying_op` 等，但不太相关。
<https://doc.rust-lang.org/std/primitive.u32.html#method.checked_add>
<https://doc.rust-lang.org/std/primitive.u32.html#method.saturating_add>

---v

### 易错性：转换
```rust
fn main() {
    let a = 1000u32 as u8;
    println!("{}", a); // 
}
```
Notes:
基本数字类型的转换也是一个常见错误点。避免使用 `as`。

---v

### 易错性：转换
- 幸运的是，Rust 对于基本类型已经相当严格。
- `TryInto` / `TryFrom` / `From` / `Into`

```rust
impl From<u16> for u32 {
  fn from(x: u16) -> u32 {
    x as u32 // ✅
  }
}
```

<!--.element: class="fragment" -->

```rust
impl TryFrom<u32> for u16 {
  fn try_from(x: u32) -> Result<u16, _> {
    if x >= u16::MAX { Err(_) } else { Ok(x as u16) }
  }
}
```

<!--.element: class="fragment" -->

Notes:
通常你无需实现 `Into` 和 `TryInto`，因为有通配实现。可参考：
<https://doc.rust-lang.org/std/convert/trait.From.html>
对于任何 T 和 U，`impl From<T> for U` 意味着 `impl Into<U> for T`

---v

### 易错性：转换
- `struct Foo<T: From<u32>>`

T 是 u32 或更大的类型。

<!--.element: class="fragment" -->

- `struct Foo<T: Into<u32>>`

`T` 是 u32 或更小的类型。

<!--.element: class="fragment" -->

- `struct Foo<T: TryInto<u32>>`

`T` 可以是任何数字类型。

<!--.element: class="fragment" -->

---v

### 易错性：转换
- Substrate 还提供了一个用于无差错饱和转换的特性。
- 可在 `sp-arithmetic` 中查看更多实用内容。

```rust
trait SaturatedConversion {
  fn saturated_into<T>(self) -> T
}

assert_eq!(u128::MAX.saturating_into::<u32>(), u32::MAX);
```

Notes:
<https://paritytech.github.io/substrate/master/sp_arithmetic/traits/trait.SaturatedConversion.html>

---

# 第二部分：FRAME 相关内容

---

## `trait Get`
一种通过 **类型** 传递 **值** 的非常基础但极具 Substrate 风格的方式。

```rust
pub trait Get<T> {
  fn get() -> T;
}
```

```rust
// 一个非常基础的通配实现，你应该非常熟悉如何阅读。
impl<T: Default> Get<T> for () {
  fn get() -> T {
    T::default()
  }
}
```

<!--.element: class="fragment" -->

```rust
struct Foo<G: Get<u32>>;
let foo = Foo<()>;
```

<!--.element: class="fragment" -->

Notes:
为 `()` 实现默认值是一种非常具有 FRAME 风格的做事方式。

---v

### `trait Get`
```rust
parameter_types! {
  pub const Foo: u32 = 10;
}
```

```rust
// 展开为：
pub struct Foo;
impl Get<u32> for Foo {
  fn get() -> u32 {
    10;
  }
}
```

<!--.element: class="fragment" -->

Notes:
你在 Rust 考试中已经实现过这个。

---

## `bounded`
- `BoundedVec`、`BoundedSlice`、`BoundedBTreeMap`、`BoundedSlice`

```rust
#[derive(Encode, Decode)]
pub struct BoundedVec<T, S: Get<u32>>(
  pub(super) Vec<T>,
  PhantomData<S>,
);
```

- &shy;<!--.element: class="fragment" --> `PhantomData`？

---v

### `bounded`
- 为什么不这样创建一个有界类型呢？ 🤔

```rust
#[cfg_attr(feature = "std", derive(Serialize))]
#[derive(Encode)]
pub struct BoundedVec<T>(
  pub(super) Vec<T>,
  u32,
);
```

---v

### `bounded`
_`Get` 特性是一种通过类型传递值的方式。类型系统主要是为编译器服务的，并且在运行时开销极小。_

---

## `trait Convert`
```rust
pub trait Convert<A, B> {
  fn convert(a: A) -> B;
}
```

```rust
pub struct Identity;
// 通配实现！
impl<T> Convert<T, T> for Identity {
  fn convert(a: T) -> T {
    a
  }
}
```

<!--.element: class="fragment" -->

Notes:
这个更简单，但可以借此机会讲解通配实现。

---v

### `Get` 和 `Convert` 的示例
```rust
/// 我的模块的一些配置。
trait Config {
  /// 能提供 `u32` 的东西。
  type MaximumSize: Get<u32>;
  /// 能够将 `u64` 转换为 `u32` 的东西，
  /// 这相当困难。
  type Convertor: Convertor<u64, u32>;
}
```

```rust
struct Runtime;
impl Config for Runtime {
  type MaximumSize = ();
  type Convertor = SomeType
}
```

<!--.element: class="fragment" -->

```rust
<Runtime as Config>::Convertor::convert(_, _);
```

<!--.element: class="fragment" -->

```rust
fn generic_fn<T: Config>() { <T as Config>::Convertor::convert(_, _)}
```

<!--.element: class="fragment" -->

Notes:
通常，在上述示例中，你必须使用这种语法：<https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#fully-qualified-syntax-for-disambiguation-calling-methods-with-the-same-name>

---

## 为元组实现特性
```rust
struct Module1;
struct Module2;
struct Module3;

trait OnInitialize {
  fn on_initialize();
}

impl OnInitialize for Module1 { fn on_initialize() {} }
impl OnInitialize for Module2 { fn on_initialize() {} }
impl OnInitialize for Module3 { fn on_initialize() {} }
```
我如何能轻松地在 `Module1`、`Module2`、`Module3` 这三个模块上调用 `OnInitialize` 呢？

Notes:
另一种方法，但这需要分配内存：

```
struct Module1;
struct Module2;
struct Module3;

trait OnInitializeDyn {
  fn on_initialize(&self);
}

impl OnInitializeDyn for Module1 { fn on_initialize(&self) {} }
impl OnInitializeDyn for Module2 { fn on_initialize(&self) {} }
impl OnInitializeDyn for Module3 { fn on_initialize(&self) {} }

fn main() {
    let x: Vec<Box<dyn OnInitializeDyn>> = vec![Box::new(Module1), Box::new(Module2)];
    x.iter().for_each(|i| i.on_initialize());
}
```

---v

### 为元组实现特性
1. `on_initialize`，理想情况下，没有 `&self`，它是在 **类型** 上定义的，而不是 **值** 上。
1. **元组** 是将 **类型** 组合在一起的自然方式（类似于使用 **向量** 是将 **值** 组合在一起的自然方式）。

```rust
// 完全限定语法 - 尖括号（turbofish）。
<(Module1, Module2, Module3) as OnInitialize>::on_initialize();
```

---v

### 为元组实现特性
唯一的问题：有大量样板代码。使用宏！

过去，我们使用 `macro_rules!` 来实现。

Notes:
```rust
macro_rules! impl_for_tuples {
    ( $( $elem:ident ),+ ) => {
        impl<$( $elem: OnInitialize, )*> OnInitialize for ($( $elem, )*) {
            fn on_initialize() {
                $( $elem::on_initialize(); )*
            }
        }
    }
}

impl_for_tuples!(A, B, C, D);
impl_for_tuples!(A, B, C, D, E);
impl_for_tuples!(A, B, C, D, E, F);
```

---v

### 为元组实现特性
然后有人创建了 `impl_for_tuples` 这个 crate。

```rust
// 最基本的形式：
#[impl_for_tuples(30)]
pub trait OnTimestampSet<Moment> {
  fn on_timestamp_set(moment: Moment);
}
```

Notes:
<https://docs.rs/impl-trait-for-tuples/latest/impl_trait_for_tuples/>

---

## 防御性编程
> 是一种防御性设计的形式，用于确保软件在不可预见的情况下持续运行……在需要 **高可用性**、**安全性** 或 **安全性** 的情况下。
- 如你所知，你（几乎）永远不应该在运行时代码中引发恐慌。

---v

### 防御性编程
- 首先提醒：不要引发恐慌，除非你想惩罚某人！
- `.unwrap()`？ 不行不行

<br />

- 对标准操作中的隐式 `unwrap` 要小心！
  - 切片/向量索引超出范围会引发恐慌
  - `.insert`、`.remove`
  - 除以零。

---v

### 防御性编程
- 在使用可能引发恐慌的操作时，就在其上方准确注释为什么你确定它不会引发恐慌。

```rust
let pos = announcements
 .binary_search(&announcement)
 .ok()
 .ok_or(Error::<T, I>::MissingAnnouncement)?;
// 索引来自 `binary_search`，因此不会超出范围。
announcements.remove(pos);
```

---v

### 防御性编程：QED
或者当使用需要解包且已知为 `Ok(_)`、`Some(_)` 的选项或结果时：

```rust
let maybe_value: Option<_> =...
if maybe_value.is_none() {
  return "..."
}

let value = maybe_value.expect("value checked to be 'Some'; qed");
```
- Q.E.D. 或 QED 是拉丁语短语 "quod erat demonstrandum" 的首字母缩写，意为 "**有待证明的**"。

---v

### 防御性编程
在编写可能引发恐慌的 API 时，要明确地对其进行文档记录，就像核心 Rust 文档那样。

```rust
/// 与 [`Vec::insert`] 具有完全相同的语义，但如果向量的新长度超过 `S`，将返回 `Err`（且不执行任何操作）。
///
/// # 恐慌
///
/// 如果 `index > len`，会引发恐慌。
pub fn try_insert(&mut self, index: usize, element: T) -> Result<(), ()> {
  if self.len() < Self::bound() {
    self.0.insert(index, element);
    Ok(())
  } else {
    Err(())
  }
}
```

---v

### 防御性编程
- 说到文档，[这里有一个非常好的指南](https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html)！
````rust
/// 将给定输入乘以 2。
///
/// 有关此操作的更多信息以及可以使用它的地方。
///
/// ```
/// fn main() {
///   let x = multiply_by_2(10);
///   assert_eq!(10, 20);
/// }
/// ```
///
/// ## 恐慌
///
/// 在如下条件下会引发恐慌。
fn multiply_by_2(x: u32) -> u32 {.. }
````

---v

### 防御性编程
- 尽量不要成为这样的人：

```rust
/// 此函数与模块 x 一起工作并将给定输入乘以 2。如果
/// 我们对其另一种变体进行优化，我们将能够实现更高的
/// 效率，但我需要考虑一下。如果输入
/// 溢出 u32 可能会引发恐慌。
fn multiply_by_2(x: u32) -> u32 {.. }
```

---v

### 防御性编程
- 防御性编程的总体理念大致如下：

```rust
// 我们有充分的理由相信这是 `Some`。
let y: Option<_> =...

// 我对此非常确定
let x = y.expect("确凿证据；qed");

// 要么返回一个合理的默认值……
let x = y.unwrap_or(reasonable_default);

// 要么返回一个错误（特别是在可调度函数中）
let x = y.ok_or(Error::DefensiveError)?;
```

Notes:
但是，例如，你绝对确定 `Error::DefensiveError` 永远不会发生，我们能否更好地强制执行呢？

---v

### 防御性编程
```rust
let x = y
 .ok_or(Error::DefensiveError)
 .map_err(|e| {
    #[cfg(test)]
    panic!("发生防御性错误：{:?}", e);

    log::error!(target: "..", "发生防御性错误：{:?}", e);
  })?;
```

---v

### 防御性编程
- 是的：[防御性特性](https://paritytech.github.io/substrate/master/frame_support/traits/trait.Defensive.html)：

```
// 要么返回一个合理的默认值……
let x = y.defensive_unwrap_or(reasonable_default);

// 要么返回一个错误（特别是在可调度函数中）
let x = y.defensive_ok_or(Error::DefensiveError)?;
```

它添加了一些样板代码，用于：
1. 当启用 `debug_assertions` 时引发恐慌（测试时）。
1. 附加一个 `log::error!`。

---

## 更多资源！😋

<img width="300px" rounded src="../scale/img/thats_all_folks.png" />

> 查看演讲者备注（点击 "s" 😉）

> 祝使用 FRAME 顺利！

Notes:
- Rust直到不久前才有u128！<https://github.com/paritytech/substrate/pull/163/files>
- `TryFrom`/`TryInto`出现的时间也不长！<https://github.com/paritytech/substrate/pull/163/files#r188938077>
- 移除了曾试图弥补`TryFrom`/`TryInto`缺失的`As`特性，相关链接：<https://github.com/paritytech/substrate/pull/2602>
- 运行时日志记录相关的合并请求：<https://github.com/paritytech/substrate/pull/3821>
- 为元组实现特性相关资料：
  - <https://stackoverflow.com/questions/64332037/how-can-i-store-a-type-in-an-array>
  - <https://doc.rust-lang.org/book/ch17-02-trait-objects.html#trait-objects-perform-dynamic-dispatch>
  - <https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#fully-qualified-syntax-for-disambiguation-calling-methods-with-the-same-name>
  - <https://turbo.fish/>
  - <https://techblog.tonsser.com/posts/what-is-rusts-turbofish>
  - <https://docs.rs/impl-trait-for-tuples/latest/impl_trait_for_tuples/>
- std/no_std相关资料：
  - <https://paritytech.github.io/substrate/master/sp_std/index.html>
  - <https://doc.rust-lang.org/core/index.html>
  - <https://doc.rust-lang.org/std/index.html>
  - <https://rust-lang.github.io/api-guidelines/naming.html#feature-names-are-free-of-placeholder-words-c-feature>

### 课后反馈：
- 讲座内容仍然有点密集和冗长，尝试精简一下
- 防御性操作的更新：<https://github.com/paritytech/substrate/pull/12967>
- 下次可以讲讲如何让存储结构体为`<T: Config>`形式。
- Cargo格式化相关内容
- SignedExtension从技术上讲应该是substrate模块的一部分。或许可以将其融入作业中。
- 增加关于`XXXNoBound`特性的讲解部分。
- 增加关于从上到下解读编译器错误的讲解部分，特别是结合FRAME中的示例。 