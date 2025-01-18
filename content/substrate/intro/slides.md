---
title: Introduction to Substrate
description: Substrate Overview for web3 builders
duration: 60 minutes
---

---
title: Substrate 入门介绍
description: 面向 web3 开发者的 Substrate 概述
duration: 60 分钟
---

# Substrate 入门介绍

---

## 在继续之前 🛑

在我讲解的同时，请克隆 `polkadot-sdk`，并运行 `cargo build && cargo build --release`。

> <https://github.com/paritytech/polkadot-sdk/>

---

## 关于这些讲座和讲师

- 从基础开始，深入底层，但注重实践。
- 有意避开 FRAME，但会为你提供在这方面取得成功所需的工具。
- 叙事重于事实。
- 随时欢迎打断和提问。

---

## 什么是 Substrate？

Substrate 是一个用于**构建区块链**的**Rust 框架**。

---v

### 为什么选择 Substrate？

<img rounded width="1000px" src="./img/dev-4-1-substrate-website.gif" />

Notes:

突出多链部分。

---v

### 为什么选择 Substrate？

<img rounded width="1000px" src="./img/dev-4.1-maximalism.png" />

Notes:

Polkadot 是这个生态系统中对链最大化主义的最大挑战，而 Substrate 在这种情况下发挥着重要作用。

---v

### 为什么选择 Substrate？

- ⛓️ 未来是多链的。

<!-- .element: class="fragment" -->

- 😭 构建一个区块链很难。升级它则更难。

<!-- .element: class="fragment" -->

- 💡 框架！

<!-- .element: class="fragment" -->

- 🧐 但该采取什么态度呢？

<!-- .element: class="fragment" -->

---

## Substrate 的核心理念 💭

**Substrate 之前**的思维方式：

- 😭 _构建一个区块链很难。升级它则更难_。
- 💪🏻 我们将投入最大的资源来确保我们做对。

<!-- .element: class="fragment" -->

---v

### Substrate 的核心理念 💭

但这种方法奏效了吗？

- 😭 比特币区块大小的争论

<!-- .element: class="fragment" -->

- 2️⃣ 二层网络及更多

<!-- .element: class="fragment" -->

- 📈 以太坊的 Gas 价格

<!-- .element: class="fragment" -->

Notes:

比特币区块大小一直以来都是一个持续的争论话题。

我并不是反对二层网络本身，但确实它们大多存在是因为底层协议太难或太慢而无法自我升级。
以太坊的 Gas 价格也表明底层协议无法满足当今的需求。

<https://en.wikipedia.org/wiki/Bitcoin_scalability_problem>
<https://ycharts.com/indicators/ethereum_average_gas_price>

---v

### Substrate 的核心理念 💭

**Substrate** 的思维方式：

- ☯️ 社会和技术在不断发展

<!-- .element: class="fragment" -->

- 🦸 人类是会犯错的

<!-- .element: class="fragment" -->

- 🧠 今天的最佳决策可能会成为明天的错误

<!-- .element: class="fragment" -->

---v

### Substrate 的核心理念 💭

由此产生的结果：

- 🦀 Rust
- 🤩 通用、模块化和可扩展的设计
- 🏦 治理 + 可升级性

Notes:

思考一下这些是如何与“你今天所做的任何决定很快就会成为错误”这一观点联系起来的。

---

## 🦀 Rust

- 第一道防线：尽可能防止人为错误。
- 安全的语言，没有内存安全问题。

Notes:

所以至少我们不想处理人为错误，只需要应对我们无法预测未来这一事实。

内存安全是大多数主要系统级编程语言中的一个基本问题。

在 Rust 中，有些这样的错误是不可能出现的。

---v

### 🦀 Rust

```c
int main() {
    int* x = malloc(sizeof(int));
    *x = 10;
    int* y = x;
    free(x);
    printf("%d\n", *y);  // 访问已释放的内存
}
```

<br />

```rust
fn main() {
    let x = Box::new(10);
    let y = x;
    println!("{}", *y); // ❌
}
```

<!-- .element: class="fragment" -->

Notes:

另一个例子：

```c
int* foo() {
    int x = 10;
    return &x;
}

int main() {
    int* y = foo();
    printf("%d\n", *y); // 访问超出其作用域的内存
}
```

<br />

```rust
fn foo() -> &'static i32 {
    let x = 10;
    &x
}

fn main() {
    let y = foo();
    println!("{}", y); // ❌
}
```

