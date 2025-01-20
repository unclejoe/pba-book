---
title: Introduction to Formal Methods for Rust
description: Introductory lesson on formal methods for Rust verification
duration: 60 minutes
---

# Rust形式化方法入门

---

## 大纲

<pba-flex center>

1. 形式化方法简介
1. Rust技术全景
1. 聚焦Kani：有界模型检查器
1. 在Substrate中的应用

</pba-flex>

---

## 形式化方法简介

#### _故事时间！_

---v

### _阿丽亚娜5号火箭 - 第501次飞行_

<img rounded style="width: 400px" src="./img/ariane.jpg" />

- 在**1996年**，这枚运载火箭在发射后39秒解体。
- **故障**：一次_溢出_，由64位到16位浮点数的转换引起。
- **失误**：复用了阿丽亚娜4号的惯性参考平台，在该平台上，由于不同的运行条件，不会发生溢出。
- **损失**：5亿美元的有效载荷，80亿美元的开发计划。

Notes:

相关文章链接：(https://www-users.cse.umn.edu/~arnold/disasters/ariane.html)

---v

## 软件正确性非常重要

> 程序测试可以用来显示错误的存在，<br />但永远不能显示错误的不存在！
>
> —— 埃兹格·迪科斯彻

**_因此，有必要超越测试_** <!-- .element: class="fragment" -->

---v

## 形式化方法来救援！

- 给定一个系统（代码）和规范（行为），以合理的数学保证来验证/证明其正确性。
- **传统上**，在诸如航空电子设备、核反应堆、医学成像等_安全关键_软件中，成本和精力是合理的。
- 然而，情况已经发生了变化……

Notes:

这就是形式化方法的动机；证明错误的不存在！在我看来，有点危言耸听。

---v

## 这不再是火箭科学！

- 亚马逊网络服务（AWS）对亚马逊S3中的键值存储节点（Rust实现）进行形式化验证。
- Meta检测安卓应用中的资源泄漏和竞态条件。
- Uber使用静态分析来查找空指针异常。
- 以太坊的信标链和Tendermint共识协议在安全性和活性保证方面进行了形式化验证。

Notes:

- 个人认为形式化方法是一种更系统的错误检测方式。
- 理想情况下，验证你的属性在所有可能的输入上是否成立。

---v

## 当今的形式化方法

从理论研究兴趣<br />到提供实用且经济高效的工具

<pba-flex center>

- 目标更聚焦，承诺不那么夸张
- 验证工具更高效
- 分析技术的组合

</pba-flex>

Notes:

- 关注特定类别的错误，如资源泄漏、数据竞争等。
- 底层约束求解器引擎的速度大幅提升。例如，微软的Z3可以求解包含数十亿个变量的约束。
- 统一的理论，界限模糊；结合了静态和动态技术。

---v

## 更像是_轻量级形式化方法_

1. 严格地**检测错误** → 证明系统的整体正确性。
1. 以开发者为中心的**可用性**（例如，工作流程集成）。

Notes:

- 意识到了开发者体验的重要性。
- 开发者不再需要学习晦涩的逻辑来编写规范。
- 你将看到验证代码是多么直观。

---v

## 形式化方法 ↔ 区块链

**终于找到了合适的应用场景！**

- 利害攸关，成本和精力是值得的。
- 业务逻辑紧凑且模块化，在一定范围内。

Notes:

- 声誉和金钱都处于风险之中。
- 一个简单的安卓应用有10万个Java类。这些技术在大型代码库上不可扩展。
- 运行时业务逻辑的复杂性要低得多。
- 对智能合约验证有很多兴趣。
- 查看Certora、Echidna、Securify等工具，[链接在此](https://ethereum.org/en/developers/docs/smart-contracts/formal-verification/)

---v

## 关键要点

<pba-flex center>

**_形式化方法是……_**

- **不是万能药**，但可以提高软件质量。
- 越来越**易于使用**。
- 对提高区块链的**可靠性和安全性**很有用。
  </pba-flex>

Notes:

- [很棒的博客](https://web.archive.org/web/20230209000724/www.pl-enthusiast.net/2017/10/23/what-is-soundness-in-static-analysis/)，解释了正确性和可处理性之间的权衡。

---

<!-- .slide: data-background-color="#4A2439" -->

## 工具全景

<img style="width: 700px" src="./img/Landscape.svg"/>

Notes:

列出工具的链接

- [Isabelle](https://isabelle.in.tum.de/)
- [Coq](https://coq.inria.fr/)
- [TLA+](https://github.com/tlaplus)
- [StateRight](https://github.com/stateright/stateright)
- [Prusti](https://www.pm.inf.ethz.ch/research/prusti.html)
- [Kani](https://github.com/model-checking/kani)
- [MIRAI](https://github.com/facebookexperimental/MIRAI)
- [Flowistry](https://github.com/willcrichton/flowistry)
- [Substrace](https://github.com/kaiserkarel/substrace)
- [Clippy](https://github.com/rust-lang/rust-clippy)

---v

## 工具全景

<pba-cols>
<pba-col center>

<img style="width: 700px" src="./img/Landscape.svg"/>

</pba-col>

<pba-col center>

#### Quint/ State-Right（模型检查器）

- 对系统进行建模和指定属性需要巨大的努力。
- 存在抽象差距。
- 对复杂属性进行推理：共识机制的安全性和活性。

</pba-col>
</pba-cols>

Notes:

- 设计层面，验证协议设计。
- 模型和实际代码之间总是存在差异。
- 安全性：永远不会发生坏事；没有两个诚实节点会就不同的状态达成一致。
- 活性：最终会有好事发生；最终2/3的节点会达成共识。

---v

## 工具全景

<pba-cols>
<pba-col center>

<img style="width: 700px" src="./img/Landscape.svg"/>

</pba-col>

<pba-col center>

#### 静态分析器

- 代码层面
- 信息/数据流属性；代码的访问控制；
- 指定预期行为（属性）。往返属性：decode (encode (x)) == x
- 默认检查：诸如算术溢出、越界访问恐慌等错误。

</pba-col>
</pba-cols>

Notes:

- 例如，对于代码访问控制：确保运行时的某些敏感部分仅可由根来源访问。
- MIRAI由Meta开发，使用一种称为抽象解释的技术；特别适用于静态检测恐慌和信息流属性。
- Kani：我们很快将深入探讨。

---v

## 工具全景

<pba-cols>
<pba-col center>

<img style="width: 700px" src="./img/Landscape.svg"/>

</pba-col>

<pba-col center>

#### 代码检查器

- 代码层面
- 检查代码异味
- 其他语法属性

</pba-col>
</pba-cols>

Notes:

- [Substrace](https://github.com/KaiserKarel/substrace)是专门用于Substrate的代码检查器。
- Flowistry允许你跟踪变量之间的依赖关系；仅对给定位置的相关部分进行切片。

---

<!-- .slide: data-background-color="#4A2439" -->

# 我们的重点：[Kani](https://github.com/model-checking/kani)

---

## Kani：Rust的模型检查工具

- 由AWS开发的[开源Rust验证器](https://github.com/model-checking/kani)。
- 使用的底层技术：有界模型检查。
- 可用于_证明_：
  - 不存在算术溢出。
  - 不存在运行时错误（索引越界、恐慌）。
  - 用户指定的属性（增强的属性测试）。
  - 使用不安全的Rust时的内存安全性。
- 如果验证失败，提供一个触发错误的具体测试用例。

Notes:

对于感兴趣的人，有界模型检查论文的链接在此[此处](https://www.cs.cmu.edu/~emc/papers/Books%20and%20Edited%20Volumes/Bounded%20Model%20Checking.pdf)。例如，当你访问/修改可变静态变量时。

---v

### _先来看点神奇的东西_

> 矩形示例演示

---v

## 证明框架

<pba-col centre>

```rust
use my_crate::{function_under_test, meets_specification, precondition};

#[kani::proof]
fn check_my_property() {
   // 创建一个非确定性输入
   let input = kani::any();

   // 根据函数的前置条件对其进行约束
   kani::assume(precondition(input));

   // 调用要验证的函数
   let output = function_under_test(input);

   // 检查它是否符合规范
   assert!(meets_specification(input, output));
}
```

<!-- .element: style="font-size:0.62em"-->

- Kani尝试证明所有有效输入都能产生符合规范的输出，且不会引发恐慌。
- 否则，Kani会生成一个指向失败的跟踪信息。

</pba-col>

---v

属性：`decode(encode(x)) == x`

<pba-cols>
<pba-col center>

测试

```rust
#[cfg(test)]
fn test_u32 {
  let val: u16 = 42;
  assert_eq!(u16::decode(&mut
    val.encode()[..]).unwrap(), val)
}
```

固定值`42`

</pba-col>

<pba-col center>

模糊测试

```rust
#[cfg(fuzzing)]
fuzz_target!(|data: &[u8]|) {
  let val = u16::arbitrary(data);
  assert_eq!(u16::decode(&mut
    val.encode()[..]).unwrap(), val)
}
```

`u16`的多个随机值

</pba-col>
</pba-cols>

<pba-col center>

Kani证明

```rust
#[cfg(kani)]
#[kani::proof]
fn proof_u32_roundtrip {
  let val: u16 = kani::any();
  assert_eq!(u16::decode(&mut
    val.encode()[..]).unwrap(), val)
}
```

穷举验证`u16`的所有值
</pba-col>

---v

### 底层原理：有界模型检查

#### 思路：

- 在（有界的）执行路径中搜索反例。
- 然而，这种搜索是一个NP难问题。

#### 方法：

- 有效地将问题简化为约束满足（SAT）问题。
- 验证简化为寻找SAT公式的可满足赋值问题。
- 利用高度优化的SAT求解器使搜索变得可行。

Notes:

Kani使用miniSAT作为后端引擎；许多其他验证工具使用Z3求解器。

---v

### 转换为约束

<pba-cols>

<pba-col centre>

#### 代码

```rust
fn foo(x: i32) -> i32 {
    let mut y: i32 = 8;
    let mut w: i32 = 0;
    let mut z: i32 = 0;
    if x != 0 {
        z -= 1;
    } else {
        w += 1;
    }
    assert!(z == 7 || w == 9);
    w+z
}
```

</pba-col>

<pba-col centre>

#### 约束

```rust
y = 8,
z = x? y-1: 0,
w = x? y+1: 0,
z != 7 /\ w != 9 (assert条件的否定)
```

- 约束被输入到求解器（minisat）中。
- 对于`x`的任何值，约束都不成立 $\implies$ 断言条件得到验证。
- 否则，求解器找到了一个失败的测试（反例）。

<!-- 在演示中显示公式中使用的子句和变量的数量 -->

</pba-col>
</pba-cols>

---v

## 它是如何处理循环的？

- 有界模型检查中的_有界_发挥作用！
- 循环被展开到一定的有界深度$k$，否则验证不会终止。
- 确定最佳的$k$值是在_可处理性_和_验证置信度_之间的权衡。

---v

## 演示：展开循环

```rust
fn initialize_prefix(length: usize, buffer: &mut [u8]) {
    // 让我们忽略无效调用
    if length > buffer.len() {
        return;
    }

    for i in 0..=length {
        buffer[i] = 0;
    }
}

#[cfg(kani)]
#[kani::proof]
#[kani::unwind(1)] // 故意设置得太低
fn check_initialize_prefix() {
    const LIMIT: usize = 10;
    let mut buffer: [u8; LIMIT] = [1; LIMIT];

    let length = kani::any();
    kani::assume(length <= LIMIT);

    initialize_prefix(length, &mut buffer);
}
```

<!-- .element: style="font-size:0.62em"-->

---v

## 处理循环：总结
**流程：**
- 从展开 $k$ 次开始。
- 如果没有发现错误，增加 $k$，直到满足以下任一条件：
  - 发现错误。
  - 验证器超时。
  - 达到预先设定的 $k$ 的上限 $N$ 。
---v
## 为自定义类型实现Arbitrary特质
```rust
use arbitrary::{Arbitrary, Result, Unstructured};

#[derive(Copy, Clone, Debug)]
pub struct Rgb {
    pub r: u8,
    pub g: u8,
    pub b: u8,
}

impl<'a> Arbitrary<'a> for Rgb {
    fn arbitrary(u: &mut Unstructured<'a>) -> Result<Self> {
        let r = u8::arbitrary(u)?;
        let g = u8::arbitrary(u)?;
        let b = u8::arbitrary(u)?;
        Ok(Rgb { r, g, b })
    }
}
```
---
## 练习
> 验证SCALE中整数类型的[固定宽度](https://github.com/paritytech/parity-scale-codec/blob/master/src/codec.rs)和[紧凑](https://github.com/paritytech/parity-scale-codec/blob/master/src/compact.rs)编码。
<br />
<br />
**开放性属性！**
- _往返性_：`Decode (Encode (x)) == x`
- `DecodeLength(x) == Decode(x).length()`
- `EncodeAppend(vec,item) == Encode(vec.append(item))`
- ......
Notes:
- 本周末的研讨会上，我们可能会探讨其中一些属性。
---
<!-- .slide: data-background-color="#4A2439" -->
# 更多验证，更少漏洞
<br />
<br />
<br />
**_提问环节_**