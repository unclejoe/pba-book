---
title: Wasm Smart Contracts in Ink!
description: A working programmer’s guide to the crypto industry
---
<img rounded style="width: 600px;" src="./img/ink-logo-with-squid-white.svg" />

# Ink! 中的 Wasm 智能合约

一份面向在职程序员的指南

Notes:
- 讲座期间随时可以提问，不要等到结束。
- 注重实践，但必要时会深入讲解。
- 示例中省略了一些复杂性（示例并非生产代码）。

---

## 简介：ink! 与 Solidity 的对比

|                 | ink!                        | Solidity      |
| --------------- | --------------------------- | ------------- |
| 虚拟机           | 任何 Wasm 虚拟机             | EVM           |
| 编码方式         | Wasm                        | EVM 字节码    |
| 编程语言         | Rust                        | 独立语言      |
| 构造函数         | 多个                        | 单个          |
| 工具             | 任何支持 Rust 的工具         | 定制工具      |
| 存储             | 变量                        | 256 位        |
| 接口？           | 有：Rust 特性               | 有            |

Notes:
- 学生们刚上完 EVM 讲座，可能会疑惑为什么还要学习另一种智能合约语言。
- 虚拟机：任何 Wasm 虚拟机：理论上可以，实际上与运行平台（Substrate 及合约模块）紧密相关。
- 工具：Solidity 已经存在多年，具有先发优势（但 ink! 是一个强大的竞争者）。
- EVM 以 256 位字为单位进行操作（意味着小于 32 字节的任何内容都会被 EVM 视为带有前导零）。

---

## 简介：ink! 概述
- Rust 中的领域特定语言（DSL）
- 继承了 Rust 的所有优势
  - 现代函数式语言
  - 类型和内存安全
- 编译为 Wasm
  - 无处不在
  - 快速

Notes:
- ink! 不是一门独立的语言。
- 可以使用为其他目的开发的大量库。
- Wasm 以浏览器为目标，并且正在迅速成为取代 JavaScript 的网络“汇编语言”。

---

## 简介：ink! 与 Substrate

<img rounded style="width: 900px;" src="./img/lego0.png" />

Notes:
- 从技术上讲，你可以将用 ink! 编写的智能合约部署到任何支持 Wasm 的区块链上。
  - 但实际上并非那么直接。
- ink! 与更大的 Substrate 框架紧密相关。
- Substrate 是一个用于从可组合模块开发定制区块链运行时的框架。

---

## 简介：ink! 与 Substrate

<img rounded style="width: 900px;" src="./img/lego1.png" />

Notes:
- 用 ink! 编写的合约会被编译为 Wasm 字节码。
- 合约模块提供：
  - 检测工具
  - 执行引擎
  - 燃气计量

---

<img rounded style="width: 800px;" src="./img/schema1.png" />

Notes:
- 合约模块对编程语言是无感知的。
- 它接受 Wasm 字节码并执行其指令。

---

<img rounded style="width: 800px;" src="./img/schema2.png" />

Notes:
- 合约本身可以用 ink! 编写。

---

<img rounded style="width: 800px;" src="./img/schema3.png" />

Notes:
- 但也可以使用任何其他能编译为 Wasm 的语言，例如：
  - Solang
  - 或者 ask!

---

## 开发：先决条件

安装所需的工具：

```sh
sudo apt install binaryen
rustup component add rust-src --toolchain nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
cargo install dylint-link
cargo install cargo-contract --force
```

