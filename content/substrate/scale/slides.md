---
title: SCALE Codec
description: SCALE Codec for web3 builders
duration: 1 hour
---

---
标题：SCALE Codec
描述：面向Web3开发者的SCALE Codec
时长：1小时
---

# SCALE Codec

---

## SCALE Codec

在本讲座结束时，你将了解到Substrate为什么使用SCALE编解码器，以及所有不同类型的数据是如何编码的。

---

### SCALE

简单串联聚合小端序

SCALE是一种轻量级格式，支持编码（和解码），使其非常适合在资源受限的执行环境中使用，如区块链运行时以及低功耗、低内存设备。

---

### 小端序

小端序系统将最低有效字节存储在最小的内存地址中。

Wasm是一个小端序系统，这使得SCALE的性能非常高。

<pba-cols>
<pba-col center>

<img src="./img/Big-Endian.svg" style="background-color: white; border-radius: 10%; border: 5px solid red;" />

</pba-col>

<pba-col center>

<img src="./img/Little-Endian.svg" style="background-color: white; border-radius: 10%; border: 5px solid green;" />

</pba-col>
</pba-cols>

---

### 为什么选择SCALE？为什么不选其他的？

- 定义简单。
- 非Rust特定（但在Rust中使用效果很好）。
  - 易于派生编解码器逻辑：`#[derive(Encode, Decode)]`
  - 对于像`MaxEncodedLen`和`TypeInfo`这样的API很实用。
  - 它不使用Rust的`std`，因此可以编译为Wasm的`no_std`。
- 共识关键/双射；一个值总是编码为一个二进制块，并且该二进制块只能解码为该值。
- 在小端序架构上支持基本类型的无复制解码。
- 它尽可能地精简和轻量级。

---

### SCALE不是自描述的

需要注意的是，编码上下文（即对类型和数据结构的了解）在编码端和解码端都需要分别知晓。

编码后的数据不包含这些上下文信息。

---

### 示例：SCALE与JSON对比

<div class="flex-container text-smaller">
<div class="left">

```rust
use parity_scale_codec::{ Encode };

#[derive(Encode)]
struct Example {
	number: u8,
	is_cool: bool,
	optional: Option<u32>,
}

fn main() {
	let my_struct = Example {
		number: 42,
		is_cool: true,
		optional: Some(69),
	};
	println!("{:?}", my_struct.encode());
	println!("{:?}", my_struct.encode().len());
}
```

```sh
[42, 1, 1, 69, 0, 0, 0]
7
```

</div>
<div class="right" style="margin-left: 10px;">

```rust
use serde::{ Serialize };

#[derive(Serialize)]
struct Example {
	number: u8,
	is_cool: bool,
	optional: Option<u32>,
}

fn main() {
	let my_struct = Example {
		number: 42,
		is_cool: true,
		optional: Some(69),
	};
	println!("{:?}", serde_json::to_string(&my_struct).unwrap());
	println!("{:?}", serde_json::to_string(&my_struct).unwrap().len());
}
```

```sh
"{\"number\":42,\"is_cool\":true,\"optional\":69}"
42
```

</div>
</div>

---

### 自己试试！

```sh
mkdir temp
cd temp
cargo init
cargo add parity-scale-codec --features derive
```

---

### 小端序与大端序输出

读取输出结果可能会让人感到困惑，同时要记住字节序的问题。

向量中字节的顺序遵循字节序，但每个字节的十六进制和二进制表示是相同的，并且与字节序无关。

`0b`前缀表示二进制表示，`0x`表示十六进制表示。

<pba-cols>
<pba-col center>

```rust
fn main() {
	println!("{:b}", 69i8);
	println!("{:02x?}", 69i8.to_le_bytes());
	println!("{:02x?}", 69i8.to_be_bytes());
	println!("{:b}", 42u16);
	println!("{:02x?}", 42u16.to_le_bytes());
	println!("{:02x?}", 42u16.to_be_bytes());
	println!("{:b}", 16777215u32);
	println!("{:02x?}", 16777215u32.to_le_bytes());
	println!("{:02x?}", 16777215u32.to_be_bytes());
}
```

</pba-col>

<pba-col center style="margin-left: 10px;">

```sh
1000101
[45]
[45]
101010
[2a, 00]
[00, 2a]
111111111111111111111111
[ff, ff, ff, 00]
[00, ff, ff, ff]
```

</pba-col>
</pba-cols>

---

### 固定宽度整数

