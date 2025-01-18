---
title: Substrate Wasm meta-protocol
description: A deeper dive into how the Wasm is meta-protocol functions in substrate.
duration: 60 minutes
---

# Substrate Wasm元协议

---

# 第一部分

- 这是一场大型讲座，所以我把它分成了两个小部分，就是这样 🫵🏻

---

## 一切始于一个运行时...

<img rounded style="width: 1200px;" src="./img/dev-4-3-substrate-wasm.png" />

---v

### 一切始于一个运行时...

- 个人观点：

> Substrate技术栈将使“链上存储的Wasm”广为人知，<br />
> 就像以太坊使“链上存储的智能合约”广为人知一样。

Notes:

> 每个区块链都做同样的事情只是时间问题。

---v

## 一切始于一个运行时...

- 客户端/运行时的划分是Substrate中最重要的设计决策之一。
  - 👿 不好：固定的观点。 <!-- .element: class="fragment" -->
  - 😇 好：使无数其他事情不必固定。 <!-- .element: class="fragment" -->

Notes:

回想一下，这种划分的边界是**状态转换**。

---

## Substrate：简要回顾

<img style="width: 1200px;" src="./img/dev-4-3-full-comm.svg" />

---v

### Substrate：简要回顾

- **主机函数**：运行时与主机环境（即Substrate客户端）进行通信的方式。

---v

### Substrate：简要回顾

- **运行时API**：Wasm Substrate运行时提供的定义明确的函数。

Notes:

构建Wasm模块的活动类似于构建运行时API。

---v

### Substrate：简要回顾

- 数据库在客户端侧，每个块存储一个不透明的键值状态。

---v

### Substrate：简要回顾

- 客户端/运行时的通信语言是SCALE：

<diagram class="mermaid">
flowchart LR
  B[已知类型，例如 `u32`] --Encode--> V["Vec(u8)"]
  V --Decode-->B
</diagram>

---

## 通过示例学习

以及一些伪代码

Notes:

在每个示例中，我们推断需要哪些主机函数和/或运行时API。

---

## 示例 #1：状态

- 运行时想要给Kian的余额增加10个单位。

---v

### 示例 #1：状态

```rust [1-100|1-2|4,5|7,8|10,11|13,14|1-100]
// 运行时决定哪个键存储Kian的余额。
key: Vec<u8> = b"kian_balance".to_vec();

// 运行时从该键读取原始字节。
let current_kian_balance_raw: Vec<u8> = host_functions::get(key);

// 并且需要知道应该将其解码为哪种类型，即u128。
let mut current_kian_balance: u128 = current_kian_balance_raw.decode();

// 实际逻辑。
current_kian_balance += 10;

// 再次将其编码为不透明的字节数组。
let new_balance_encoded: Vec<u8> = current_kian_balance.encode();

// 再次写入编码后的字节。
host_functions::set(key, new_balance_encoded);
```

---v

### 示例 #1：状态

- 💡 运行时需要主机函数来读写状态。

```rust
fn get(key: Vec<u8>) -> Vec<u8>;
fn set(key: Vec<u8>, value: Vec<u8>);
```

Notes:

当然，这些函数的输入输出都是不透明的字节，因为客户端不知道状态布局。

---v

### 示例 #1：状态

- 我们能不能像这样与客户端通信呢？

```rust
fn set_balance(who: AccountId, amount: u128)
```

Notes:

这意味着客户端必须无限期地知道账户ID和余额所需的类型。此外，它还必须知道某人余额的最终键。

---v

### 示例 #1：状态

- 例外情况：

```rust
/// 客户端已知的键。
mod well_known_keys {
  const CODE: &[u8] = b":code";
}
```

Notes:

参见 <https://paritytech.github.io/substrate/master/sp_storage/well_known_keys/index.html>

---v

### 示例 #1：状态

<img style="width: 1000px;" src="./img/dev-4-1-state-opaque.svg" />

---

## 示例 #2：块导入

---v

### 示例 #2：块导入

- 客户端对状态的看法 -> 不透明。
- 客户端对交易的看法呢？ 🤔

<!-- .element: class="fragment" -->

Notes:

简短的答案是：任何属于STF定义的部分对客户端来说都必须是不透明的，并且是可升级的，但我们稍后会学到这一点。

---v

### 示例 #2：块导入

- 交易格式根据定义也是状态转换函数的一部分。
- 那么块中的头部和其他字段呢？

Notes:

也就是说，我们是否希望能够以无分叉的方式更新我们的交易格式呢？我们希望运行时也能够以无分叉的方式更改其交易格式。

后者的答案更为复杂。简短的答案是，像头部这样的字段必须在客户端和运行时之间已知并确定。如果你想更改头部格式，那就是硬分叉。

`digest` 的概念是一种在不进行破坏性更改的情况下将附加数据放入头部的方法，但这超出了本讲座的范围。

然而，与其他原语一样，Substrate允许你在构建区块链时随时更改头部类型。
这是通过 `sp-runtime` 中的一组特征来实现的。
值得注意的是，这个包中的 `trait Block` 和 `trait Header` 定义了什么是头部和块，只要你满足这些条件，就没问题。

此外，Substrate在 <https://paritytech.github.io/substrate/master/sp_runtime/generic/index.html> 中为所有这些类型提供了一组实现。

---v

<img style="width: 1200px;" src="./img/dev-4-3-block-opaque.svg" />

---v

### 示例 #2：块导入

<pba-cols>

<pba-col>

```rust
struct ClientBlock {
  header: Header,
  transactions: Vec<Vec<u8>>
}
```

</pba-col>

<pba-col>

```rust
struct RuntimeBlock {
  header: Header,
  transaction: Vec<KnownTransaction>
}
```

</pba-col>

</pba-cols>

Notes:

此幻灯片故意使用 `transaction` 关键字而不是 `extrinsic`。

---v

### 示例 #2：块导入