---v

### 🦀 Rust

> 微软和谷歌都表示，软件内存安全问题是导致其约 70% 漏洞的原因。

Notes:

<https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3215760/nsa-releases-guidance-on-how-to-protect-against-software-memory-safety-issues/#:~:text=Microsoft%20and%20Google%20have%20each,70%20percent%20of%20their%20vulnerabilities.>

---v

### 🦀 Rust

- 🏎️ 大多数 Rust 抽象是**零成本**的。
- ⏰ Rust 几乎没有“运行时”。

<br />

<img rounded width="900px" src="./img/dev-4-1-speed.png" />

Notes:

不过这并不完全准确，Rust 有一个小的运行时，比如异常处理程序等。Rust for Rustacean's 中关于 `no_std` 的章节对这一点有很好的阐述。
另外，现在是讨论我们如何以不同方式使用“运行时”的好时机。

---

## 🤩 通用、模块化和可扩展的设计

- 第二道防线。
- 我们的**执行**（可能得益于 Rust）是完美的，但我们无法预测未来。

Notes:

这就是模块化、通用设计发挥作用的地方。你可以根据未来的需求轻松更改组件。

---v

### 🤩 通用、模块化和可扩展的设计

- 多种共识引擎（BABE/Grandpa/AURA/PoW/Sassafras）

<!-- .element: class="fragment" -->

- 多种网络协议（QUIC, TCP）

<!-- .element: class="fragment" -->

- 多种数据库实现（ParityDB, RocksDB）

<!-- .element: class="fragment" -->

- 高度可配置的、基于图的交易池。

<!-- .element: class="fragment" -->

- 易于更改的基本类型：AccountId、Signature、BlockNumber、Header、Hash 等等。

<!-- .element: class="fragment" -->

Notes:

FRAME 在这方面更进一步，但这是后续的内容。

这些都是 Substrate 层面上通用、模块化和可扩展的例子。FRAME 在此基础上更进一步，但后续会详细介绍。

---v

### 🤩 通用、模块化和可扩展的设计

- **AlephZero**：自定义终局性，基于有向无环图（DAG），1 秒出块时间。
- **Moonbeam**：与以太坊兼容，使用 Substrate 构建。
- **HydraDX**：自定义交易池逻辑以匹配去中心化交易所（DEX）订单。
- **Kulupu**：工作量证明，自定义哈希算法。

Notes:

Substrate 从底层开始就被设计成易于为某些功能提供多种实现。大量使用特性（traits）和泛型是实现这一点的关键。如前所述，Substrate 有很多 API 和可选实现。你受限于 API，但不受特定实现的限制。

---

## 🏦 治理 + 可升级性

- 第三道、也是最后一道不可协商的防线，以经受住时间的考验。

---v

### 🏦 治理 + 可升级性

- 我们有正确的代码，并且组件易于交换、替换和升级。
- 如果我们不能就替换/升级的内容达成一致，那又有什么用呢？
- 治理！

<!-- .element: class="fragment" -->

- 如果升级无法实施，治理又有什么用呢？

<!-- .element: class="fragment" -->

- （无需信任的）可升级性！

<!-- .element: class="fragment" -->

Notes:

即使我们可以进行治理，但如果仍然需要“信任”来实施升级，那也没好到哪里去。本质上，如果一个升级机制不是自动执行的，那它还不如放在链下，仅仅作为一种信号机制。

---v

### 🏦 治理 + 可升级性

- ✅ 治理：容易
- 😢 可升级性：没那么容易

---v

### 🏦 治理 + 可升级性

- 一个典型的区块链是如何进行自我升级的？

Notes:

1. 讨论，链下信号传递
2. 可能的链上投票
3. 硬分叉（或类似的分叉）

---v

### 🏦 治理 + 可升级性

<img src="./img/dev-4-1-substrate-monol.svg" />

---v

### 🏦 治理 + 可升级性

<img src="./img/dev-4-1-substrate-monol-2.svg" />

Notes:

问题在于这个系统是一个庞大的单体协议。更新其中任何一部分都需要更新整个系统。

---v

### 🏦 治理 + 可升级性

_使一个协议真正可升级的方法是设计一个不可升级的元协议。_

---v

### 🏦 治理 + 可升级性

<img src="./img/dev-4-1-substrate-meta.svg" />

Notes:

在这个图中，元协议（即 Substrate 客户端）无法在无分叉的情况下进行升级。它只能通过分叉来升级。而 Wasm 协议则可以在无分叉的情况下进行升级。

---v

### 🏦 治理 + 可升级性

<img src="./img/dev-4-1-substrate-meta-substrate.svg" />

---v

### 🏦 治理 + 可升级性

- 固定的元协议？
- &shy;<!-- .element: class="fragment" --> “_状态机作为存储的 Wasm_” 在 Substrate 客户端中。
- <!-- .element: class="fragment" --> 本质上可升级的协议？
- <!-- .element: class="fragment" --> Substrate Wasm 运行时

---

### Substrate 架构

<img src="./img/dev-4-1-substrate.svg" />

---v

#### Substrate（简化）架构

<pba-cols>

<pba-col center>
<h3 style="color: var(--substrate-runtime); top: 0"> 运行时（协议） </h3>

- 应用逻辑
- Wasm（可能是 **FRAME**）
- 作为链状态的一部分存储
- 也称为：STF

</pba-col>

<pba-col center>
<h3 style="color: var(--substrate-host); top: 0"> 客户端（元协议） </h3>

- 原生二进制文件
- 执行 Wasm 运行时
- 其他所有内容：数据库、网络、内存池、共识等
- 也称为：主机

</pba-col>

</pba-cols>

---

## 运行时

<div>

- 运行时 -> **应用逻辑**。

</div>
<!-- .element: class="fragment" -->
<div>

- 一个**花哨**的术语：运行时 -> **状态转换函数**。

</div>
<!-- .element: class="fragment" -->
<div>

- 一个**技术**术语：运行时 -> **如何执行区块**。

</div>
<!-- .element: class="fragment" -->

Notes:

- 我个人会将运行时称为 STF，以避免与一般编程中的“运行时”概念混淆，但现在可能有点晚了。
- 在 Wasm 运行时的定义中，让我们回顾一下状态转换是什么。
- 区块执行的定义将在 Wasm-meta 讲座中更详细地介绍。

---

## 状态转换函数

**状态**

<img style="width: 600px" src="./img/dev-4-1-state-def.svg" />

Notes:

我们想要达成共识的所有数据集合。
键值对。
与每个区块相关联。

---v

### 状态转换函数

**转换函数**

<img width="400px" src="./img/dev-4-1-state-transition-def.svg" />

---v

### 状态转换函数

$$STF = F(block_{N}, state_{N}, code_{N}): state_{N+1}$$

---v

### 状态转换函数

<img style="width: 1200px;" src="./img/dev-4-1-state.svg" />

Notes:

此图中的 Wasm 运行时实际上是从状态中获取的（见 `0x123`）

---v

### 状态转换函数

<img style="width: 1200px;" src="./img/dev-4-1-state-code.svg" />

---v

### 状态转换函数
![状态转换函数相关图片](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149452.png)
Notes:
这就是元协议如何使系统具备可升级性。
我们能在N + 1阶段更新代码吗？默认情况下不行，因为在查看区块之前我们就加载了WebAssembly（Wasm）。
重要的是：状态并不在区块内，每个状态都关联着一个区块。
保留状态完全是可选的。你总是可以通过重新执行区块`[0, .., N - 1]`来重新创建区块`N`的状态。
当然，更改Wasm代码不是任何人都能做的，这取决于治理机制。
---
## Substrate完整架构
![Substrate完整架构](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149453.png)
---
## Wasm运行时的积极影响 🔥
---v
### 🤖 确定性执行
- 可移植且具有确定性。
Notes:
Wasm的指令集是确定性的，所以一切都能正常运行 。
---v
### 🧱 沙盒机制
- 在执行不可信代码时很有用。
  1. 智能合约
  2. 平行链运行时
Notes:
我们如何确保它们既不会进入无限循环，也不会尝试访问文件系统呢？
---v
### 🌈 更易于（轻量级）客户端开发
Notes:
对于客户端而言，你的客户端只需要实现一组宿主环境，而无需重新实现业务逻辑。
简单对比一下创建以太坊替代客户端的过程，在以太坊中你需要重新实现以太坊虚拟机（EVM）。
这同样适用于轻量级客户端，因为它们无需处理状态转换函数。
---v
### 😎 无分叉升级
![无分叉升级1](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149454.png)
---v
### 😎 无分叉升级
![无分叉升级2](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149455.png)
---v
### 😎 无分叉升级
此次更新具有以下特点：
1. 无分叉
2. 自动执行
Notes:
花点时间理解一下这次升级是无分叉的。运行时得到了升级，但客户端没有。事实上，客户端根本不需要知道这一点。
这就是元协议所实现的功能。
---
## Wasm运行时的负面影响
- 😩 资源受限（内存、速度、宿主访问）。
- 🌈 客户端的多样性并不等同于状态转换的多样性
Notes:
- 4GB内存，并且我们还会进一步限制。
- Wasm自身没有分配器和异常处理程序。
- 根据执行器或执行方法的不同，可能比原生代码慢。
- 对宿主环境的访问有限，所有操作都需要通过系统调用完成。
由于所有客户端的运行时都是相同的，所以状态转换的多样性较少。
如果其中存在漏洞，所有人都会受到影响。
---
## 共识与运行时的关系 🤔
- 是的，共识并非区块链运行时的核心部分。为什么呢？
- 它不属于状态转换函数（STF）！
<!-- .element: class="fragment" -->
- 共识协议对于运行时而言，就如同HTTP对于Facebook一样。
<!-- .element: class="fragment" -->
Notes:
乔希（Joshy）的评论：
我认为这一点很重要。运行时是你想要运行的应用程序。
共识在这个应用程序之外，帮助我们就这个运行时的官方状态达成一致。上一轮我用了这个类比。
想象一个电视剧的编剧室。编剧们围坐在一起，为未来的剧集构思潜在的情节要点。他们的任何想法都可能行得通。但最终他们需要就下一集实际播出的内容达成一致。
---
## 数据库与状态的关系 🤔
- 状态是与一个区块相关联的所有键值数据的集合。
- 数据库是用于将这些数据存储在磁盘上的组件，可能是键值存储，也可能不是。
![数据库与状态关系](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149456.png)
Notes:
状态有时也被称为“存储”。
---
## 数据库与运行时的关系 🤔
- 是的，数据存储在运行时之外。为什么呢？
- Wasm运行时没有存储数据的方式。
<!-- .element: class="fragment" -->
- 然而，数据的解释取决于运行时。
<!-- .element: class="fragment" -->
---v
### 数据库与运行时的关系 🤔
![数据库与运行时关系](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149457.png)
---v
## 客户端：数据库 🤔
- 从客户端的角度来看，数据库是一个无类型的键值存储。
- 运行时知道哪些键值对代表什么。
---
## 轻量级客户端的状态
- 轻量级客户端只跟踪区块头，因此知道状态根，并且可以请求状态证明以执行更多操作。
---v
### 轻量级客户端的状态
<pba-cols>
  <pba-col>
    - 不仅可行，它们还可以作为Wasm在浏览器中运行！
    - “Substrate Connect” / SMOLDOT
  </pba-col>
  <pba-col>
    <img style="width: 600px;" src="./img/dev-4-1-smoldot.svg" />
  </pba-col>
</pba-cols>
Notes:
什么是轻量级客户端？它只跟踪区块头，因此知道状态根以及其他一些信息，如果它想要执行更多操作，其他节点会向它发送状态证明。
SMOLDOT并不完全是一个Substrate客户端。它主要是为与Polkadot一起工作而设计的。但是通过最小的调整，你可以让它适用于更多基于Substrate的链。
这与共识以及客户端和运行时的其他一些部分并非100%独立这一事实有关。例如，GRANDPA在运行时一侧有一个模块，但主要在客户端中实现。现在，配置了GRANDPA的客户端只能与同样配置了GRANDPA的运行时一起工作。
---
## 通信路径
![通信路径1](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149458.png)
---v
### 通信路径
![通信路径2](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149459.png)
---v
### 示例：SCALE与JSON对比
- SCALE是一种高效的、非描述性的二进制编码格式，在Substrate生态系统中被广泛使用。
---v
### 示例：SCALE与JSON对比
```rust
use parity_scale_codec::{Encode};

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
    // [42, 1, 1, 69, 0, 0, 0]
    println!("{:?}", my_struct.encode().len());
    // 7
}
```
---v
### 示例：SCALE与JSON对比
```rust
use serde::{Serialize};

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
    // "{\"number\":42,\"is_cool\":true,\"optional\":69}"
    println!("{:?}", serde_json::to_string(&my_struct).unwrap().len());
    // 42
}
```
---
## Substrate与Polkadot
![Substrate与Polkadot](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149460.png)
---
## Substrate与智能合约
![Substrate与智能合约](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149461.png)
---v
### Substrate与智能合约
> 一个Substrate-Connect扩展正在同步一条其运行时正在执行Wasm合约的链。
问题：有多少个嵌套的Wasm块在相互执行？
---v
### Substrate与智能合约
<pba-cols>
  <pba-col center>
    <img style="width: 600px;" src="./img/mind-blow-galaxy.gif" />
  </pba-col>
  <pba-col>
    - 浏览器正在执行：
      - 一个Wasm块（substrate-connect）
      - 该块执行另一个Wasm块（运行时）
      - 运行时再执行一个Wasm块（合约）
  </pba-col>
</pba-cols>
---v
### Substrate与智能合约
![Substrate与智能合约](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149462.png)
---v
### Substrate与智能合约
- 那么什么时候应该使用智能合约（Ink!）编写，什么时候应该使用运行时（FRAME）编写呢？
Notes:
昨天也有人问我这个问题。我最新的答案是：如果你不需要区块链客户端/运行时提供的任何定制功能，并且共享平台的性能对你来说可以接受，那么就选择智能合约。如果你有更多需求，那么你就需要一个“运行时”（某种链、平行链或独立链）。
定制功能的一个例子是运行时可以访问`on_initialize`等函数 。
此外，合约无法进行无手续费交易。
而且，合约通常依赖于一种代币来支付gas，而运行时原则上可以无代币且无手续费。
---
## 技术自由度与易用性
![技术自由度与易用性](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149463.png)
---
### Substrate：区块链的游戏机！
<pba-cols>
  <pba-col>
    <img src="./img/nintendo-console-2.png" style="width:400px;" />
    Substrate客户端
  </pba-col>
  <pba-col>
    <img src="./img/nintendo-game.png" style="width:400px;" />
    Substrate的Wasm运行时
  </pba-col>
</pba-cols>
Notes:
另一个很好的类比：客户端就像现场可编程门阵列（FPGA），而FRAME/Wasm就像超高速集成电路硬件描述语言（VHDL）。
---
## 讲座回顾
- Substrate的设计源于3个核心原则：
  - **Rust**、**通用设计**、**可升级性/治理**
- 客户端/运行时架构
- 状态转换
- Wasm的积极和消极影响
- Substrate与Polkadot及其他链的关系。
- Substrate在智能合约方面的应用。
---v
### 回顾：Substrate架构
![Substrate架构](https://doc-ai-img-1259601696.cos.ap-beijing.myqcloud.com/6499674991493474524/%E5%9B%BE%E7%89%871694950901067149464.png)
---v
## 回顾：🏦 治理与可升级性
一个经得起时间考验的系统必须具备：
1. 通用性
2. 可治理性
3. 无需信任的可升级性。
Substrate的Wasm元协议恰好实现了最后一点✅
<!-- .element: class="fragment" -->
Notes:
问题：你会如何用文字描述Substrate的元协议？
客户端本质上是一个只做一件事的Wasm元协议。这个元协议是硬编码的，但协议本身是灵活的。
---
## 本模块剩余内容！😈
#### 主讲座
- Wasm元协议
- Substrate存储
#### 辅助讲座
- 交易池
- Substrate：给我看代码
- Substrate交互
- SCALE
#### 评分活动
- 无FRAME实践
---v
### 本模块剩余内容！😈
#### 第0天
- 介绍✅（60分钟）
- Wasm元协议（120分钟以上）
  - 活动：在Substrate中查找运行时API和宿主函数
- 🌭 午餐休息
- 给我看代码（60分钟）
- Substrate交互（60分钟）
- 无FRAME实践活动（60分钟）
Notes:
我们意识到本模块在讲座时间分配上严重不均，但这是有意为之，我们想看看效果如何。这样可以让你更早地开始完成作业。
---v
## 本模块剩余内容！😈
#### 第1天
- 交易池（60分钟）
- SCALE（60分钟）
- Substrate/FRAME技巧与窍门
- 🌭 午餐休息
- 无FRAME实践活动
---v
## 本模块剩余内容！😈
#### 第2天
- Substrate存储（90分钟）
- 无FRAME实践活动
- 🌭 午餐休息
- 模块结束🎉
---

# 额外资源！😋
> 查看演讲者备注（点击“s” 😉）
<img width="300px" rounded src="../scale/img/thats_all_folks.png" />
Notes:
- smoldot提供的关于Substrate/区块链各方面的优秀文档：<https://docs.rs/smoldot/latest/smoldot/>
- 深入了解Parity使用Rust的原因：<https://www.parity.io/blog/why-rust/>
- 关于JVM与Wasm的有趣问题：<https://stackoverflow.com/questions/58131892/why-the-jvm-cannot-be-used-in-place-of-webassembly>
- Rust安全性相关：<https://stanford-cs242.github.io/f18/lectures/05-1-rust-memory-safety.html>
- <https://www.reddit.com/r/rust/comments/5y3cxb/how_many_security_exploits_would_rust_prevent/>
- Substrate客户端在性能上应具备一定程度的确定性。如果权威节点的性能差异极大，它们可能会最终确定不同的分叉。
- 有人尝试编写FRAME的替代方案，如AssemblyScript，项目地址：<https://github.com/LimeChain/as-substrate-runtime>
- 思考运行时与智能合约之间的区别：
  - 从某种意义上讲，运行时也是一种智能合约，但并非由用户部署。
  - <https://en.wikipedia.org/wiki/Smart_contract>
  - <https://www.futurelearn.com/info/courses/defi-exploring-decentralised-finance-with-blockchain-technologies/0/steps/251885#:~:text=to%20the%201990s.-,Writing%20in%201994%2C%20the%20computer%20scientist%20Nick%20Szabo%20defined%20a,of%20artificial%20intelligence%20is%20implied.>
- Substrate Primitives（`sp-*`）、Frame（`frame-*`）、pallets（`pallets-*`）、二进制文件（`/bin`）以及所有其他工具均遵循[Apache 2.0许可协议](https://www.apache.org/licenses/LICENSE-2.0.html) 。Substrate客户端（`/client/*` / `sc-*`）遵循[GPL v3.0许可协议](https://www.gnu.org/licenses/gpl-3.0.html)，并带有[类路径链接例外条款](https://www.gnu.org/software/classpath/license.html)。
- Apache2许可赋予团队在发布内容和发布方式上的充分自由，并为商业团队提供清晰的许可说明。
- GPL3确保对Substrate核心逻辑（例如Substrate的内部共识、加密或数据库代码）所做的任何深层次改进都能回馈给社区，使所有人受益。
- 当前使用的是Wasm二进制规范v1，可在此处了解新版本的更多信息：<https://webassembly.github.io/spec/core/binary/index.html>
### 课后反馈
- 每部分结束后给出总结，提供更清晰的学习路径（肖恩建议）。
---
## 附录：到底什么是Wasm？
> WebAssembly（缩写为Wasm）是一种用于基于栈的虚拟机的二进制指令格式。Wasm被设计为编程语言的可移植编译目标，支持在Web上部署客户端和服务器应用程序。
---v
### 到底什么是Wasm？
<img style="width: 1400px;" src="./img/dev-4-1-wasm-langs.svg" />
---v
### 到底什么是Wasm？
<pba-cols>
<pba-col>
- Wasm与Web紧密结合
- 支持流传输和快速编译
- 设计时考虑了宿主概念，具有沙盒环境和受限的系统调用权限
> 有人还记得“Java小程序”吗？
</pba-col>
<pba-col>
<img style="height: 700px;" src="./img/dev-4-1-wasm.svg" />
</pba-col>
</pba-cols>
Notes:
人们曾尝试将类似JVM的技术嵌入浏览器（即“Java小程序”），但并未成功。
---v
### 如何编写Wasm运行时？
- 任何能够编译为Wasm并暴露一组固定函数供客户端使用的语言均可。
- 当然，Substrate提供了一个名为 **FRAME™️** 的框架，使开发更加便捷。
---
## 附录：更多Substrate与Polkadot的图示
Notes:
我最近绘制了这些图，用于解释Substrate、Cumulus和Polkadot之间的关系。图中分别使用最通用的术语“主机”和“STF”来指代客户端和运行时。
---v
### Substrate
<img style="width: 1400px;" src="./img/dev-4-1-substrate-new-1.svg" />
---v
### Polkadot
<img style="width: 1400px;" src="./img/dev-4-1-substrate-new-2.svg" />
---v
### 一条平行链
<img style="width: 1400px;" src="./img/dev-4-1-substrate-new-3.svg" /> 