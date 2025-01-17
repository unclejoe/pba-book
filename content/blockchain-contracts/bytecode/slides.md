---
title: Platform Agnostic Bytecode
description: What are PABs and why they exist?
duration: 1 hour
---

# 平台无关字节码

---

## 编译器回顾

<img src="./img/compiling.png" />

🤯 有趣的拓展阅读： <!-- .element: class="fragment" data-fragment-index="1" -->
[关于信任的反思](https://www.cs.cmu.edu/~rdriley/487/papers/Thompson_1984_ReflectionsonTrustingTrust.pdf) <!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

只是快速回顾一下编译器的工作原理。
人们用像劳伦提到的那种人类可读的语言编写程序。
然后编译器将该程序的语义翻译成一种更低级、更易于机器读取的等效程序，称为字节码。

点击

每当我展示这个图表或谈论编译器时，我总是喜欢提到我最喜欢的一篇文章。
肯·汤普森（Ken Thompson）1984年的图灵奖演讲。

---

## 定义

平台无关字节码（PAB）是一种遵循两个主要原则的字节码：

- 图灵完备性，就像标准字节码所具备的那样

<!-- .element: class="fragment" data-fragment-index="1" -->

- 支持使它能在每台机器上运行的工具

<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:

理想情况下，像这样的字节码被设计为在遵循通用已知模式的虚拟机上执行。

---

<pba-cols>
<pba-col left>

<pba-flex center>

###### 高级语言

<img style="width: 30%" src="./img/rust_logo.png" />

<img style="width: 30%" src="./img/c_logo.png" />

<img style="width: 30%" src="./img/c++_logo.png" />

</pba-flex>
</pba-col>
<!-- .element: class="fragment" data-fragment-index="1" -->

<pba-col center>
<pba-flex center>

###### 平台无关字节码（PABs）

<img style="width: 30%" src="./img/jvm_logo.png" />
<img style="width: 30%" src="./img/wasm_logo.png" />
<img style="width: 20%" src="./img/eth_logo.png" />
<img style="width: 30%" src="./img/risc-v_logo.png" />

</pba-flex>
</pba-col>
<!-- .element: class="fragment" data-fragment-index="2" -->

<pba-col right>
<pba-flex center>

###### 架构的字节码

<img style="width: 30%" src="./img/intel_logo.png" />
<img style="width: 30%" src="./img/arm_logo.jpg" />
<img style="width: 30%" src="./img/risc-v_logo.png" />

</pba-flex>
</pba-col>
<!-- .element: class="fragment" data-fragment-index="3" -->

</pba-cols>

Notes:

从左到右，你可以看到最终将在某台机器上运行的程序的不同抽象层次。
一般来说，如果你想通过平台无关字节码（PAB），从高级语言开始需要两个编译步骤。

目前使用的平台无关字节码（PAB）的其他示例：

- 在Linux内核中 -> eBPF
- 在浏览器中 -> WebAssembly（Wasm）
- 在区块链中 -> WebAssembly（Wasm）
  - 全节点
  - 轻节点（Wasm嵌套在Wasm中）
- LLVM工具链 -> LLVM中间表示（LLVM IR）

---v

## 在平台无关字节码（PAB）中编译

<img src="./img/compiling_twice.png" />

Notes:

所以当我们使用平台无关字节码（PAB）时，我们需要编译两次。
当然，这就是使用平台无关字节码（PAB）的代价。
在本课程中，我们还将探讨其优势。

---

#### 平台无关字节码（PAB）允许的是：

<pba-flex center>

- 可移植性
  <!-- .element: class="fragment" data-fragment-index="1" -->
      - 避免硬件集中化
  <!-- .element: class="fragment" data-fragment-index="3" -->
- 确定性
  <!-- .element: class="fragment" data-fragment-index="2" -->
      - 使共识成为可能
  <!-- .element: class="fragment" data-fragment-index="4" -->

</pba-flex>

Notes:

平台无关字节码（PAB）的主要目标是使代码具有**可移植性**，你应该能够编译一次，然后随意分享，而无需关心它将在何种架构上执行。
当然，在去中心化网络中，我们希望不同架构的节点在输入相同的情况下能得到相同的结果，这就是所谓的**确定性**，如果平台无关字节码（PAB）没有确定性，那么达成共识是不可能的。

---v

##### 这就是为什么平台无关字节码（PAB）如此重要

---

## 理想特性

- 硬件独立性

<!-- .element: class="fragment" data-fragment-index="1" -->

- 效率

<!-- .element: class="fragment" data-fragment-index="2" -->

- 工具简单性

<!-- .element: class="fragment" data-fragment-index="3" -->

- 作为编译目标的支持

<!-- .element: class="fragment" data-fragment-index="4" -->

- 沙盒化

<!-- .element: class="fragment" data-fragment-index="5" -->

Notes:

- 硬件独立性：它不应与特定架构紧密相关，否则在不同机器上的执行可能会很复杂。
- 效率：平台无关字节码（PAB）的执行应该是高效的，平台无关字节码（PAB）面临的问题是，在执行时还需要考虑“翻译”成机器字节码或进行解释的过程。
- 作为编译目标的支持：平台无关字节码（PAB）应该能够被尽可能多的高级语言编译。
- 工具简单性：如果使平台无关字节码（PAB）可执行的工具极其复杂，那么就没人会使用它。

---v

### 沙盒化？

一个用于运行不受信任的代码而不影响主机的环境。

<!-- .element: class="fragment" data-fragment-index="1" -->

<img style="height: 300px" src="./img/sandbox.jpg" />

智能合约是在他人基础设施上可能被执行的**任意代码**，我们不希望智能合约能够破坏其运行所在的节点。

<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:

点击阅读定义

“沙盒”这个术语类似于孩子们在沙箱里玩耍。
父母把孩子放在沙箱里，告诉他们可以在沙箱里玩耍，只要他们待在里面就是安全的。
不要走进树林被蛇咬，或者走到马路上被车撞。
就待在沙箱里。

当然，这个类比并不完美。
沙箱里的孩子待在那里是因为父母要求他们这样做。
他们随时都可以离开。
对于实际的不受信任的代码，一个更好的类比是封闭式花园或“监狱”。

---v

### 沙盒化？

<img src="./img/jail.jpg" /> <!-- .element: class="fragment" data-fragment-index="1" -->

沙盒化环境必须由平台无关字节码（PAB）的执行器创建。

<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:

当然，安全性可以从不同的角度来看，一些例子包括：

- 编译花费时间过长 -> 编译炸弹
- 访问环境 -> “缓冲区溢出”技术

这些问题本身无法由平台无关字节码（PAB）解决，但它们可以提供良好的指导方针和代码设计，使执行器能够实现100%的安全实现。

---

## 平台无关字节码（PAB）的生命周期示例

<div class="r-stack">
<img style="width: 70%" src="./img/pab_path_1.svg" />
<img style="width: 70%" src="./img/pab_path_2.svg"/>
<!-- .element: class="fragment" data-fragment-index="1" -->
<img style="width: 70%" src="./img/pab_path_3.svg"/>
<!-- .element: class="fragment" data-fragment-index="2" -->
<img style="width: 70%" src="./img/pab_path_4.svg"/>
<!-- .element: class="fragment" data-fragment-index="3" -->
<img style="width: 70%" src="./img/pab_path_5.svg"/>
<!-- .element: class="fragment" data-fragment-index="4" -->
<img style="width: 70%" src="./img/pab_path_6.svg"/>
<!-- .element: class="fragment" data-fragment-index="5" -->
</div>

---

<!-- .slide: data-background-color="#4A2439" -->

<img rounded style="width: 300px" src="./img/question-mark.svg" />

---

<pba-cols>
<pba-col center>

# WebAssembly

<!-- .element: class="fragment" data-fragment-index="1" -->

</pba-col>
<pba-col center>

<img style="width: 70%" src="./img/wasm_logo.png" />

</pba-col>
</pba-cols>

---

## WebAssembly的关键点

<pba-flex center>

- 硬件无关
  <!-- .element: class="fragment" data-fragment-index="1" -->
  - 基于栈的虚拟机的二进制指令格式
  <!-- .element: class="fragment" data-fragment-index="1" -->
- 被许多语言支持作为编译目标
  <!-- .element: class="fragment" data-fragment-index="2" -->
  - Rust、C、C++ 以及许多其他语言
  <!-- .element: class="fragment" data-fragment-index="2" -->
- 快速（具有接近原生的性能）

<!-- .element: class="fragment" data-fragment-index="3" -->

- 安全（在沙盒化环境中执行）

<!-- .element: class="fragment" data-fragment-index="4" -->

- 开放（程序可以与它们的环境进行互操作）

<!-- .element: class="fragment" data-fragment-index="5" -->

</pba-flex>

Notes:

WebAssembly似乎符合我们之前定义的所有评分要点。

---

## 基于栈的虚拟机示例

<pba-cols>
<pba-col center>

在WebAssembly文本表示（.wat）中进行两数相加

<!-- .element: class="fragment fade-out" data-fragment-index="1" -->

```wasm [1-12|5|6|8]
(module
  (import "console" "log" (func $log (param i32)))
  (func $main
    ;; 把 `10` 和 `3` 压入栈中
    i32.const 10
    i32.const 3

    i32.add ;; 把两个数字相加
    call $log ;; 记录结果
  )
  (start $main)
)
```

<!-- .element: class="fragment" data-fragment-index="0" -->

</pba-col center>
<pba-col center>

<div class="r-stack">
<img src="./img/stack_1.svg" style="width: 100%">
<!-- .element: class="fragment" data-fragment-index="1" -->
<img src="./img/stack_2.svg" style="width: 100%">
<!-- .element: class="fragment" data-fragment-index="2" -->
<img src="./img/stack_3.svg" style="width: 100%">
<!-- .element: class="fragment" data-fragment-index="3" -->
<img src="./img/stack_4.svg" style="width: 100%">
<!-- .element: class="fragment" data-fragment-index="4" -->
<img src="./img/stack_5.svg" style="width: 100%">
<!-- .element: class="fragment" data-fragment-index="5" -->
<img src="./img/stack_6.svg" style="width: 100%">
<!-- .element: class="fragment" data-fragment-index="6" -->
</div>

</pba-col>
</pba-cols>

Notes:

WebAssembly也有文本表示形式，
WebAssembly文本格式（Wat）有一些特性可以提高可读性：

- 栈推送操作可以与其消耗指令分组。
- 标签可以应用于元素。
- 块可以用括号括起来，而不是使用显式的开始/结束指令。

指令将结果压入栈中，并使用栈中的值作为参数，编译过程通常会将这种基于栈的字节码转换为基于寄存器的字节码，其中寄存器用于将值传递给指令作为主要机制。
编译将尝试省略WebAssembly栈，只使用架构寄存器。

WebAssembly中还有另一种栈，称为影子栈，了解更多信息的资源：<https://hackmd.io/RNp7oBzKQmmaGvssJDHxrw>

---

## WebAssembly似乎是一个完美的平台无关字节码（PAB），但是

- 它是如何与环境进行通信的？

<!-- .element: class="fragment" data-fragment-index="1" -->

- 内存是如何管理的？

<!-- .element: class="fragment" data-fragment-index="2" -->

- 它是如何执行的？

<!-- .element: class="fragment" data-fragment-index="4" -->

Notes:

假设我们之前所说的都成立，WebAssembly似乎是完美的，但这些事情到底是如何运作的呢？

---
### 与环境通信
我们把接收WebAssembly二进制文件作为输入并执行它的程序称为**嵌入器**。
<!-- .element: class="fragment" data-fragment-index="0" -->
- WebAssembly二进制文件可能期望从嵌入器获取参数
  - 嵌入器 -> WebAssembly
  <!-- .element: class="fragment" data-fragment-index="1" -->
- 嵌入器可能会根据WebAssembly的返回值采取行动
  - WebAssembly -> 嵌入器
  <!-- .element: class="fragment" data-fragment-index="2" -->
---v
### 问题
**WebAssembly无法直接访问代码执行所在的计算环境**。
<br>
### 解决方案
<!-- .element: class="fragment" data-fragment-index="1" -->
<img src="./img/env_communication.svg" style="width: 70%">
<!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
- 与环境的所有交互只能通过一组由嵌入器提供并在WebAssembly中导入的函数来实现，这些函数被称为**宿主函数**。
- 嵌入器能够调用WebAssembly二进制文件中定义的函数，即**运行时应用程序编程接口（Runtime API）**，并通过共享内存传递参数。
---
## 内存
除了栈，WebAssembly还可以访问嵌入器提供的**线性内存**。
<!-- .element: class="fragment" data-fragment-index="0" -->
<br>
- 该区域也将用作数据共享的边界
- 为确保安全，嵌入器会采取极为复杂的操作
  <!-- .element: class="fragment" data-fragment-index="1" -->
Notes:
从WebAssembly角度看，线性内存是按字节寻址的。
可使用名为“store”和“load”的函数来操作线性内存。
Rust编译器通过在线性内存中模拟一个额外的栈，用于动态/堆内存以及向函数传递非原始值 ，这个模拟的栈（影子栈）相当于其他架构中的栈。
---v
### 示例
<div class="r-stack">
  <img src="./img/linear_memory_1.svg" style="width: 70%">
  <!-- .element: class="fragment fade-out" data-fragment-index="1" -->
  <img src="./img/linear_memory_2.svg" style="width: 70%">
  <!-- .element: class="fragment" data-fragment-index="1" -->
</div>
Notes:
例如，WebAssembly将线性内存视为字节数组，若要访问第二个字节，会使用索引1。
执行时，嵌入器会检测到该访问操作，并将对索引1处线性内存的访问转换为对base_linear_memory + 1处的标准内存访问。
会发生缓冲区溢出吗？WebAssembly使用32位，这使得偏移量不可能大于4GiB，这意味着嵌入器可在其虚拟内存中留出4GiB空闲空间，使WebAssembly二进制文件无法访问任何环境信息。
即使偏移量只能为正数，有些嵌入器也会将线性内存起始地址（BLM）前的2GiB定义为受保护区域，这样如果WebAssembly代码诱使嵌入器将偏移量视为有符号数，就会引发操作系统错误。
---
## WebAssembly的执行方式
<pba-flex left>
执行WebAssembly有多种方式：
- 提前编译
- 即时编译
- 单遍编译
- 解释执行
- ……
  <!-- .element: class="fragment data-fragment-index="1" -->
</pba-flex>
Notes:
- 提前编译（AOT）：在开始时编译所有代码，这有助于大幅提升最终代码的执行效率。
- 即时编译（JIT）：仅在需要时编译代码，例如函数只有在被调用时才编译，这种方式只能实现部分优化。
- 单遍编译（SPC）：这是一种按线性时间进行的特定编译技术，对代码仅遍历一次就完成编译。
- 解释执行：将WebAssembly二进制文件像其他解释型语言一样在虚拟机中执行。
---v
### Wasmtime
- 它是一个独立的WebAssembly运行环境。
- Wasmtime基于优化的Cranelift代码生成器构建，能在运行时（即时编译，JIT）或提前（提前编译，AOT）快速生成高质量的机器代码。
- 它在沙盒环境中执行已编译的WebAssembly二进制文件，同时确保高度安全性。
  <!--TODO: graphics-->
Notes:
- Wasmtime文档：<https://docs.wasmtime.dev/>
- 在Substrate中用作区块链逻辑的嵌入器。
Cranelift是一个快速、安全、相对简单且创新的编译器后端。它将前端生成的程序中间表示编译为可执行的机器代码。
---v
#### Wasmtime中WebAssembly的生命周期
<div class="r-stack">
  <img style="width: 70%" src="./img/wasmtime_exec_1.svg" />
  <img style="width: 70%" src="./img/wasmtime_exec_2.svg"/>
  <!-- .element: class="fragment data-fragment-index="1" -->
  <img style="width: 70%" src="./img/wasmtime_exec_3.svg"/>
  <!-- .element: class="fragment data-fragment-index="2" -->
  <img style="width: 70%" src="./img/wasmtime_exec_4.svg"/>
  <!-- .element: class="fragment data-fragment-index="3" -->
</div>
---v
### Wasmi
- 它是一个支持诸如WebAssembly自身等嵌入式环境的WebAssembly运行环境。
- 专注于简单、正确且具有确定性的WebAssembly执行。
- 执行技术是解释执行，但：
  - WebAssembly代码会被转译为WasmI中间表示（IR），这是另一种基于栈的字节码。
  - WasmI中间表示随后由虚拟机进行解释执行。
  <!--TODO: graphics-->
Notes:
- 从基于栈的中间表示转换为基于寄存器的中间表示的提案：<https://github.com/paritytech/wasmi/issues/361>
- 解释将WebAssembly转换为基于寄存器代码的效率的文章：<https://www.intel.com/content/www/us/en/developer/articles/technical/webassembly-interpreter-design-wasm-micro-runtime.html>
由于其特性，Wasmi主要用于在链上执行智能合约。
---v
#### Wasmi中WebAssembly的生命周期
<div class="r-stack">
  <img style="width: 70%" src="./img/wasmi_exec_1.svg" />
  <img style="width: 70%" src="./img/wasmi_exec_2.svg"/>
  <!-- .element: class="fragment data-fragment-index="1" -->
  <img style="width: 70%" src="./img/wasmi_exec_3.svg"/>
  <!-- .element: class="fragment data-fragment-index="2" -->
  <img style="width: 70%" src="./img/wasmi_exec_4.svg"/>
  <!-- .element: class="fragment data-fragment-index="3" -->
</div>
<!-- 这张幻灯片很不错，但对Substrate的介绍不够。
还有轻客户端，其运行时和客户端都用WebAssembly实现，因此我们有：
- 浏览器作为节点客户端的嵌入器
  - 节点客户端作为节点运行时的嵌入器
    - 节点运行时作为智能合约的嵌入器
  <img style="height: 30vh" src="./img/mind-blown-explosion.gif" />
我们有一个平台无关字节码（PAB）自身嵌套的双重递归。 -->
---
# 替代方案
---v
## 以太坊虚拟机（EVM）
- **以太坊虚拟机**执行基于栈的机器指令。
  - 有趣的是：这里的字节码是为在区块链中执行而创建的，因此指令不依赖硬件，但有与密码学及其他区块链相关的指令。
---v
## CosmWasm
- 始终使用WebAssembly，但使用不同的工具。
- 他们使用CosmWasm作为嵌入器，内部使用单遍编译器Wasmer。
---v
## Solana eBPF
- eBPF被用作平台无关字节码（PAB），但eBPF本身有很多限制。
- Solana对LLVM的eBPF后端进行了分叉，以使每个程序都能编译为eBPF格式。
- 嵌入器是rBFP，一个用于eBPF程序的虚拟机。
Notes:
<https://forum.polkadot.network/t/ebpf-contracts-hackathon/1084>
---v
## RISC-V ?!
- RISC-V是一种新的指令集架构。
- 主要目标是：
  - 成为适合直接在原生硬件上实现的真正指令集架构。
  - 避免“过度架构设计”。
<br>
由于其简单性和“硬件独立性”，目前正在进行实验，以测试它是否适合成为新的波卡智能合约语言。
Notes:
- 关于使用RISC-V作为智能合约语言的讨论：<https://forum.polkadot.network/t/exploring-alternatives-to-wasm-for-smart-contracts/2434>
- RISC-V指令集手册，非特权指令集架构：<https://github.com/riscv/riscv-isa-manual/releases/download/Ratified-IMAFDQC/riscv-spec-20191213.pdf>
---
## 活动：将Rust编译为WebAssembly
- 让我们创建一个简单的可编译为WebAssembly的Rust包！
- 克隆代码仓库。
---v
### 活动：将Rust编译为WebAssembly
- 目标三元组由三个用连字符分隔的字符串组成，末尾可能还有一个用连字符前缀的可选字符串。
- 第一个是**架构**，第二个是**“供应商”**，第三个是**操作系统类型**，可选的第四个是环境类型。
* `wasm32-unknown-emscripten`：旧版，提供类似`std`的环境。
* `wasm32-unknown-unknown` ✓ WebAssembly：可在任何地方编译，可在任何地方运行，无`std`。
* `wasm32-wasi` ✓ 带WASI的WebAssembly。
---v
### Rust -> WebAssembly细节
```rust
#[no_mangle] // 链接时不重命名符号
pub extern "C" fn add_one() { // 使用C风格的应用程序二进制接口（ABI）
  ...
}
```
如果是库：
```
[lib]
crate-type = ["cdylib"]
```
---v
### 活动：将Rust编译为WebAssembly
```
rustup target add wasm32-unknown-unknown
cargo build --target wasm32-unknown-unknown --release
wasmtime ./target/wasm32-unknown-unknown/release/wasm-crate.wasm --invoke <func_name> <arg1> <arg2> ...
```
---v
## 额外资源! 😋
> 查看演讲者备注（点击“s” 😉）
Notes:
- 更多关于平台无关字节码（PAB）的内容：
  - <https://github.com/gabriele-0201/IPABDN/blob/main/thesis/IPABDN.pdf>
- 更多关于Rust目标规范的内容：
  - <https://rust-lang.github.io/rfcs/0131-target-specification.html>
- Lin Clark关于WASI的精彩演讲（不过与我们的工作不是特别相关）：
  - <https://www.youtube.com/watch?v=fh9WXPu0hw8>
  - <https://www.youtube.com/watch?v=HktWin_LPf4>
- `wasm-unknown`与`wasm-wasi`的对比：
  - <https://users.rust-lang.org/t/wasm32-unknown-unknown-vs-wasm32-wasi/78325/5>
- `extern "C"`：
  - <https://doc.rust-lang.org/std/keyword.extern.html>
  - <https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html#using-extern-functions-to-call-external-code>
- 这本书的第11章很值得一读：<https://nostarch.com/rust-rustaceans> 