```rust [1-100|1-2|4-6|8-9|1-100]
// 从外部世界获取块。
let opaque_block: ClientBlock = networking::import_queue::next_block();

// 初始化一个Wasm运行时。
let code = database::get(well_known_keys::CODE);
let runtime = wasm::Executor::new(code);

// 调用这个运行时。
runtime.execute_block(opaque_block);
```

---v

### 示例 #2：块导入

- 💡 客户端需要一个运行时API来请求运行时执行该块。

```rust
fn execute_block(opaque_block: ClientBlock) -> Result<_, _> { .. }
```

Notes:

`execute_block` 是任何基于Substrate的运行时为了被称为“区块链运行时”而必须实现的最基本、最根本的运行时API。

---

### 示例 #2：块导入：缺少的东西

```rust
// 🤔
let code = database::get(well_known_keys::CODE);

// 🤔
runtime.execute_block(opaque_block);
```

<img style="width: 600px" src="./img/dev-4-1-state-database.svg" />

Notes:

- 我们从哪个块的状态中获取代码？？
- 这可能会在内部调用 `host_functions::{get/set}`。
  我们返回什么？

---v

### 示例 #2：块导入

```rust [1-100|1-2|4-6|8-10|12-15|17-100]
// 从外部世界获取块。
let block: ClientBlock = networking::import_queue::next_block();

// 获取父块的状态。
let parent = block.header.parent_hash;
let mut state = database::state_at(parent);

// 从父块的 `state` 初始化一个Wasm运行时！
let code = state::get(well_known_keys::CODE);
let runtime = wasm::Executor::new(code);

// 调用这个运行时，更新 `state`。
state.execute(|| {
  runtime.execute_block(block);
});

// 创建下一个块的状态
database::store_state(block.header.hash, state)
```

Notes:

- 问题：为什么 `state` 被定义为 `mut`？
- 在这些代码片段中，基本上 `state.execute` 内部的所有内容都是在Wasm中执行的。

---v

### 示例 #2：块导入

- 一个状态键仅在给定的块中有意义。
- 一个 `:code` 仅在给定的块中有意义。

<!-- .element: class="fragment" -->

- 💡 一个运行时（API）仅在给定的块中执行时才有意义。

<!-- .element: class="fragment" -->

Notes:

- 就像Alice的余额值只有在给定的块中读取时才有意义一样。

- 基于此：

  - 加载正确的运行时代码。
  - 提供正确的状态（和其他主机函数）。

- 同样，几乎所有与运行时交互的RPC操作都有一个 `Option<Hash>` 参数。
  这指定了“从哪个块加载运行时和状态”。

---v

### 示例 #2：块导入

- 我可以再添加一点小细节，使其更准确... 🤌

---v

### 示例 #2：块导入

```rust [14-20]
// 从外部世界获取块。
let block: ClientBlock = networking::import_queue::next_block();

// 获取父块的哈希。注意，`sp_runtime::traits::Header` 提供了这个功能。
let parent = block.header.parent_hash;
let mut state = database::state_at(parent);

// 从父块的 `state` 初始化一个Wasm运行时！
let code = state::get(well_known_keys::CODE);
let runtime = wasm::Executor::new(code);

// 调用这个运行时，更新 `state`。
state.execute(|| {
  // 在此处，我们可能会多次调用 `host_functions::set`。
  runtime.execute_block(block);

  let new_state_root = host_functions::state_root();
  let claimed_state_root = block.header.state_root;
  assert_eq!(new_state_root, claimed_state_root);
});

// 创建下一个块的状态
database::store_state(block.header.hash, state)
```

---v

### 示例 #2：块导入：回顾

<img style="width: 1200px;" src="./img/dev-4-3-import.svg" />

---

## 插曲：外在（Extrinsic）

- 前面的幻灯片以简化的方式使用了“交易”这个术语。
  让我们来纠正一下。

---v

### 插曲：外在（Extrinsic）

<diagram class="mermaid" center>
  %%{init: {'theme': 'dark', 'themeVariables': { 'darkMode': true }}}%%
  flowchart TD
    E(Extrinsic) ---> I(Inherent);
    E --> T(Transaction)
    T --> ST("Signed (aka. Transaction)")
    T --> UT(Unsigned)
</diagram>

---v

### 插曲：外在（Extrinsic）

- 外在（Extrinsic）是来自运行时外部的数据。
- &shy;<!-- .element: class="fragment" --> 固有（Inherent）是块作者直接放入块中的数据。
- &shy;<!-- .element: class="fragment" --> 是的，交易是**一种外在（Extrinsic）类型**，但不是所有的外在（Extrinsic）都是交易。
- &shy;<!-- .element: class="fragment" --> 那么，为什么叫“交易池”而不是“外在（Extrinsic）池”呢？

Notes:

外在（Extrinsic）只是可以包含在块中的数据块。
固有（Inherent）是块构建者在生产过程中自己创建的外在（Extrinsic）类型。
它们是未签名的，因为可以断言它们“本质上是正确的”，因为它们通过了所有验证者的验证。
名义上，起源可以说是多个验证者。
例如，设置时间戳的固有（Inherent）。
如果数据足够不正确（即时间错误），那么该块将不会被足够多的验证者接受，也不会成为规范块。
所以“无人”起源实际上是验证者的默许。
交易通常是对链有价值的意见陈述（因为要支付费用或有其他好处）。
交易池会过滤出哪些交易确实有价值，并且节点会共享这些交易。

---

## 示例 #3：区块创作

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-0-author-pool.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-1-author-pool.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-2-author-pool.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-3-author-pool.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-4-author-pool.svg" />

Notes:
重点是，最终池会构建一个“就绪交易”的列表。

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-0-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-1-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-2-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-3-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-4-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-5-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-6-author-builder.svg" />

---v
### 示例 #3：区块创作

<img style="width: 1400px;" src="./img/dev-4-3-7-author-builder.svg" />

---v

### 示例 #3：区块创作