基本整数使用固定宽度的小端序（LE）格式进行编码。

```rust
use parity_scale_codec::Encode;

fn main() {
	println!("{:02x?}", 69i8.encode());
	println!("{:02x?}", 69u8.encode());
	println!("{:02x?}", 42u16.encode());
	println!("{:02x?}", 16777215u32.encode());
}
```

```sh
[45]
[45]
[2a, 00]
[ff, ff, ff, 00]
```

注意：

注意前两个结果是相同的。SCALE不描述类型。解码器负责将其解码为某种1字节宽度的类型，无论是`u8`、`i8`还是其他类型。

---

### 紧凑整数

“紧凑”或通用整数编码足以对大整数（最大到2<sup>536</sup>）进行编码，并且在编码大多数值时比固定宽度版本更高效。

不过对于单字节值，固定宽度整数编码永远不会更差。

---

### 紧凑前缀

<div class="text-small">

<!-- prettier-ignore -->
| `0b00` | `0b01` | `0b10` | `0b11` |
| ------ | ------ | ------ | ------ |
| 单字节模式；高六位是值的小端序编码。仅对`0`到`63`的值有效。 | 双字节模式：高六位和后续字节是值的小端序编码。仅对`64`到`(2^14 - 1)`的值有效。 | 四字节模式：高六位和后续三个字节是值的小端序编码。仅对`(2^14)`到`(2^30 - 1)`的值有效。 | 大整数模式：高六位是后续字节数加4。值以小端序编码包含在后续字节中。最后（最高有效）字节必须非零。仅对`(2^30)`到`(2^536 - 1)`的值有效。 |

</div>

紧凑/通用整数编码时，最低两位表示模式。

---

### 紧凑整数：0

```rust
use parity_scale_codec::{Encode, HasCompact};

#[derive(Encode)]
struct AsCompact<T: HasCompact>(#[codec(compact)] T);

fn main() {
	println!("{:02x?}", 0u8.encode());
	println!("{:02x?}", 0u32.encode());
	println!("{:02x?}", AsCompact(0u8).encode());
	println!("{:02x?}", AsCompact(0u32).encode());
}
```

```sh
[00]
[00, 00, 00, 00]
[00]
[00]
```

---

### 紧凑整数：42

```rust
use parity_scale_codec::{Encode, HasCompact};

#[derive(Encode)]
struct AsCompact<T: HasCompact>(#[codec(compact)] T);

fn main() {
	println!("{:02x?}", 42u8.encode());
	println!("{:02x?}", 42u32.encode());
	println!("{:02x?}", AsCompact(42u8).encode());
	println!("{:02x?}", AsCompact(42u32).encode());
}
```

```sh
[2a]
[2a, 00, 00, 00]
[a8]
[a8]
```

- 42的二进制表示：`0b101010` = `[0x2a]`。
- 在最低有效位添加`00`。
- `0b10101000` = `[0xa8]` = 十进制的168。

---

### 紧凑整数：69

```rust
use parity_scale_codec::{Encode, HasCompact};

#[derive(Encode)]
struct AsCompact<T: HasCompact>(#[codec(compact)] T);

fn main() {
	println!("{:02x?}", 69u8.encode());
	println!("{:02x?}", 69u32.encode());
	println!("{:02x?}", AsCompact(69u8).encode());
	println!("{:02x?}", AsCompact(69u32).encode());
}
```

```sh
[45]
[45, 00, 00, 00]
[15, 01]
[15, 01]
```

- 69的二进制表示：`0b1000101` = `[0x45]`。
- 在最低有效位添加`01`。
- `0b100010101` = `[0x15, 0x01]` = 十进制的277。

---

### 紧凑整数：65535 (u16::MAX)

```rust
use parity_scale_codec::{Encode, HasCompact};

#[derive(Encode)]
struct AsCompact<T: HasCompact>(#[codec(compact)] T);

fn main() {
	println!("{:02x?}", 65535u16.encode());
	println!("{:02x?}", 65535u32.encode());
	println!("{:02x?}", AsCompact(65535u16).encode());
	println!("{:02x?}", AsCompact(65535u32).encode());
}
```

```sh
[ff, ff]
[ff, ff, 00, 00]
[fe, ff, 03, 00]
[fe, ff, 03, 00]
```

- 65535的二进制表示：`0b1111111111111111` = `[0xff, 0xff]`。
- 在最低有效位添加`10`。
- `0b111111111111111110` = `[0xfe, 0xff, 0x03, 0x00]`：十进制的262142。