- [binaryen](https://github.com/WebAssembly/binaryen) 是一个 WebAssembly 编译器。
- [dylint-link](https://github.com/trailofbits/dylint) 增加了特定于 DSL 的代码检查。

Notes:
- Binaryen 是一个用于 WebAssembly 的编译器和工具链基础设施库。
- 目前 ink! 使用了一些 Rust 的不稳定特性，因此需要使用 nightly 版本。
- 需要 Rust 源代码才能将其编译为 Wasm。
- 添加了 Wasm 目标。
- cargo-contract 是一个功能齐全的命令行工具，用于编译、部署和与合约交互。

---

## 开发：cargo-contract

创建一个合约：

```sh
cargo contract new flipper
```

```sh
/home/CloudStation/Blockchain-Academy/flipper:
  drwxrwxr-x 2 filip filip 4096 Jul  7 11:11 .
  drwxr-xr-x 5 filip filip 4096 Jul  7 11:11 ..
  -rwxr-xr-x 1 filip filip  573 Jul  7 11:11 Cargo.toml
  -rwxr-xr-x 1 filip filip  285 Jul  7 11:11 .gitignore
  -rwxr-xr-x 1 filip filip 5186 Jul  7 11:11 lib.rs
```

Notes:
- 询问有多少学生用 Rust 编写过代码，这对他们来说应该很熟悉。

---

## 开发：Cargo.toml

<div style="font-size: 0.72em;">

```toml
[package]
version = "0.1.0"
authors = ["fbielejec"]
edition = "2021"

[dependencies]
ink = { version = "=4.2.1", default-features = false }
scale = { package = "parity-scale-codec", version = "3", default-features = false, features = ["derive"] }
scale-info = { version = "2.6", default-features = false, features = ["derive"], optional = true }

[lib]
path = "lib.rs"

[features]
default = ["std"]
std = [
  "ink/std",
  "scale/std",
  "scale-info/std",
]
```

</div>

Notes:
- 谁知道为什么标准库不是默认包含的？
- 答案：合约被编译为 Wasm（在沙盒环境中执行，没有系统接口、没有输入输出、没有网络）。

---

## 开发合约

合约代码：

<div style="font-size: 0.62em;">

```rust
#[ink::contract]
pub mod flipper {

    #[ink(storage)]
    pub struct Flipper {
        value: bool,
    }

    impl Flipper {
        #[ink(constructor)]
        pub fn new(init_value: bool) -> Self {
            Self { value: init_value }
        }

        #[ink(constructor)]
        pub fn default() -> Self {
            Self::new(Default::default())
        }

        #[ink(message)]
        pub fn flip(&mut self) {
            self.value = !self.value;
        }

        #[ink(message)]
        pub fn get(&self) -> bool {
            self.value
        }
    }
}
```

</div>

Notes:
- 一个基本的合约，用于翻转存储中的一个位。
- 合约将包含存储定义、构造函数、消息。
- 这些都被分组在一个模块中。

---

## 开发合约：编译与工件

编译：

```sh
cargo +nightly contract build
```

工件：

```
 [1/*] Building cargo project
    Finished release [optimized] target(s) in 0.09s

The contract was built in RELEASE mode.

Your contract artifacts are ready.
You can find them in:
/home/CloudStation/Blockchain-Academy/flipper/target/ink

  - flipper.contract (代码 + 元数据)
  - flipper.wasm (合约的代码)
  - flipper.json (合约的元数据)
```

Notes:
- 生成 Wasm 字节码和一些其他工件：
- .wasm 是合约编译后的字节码。
- .json 是合约的应用二进制接口（ABI），也就是元数据（供例如去中心化应用程序使用），包含事件、存储、交易的定义。
- .contract 包含上述两者。

---

## 开发合约：实例化

部署：

```sh
cargo contract instantiate --constructor default --suri //Alice
  --skip-confirm --execute
```

输出：

<div style="font-size: 0.82em;">

```sh [13-14]
 Dry-running default (skip with --skip-dry-run)
    Success! Gas required estimated at Weight(ref_time: 138893374, proof_size: 16689)
...
  Event Contracts ➜ CodeStored
         code_hash: 0xbf18c768eddde46205f6420cd6098c0c6e8d75b8fb042d635b1ba3d38b3d30ad
       Event Contracts ➜ Instantiated
         deployer: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
         contract: 5EXm8WLAGEXn6zy1ebHZ4MrLmjiNnHarZ1pBBjZ5fcnWF3G8
...
       Event System ➜ ExtrinsicSuccess
         dispatch_info: DispatchInfo { weight: Weight { ref_time: 2142580978, proof_size: 9009 }, class: Normal, pays_fee: Yes }

   Code hash 0xbf18c768eddde46205f6420cd6098c0c6e8d75b8fb042d635b1ba3d38b3d30ad
    Contract 5EXm8WLAGEXn6zy1ebHZ4MrLmjiNnHarZ1pBBjZ5fcnWF3G8
```

</div>

Notes:
- 我们看到了一些关于燃气使用的信息。
- 我们看到两个事件，一个是存储合约代码的事件，另一个是实例化合约的事件。
  - 为什么会这样？
  - 代码和实例是分离的，我们稍后会再讨论这个问题。
- 最后我们看到了代码哈希和新创建的合约地址。

---

## 与合约交互：查询

```sh
cargo contract call --contract 5EXm8WLAGEXn6zy1ebHZ4MrLmjiNnHarZ1pBBjZ5fcnWF3G8
  --message get --suri //Alice --output-json
```

- 合约状态？
- 提示：调用了 `default` 构造函数。

Notes:
- 谁能告诉我此时的合约状态是什么？

---

## 与合约交互：查询

<!-- 查询合约状态： -->

<!-- ```sh -->
<!-- cargo contract call --contract 5EXm8WLAGEXn6zy1ebHZ4MrLmjiNnHarZ1pBBjZ5fcnWF3G8 -->
<!--   --message get --suri //Alice --output-json -->
<!-- ``` -->

<!-- 结果： -->

```[6]
"data": {
  "Tuple": {
    "ident": "Ok",
    "values": [
      {
        "Bool": false
      }
    ]
  }
}
```

---

## 交互：交易

签名并执行一个交易：

```sh
cargo contract call --contract 5EXm8WLAGEXn6zy1ebHZ4MrLmjiNnHarZ1pBBjZ5fcnWF3G8
  --message flip --suri //Alice --skip-confirm --execute
```

查询状态：

```sh
cargo contract call --contract 5EXm8WLAGEXn6zy1ebHZ4MrLmjiNnHarZ1pBBjZ5fcnWF3G8
  --message get --suri //Alice --output-json
```

结果：

<div style="font-size: 0.82em;">

```[6]
"data": {
  "Tuple": {
    "ident": "Ok",
    "values": [
      {
        "Bool": true
      }
    ]
  }
}
```

</div>

Notes:
- 如果我再次查询，这个位就会翻转。
- 没有什么意外的。

---

## 开发环境：合约用户界面（Contracts UI）

<img rounded style="width: 1400px;" src="./img/contracts_ui_1.jpg" />

Notes:
- 还有一个图形化环境用于部署和与合约交互。
- 部署并创建一个 flipper 实例。

---

## 开发环境：合约用户界面（Contracts UI）

<img rounded style="width: 1400px;" src="./img/contracts_ui_2.jpg" />

Notes:
- 调用一个交易。

---

## 开发环境：合约用户界面（Contracts UI）

<img rounded style="width: 1400px;" src="./img/contracts_ui_3.jpg" />

Notes:
- 查询状态。

---

## 开发合约：构造函数

```rust [7,12,17|13,18,7-9|2-4,8]
#[ink(storage)]
pub struct Flipper {
    value: bool,
}

#[ink(constructor)]
pub fn new(init_value: bool) -> Self {
    Self { value: init_value }
}

#[ink(constructor)]
pub fn default() -> Self {
    Self::new(Default::default())
}

#[ink(constructor)]
pub fn non_default() -> Self {
    Self::new(false)
}
```

Notes:
- 让我们剖析一下合约代码的结构。
- 构造函数的数量没有限制。
- 构造函数可以调用其他构造函数。
- 构造函数返回初始存储。
- 很多复杂性都方便地隐藏在宏后面。

---

## 开发合约：查询

```rust
#[ink(message)]
pub fn get(&self) -> bool {
    self.value
}
```
- `#[ink(message)]`是我们告知ink!这是一个可以在合约上调用的函数的方式。
- `&self`是对合约存储的一个引用。

<!-- #你正在这个上调用此方法 -->

Notes:
- 返回存储在链上的合约状态信息。
- 访问存储，对其进行解码并返回值。

---
## 开发合约：修改

```rust [1-2|6]
#[ink(message, payable)]
pub fn place_bet(&mut self, bet_type: BetType) -> Result<()> {
    let player = self.env().caller();
    let amount = self.env().transferred_value();
   ...
    self.data.set(&data);
   ...
```
- `&mut self`是你正在调用此方法的对象的可变引用。
- `payable`允许在调用ink!消息时接收值。

Notes:
- 构造函数本质上是可支付的。
- 如果ink!消息未标记为可支付，它将拒绝带有资金的调用。
- 可变引用允许我修改存储。
- 查询是免费的，修改是计量的（你需要支付燃气）。
  - 你还将为这类交易中的查询付费。

---
## 合约：错误处理

<div style="font-size: 0.72em;">

```rust [1-4|8-11,16|14,20]
pub enum MyResult<T, E> {
    Ok(value: T),
    Err(msg: E),
}

#[derive(Debug, PartialEq, Eq, Encode, Decode)]
#[cfg_attr(feature = "std", derive(scale_info::TypeInfo))]
pub enum MyError {
    InkEnvError(String),
    BettingPeriodNotOver,
}

#[ink(message)]
pub fn spin(&mut self) -> Result<()> {
    if!self.is_betting_period_over() {
        return Err(MyError::BettingPeriodNotOver);
   ...
};

pub type Result<T> = core::result::Result<T, MyError>;
```

</div>
- ink!使用惯用的Rust错误处理：`Result<T,E>`类型。
- 使用Err变体传递你自己的语义。
- 类型别名可减少样板代码并增强可读性。

Notes:
- ink!使用惯用的Rust错误处理。
- ~~消息是`系统边界`~~
- 返回错误变体或引发恐慌会回滚事务。
  - 引发恐慌与返回Err变体相同（`Result`只是一种更友好的方式）。

---
## 错误处理：调用栈

```rust
#[ink(message)]
pub fn flip(&mut self) {
    self.value =!self.value;

    if self.env().block_number() % 2!= 0 {
      panic!("Oh no!")
    }

}
```
- 如果在奇数块号调用此事务，此合约的状态会是什么？

Notes:
- 答案：与事务发生前的状态相同。
  - 返回错误变体将回滚调用栈上的整个事务。

---
## 合约：事件

```rust
#[ink(event)]
#[derive(Debug)]
pub struct BetPlaced {
    #[ink(topic)]
    player: AccountId,
    #[ink(topic)]
    bet_type: BetType,
    amount: Balance,
}
```
- 事件是让外部世界知晓合约内部发生情况的一种方式。
- `#[ink(event)]`是定义事件的宏。
- 主题标记用于索引的字段。

Notes:
- 事件对于去中心化应用程序（dapps）尤其重要。
- 存储是昂贵的：例如，直接从链上读取聚合数据是不可能/不切实际的。
- 去中心化应用程序可以监听事件，进行规范化并存储在链下，然后回答复杂的查询。

---
## 合约：事件

```rust
#[ink(message)]
pub fn flip(&mut self) {

    Self::emit_event(
        self.env(),
        Event::Flipped(Flipped { }),
    );

    self.value =!self.value;

    if self.env().block_number() % 2 == 0 {
      panic!("Oh no!")
    }

}
```
- 回滚事务的事件会怎样？
- 此事件会在奇数块中发出吗？

Notes:
- 答案：会，不过只是因为我反转了条件。

---
## 合约：定义共享行为

<div style="font-size: 0.5em;">

```rust [1-14|17,22]
#[ink::trait_definition]
pub trait PSP22 {
    #[ink(message)]
    fn total_supply(&self) -> Balance;

    #[ink(message)]
    fn balance_of(&self, owner: AccountId) -> Balance;

    #[ink(message)]
    fn approve(&mut self, spender: AccountId, amount: Balance) -> Result<(), PSP22Error>;

    #[ink(message)]
    fn transfer(&mut self, to: AccountId, value: Balance, data: Vec<u8>) -> Result<(), PSP22Error>;
   ...

impl SimpleDex {
    use psp22_trait::{PSP22Error, PSP22};

    /// Returns balance of a PSP22 token for an account
    fn balance_of(&self, token: AccountId, account: AccountId) -> Balance {
        let psp22: ink::contract_ref!(PSP22) = token.into();
        psp22.balance_of(account)
    }
   ...
```

</div>
- 特质定义：`#[ink::trait_definition]`。
- 共享特质定义以进行跨合约调用。

Notes:
- （部分）PSP22（类似ERC20）合约定义。
- 所有遵循此定义的合约都需要实现它。
- 现在你可以与其他合约共享特质定义。
- 同时获取实例的类型化引用。

---
## 深入探究：存储

```rust
use ink::storage::Mapping;

#[ink(storage)]
#[derive(Default)]
pub struct Token {
    total_supply: Balance,
    balances: Mapping<AccountId, Balance>,
    allowances: Mapping<(AccountId, AccountId), Balance>,
}
```

Notes:
- 既然我们已经浅尝辄止，让我们更深入地剖析一下。
- 从存储开始。
- 这段代码实际上将什么放入了链上存储？

---
<img rounded style="width: 1000px;" src="./img/storage.svg" />

<font color="#8d3aed">SCALE</font>（<font color="#8d3aed">S</font>imple <font color="#8d3aed">C</font>oncatenated <font color="#8d3aed">A</font>ggregate <font color="#8d3aed">L</font>ittle <font color="#8d3aed">E</font>ndian）

Notes:
- 托盘合约存储的组织方式类似于键值数据库。
- 每个存储单元都有一个唯一的存储键，并指向一个经SCALE编码的值。
- SCALE编解码器不是自描述的（参考元数据）。

---
## SCALE：不同类型的示例

<div style="font-size: 0.72em;">

| 类型         | 解码                               | 编码                     | 备注                                                                         |
| ------------ | ---------------------------------- | ------------------------ | ------------------------------------------------------------------------------ |
| 布尔型      | true                               | 0x0                      | 使用单个字节的最低有效位编码                           |
|              | false                              | 0x1                      |                                                                                |
| 无符号整数 | 42                                 | 0x2a00                    |                                                                                |
| 枚举         | enum IntOrBool { Int(u8), Bool(bool)} | 0x002a 和 0x0101            | 第一个字节编码变体索引，其余字节编码数据          |
| 元组        | (3, false)                         | 0x0c00                    | 每个编码值的连接                                            |
| 向量       | [4, 8, 15, 16, 23, 42]              | 0x18040008000f00100017002a00 | 先编码向量长度，然后连接每个元素的编码 |
| 结构体       | {x:30u64, y:true}                     | [0x1e,0x0,0x0,0x0,0x1]     | 名称被忽略，是Vec<u8>结构，只看顺序                       |

</div>

Notes:
- 此表并非详尽无遗。
- 结构体示例：存储为向量，名称被忽略，只看顺序，前四个字节编码64字节整数，最后一个字节的最低有效位编码布尔值。

---
## 存储：紧凑布局

```rust [6]
use ink::storage::Mapping;

#[ink(storage)]
#[derive(Default)]
pub struct Token {
    total_supply: Balance,
    balances: Mapping<AccountId, Balance>,
    allowances: Mapping<(AccountId, AccountId), Balance>,
}
```
- 默认情况下，ink!将所有存储结构体字段存储在单个存储单元下（`紧凑`布局）。

Notes:
- 我们已经讨论了存储是一个键值数据库，现在来精确看看它是如何使用的。
- 可以完全存储在单个存储单元下的类型称为紧凑布局。
- 默认情况下，ink!将所有存储结构体字段存储在单个存储单元下。
- 因此，与合约存储交互的消息将始终需要读取和解码整个合约存储结构体。
- 这可能是你想要的，也可能不是。

---
## 存储：紧凑布局

```rust [1-4,7]
use ink::storage::traits::{
    StorageKey,
    ManualKey,
};

#[ink(storage)]
pub struct Flipper<KEY: StorageKey = ManualKey<0xcafebabe>> {
    value: bool,
}
```
- 合约根存储结构体的存储键默认为`0x00000000`。
- 不过你可以将其存储在任意的4字节键下。

---
## 存储：紧凑布局

<div style="font-size: 0.82em;">

```json
"storage": {
  "root": {
    "layout": {
      "struct": {
        "fields": [
          {
            "layout": {
              "leaf": {
                "key": "0xcafebabe",
                "ty": 0
              }
            },
            "name": "value"
          }
        ],
        "name": "Flipper"
      }
    },
    "root_key": "0xcafebabe"
  }
}
```

</div>

Notes:
- 紧凑布局的演示——值存储在根键下。

---
## 存储：非紧凑布局

```rust [1,7-8]
use ink::storage::Mapping;

#[ink(storage)]
#[derive(Default)]
pub struct Token {
    total_supply: Balance,
    balances: Mapping<AccountId, Balance>,
    allowances: Mapping<(AccountId, AccountId), Balance>,
}
```
- 映射由直接存储在合约存储单元中的键值对组成。
- 每个映射值都存储在其自己的存储键下。
- 映射值没有连续的存储布局：**无法迭代映射的内容！**

Notes:
- 当你需要存储大量相同类型的值时使用映射。
- 如果你的消息只访问映射的一个键，它不会加载整个映射，而只加载正在访问的值。
- ink!中还有其他集合类型：HashMap或BTreeMap（仅举几例）。
  - 这些数据结构都是紧凑的，与映射不同！

---
## 存储：使用`Mapping`

```rust
pub fn transfer(&mut self) {
    let caller = self.env().caller();

    let balance = self.balances.get(caller).unwrap_or(0);
    let endowment = self.env().transferred_value();

    balance += endowment;
}
```
- 这里有什么问题？

Notes:
- 使用映射：
- 答案：Mapping::get()方法将产生一个拥有的值（一个本地副本），而不是存储的直接引用。
  对此值的更改不会“自动”反映在合约的存储中。
  为避免这个常见的陷阱，修改后必须将值重新插入相同的键。
  上面示例中的转移函数说明了这一点：

---
## 存储：使用`Mapping`

```rust
pub fn transfer(&mut self) {
    let caller = self.env().caller();

    let balance = self.balances.get(caller).unwrap_or(0);
    let endowment = self.env().transferred_value();

    self.balances.insert(caller, &(balance + endowment));
}
```
- `Mapping::get()`返回一个本地副本，而不是存储的可变引用！

Notes:
- 使用映射：
- `Mapping::get()`方法将产生一个拥有的值（一个本地副本）。
- 对此值的更改根本不会反映在合约的存储中！
- 你需要将其重新插入相同的键。

---
## 存储：惰性存储

```rust [1,5]
use ink::storage::{traits::ManualKey, Lazy, Mapping};

#[ink(storage)]
pub struct Roulette {
    pub data: Lazy<Data, ManualKey<0x44415441>>,
    pub bets: Mapping<u32, Bet, ManualKey<0x42455453>>,
}
```
- 每个包装在`Lazy`中的类型都有一个单独的存储单元。
- `ManualKey`为其分配显式存储键。
- 为什么你会想要使用`ManualKey`而不是生成的键？

Notes:
- 如果我们在合约存储中存储一个大部分事务都不需要访问的大型集合，紧凑布局可能会出现问题。
- 用于解码的缓冲区有16kb的硬限制，尝试解码更多内容的合约将陷入陷阱/回滚。
- 惰性存储提供单元访问，就像映射一样。
- 惰性存储单元可以自动分配或手动选择。
- 对于可升级的合约，使用ManualKey而不是AutoKey可能特别理想，因为在合约的新版本中，使用AutoKey可能会为同一字段生成不同的存储键。
  - 这可能会在升级后破坏你的合约！

---
## 存储：惰性存储

<img rounded style="width: 1000px;" src="./img/storage-layout.svg" />

Notes:
- 只有指向惰性类型的指针（键）存储在根键下。
- 只有在读取`d`时，才会解引用指针并解码其值。
- 这里的“惰性”有点用词不当，因为存储已经初始化了。

---

## 合约可升级性：`set_code_hash`

```rust [3]
#[ink(message)]
pub fn set_code(&mut self, code_hash: [u8; 32]) -> Result<()> {
    ink::env::set_code_hash(&code_hash)?;
    Ok(())
}
```
- 在智能合约（SC）的生命周期中，通常需要进行升级或错误修复。
- 合约的代码和它的实例是分离的。
- 合约的地址可以更新，以指向存储在链上的不同代码。

Notes:
- 仅追加不等于不可变。
- 例如在Solidity中已知的代理模式仍然可行。
- 在Substrate框架内，合约的代码存储在链上，而其实例是指向该代码的指针。
- 激励用户自行清理。
- 极大的存储优化。
---

## 合约可升级性：访问控制

```rust [3]
#[ink(message)]
pub fn set_code(&mut self, code_hash: [u8; 32]) -> Result<()> {
    ensure_owner(self.env().caller())?;
    ink::env::set_code_hash(&code_hash)?;
    Ok(())
}
```
Notes:
- 你肯定不想让这个消息处于未受保护的状态。
- `ensure_owner`的解决方案可以从非常简单的地址检查。
- 到存储和维护在单独合约中的多角色访问控制账户数据库。
---

## 可升级性：存储

<div style="font-size: 0.72em;">
```rust [1-4,6-10|1-4,12-16|18-21|23-26]
#[ink(message)]
pub fn get_values(&self) -> (u32, bool) {
    (self.x, self.y)
}

#[ink(storage)]
pub struct MyContractOld {
    x: u32,
    y: bool,
}

#[ink(storage)]
pub struct MyContractNew {
    y: bool,
    x: u32,
}
```
</div>
- 确保更新后的代码与现有合约状态兼容。
- 这个getter函数能在新定义和旧存储下正常工作吗？

Notes:
- 各种可能导致向后不兼容的潜在更改：
  - 改变变量的顺序。
  - 在现有变量之前引入新变量。
  - 改变变量类型。
  - 删除变量。
- 答案：不行，SCALE编码不考虑名称，只考虑顺序。
---

## 可升级性：存储迁移

<div style="font-size: 0.82em;">
```rust [1-13|15-17]
// 新合约代码
#[ink(message)]
pub fn migrate(&mut self) -> Result<()> {
    if let Some(OldContractState { field_1, field_2 }) = get_contract_storage(&123)? {
        self.updated_old_state.set(&UpdatedOldState {
            field_1: field_2,
            field_2: field_1,
        });
        return Ok(());
    }

    return Err(Error::MigrationFailed);
}

// 旧合约代码
#[ink(message)]
pub fn set_code(&mut self, code_hash: [u8; 32], callback: Option<Selector>)
```
</div>
Notes:
- 如果新合约代码与存储的状态不匹配，可以执行存储迁移。
- 可以想象常规关系型数据库和模式迁移。
- 一个好的模式是在一个原子事务中执行更新和迁移：
  - 如果任何操作失败，整个事务将回滚。
  - 不会导致损坏状态。
  - 确保它可以在一个块内完成！
---

## 常见漏洞

```rust
impl MyContract {

  #[ink(message)]
  pub fn terminate(&mut self) -> Result<()> {
      let caller = self.env().caller();
      self.env().terminate_contract(caller)
  }

 ...
}
```
- 这个合约有什么问题？
- 你会如何修复它？

Notes:
- 我们从简单的开始。
- 答案：没有访问控制（AC）。
- 发生过Parity钱包1.5亿的“黑客攻击”。
---

## 常见漏洞：过去的教训

<img rounded style="width: 900px;" src="./img/anyone_can_kill_it.jpg" />

<div style="font-size: 0.72em;">
- [攻击详情](https://github.com/openethereum/parity-ethereum/issues/6995)：
- <https://etherscan.io/address/0x863df6bfa4469f3ead0be8f9f2aae51c91a907b4#code>

<!-- ```solidity -->
<!-- function kill(address _to) onlymanyowners(sha3(msg.data)) external { -->
<!--   suicide(_to); -->
<!-- } -->

<!-- function initMultiowned(address[] _owners, uint _required) only_uninitialized { -->
<!--   m_numOwners = _owners.length + 1; -->
<!--   m_owners[1] = uint(msg.sender); -->
<!--   m_ownerIndex[uint(msg.sender)] = 1; -->
<!--   for (uint i = 0; i < _owners.length; ++i) -->
<!--   { -->
<!--     m_owners[2 + i] = uint(_owners[i]); -->
<!--     m_ownerIndex[uint(_owners[i])] = 2 + i; -->
<!--   } -->
<!--   m_required = _required; -->
<!-- } -->
<!-- ``` -->
</div>
Notes:
- 可能看起来微不足道，但过去发生过非常相似的攻击，导致大量资金被困。
- 见：<https://etherscan.io/address/0x863df6bfa4469f3ead0be8f9f2aae51c91a907b4#code>。
- 黑客“意外地”调用了一个未受保护的`initMultiowned`并继续删除了合约代码。
---

## 常见漏洞

```rust [3,8,12-14]
    #[ink(storage)]
    pub struct SubstrateNameSystem {
        registry: Mapping<AccountId, Vec<u8>>,
    }

    impl SubstrateNameSystem {
        #[ink(message, payable)]
        pub fn register(&mut self, name: Vec<u8>) {
            let owner = self.env().caller();
            let fee = self.env().transferred_value();

            if!self.registry.contains(owner) && fee >= 100 {
                self.registry.insert(owner, &name);
            }
        }
```
- 一个注册费用为100皮可的链上域名注册系统。
- 为什么这是个坏主意？

Notes:
- 链上的一切都是公开的。
- 它很快就会被抢先操作。
- 你能提出一个更好的设计吗？
- 答案：提交/揭示或拍卖。

---
## 常见漏洞

<div style="font-size: 0.72em;">
```rust [3-7,12,18]
#[ink(message)]
pub fn swap(
    &mut self,
    token_in: AccountId,
    token_out: AccountId,
    amount_token_in: Balance,
) -> Result<(), DexError> {
    let this = self.env().account_id();
    let caller = self.env().caller();

    let amount_token_out = self.out_given_in(token_in, token_out, amount_token_in)?;

    // 将token_in从用户转移到合约
    self.transfer_from_tx(token_in, caller, this, amount_token_in)?;

    // 将token_out从合约转移到用户
    self.transfer_tx(token_out, caller, amount_token_out)?;
   ...
}
```
</div>
- 合约是一个遵循流行的自动化做市商（AMM）设计的去中心化交易所（DEX）。
- 该事务根据当前价格将池中的一个PSP22代币的指定数量交换为另一个PSP22代币。
- 这里可能会出什么问题？

Notes:
答案：
- 没有设置滑点保护。
- 机器人将在交易执行前抢先购买token_out，抢先执行受害者的事务。
- 这种购买将提高受害者交易者的资产价格并增加其滑点。
- 如果机器人在受害者的事务之后立即卖出（对受害者进行后向攻击），这就是三明治攻击。
---

## 常见漏洞

```rust [7,12-14]
#[ink(message)]
pub fn swap(
    &mut self,
    token_in: AccountId,
    token_out: AccountId,
    amount_token_in: Balance,
    min_amount_token_out: Balance,
) -> Result<(), DexError> {

   ...

    if amount_token_out < min_amount_token_out {
        return Err(DexError::TooMuchSlippage);
    }

...
}
```
Notes:
- 已设置滑点保护。

---
## 常见漏洞

- 整数溢出。
- 重入漏洞。
- 女巫攻击。
-...
- 监管攻击 😅
-...

Notes:
- 可能的攻击列表很长。
- 无法在一次讲座中全部涵盖。
- 基本操作：找一家可靠的公司进行审计。
- 公布你的源代码（隐匿性带来的安全不是真正的安全）。
---

## 暂停

可选挑战：[github.com/Polkadot-Blockchain-Academy/ink-adder](https://github.com/Polkadot-Blockchain-Academy/ink-adder)

Notes:
Piotr会接着讲述从合约中进行运行时调用和编写自动化测试。在此期间，有一个15分钟的挑战给你。
---

## 与执行环境的交互

```rust [5-6]
impl MyContract {
 ...
  #[ink(message)]
  pub fn terminate(&mut self) -> Result<()> {
      let caller = self.env().caller();
      self.env().terminate_contract(caller)
  }
 ...
}
```
---

## 区块链节点洋葱结构

---
## 区块链节点洋葱结构

<br />

<img style="margin-top: 50px;margin-bottom: 50px" width="800" src="./img/onions.png" />

---
## 区块链节点洋葱结构

<img style="margin-top: 10px" width="600" src="./img/blockchain-onion-1.svg" />
- 网络。
- 块的生成、传播、最终确定。
- 存储管理。
- 链下维护、查询、索引。
---

## 区块链节点洋葱结构

<img style="margin-top: 50px;margin-bottom: 50px" width="800" src="./img/blockchain-onion-2.svg" />
- 基于前一个状态和单个事务计算新状态。
---

## 区块链节点洋葱结构

<img style="margin-top: 100px;margin-bottom: 50px" width="800" src="./img/blockchain-onion-3.svg" />
- 执行合约调用。
---

## 标准API

- `caller()`。
- `account_id()`。
- `balance()`。
- `block_number()`。
- `emit_event(event: Event)`。
- `transfer(dest: AccountId, value: Balance)`。
- `hash_bytes(input: &[u8], output: &mut [u8])`。
- `debug_message(msg: &str)`。
- [还有更多](https://docs.rs/ink_env/4.2.1/ink_env/index.html#functions)。

---
## 标准API

```rust
impl MyContract {
 ...
  #[ink(message)]
  pub fn terminate(&mut self) -> Result<()> {
      let caller = self.env().caller();
      self.env().terminate_contract(caller)
  }
 ...
}
```
---

## 与状态转换函数的交互

<br />

<div class="flex-container fragment">
<div class="left">
<div style="text-align: center"> <center><h2><pre> 用户API </pre></h2></center> </div>

<ul>
<li>代币转移</li>
<li>质押</li>
<li>投票</li>
<li>合约调用</li>
<li>...</li>
</ul>
</div>

<div class="left fragment">
<div style="text-align: center"> <center><h2><pre> 合约API </pre></h2></center> </div>

<ul>
<li>高级加密</li>
<li>绕过标准限制</li>
<li>外包计算</li>
<li>...</li>
</ul>
</div>
</div>
---

## 与状态转换函数的交互

<br />

<div class="flex-container">
<div class="left">
<div style="text-align: center"> <center><h2><pre> 用户API </pre></h2></center> </div>
<div style="text-align: center"> <center><h2><pre> （通常针对人类） </pre></h2></center> </div>

<ul>
<li>代币转移</li>
<li>质押</li>
<li>投票</li>
<li>合约调用</li>
<li>...</li>

**_运行时调用_**

</ul>
</div>

<div class="left">
<div style="text-align: center"> <center><h2><pre> 合约API </pre></h2></center> </div>
<div style="text-align: center"> <center><h2><pre> （仅针对合约） </pre></h2></center> </div>

<ul>
<li>高级加密</li>
<li>绕过标准限制</li>
<li>外包计算</li>
<li>...</li>

<br />

**_链扩展_**

</div>
</div>

---

## Runtime

在 Polkadot 生态系统中，状态转换函数被称为 **runtime**。

---
## 调用 runtime

```rust [7-10]
#[ink(message)]
pub fn transfer_through_runtime(
    &mut self,
    receiver: AccountId,
    value: Balance,
) -> Result<(), RuntimeError> {
    let call_object = RuntimeCall::Balances(BalancesCall::Transfer {
        receiver,
        value,
    });

    self.env().call_runtime(&call_object)
}
```
---

## 调用 runtime

```rust [12]
#[ink(message)]
pub fn transfer_through_runtime(
    &mut self,
    receiver: AccountId,
    value: Balance,
) -> Result<(), RuntimeError> {
    let call_object = RuntimeCall::Balances(BalancesCall::Transfer {
        receiver,
        value,
    });

    self.env().call_runtime(&call_object)
}
```
---

## 链扩展
---

## 提供 `ChainExtension` 特质

```rust [1-7]
#[ink::chain_extension]
pub trait OutsourceHeavyCrypto {
  type ErrorCode = OutsourcingErr;

  #[ink(extension = 41)]
  fn outsource(input: Vec<u8>) -> [u8; 32];
}

pub enum OutsourcingErr {
  IncorrectData,
}

impl ink::env::chain_extension::FromStatusCode for OutsourcingErr {
  fn from_status_code(status_code: u32) -> Result<(), Self> {
    match status_code {
      0 => Ok(()),
      1 => Err(Self::IncorrectData),
      _ => panic!("encountered unknown status code"),
    }
  }
}
```
---

## 提供 `ChainExtension` 特质

```rust [9-21]
#[ink::chain_extension]
pub trait OutsourceHeavyCrypto {
  type ErrorCode = OutsourcingErr;

  #[ink(extension = 41)]
  fn outsource(input: Vec<u8>) -> [u8; 32];
}

pub enum OutsourcingErr {
  IncorrectData,
}

impl ink::env::chain_extension::FromStatusCode for OutsourcingErr {
  fn from_status_code(status_code: u32) -> Result<(), Self> {
    match status_code {
      0 => Ok(()),
      1 => Err(Self::IncorrectData),
      _ => panic!("encountered unknown status code"),
    }
  }
}
```
---

## 包含扩展到 `Environment` 特质实例中

```rust
pub enum EnvironmentWithOutsourcing {}
impl Environment for EnvironmentWithOutsourcing {
   ... // 使用 `DefaultEnvironment` 的默认值
    type ChainExtension = OutsourceHeavyCrypto;
}

#[ink::contract(env = crate::EnvironmentWithOutsourcing)]
mod my_contract {
 ...
}
```
---

## 包含扩展到 `Environment` 特质实例中

```rust
#[ink::contract(env = crate::EnvironmentWithOutsourcing)]
mod my_contract {
  fn process_data(&mut self, input: Vec<u8>) -> Result<(), OutsourcingErr> {
    self.env().extension().outsource(subject)
  }
}
```
---

## 处理扩展调用

```rust [5-11]
pub struct HeavyCryptoOutsourcingExtension;

impl ChainExtension<Runtime> for HeavyCryptoOutsourcingExtension {
  fn call<E: Ext>(&mut self, env: Env) -> Result<RetVal, DispatchError> {
    match env.func_id() {
      41 => internal_logic(),
      _ => {
        error!("Called an unregistered `func_id`: {func_id}");
        return Err(DispatchError::Other("Unimplemented func_id"))
      }
    }
    Ok(RetVal::Converging(0))
}
```
---

## 链扩展：更进一步

<img style="margin-top: 100px;margin-bottom: 50px" width="800" src="./img/chain-extension-reach.svg" />
---


## 测试合约
---

## 测试合约

<img style="margin-top: 100px;margin-bottom: 50px" width="800" src="./img/blockchain-onion-3.svg" />


---
## 测试合约

<img style="margin-top: 100px;margin-bottom: 50px" width="1000" src="./img/testing-contract-stack.svg" />

---

## 单元测试

```rust [1-3]
#[ink::test]
fn erc20_transfer_works() {
  let mut erc20 = Erc20::new(100);

  assert_eq!(erc20.balance_of(BOB), 0);
  // Alice transfers 10 tokens to Bob.
  assert_eq!(erc20.transfer(BOB, 10), Ok(()));
  // Bob owns 10 tokens.
  assert_eq!(erc20.balance_of(BOB), 10);

  let emitted_events = ink::env::test::recorded_events().collect::<Vec<_>>();
  assert_eq!(emitted_events.len(), 2);

  // Check first transfer event related to ERC-20 instantiation.
  assert_transfer_event(
    &emitted_events[0], None, Some(ALICE), 100,
  );
  // Check the second transfer event relating to the actual transfer.
  assert_transfer_event(
    &emitted_events[1], Some(ALICE), Some(BOB), 10,
  );
}
```
---

## 单元测试

```rust [5-9]
#[ink::test]
fn erc20_transfer_works() {
  let mut erc20 = Erc20::new(100);

  assert_eq!(erc20.balance_of(BOB), 0);
  // Alice transfers 10 tokens to Bob.
  assert_eq!(erc20.transfer(BOB, 10), Ok(()));
  // Bob owns 10 tokens.
  assert_eq!(erc20.balance_of(BOB), 10);

  let emitted_events = ink::env::test::recorded_events().collect::<Vec<_>>();
  assert_eq!(emitted_events.len(), 2);

  // Check first transfer event related to ERC-20 instantiation.
  assert_transfer_event(
    &emitted_events[0], None, Some(ALICE), 100,
  );
  // Check the second transfer event relating to the actual transfer.
  assert_transfer_event(
    &emitted_events[1], Some(ALICE), Some(BOB), 10,
  );
}
```

---

## 单元测试

```rust [11-22]
#[ink::test]
fn erc20_transfer_works() {
  let mut erc20 = Erc20::new(100);

  assert_eq!(erc20.balance_of(BOB), 0);
  // Alice transfers 10 tokens to Bob.
  assert_eq!(erc20.transfer(BOB, 10), Ok(()));
  // Bob owns 10 tokens.
  assert_eq!(erc20.balance_of(BOB), 10);

  let emitted_events = ink::env::test::recorded_events().collect::<Vec<_>>();
  assert_eq!(emitted_events.len(), 2);

  // Check first transfer event related to ERC-20 instantiation.
  assert_transfer_event(
    &emitted_events[0], None, Some(ALICE), 100,
  );
  // Check the second transfer event relating to the actual transfer.
  assert_transfer_event(
    &emitted_events[1], Some(ALICE), Some(BOB), 10,
  );
}
```
---

## E2E 测试

```rust [1-7]
#[ink_e2e::test]
async fn e2e_transfer(mut client: ink_e2e::Client<C, E>) -> E2EResult<()> {
  let constructor = Erc20Ref::new(total_supply);
  let erc20 = client
         .instantiate("erc20", &ink_e2e::alice(), constructor, 0, None)
         .await
         .expect("instantiate failed");

  let mut call = erc20.call::<Erc20>();
  let total_supply_msg = call.total_supply();
  let total_supply_res = client
         .call_dry_run(&ink_e2e::bob(), &total_supply_msg, 0, None)
         .await;
 ...
}
```
---

## E2E 测试

```rust [14]
#[ink_e2e::test]
async fn e2e_transfer(mut client: ink_e2e::Client<C, E>) -> E2EResult<()> {
    let constructor = Erc20Ref::new(total_supply);
    let erc20 = client
       .instantiate("erc20", &ink_e2e::alice(), constructor, 0, None)
       .await
       .expect("instantiate failed");

    let mut call = erc20.call::<Erc20>();
    let total_supply_msg = call.total_supply();
    let total_supply_res = client
       .call_dry_run(&ink_e2e::bob(), &total_supply_msg, 0, None)
       .await;
    //... 此处代码未完整，可能后续会对 total_supply_res 进行处理
}
```
---


## E2E 管道：处处是陷阱

```
1. 准备和编码交易数据（客户端侧）
1. 签署交易（客户端侧）
1. 将交易发送到节点（客户端侧）
1. 块和事件订阅（客户端侧）
1. 交易池处理（节点侧）
1. 块构建（节点侧）
1. 块传播（节点侧）
1. 导入队列处理（节点侧）
1. 块最终化（节点侧）
1. 块执行（节点侧）
1. 交易执行（运行时侧）
1. 事件发送（节点侧）
1. 事件捕获（客户端侧）
1. 事件处理（客户端侧）
1. 通过 RPC 调用获取状态（客户端侧）
1. 状态报告（节点侧）
1. 状态验证（客户端侧）
```
---

## E2E 管道：处处是陷阱

<img style="margin-top: 100px;margin-bottom: 50px" width="800" src="./img/trap.gif" />

---


## 测试核心

```
1. 准备和编码交易数据（给定）
1. 交易执行（当...时）
1. 状态验证（然后）
```
---

## 准 E2E 测试

```rust
#[test]
fn flipping() -> Result<(), Box<dyn Error>> {
    let init_value = Session::<MinimalRuntime>::new(transcoder())?
       .deploy_and(bytes(), "new", &["true".to_string()], vec![])?
       .call_and("flip", &[])?
       .call_and("flip", &[])?
       .call_and("flip", &[])?
       .call_and("get", &[])?
       .last_call_return()
       .expect("Call was successful");

    assert_eq!(init_value, ok(Value::Bool(false)));

    Ok(())
}
```
---
## 使用 `drink-cli` 本地操作合约