```rust [1-100|1-2|4-5|7-9|11-12|14-20|21-100]
// 根据我们拥有的任何共识规则获取最佳区块。
let (best_number, best_hash) = consensus::best_block();

// 获取最新状态。
let mut state = database::state_at(best_hash);

// 初始化一个Wasm运行时。
let code = state::get(well_known_keys::CODE);
let runtime = wasm::Executor::new(code);

// 获取一个空的客户端区块。
let mut block: ClientBlock = Default::default();

// 反复应用交易。
while let Some(next_transaction) = transaction_pool_iter::next() {
    state.execute(|| {
        runtime.apply_extrinsic(next_transaction);
    });
    block.extrinsics.push(next_transaction);
}

// 设置新的状态根。
block.header.state_root = state.root();
```

Notes:
- `next_ext` 的类型是什么？ `Vec<u8>`
- 我们真的会一直循环直到交易池为空吗？可能不会！

---v
### 示例 #3：区块创作

- 基于Substrate的运行时允许在每个区块的开始和结束时执行一些操作。
- ✋🏻 回想一下，智能合约无法做到这一点。

---v
### 示例 #3：区块创作

```rust [14-15,25-26]
// 根据我们拥有的任何共识规则获取最佳区块。
let (best_number, best_hash) = consensus::best_block();

// 获取最新状态。
let mut state = database::state_at(best_hash);

// 初始化一个Wasm运行时。
let code = state::get(well_known_keys::CODE);
let runtime = wasm::Executor::new(code);

// 获取一个空的客户端区块。
let mut block: ClientBlock = Default::default();

// 告知这个运行时你希望开始一个新区块。
runtime.initialize_block();

// 反复应用交易。
while let Some(next_ext) = transaction_pool_iter::next() {
    state.execute(|| {
        runtime.apply_extrinsic(next_ext);
    });
    block.extrinsics.push(next_ext);
}

// 告知运行时我们已完成。
runtime.finalize_block();

// 设置新的状态根。
block.header.state_root = state.root();
```

---v
### 示例 #3：区块创作

- 那么固有信息（Inherents）呢？

---v
### 示例 #3：区块创作

```rust [14-26]
// 根据我们拥有的任何共识规则获取最佳区块。
let (best_number, best_hash) = consensus::best_block();

// 获取最新状态。
let mut state = database::state_at(best_hash);

// 初始化一个Wasm运行时。
let code = state::get(well_known_keys::CODE);
let runtime = wasm::Executor::new(code);

// 获取一个空的客户端区块。
let mut block: ClientBlock = Default::default();

// 告知这个运行时你希望开始一个新区块。
runtime.initialize_block();

let inherents: Vec<Vec<u8>> = block_builder::inherents();
block.extrinsics = inherents;

// 反复应用交易。
while let Some(next_ext) = transaction_pool_iter::next() {
    state.execute(|| {
        runtime.apply_extrinsic(next_ext);
    });
    block.extrinsics.push(next_ext);
}

// 告知运行时我们已完成。
runtime.finalize_block();

// 设置新的状态根。
block.header.state_root = state.root();
```

Notes:
虽然原则上固有信息可以出现在区块的任何位置，但由于FRAME将它们限制在首位，我们的示例也遵循这一点。
如果你想查看此代码的真实版本，请查看这个crate：
https://paritytech.github.io/substrate/master/sc_basic_authorship/index.html

---v
### 示例 #3：区块创作

```rust
fn initialize_block(..) {... }
// 注意不透明的外在类型。
fn apply_extrinsic(extrinsic: Vec<u8>) {... }
fn finalize_block(..) {... }
```

Notes:
实际上，客户端也会在运行时的帮助下构建其固有信息列表。

---
## 但是等一下 😱

- 如果 `code` 发生变化，以下所有内容也可能会改变：
  - <!--.element: class="fragment" --> Kian的余额的状态键是什么。
  - <!--.element: class="fragment" --> 有效的外在格式是什么。

- <!--.element: class="fragment" --> 一个应用程序（例如钱包）究竟该如何应对呢？

---v
### 但是等一下 😱

- 元数据 🎉

```rust
fn metadata() -> Vec<u8> {... }
```

Notes:
注意不透明的返回类型。
为了解决上述问题，元数据必须是一个运行时API。
<hr>
- 元数据包含有关所有存储项、所有外在信息等的所有基本信息。
  它还将帮助客户端/应用程序将它们解码为正确的类型。
- Substrate本身并未规定元数据应该是什么样的。
  它是 `Vec<u8>`。
- 基于FRAME的运行时公开了某种格式，该格式在生态系统中被广泛采用。

---v
### 但是等一下 😱

- 回想一下“运行时仅在某个特定区块才有意义”这一事实。

<div class="fragment">
- 区块 `N` 和 `N+1` 处的两个不同运行时会返回不同的元数据 ✅。
</div>

Notes:
这里的应用程序/客户端，我指的是任何人/任何事物。
Substrate客户端实际上并不使用元数据，因为它是动态类型的，但如果需要，它可以使用。

---
### 激进的可升级性

是以极端的不透明/动态类型为代价的。

Notes:
我希望两者兼得，但并不容易。

一些个人看法：激进的可升级性是最大的优势，也可以说是Substrate生态系统的主要开发问题之一。
编写客户端，如区块浏览器、扫描器，甚至是交易所集成，比编写一个格式固定且最多每18个月更新一次的区块链要困难得多。
话虽如此，这场战斗在我看来是显而易见的：我们必须获胜。
当以太坊首次引入智能合约时，大家可能都遇到了类似的问题。
这是同一类问题，只是处于不同的层面。

---

## 无感知客户端 🙈🙉
- 客户端被 “蒙在鼓里” 的根本原因是，这样它就无需关心运行时从一个区块到另一个区块的升级情况。
---v
## 无感知客户端 🙈🙉
$$STF = F(blockBody_{N}, state_{N}) > state_{N + 1}$$
_任何属于状态转换函数（STF）的部分对客户端来说都是不透明的，但它可以无分叉地改变！_
- <!-- .element: class="fragment" --> `F` 本身（即你的Wasm代码块）？它可以改变！
- <!-- .element: class="fragment" --> 外部交易格式（Extrinsic format）？它可以改变！
- <!-- .element: class="fragment" --> 状态格式？它可以改变！
Notes:
从本质上讲，STF的所有组件对客户端都必须是不透明的，以`Vec<u8>`的形式呈现。元数据会在需要时提供帮助。这就是Substrate能够实现无分叉升级的原因。
---v
## 无感知客户端 🙈🙉
- 新的宿主函数会怎样呢？
- <!-- .element: class="fragment" --> 新的区块头字段呢？
- <!-- .element: class="fragment" --> 新的哈希原语呢？
🥺 那就不再是无分叉的了。
<!-- .element: class="fragment" -->
Notes:
不过请记住，Substrate的可扩展性和通用性在这里同样适用。对于像区块头这类情况，存在一些变通方法，比如`digest`字段。以无分叉的方式更改这些内容很困难。但如果你只是想在创世时更改它们并启动一条新链，那么更改起来就非常容易。
---
## Substrate：全貌
<img style="width: 1200px;" src="../intro/img/dev-4-3-full.svg" />
Notes:
现在可以提出任何遗漏的问题。
---
## 活动：查找API和宿主函数
---v
### 查找API和宿主函数
- 查找`impl_runtime_apis!{...}`和`decl_runtime_apis!{...}`宏调用。
  - 同时尝试找到调用给定API的相应客户端代码。
- 查找`#[runtime_interface]`宏，并尝试找到宿主函数的使用之处！
- 你有15分钟时间！
---v
### 查找API和宿主函数
活动成果：
- `Core`是导入的关键。
- 验证者使用`TaggedTransactionQueue`和`BlockBuilder`。
- `header: Header`被四处传递。
Notes:
这里出现的一个问题是，为什么我们没有多个运行时，比如一个仅用于导入，一个仅用于创建区块等等？实际情况是，虽然它们名称不同，但底层代码95%是相同的。
---v
### 查找API和宿主函数
<pba-cols>
<pba-col>
#### 区块导入
```rust
runtime.execute_block(block);
```
</pba-col>
<pba-col>
#### 区块创建
```rust
runtime.initialize_block(raw_header);
loop {
  runtime.apply_extrinsic(ext);
}
let final_header = runtime.finalize_block();
```
</pba-col>
</pba-cols>
Notes:
坦率地说，这些仍然是简化后的内容。例如，这里并没有真正体现固有数据（Inherent）。
---v
### 查找API和宿主函数
- 最重要的宿主函数
```
sp_io::storage::get(..);
sp_io::storage::set(..);
sp_io::storage::root();
```
---

## 讲座回顾（第一部分）
- 复习“运行时API”和“宿主函数”的概念。
- 深入了解区块导入和创建过程。
- 无感知客户端。
- 元数据。
---
# 第二部分
---
## 定义运行时API
```rust [1-7|9-15|17-100|1-100]
// 在客户端/运行时共有的部分 => substrate-primitive。
decl_runtime_apis! {
    pub trait Core {
        fn version() -> RuntimeVersion;
        fn execute_block(block: Block) -> bool;
    }
}
// 在运行时代码中。
impl_runtime_apis! {
    impl sp_api::Core<Block> for Runtime {
        fn version() -> RuntimeVersion { /* 相关内容 */ }
        fn execute_block(block: Block) -> bool { /* 相关内容 */ }
    }
}
// 在客户端代码中。
let block_hash = "0xffff...";
let block = Block { ... };
let outcome: Vec<u8> = api.execute_block(block, block_hash).unwrap();
```
Notes:
- 所有运行时API默认对`<Block>`是泛型的。
- 所有运行时API都在一个**特定区块**上执行，这是一个隐式的“at”参数。
- 在使用API时，所有数据的传输都是通过SCALE编码的，但像`impl_runtime_apis`这样的抽象机制将这一过程对开发者隐藏起来了。
---
## 定义宿主函数
```rust
// 在substrate primitives中，通常在`sp_io`里。
#[runtime_interface]
pub trait Storage {
    fn get(&self, key: &[u8]) -> Option<Vec<u8>> {...}
    fn get(&self, key: &[u8], value: &[u8]) -> Option<Vec<u8>> {...}
    fn root() -> Vec<u8> {...}
}
#[runtime_interface]
pub trait Hashing {
    fn blake2_128(data: &[u8]) -> [u8; 16] {
        sp_core::hashing::blake2_128(data)
    }
}
// 在substrate运行时中
let hashed_value = sp_io::storage::get(b"key")
    .and_then(sp_io::hashing::blake2_128)
    .unwrap();
```
---
## 注意事项
---
## 注意事项：速度
- （新的）Wasmtime近乎原生速度 🏎️。
- （旧的）`wasmi`速度明显较慢 🐢。
Notes:
`wasmi`速度较慢是采用原生执行的原因之一。如今也有关于探索使用RISC-V指令集架构替代Wasm的讨论，相关内容可参考https://github.com/paritytech/substrate/issues/13640。
---v
### 注意事项：速度
<pba-cols>
<pba-col center>
- 宿主环境通常**速度更快**且**功能更强**，但调用宿主环境以及复制数据会产生一次性开销。
- &shy;<!-- .element: class="fragment" -->🤔 哈希和加密操作呢？
- &shy;<!-- .element: class="fragment" -->🤔 存储呢？
</pba-col>
<pba-col center>
<img style="width: 700px;" src="../storage/img/dev-4-3-io.svg" />
</pba-col>
</pba-cols>
Notes:
出于性能考虑，哈希和加密操作作为宿主函数来实现。存储操作也由宿主环境负责，因为运行时自身无法实现。
- 跨越运行时边界类似于CPU访问内存。另一方面，像`next_storage`这样的操作成本较高（通常在运行时对状态进行迭代很昂贵）。这种设计与内存位置有关，虽然存在替代方案，但当前设计较为简单（简单即良好设计）。
- 问题：我们使用宿主函数来运行计算密集型代码，但如果Wasm中添加了SIMD优化，哈希的宿主函数还有用吗？回答：拭目以待，但Wasm中哈希函数的SIMD优化可能会使其速度大幅提升。再次强调，使用宿主函数来加速需要谨慎权衡，Wasm中传输参数的成本可能高于实际哈希计算成本。
---
### 注意事项：原生运行时
<img style="width: 1200px;" src="./img/dev-4-3-native.svg" />
---v
### 注意事项：原生运行时
- 记住`Core` API中的`fn version()`函数！
```rust [1-100|4-5]
/// 运行时版本。
#[sp_version::runtime_version]
pub const VERSION: RuntimeVersion = RuntimeVersion {
    spec_name: create_runtime_str!("node"),
    spec_version: 268,
    impl_name: create_runtime_str!("substrate-node"),
    impl_version: 0,
    authoring_version: 10,
    apis: RUNTIME_API_VERSIONS,
    transaction_version: 2,
    state_version: 1,
};
```
---v
### 注意事项：原生运行时
- 只有在规范版本匹配时，原生运行时才是一个可行选项！
```rust
fn execute_native_else_wasm() {
    let native_version = runtime::native::api::version();
    let wasm_version = runtime::wasm::api::version();
    // 如果规范名称和版本匹配。
    if native_version == wasm_version {
        runtime::native::execute();
    } else {
        runtime::wasm::execute();
    }
}
```
---v
### 注意事项：原生运行时
- 原生运行时的时代即将结束 💀。
---v
### 注意事项：原生运行时
- 问题：如果升级运行时却忘记提升规范版本会怎样？
- &shy;<!-- .element: class="fragment" --> 问题：如果运行时升级只是调整实现细节，而不涉及规范呢？
Notes:
如果所有人都在执行Wasm，理论上不会有问题，但这会造成极大的混淆，切勿这样做。但如果部分节点执行原生运行时，就会出现共识错误。
---v
## 说到版本...
- 务必理解其中的差异！ 👍
  - 客户端版本
  - 运行时版本