---

### 紧凑整数是“向后兼容”的

如你所见，你可以“升级”一个类型而不影响编码。

---

### 枚举

前缀为索引（`u8`），然后是值（如果有）。

```rust
use parity_scale_codec::Encode;

#[derive(Encode)]
enum Example {
	First,
	Second(u8),
	Third(Vec<u8>),
	Fourth,
}

fn main() {
	println!("{:02x?}", Example::First.encode());
	println!("{:02x?}", Example::Second(2).encode());
	println!("{:02x?}", Example::Third(vec![0, 1, 2, 3, 4]).encode());
	println!("{:02x?}", Example::Fourth.encode());
}
```

```sh
[00]
[01, 02]
[02, 14, 00, 01, 02, 03, 04]
[03]
```

---

### 元组和结构体

只需对各项进行编码并连接起来。

```rust
use parity_scale_codec::Encode;

#[derive(Encode)]
struct Example {
	number: u8,
	is_cool: bool,
	optional: Option<u32>,
}

fn main() {
	let my_struct = Example {
		number: 0,
		is_cool: true,
		optional: Some(69),
	};
	println!("{:02x?}", (0u8, true, Some(69u32)).encode());
	println!("{:02x?}", my_struct.encode());
}
```

```sh
[00, 01, 01, 45, 00, 00, 00]
[00, 01, 01, 45, 00, 00, 00]
```

注意：

注意元组和结构体的编码是相同的，即使结构体有命名字段。

---

## 嵌入式紧凑

```rust
use parity_scale_codec::Encode;

#[derive(Encode)]
struct Example {
	number: u64,
	#[codec(compact)]
	compact_number: u64,
}

#[derive(Encode)]
enum Choices {
	One(u64, #[codec(compact)] u64),
}

fn main() {
	let my_struct = Example { number: 42, compact_number: 1337 };
	let my_choice = Choices::One(42, 1337);
	println!("{:02x?}", my_struct.encode());
	println!("{:02x?}", my_choice.encode());
}
```

```sh
[2a, 00, 00, 00, 00, 00, 00, 00, e5, 14]
[00, 2a, 00, 00, 00, 00, 00, 00, 00, e5, 14]
```

---