---v
### 说到版本...
<img style="width: 1200px;" src="./img/dev-4-1-substrate-meta-version.svg" />
---v
### 说到版本...
<img style="width: 1200px;" src="./img/dev-4-3-telemetry.png" />
---v
### 说到版本...
<img style="width: 1200px;" src="./img/dev-4-3-PJS.png" />
---v
### 说到版本...
- 当Parity发布新的`parity - polkadot`客户端二进制文件时会发生什么？
- 当Polkadot社区想要更新运行时会怎样？
---
## 注意事项：运行时恐慌（Panic）
- 如果运行时调用，如`execute_block`或`apply_extrinsic`发生恐慌（Panic）会怎样 😱？
- 为了回答这个问题，我们先来看看验证者的经济模型。
---v
### 注意事项：运行时恐慌（Panic）
- 从更广泛的意义上讲，所有验证者都不想浪费时间。
- 在构建区块时，有时浪费时间不可避免（何时会这样呢？） <!-- .element: class="fragment" -->
- 但在导入区块时，节点不会容忍这种情况。 <!-- .element: class="fragment" -->
---v
### 注意事项：运行时恐慌（Panic）
- 运行时恐慌（Panic）是一种（免费的）浪费验证者时间的方式。
- 实际上，Wasm实例会终止，状态变更会回滚。
  - 任何费用支付也会回滚。
- 消耗资源但未支付费用的交易也有类似影响。 <!-- .element: class="fragment" -->
Notes:
虽然你可能认为状态回滚是件好事，但这其实是主要问题，也是不应让任意用户可访问的代码路径发生恐慌（Panic）的主要原因。因为为运行时API调用的无效执行所支付的任何费用也会被回滚。换句话说，运行时中的恐慌（Panic）通常会让所有人的时间被无限期免费浪费，也就是一种拒绝服务（DOS）攻击向量。
> `initialize_block`和`finalize_block`中的恐慌（Panic）会产生更严重的后果，这将在FRAME部分进一步讨论。
实践建议：创建一个会发生恐慌（Panic）的运行时，并进行拒绝服务攻击测试。
针对FRAME的实践建议：找出运行时正确触发恐慌（Panic）的所有情况（如时间戳错误、验证者被禁用）
---v
### 注意事项：运行时恐慌（Panic）
- 用户可调用的代码路径发生恐慌（Panic）会怎样？
- 🤬 困扰/拒绝服务攻击可怜的验证者 <!-- .element: class="fragment" -->
- 区块链的“自动”部分，如“initialize_block”发生恐慌（Panic）呢？ <!-- .element: class="fragment" -->
- 😱 永远卡住 <!-- .element: class="fragment" -->
---v
### 注意事项：运行时恐慌（Panic）
- 这就是为什么，尽管成本高昂，交易池检查通常至少会包含某种随机数（nonce）和支付检查，以确保你能够支付交易费用。
---v
### 注意事项：运行时恐慌（Panic）
<diagram class="mermaid">
graph LR
    TransactionPool --"😈"--> Authoring --"😇"--> Import
</diagram>
Notes:
考虑两种情况：
1. 一个发生恐慌（Panic）的交易
2. 一个无法支付费用，但交易池错误验证通过的交易。
当你创建一个区块时，你希望交易池已经为你预先验证了交易，但你无法确定。交易池无法预先执行交易。如果验证失败，你仍需继续。例如，交易池可能验证了一个无效的随机数（nonce），或者一笔费用支付。在这种情况下，区块创建者浪费了时间，但其他人不会。相反，一旦你创建了一个区块，导入者期望你只放入了有效的交易，即那些不会在**包含**过程中失败的交易。注意，一笔交易可能仍然会失败（如转账失败），但只要它能够支付费用，就会被**正常包含**。更多信息可参考Substrate中`ApplyExtrinsicResult`的文档。
---
### 注意事项：修改宿主函数
- 运行时升级现在需要一个新的`sp_io::new_stuff::foo()`函数。我们还能进行正常的运行时升级吗？
<div>
- 客户端需要先升级。不再有完全无分叉的升级 😢。
</div>
<!-- .element: class="fragment" -->
---v
### 注意事项：破坏宿主函数
- 这里有另一个来自Substrate的例子：
```rust
// 旧版本
fn root(&mut self) -> Vec<u8> { .. }
// 新版本
fn root(&mut self, version: StateVersion) -> Vec<u8> { .. }
```
<div>
- 在一段时间内，客户端需要同时支持这两个版本...🤔
</div>
<!-- .element: class="fragment" -->
<div>
- 旧的宿主函数什么时候可以删除呢？
</div>
<!-- .element: class="fragment" -->
---v
### 宿主函数...
## 必须永久保留 😈
<!-- .element: class="fragment" -->
- 可选活动：访问Substrate代码仓库，查找修改过宿主函数的拉取请求（PR），并查看PR讨论。有一些标签可以帮助你找到这类PR 😉。
<!-- .element: class="fragment" -->
---

## 实践：检查Wasm代码
---v
- `wasm2wat polkadot_runtime.wasm > dump | rg import`
```
(import "env" "memory" (memory (;0;) 22))
(import "env" "ext_crypto_ed25519_generate_version_1" (func $ext_crypto_ed25519_generate_version_1 (type 17)))
(import "env" "ext_crypto_ed25519_verify_version_1" (func $ext_crypto_ed25519_verify_version_1 (type 18)))
(import "env" "ext_crypto_finish_batch_verify_version_1" (func $ext_crypto_finish_batch_verify_version_1 (type 11)))
(import "env" "ext_crypto_secp256k1_ecdsa_recover_version_2" (func $ext_crypto_secp256k1_ecdsa_recover_version_2 (type 19)))
(import "env" "ext_crypto_secp256k1_ecdsa_recover_compressed_version_2" (func $ext_crypto_secp256k1_ecdsa_recover_compressed_version_2 (type 19)))
(import "env" "ext_crypto_sr25519_generate_version_1" (func $ext_crypto_sr25519_generate_version_1 (type 17)))
(import "env" "ext_crypto_sr25519_public_keys_version_1" (func $ext_crypto_sr25519_public_keys_version_1 (type 5)))
(import "env" "ext_crypto_sr25519_sign_version_1" (func $ext_crypto_sr25519_sign_version_1 (type 20)))
(import "env" "ext_crypto_sr25519_verify_version_2" (func $ext_crypto_sr25519_verify_version_2 (type 18)))
(import "env" "ext_crypto_start_batch_verify_version_1" (func $ext_crypto_start_batch_verify_version_1 (type 14)))
(import "env" "ext_misc_print_hex_version_1" (func $ext_misc_print_hex_version_1 (type 16)))
(import "env" "ext_misc_print_num_version_1" (func $ext_misc_print_num_version_1 (type 16)))
(import "env" "ext_misc_print_utf8_version_1" (func $ext_misc_print_utf8_version_1 (type 16)))
(import "env" "ext_misc_runtime_version_version_1" (func $ext_misc_runtime_version_version_1 (type 21)))
(import "env" "ext_hashing_blake2_128_version_1" (func $ext_hashing_blake2_128_version_1 (type 22)))
(import "env" "ext_hashing_blake2_256_version_1" (func $ext_hashing_blake2_256_version_1 (type 22)))
(import "env" "ext_hashing_keccak_256_version_1" (func $ext_hashing_keccak_256_version_1 (type 22)))
(import "env" "ext_hashing_twox_128_version_1" (func $ext_hashing_twox_128_version_1 (type 22)))
(import "env" "ext_hashing_twox_64_version_1" (func $ext_hashing_twox_64_version_1 (type 22)))
(import "env" "ext_storage_append_version_1" (func $ext_storage_append_version_1 (type 23)))
(import "env" "ext_storage_clear_version_1" (func $ext_storage_clear_version_1 (type 16)))
(import "env" "ext_storage_clear_prefix_version_2" (func $ext_storage_clear_prefix_version_2 (type 24)))
(import "env" "ext_storage_commit_transaction_version_1" (func $ext_storage_commit_transaction_version_1 (type 14)))
(import "env" "ext_storage_exists_version_1" (func $ext_storage_exists_version_1 (type 22)))
(import "env" "ext_storage_get_version_1" (func $ext_storage_get_version_1 (type 21)))
(import "env" "ext_storage_next_key_version_1" (func $ext_storage_next_key_version_1 (type 21)))
(import "env" "ext_storage_read_version_1" (func $ext_storage_read_version_1 (type 25)))
(import "env" "ext_storage_rollback_transaction_version_1" (func $ext_storage_rollback_transaction_version_1 (type 14)))
(import "env" "ext_storage_root_version_2" (func $ext_storage_root_version_2 (type 5)))
(import "env" "ext_storage_set_version_1" (func $ext_storage_set_version_1 (type 23)))
(import "env" "ext_storage_start_transaction_version_1" (func $ext_storage_start_transaction_version_1 (type 14)))
(import "env" "ext_trie_blake2_256_ordered_root_version_2" (func $ext_trie_blake2_256_ordered_root_version_2 (type 26)))
(import "env" "ext_offchain_is_validator_version_1" (func $ext_offchain_is_validator_version_1 (type 11)))
(import "env" "ext_offchain_local_storage_clear_version_1" (func $ext_offchain_local_storage_clear_version_1 (type 27)))
(import "env" "ext_offchain_local_storage_compare_and_set_version_1" (func $ext_offchain_local_storage_compare_and_set_version_1 (type 28)))
(import "env" "ext_offchain_local_storage_get_version_1" (func $ext_offchain_local_storage_get_version_1 (type 29)))
(import "env" "ext_offchain_local_storage_set_version_1" (func $ext_offchain_local_storage_set_version_1 (type 30)))
(import "env" "ext_offchain_network_state_version_1" (func $ext_offchain_network_state_version_1 (type 15)))
(import "env" "ext_offchain_random_seed_version_1" (func $ext_offchain_random_seed_version_1 (type 11)))
(import "env" "ext_offchain_submit_transaction_version_1" (func $ext_offchain_submit_transaction_version_1 (type 21)))
(import "env" "ext_offchain_timestamp_version_1" (func $ext_offchain_timestamp_version_1 (type 15)))
(import "env" "ext_allocator_free_version_1" (func $ext_allocator_free_version_1 (type 1)))
(import "env" "ext_allocator_malloc_version_1" (func $ext_allocator_malloc_version_1 (type 0)))
(import "env" "ext_offchain_index_set_version_1" (func $ext_offchain_index_set_version_1 (type 23)))
(import "env" "ext_default_child_storage_clear_version_1" (func $ext_default_child_storage_clear_version_1 (type 23)))
(import "env" "ext_default_child_storage_get_version_1" (func $ext_default_child_storage_get_version_1 (type 24)))
(import "env" "ext_default_child_storage_next_key_version_1" (func $ext_default_child_storage_next_key_version_1 (type 24)))
(import "env" "ext_default_child_storage_set_version_1" (func $ext_default_child_storage_set_version_1 (type 31)))
(import "env" "ext_logging_log_version_1" (func $ext_logging_log_version_1 (type 30)))
(import "env" "ext_logging_max_level_version_1" (func $ext_logging_max_level_version_1 (type 11)))
```
<!-- .element: class="fragment" -->
---v
- `wasm2wat polkadot_runtime.wasm > dump | rg export`
```
(export "__indirect_function_table" (table 0))
(export "Core_version" (func $Core_version))
(export "Core_execute_block" (func $Core_execute_block))
(export "Core_initialize_block" (func $Core_initialize_block))
(export "Metadata_metadata" (func $Metadata_metadata))
(export "BlockBuilder_apply_extrinsic" (func $BlockBuilder_apply_extrinsic))
(export "BlockBuilder_finalize_block" (func $BlockBuilder_finalize_block))
(export "BlockBuilder_inherent_extrinsics" (func $BlockBuilder_inherent_extrinsics))
(export "BlockBuilder_check_inherents" (func $BlockBuilder_check_inherents))
(export "NominationPoolsApi_pending_rewards" (func $NominationPoolsApi_pending_rewards))
(export "NominationPoolsApi_points_to_balance" (func $NominationPoolsApi_points_to_balance))
(export "NominationPoolsApi_balance_to_points" (func $NominationPoolsApi_balance_to_points))
(export "StakingApi_nominations_quota" (func $StakingApi_nominations_quota))
(export "TaggedTransactionQueue_validate_transaction" (func $TaggedTransactionQueue_validate_transaction))
(export "OffchainWorkerApi_offchain_worker" (func $OffchainWorkerApi_offchain_worker))
(export "ParachainHost_validators" (func $ParachainHost_validators))
(export "ParachainHost_validator_groups" (func $ParachainHost_validator_groups))
(export "ParachainHost_availability_cores" (func $ParachainHost_availability_cores))
(export "ParachainHost_persisted_validation_data" (func $ParachainHost_persisted_validation_data))
(export "ParachainHost_assumed_validation_data" (func $ParachainHost_assumed_validation_data))
(export "ParachainHost_check_validation_outputs" (func $ParachainHost_check_validation_outputs))
(export "ParachainHost_session_index_for_child" (func $ParachainHost_session_index_for_child))
(export "ParachainHost_validation_code" (func $ParachainHost_validation_code))
(export "ParachainHost_candidate_pending_availability" (func $ParachainHost_candidate_pending_availability))
(export "ParachainHost_candidate_events" (func $ParachainHost_candidate_events))
(export "ParachainHost_session_info" (func $ParachainHost_session_info))
(export "ParachainHost_dmq_contents" (func $ParachainHost_dmq_contents))
(export "ParachainHost_inbound_hrmp_channels_contents" (func $ParachainHost_inbound_hrmp_channels_contents))
(export "ParachainHost_validation_code_by_hash" (func $ParachainHost_validation_code_by_hash))
(export "ParachainHost_on_chain_votes" (func $ParachainHost_on_chain_votes))
(export "ParachainHost_submit_pvf_check_statement" (func $ParachainHost_submit_pvf_check_statement))
(export "ParachainHost_pvfs_require_precheck" (func $ParachainHost_pvfs_require_precheck))
(export "ParachainHost_validation_code_hash" (func $ParachainHost_validation_code_hash))
(export "BeefyApi_beefy_genesis" (func $BeefyApi_beefy_genesis))
(export "BeefyApi_validator_set" (func $BeefyApi_validator_set))
(export "BeefyApi_submit_report_equivocation_unsigned_extrinsic" (func $BeefyApi_submit_report_equivocation_unsigned_extrinsic))
(export "BeefyApi_generate_key_ownership_proof" (func $BeefyApi_generate_key_ownership_proof))
(export "MmrApi_mmr_root" (func $MmrApi_mmr_root))
(export "MmrApi_mmr_leaf_count" (func $MmrApi_mmr_leaf_count))
(export "MmrApi_generate_proof" (func $MmrApi_generate_proof))
(export "MmrApi_verify_proof" (func $MmrApi_verify_proof))
(export "MmrApi_verify_proof_stateless" (func $MmrApi_verify_proof_stateless))
(export "GrandpaApi_grandpa_authorities" (func $GrandpaApi_grandpa_authorities))
(export "GrandpaApi_current_set_id" (func $GrandpaApi_current_set_id))
(export "GrandpaApi_submit_report_equivocation_unsigned_extrinsic" (func $GrandpaApi_submit_report_equivocation_unsigned_extrinsic))
(export "GrandpaApi_generate_key_ownership_proof" (func $GrandpaApi_generate_key_ownership_proof))
(export "BabeApi_configuration" (func $BabeApi_configuration))
(export "BabeApi_current_epoch_start" (func $BabeApi_current_epoch_start))
(export "BabeApi_current_epoch" (func $BabeApi_current_epoch))
(export "BabeApi_next_epoch" (func $BabeApi_next_epoch))
(export "BabeApi_generate_key_ownership_proof" (func $BabeApi_generate_key_ownership_proof))
(export "BabeApi_submit_report_equivocation_unsigned_extrinsic" (func $BabeApi_submit_report_equivocation_unsigned_extrinsic))
(export "AuthorityDiscoveryApi_authorities" (func $AuthorityDiscoveryApi_authorities))
(export "SessionKeys_generate_session_keys" (func $SessionKeys_generate_session_keys))
(export "SessionKeys_decode_session_keys" (func $SessionKeys_decode_session_keys))
(export "AccountNonceApi_account_nonce" (func $AccountNonceApi_account_nonce))
(export "TransactionPaymentApi_query_info" (func $TransactionPaymentApi_query_info))
(export "TransactionPaymentApi_query_fee_details" (func $TransactionPaymentApi_query_fee_details))
(export "TransactionPaymentApi_query_weight_to_fee" (func $TransactionPaymentApi_query_weight_to_fee))
(export "TransactionPaymentApi_query_length_to_fee" (func $TransactionPaymentApi_query_length_to_fee))
(export "TransactionPaymentCallApi_query_call_info" (func $TransactionPaymentCallApi_query_call_info))
(export "TransactionPaymentCallApi_query_call_fee_details" (func $TransactionPaymentCallApi_query_call_fee_details))
(export "TransactionPaymentCallApi_query_weight_to_fee" (func $TransactionPaymentCallApi_query_weight_to_fee))
(export "TransactionPaymentCallApi_query_length_to_fee" (func $TransactionPaymentCallApi_query_length_to_fee))
(export "TryRuntime_on_runtime_upgrade" (func $TryRuntime_on_runtime_upgrade))
(export "TryRuntime_execute_block" (func $TryRuntime_execute_block))
(export "__data_end" (global 1))
(export "__heap_base" (global 2))
```
---v
### 实践：检查Wasm代码
- 当你学习到Polkadot模块并构建你的第一条平行链时，重复上述操作，我保证你会学到不少东西 。
---
### 活动：运行时中预期的恐慌
- 查看`frame - executive`库的代码。
  找出`panic!()`的使用实例，并尝试理解其用途。
- 你有15分钟时间！
<diagram class="mermaid">
graph LR
    TransactionPool --"😈"--> Authoring --"😇"--> Import
</diagram>
---
## 讲座回顾（第二部分）
- 回顾宿主函数和运行时API的语法。
- 注意事项：
  - 速度
  - 原生执行和版本控制
  - 运行时恐慌
  - 修改宿主函数
---
## 额外资源！😋
> 查看演讲者备注（点击“s” 😉）
<img width="300px" rounded src="../scale/img/thats_all_folks.png" />
Notes:
- 近期对区块构建API集的一些重大更改：https://github.com/paritytech/substrate/pull/14414
- 用于构建创世配置的新运行时API：https://github.com/paritytech/substrate/pull/14310
- 所有添加新宿主函数的Substrate拉取请求：https://github.com/paritytech/substrate/issues?q=label%3AE4 - newhostfunctions+is%3Aclosed
- 所有要求客户端先更新的Substrate拉取请求：https://github.com/paritytech/substrate/issues?q=is%3Aclosed+label%3A%22E10 - client - update - first+%F0%9F%91%80%22
- 新的元数据版本，包括运行时API的类型：https://github.com/paritytech/substrate/issues/12939
- API版本控制的最新进展：https://github.com/paritytech/substrate/issues/13138
- 在Substrate中，一种类型需要提供可以提供和执行宿主函数的环境。
> 我们将此称为“外部环境”，由`trait Externalities`表示。
```rust
SomeExternalities.execute_with(|| {
    let x = sp_io::storage::get(b"foo");
});
```
### 课后笔记
---
## 附录
未涵盖但相关的内容。
---v
### 考量：运行时API版本控制
- 原理相同，但通常更易于处理。
- 元数据是运行时的一部分，每个区块都有对应的元数据 。
- 用动态类型语言编写的部分通常不会有问题😎。
Notes:
此外，可以说运行时在这方面起主导作用。客户端必须全力为运行时服务，但运行时可能会根据特定应用场景选择是否支持某些API。
回想一下之前的幻灯片内容：
> - 许多其他运行时API可能会根据具体情况变为可选。
---v
### 考量：运行时API版本控制
- Substrate客户端中的Rust代码（静态类型）确实会在意变更是否为破坏性的。
  - 例如，输入/输出类型发生变化，Rust代码无法处理这种情况！
---v
### 考量：运行时API版本控制
```rust
sp_api::decl_runtime_apis! {
    // 最新版本
    fn foo() -> u32;
    // 旧版本
    #[changed_in(4)]
    fn foo() -> u64;
}
let new_return_type = if api.version < 4 {
    // 这个奇怪的函数名是由decl_runtime_apis!宏生成的！
    let old_return_type = api.foo_before_version_4();
    // 以某种方式进行转换。暂不考虑具体实现
    old_return_type.try_into().unwrap()
} else {
    api.foo()
}
```
---v
### 考量：运行时API版本控制
> 经验法则：每次更改宿主函数/运行时API的签名，即更改输入/输出类型时，都需要考虑这一点。
但具体该怎么做取决于实际场景。
### 活动：API版本控制
- 深入研究Substrate，查找所有`#[changed_in(_)]`宏的实例，以检测运行时API的版本。
- 然后查看其在客户端代码中的使用方式及是否被使用。
- 查找`sp - io`中所有`#[version]`宏，以找到所有有版本控制的宿主函数。