### 单元类型、布尔类型、可选类型和结果类型
```rust
use parity_scale_codec::Encode;

fn main() {
    println!("{:02x?}", ().encode());
    println!("{:02x?}", true.encode());
    println!("{:02x?}", false.encode());
    println!("{:02x?}", Ok::<u32, ()>(42u32).encode());
    println!("{:02x?}", Err::<u32, ()>(()).encode());
    println!("{:02x?}", Some(42u32).encode());
    println!("{:02x?}", None::<u32>.encode());
}
```
```sh
[]
[01]
[00]
[00, 2a, 00, 00, 00]
[01]
[01, 2a, 00, 00, 00]
[00]
```
---
### 数组、向量和字符串
- 数组：直接将元素连接起来。
- 向量：还会在前面加上长度（紧凑编码）。
- 字符串：就是作为UTF - 8字符的`Vec<u8>`。
```rust
use parity_scale_codec::Encode;

fn main() {
    println!("{:02x?}", [0u8, 1u8, 2u8, 3u8, 4u8].encode());
    println!("{:02x?}", vec![0u8, 1u8, 2u8, 3u8, 4u8].encode());
    println!("{:02x?}", "hello".encode());
    println!("{:02x?}", vec![0u8; 1024].encode());
}
```
```sh
[00, 01, 02, 03, 04]
[14, 00, 01, 02, 03, 04]
[14, 68, 65, 6c, 6c, 6f]
[01, 10, 00, 00, ... 此处省略部分内容 ... , 00]
```
注意：
注意长度前缀可能是多个字节，如最后一个示例所示。
---
### 解码
同样，我们可以获取原始字节，并将其解码为已知类型。
元数据可用于告知程序如何正确解码一种类型……
但是，如果信息错误或没有信息，就无法得知数据的正确格式。
---
### 解码示例
```rust
use parity_scale_codec::{ Encode, Decode, DecodeAll };

fn main() {
    let array = [0u8, 1u8, 2u8, 3u8];
    let value: u32 = 50462976;

    println!("{:02x?}", array.encode());
    println!("{:02x?}", value.encode());
    println!("{:?}", u32::decode(&mut &array.encode()[..]));
    println!("{:?}", u16::decode(&mut &array.encode()[..]));
    println!("{:?}", u16::decode_all(&mut &array.encode()[..]));
    println!("{:?}", u64::decode(&mut &array.encode()[..]));
}
```
```sh
[00, 01, 02, 03]
[00, 01, 02, 03]
Ok(50462976)
Ok(256)
Err(Error { cause: None, desc: "Input buffer has still data left after decoding!" })
Err(Error { cause: None, desc: "Not enough data to fill buffer" })
```
注意：
- 解码可能会失败
- 值可能解码错误
---
### 解码限制
- 解码并非没有成本！
- 解码类型越复杂，解码值所需的计算量就越大。
- 通常你总是希望使用`decode_with_depth_limit`。
- Substrate使用的限制值为256 。
---
### 解码炸弹
这里有一个解码炸弹的示例。
```rust
use parity_scale_codec::{ Encode, Decode, DecodeLimit };

#[derive(Encode, Decode, Debug)]
enum Example {
    First,
    Second(Box<Self>),
}

fn main() {
    let bytes = vec![1, 1, 1, 1, 1, 0];
    println!("{:?}", Example::decode(&mut &bytes[..]));
    println!("{:?}", Example::decode_with_depth_limit(10, &mut &bytes[..]));
    println!("{:?}", Example::decode_with_depth_limit(3, &mut &bytes[..]));
}
```
```sh
Ok(Second(Second(Second(Second(Second(First))))))
Ok(Second(Second(Second(Second(Second(First))))))
Err(Error { cause: Some(Error { cause: Some(Error { cause: Some(Error { cause: Some(Error { cause: None, desc: "Maximum recursion depth reached when decoding" }), desc: "Could not decode `Example::Second.0`" }), desc: "Could not decode `Example::Second.0`" }), desc: "Could not decode `Example::Second.0`" }), desc: "Could not decode `Example::Second.0`" })
```
---
### 例外：BTreeSet
BTreeSet会从无序集合中解码，但解码结果会是有序的。
要小心……这个操作不是双射的。
```rust
use parity_scale_codec::{ Encode, Decode, alloc::collections::BTreeSet };

fn main() {
    let vector = vec![4u8, 3u8, 2u8, 1u8, 0u8];
    let vector_encoded = vector.encode();
    let btree = BTreeSet::<u8>::decode(&mut &vector_encoded[..]).unwrap();
    let btree_encoded = btree.encode();

    println!("{:02x?}", vector_encoded);
    println!("{:02x?}", btree_encoded);
}
```
```sh
[14, 04, 03, 02, 01, 00]
[14, 00, 01, 02, 03, 04]
```
---
### 优化和技巧
- `DecodeLength`：在不解码所有内容的情况下读取集合（如`Vec`）的长度。
- `EncodeAppend`：在不解码所有其他元素的情况下追加一个元素。（如`Vec`）
---
### 实现
SCALE Codec已在其他语言中实现，包括：
- Python: [`polkascan/py-scale-codec`](https://github.com/polkascan/py-scale-codec/)
- Golang: [`itering/scale.go`](https://github.com/itering/scale.go/)
- C: [`MatthewDarnell/cScale`](https://github.com/MatthewDarnell/cScale/)
- C++: [`soramitsu/scale-codec-cpp`](https://github.com/qdrvm/scale-codec-cpp/)
- JavaScript: [`polkadot-js/api`](https://github.com/polkadot-js/api/)
- TypeScript: [`scale-ts`](https://github.com/unstoppablejs/unstoppablejs/tree/main/packages/scale-ts#scale-ts)
- AssemblyScript: [`LimeChain/as-scale-codec`](https://github.com/LimeChain/as-scale-codec/)
- Haskell: [`airalab/hs-web3`](https://github.com/airalab/hs-web3/tree/master/packages/scale/)
- Java: [`emeraldpay/polkaj`](https://github.com/emeraldpay/polkaj/)
- Ruby: [`wuminzhe/scale_rb`](https://github.com/wuminzhe/scale_rb/)
---
## 缺少元数据？
为了使SCALE在Substrate和Polkadot生态系统中作为一种编码格式发挥作用，我们需要找到一种方法来提供关于我们预期的所有类型以及预期使用这些类型时机的**元数据**。
提示：我们确实有办法。
---
### 记住，归根结底，一切都是0和1 。
---
## 更多资源！😋
> 查看演讲者备注（点击“s” 😉）
<img width="300px"  rounded src="./img/thats_all_folks.png" />
Notes: