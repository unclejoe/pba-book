---
title: EVM, Solidity, and Vyper
description: Overview and history of the EVM and languages that compile to it.
  Focus on architecting real-world smart contracts.
---

# EVM、Solidity 和 Vyper

---

## EVM

**以太坊虚拟机**

专为以太坊的约束和特性设计的虚拟机

---

## EVM 属性

- 确定性：执行结果易于达成共识
- 抗垃圾信息：CPU 及其他资源在非常精细的层面上进行计量
- 图灵完备（有一个注意事项）
- 基于栈的设计
- 以太坊特定（EVM 可以查询区块哈希、账户和余额等）

Notes:

EVM 必须 100% 确定，并且每个实现都要产生相同的结果。即使两个运行节点之间有最微小的差异，也会导致不同的区块哈希，从而破坏对结果的共识。

---

## 以太坊历史

<pba-flex center>

- 2013 年 11 月：Vitalik 发布白皮书（WP）
- 2014 年 4 月：Gav 发布黄皮书（YP）
- 2014 年 7 月：筹集 1800 万美元（首次代币发行（ICO）持续了 42 天）
- 2015 年 7 月：Frontier 版本发布——基本功能，工作量证明
- 2015 年 9 月：Frontier “解冻”，引入难度炸弹
- 2016 年 7 月：DAO 分叉
- 2016 - 2019 年：优化、操作码成本调整、难度炸弹延迟
- 2020 年：质押合约部署，信标链启动
- 2021 年：EIP - 1559，为合并做准备
- 2022 年：合并（权益证明）
- 2023 年：支持质押提款

</pba-flex>

---v

## DAO 黑客攻击事件

1. 2016 年：筹集了价值 1.5 亿美元的 ETH
1. 同年晚些时候：360 万 ETH 被窃取
1. 重入攻击
1. “主网”以太坊分叉以追溯撤销黑客攻击
1. 以太坊经典：代码即法律

Notes:

DAO（“去中心化自治组织”）非常类似于由代码而非人类运营的商业实体。像任何其他商业实体一样，它有资产并可以开展业务，但它的法律地位尚不明确。

以太坊上最早的 DAO（“The DAO”）由于其代码中的漏洞而遭受了灾难性的黑客攻击。DAO 社区对于是否进行硬分叉以撤销黑客攻击存在分歧，导致以太坊分裂成两条不同的链。

---v

## 第一个智能合约平台

作为智能合约的先驱，以太坊面临着许多挑战。

- 性能：定价过低的操作码被用作垃圾信息或拒绝服务（DoS）攻击的手段
- 高额 gas 费用：需求过高，区块空间供应不足
- 抢先交易：在其他交易之前、之后或替代其他交易插入交易以获取经济利益
- 黑客攻击：许多黑客攻击利用了智能合约的漏洞
- 智能合约协议聚合方面的问题
- 存储膨胀：存储激励机制不协调

---v

## 特性

- 一切都是 256 位
- 没有浮点运算
- 回滚
- 重入
- 指数级内存扩展成本

---

## Gas

> 图灵完备性和停机问题

- EVM：图灵完备的指令集

- 但停机问题怎么办？
- 显然不能允许无限循环
- 解决方案：Gas 计量器，一种为每个操作码执行预先付费的方式

Notes:

停机问题告诉我们，不可能知道一个任意程序是否会正常停止。为了防止这种滥用，我们在每次操作码执行之前检查是否还有 gas。由于 gas 是有限的，这确保了没有 EVM 执行会永远运行，并且所有执行的工作都得到了适当的支付。

---v

## Gas 计量器

<img style="width: 1200px" src="./img/GasometerDiagram.png" />

- 在每个操作码之前进行检查，以确保可以支付 gas
- 安全：防止执行未付费的工作
- 确定性：结果明确无误
- 非常低效：有很多分支和额外的工作

Notes:

这不仅可以防止滥用，而且关键是允许节点就防止滥用达成共识。一个中心化的服务可以很容易地施加时间限制，但去中心化的节点无法就这种限制的结果达成一致（或者无法相互信任）。

---v

## Gas 限制和价格

Gas：EVM 执行资源的记账单位。

- `gas_limit`：指定一个交易可以支付的最大 gas 量
- `gas_price`：指定一个交易每单位 gas 要支付的确切价格

一个交易必须能够支付 `gas_limit * gas_price` 才能有效。

Notes:

这个金额最初会从交易的发送者账户中扣除，交易执行后，任何剩余的 gas 都会被退还。

---v

## EIP - 1559

对 gas 定价机制的一种改进。

```
gas_price --> max_base_fee_per_gas
          \-> max_priority_fee_per_gas
```

<!-- FIXME TODO mermaid or img for above -->

- 将小费与 gas 价格分开
- `base_fee` 是一个算法确定的 gas 价格，这是实际支付并被销毁的费用
- ……如果 (`base_fee < max_base_fee + max_priority_fee`)，可能还需要支付小费
- 基于算法的、与拥堵相关的乘数控制 `base_fee`

Notes:

<https://eips.ethereum.org/EIPS/eip-1559>
在伦敦硬分叉中引入。

---v

## OOG（耗尽 gas）和 Gas 估算

如果一个交易在未完成时耗尽了其 `gas_limit`，它将产生一个 OOG（耗尽 gas）错误，并且 EVM 中所做的所有更改都将被回滚（除了费用支付）。

为了估算一个交易所需的 gas 量，可以使用一个 RPC 方法（`eth_estimateGas`）对交易进行一次试运行并记录使用的 gas 量。

然而，有几个注意事项：

- 针对当前状态运行（状态可能会改变）
- RPC 节点可能会欺骗你
- 这会产生昂贵的基础设施开销，并且可能成为垃圾信息攻击的手段

---

## 账户类型

以太坊有两种类型的账户。
两者都使用 160 位的账户 ID，并且可以持有和发送以太币，但它们的控制方式非常不同。

---v

## 账户类型

<pba-cols>
<pba-col>

**外部拥有账户（EOA）**

- 传统的用户控制账户
- 通过私钥控制
- 账户 ID 由公钥哈希生成
- 使用递增的 nonce 来防止重放攻击

</pba-col>
<pba-col>

**合约账户**

- 由不可变的字节码控制
- 只能严格按照代码规定执行操作
- 字节码部署时确定性地生成账户 ID

</pba-col>
</pba-cols>

---

## 交易

交易是来自外部拥有账户（EoA）的已签名有效载荷，其中包含有关交易应该做什么以及如何支付的详细信息。

---v

## 交易字段：

- **value**：随交易发送的 0 或更多以太币
- **to**：此交易的目标
- **input**：用于创建或调用合约的可选输入数据
- **gas_limit**：交易将支付的最大 gas 量
- **gas_price**：（或 EIP - 1559 等效值）
- **nonce**：防止重放攻击并强制排序
- **signature**：证明私钥的所有权，允许获取接收方账户 ID
- _可能还有更多_

---v

## 可能的用例：

- 调用合约的外部函数（`input` 指定函数和参数）
- 创建合约（`input` 指定合约的字节码）
- 都不做（`input` 为空）

在所有情况下，都可以发送以太币（“都不做” 即为普通的以太币转账）。

---v

## 交易有效性

在执行（或传播）交易之前，应该进行一些有效性检查：

- `gas_limit` 是否足够？（至少 21000 用于支付处理费用）
- 签名是否有效？（副作用：恢复公钥）
- 账户是否能够支付 `gas_limit * gas_price`？
- 这个 nonce 对于该账户是否有效（且合理）？

Notes:

这些检查会带来开销，因此如果交易无效，尽快丢弃无效交易非常重要。这包括不将其传播给对等节点，因为对等节点也需要对其进行验证。

---

## 操作码和字节码

操作码是一个单字节，代表虚拟机要执行的一条指令。

EVM 一次执行一个操作码的字节码，直到执行完毕、显式停止或 gas 计量器耗尽 gas。

Notes:

函数编译成一系列操作码，我们称之为字节码。这些字节码被打包在一起，成为链上合约代码。

---

## ABI

ABI（“应用程序二进制接口”）通过注释函数和其他对象的位置以及它们的格式来描述合约的字节码。

---v

<!-- .slide: data-background-color="#4A2439" -->

## 练习

查看 [Etherscan 上的合约代码](https://etherscan.io/token/0x1f9840a85d5af5bf1d1762f925bdaddc4201f984?a=0xd0fc8ba7e267f2bc56044a7715a489d851dc6d78#code)

---v

## 沙盒化合约状态

`合约账户` 包含一个沙盒化状态，用于存储合约写入存储的所有内容。
合约不能写入其自身沙盒之外的存储，但它们可以调用其他合约，而这些合约的字节码可能会写入其各自的存储。

---v

## 调用合约

合约函数可以通过两种不同的方式调用：

- 外部拥有账户（EoA）可以直接调用合约函数
- 合约可以调用其他合约（称为“消息传递”）

---v

## 合约消息传递的类型

- 普通 `call`：调用另一个合约并可以更改其自身状态
- `staticcall`：一种“安全”的调用另一个合约且不改变状态的方式
- `delegatecall`：一种调用另一个合约但修改我们自身状态的方式

Notes:

交易是状态改变的唯一方式。

---v

## 消息对象

在合约调用的上下文中，我们始终有 `msg` 对象，它让我们知道我们是如何被调用的。

```
msg.data (bytes)：调用的完整调用数据（输入数据）
msg.gas (uint256)：可用 gas
msg.sender (address)：当前消息的发送者
msg.sig (bytes4)：调用数据的前 4 个字节（函数签名）
msg.value (uint256)：此调用发送的以太币数量
```

---v

## 以太币面额

以太币通过整数运算进行存储和操作。
为了避免小数运算的复杂性，它以一个非常小的整数单位存储：`Wei`。

```
1 以太币 = 1_000_000_000_000_000_000 Wei（10^18）
```

Notes:

使用这种微不足道的单位进行整数运算，大多可以避免截断问题，并且易于就结果达成共识。

---v

## 命名面额

其他面额已有官方命名，但不常用：

```
wei                 = 1 wei
kwei (babbage)      = 1_000 wei
mwei (lovelace)     = 1_000_000 wei
gwei (shannon)      = 1_000_000_000 wei
microether (szabo)  = 1_000_000_000_000 wei
milliether (finney) = 1_000_000_000_000_000 wei
ether               = 1_000_000_000_000_000_000 wei
```

`gwei` 常用于谈论 gas。

---

## 编程 EVM

最终，通过创建字节码来对 EVM 进行编程。
虽然可以手动编写字节码或使用低级汇编语言，但使用高级语言要实用得多。
我们将特别关注两种语言：

<pba-flex center>

- Solidity
- Vyper

</pba-flex>

---

## Solidity

<pba-flex center>

- 专为 EVM 设计
- 类似于 C++、Java 等
- 包括继承（包括多重继承）
- 因难以对安全性进行推理而受到批评

</pba-flex>

---v

## 基础

```Solidity
// 'contract' 类似于其他面向对象语言中的 'class'
contract Foo {
    // 合约的成员变量存储在链上
    public uint bar;

    constructor(uint value) {
        bar = value;
    }
}
```

---v

## 函数

```Solidity
contract Foo {
    function doSomething() public returns (bool) {
        return true;
    }
}
```

---v

## 修饰符

一种特殊的函数，可以作为其他函数的前置条件运行

```Solidity
contract Foo {
    address deployer;

    constructor() {
        deployer = msg.sender;
    }

    // 确保只有合约部署者可以调用给定的函数
    modifier onlyDeployer {
        require(msg.sender == deployer);
        _; // 原始函数在此处插入
    }

    function doSomeAdminThing() public onlyDeployer() {
        // 只有 onlyDeployer() 通过时才能调用此函数
    }
}
```

Notes:

尽管修饰符可以是一种要求先决条件的优雅方式，但它们可以做到完全任意的东西，审计代码需要仔细阅读它们。

---v

## 可支付的

```Solidity
contract Foo {
    uint256 received;
    // 此函数可在接收以太币（Ether）时被调用。
    // 在这个简单的例子中，合约不会对以太币做任何处理（实际上意味着以太币会丢失），但它会忠实地
    // 追踪收到的金额。
    function deposit() public payable {
        received += msg.value;
    }
}
```

Notes:
实际的支付记账由 EVM 自动处理，我们无需更新自己的账户余额。
---v
## 类型：“值”类型

```Solidity
contract Foo {
    // 值类型存储在内存中，赋值时需要完整复制。
    function valueTypes() public {
        bool b = false;

        // 有符号和无符号整数
        int32 i = -1;
        int256 i2 = -10000;
        uint8 u1 = 255;
        uint16 u2 = 10000;
        uint256 u3 = 99999999999999;

        // 固定长度字节序列（1 到 32 字节）
        // 可以对这些进行许多位操作
        bytes1 oneByte = 0x01;

        // 地址表示一个 20 字节的以太坊地址
        address a = 0x101010101010101010101010101010101010101010101010;
        uint256 balance = a.balance;

        // 还有：枚举

        // 每个变量都是一个独立的副本
        int x = 1;
        int y = x;
        y = 2;
        require(x == 1);
        require(y == 2);
    }
}
```
---v
## 类型：“引用”类型

```Solidity
contract Foo {
    mapping(uint => uint) forLater;

    // 引用类型存储为对其他位置的引用。
    // 赋值时只需复制它们的引用。
    function referenceTypes() public {
        // 数组
        uint8[3] memory arr = [1, 2, 3];

        // 映射：只能从状态变量初始化
        mapping(uint => uint) storage balances = forLater;
        balances[0] = 500;

        // 动态长度字符串
        string memory foo = "<3 Solidity";

        // 还有：结构体

        // 两个或多个变量可以共享一个引用，所以要小心！
        uint8[3] memory arr2 = arr;
        arr2[0] = 42;
        require(arr2[0] == 42);
        require(arr[0] == 42); // arr 和 arr2 是同一个东西，所以对一个的修改会影响另一个
    }
}
```
---v
## 数据位置
`数据位置`指的是`引用类型`的存储位置。
由于这些是按引用传递的，它实际上决定了这个引用指向何处。
它可以是以下三个位置之一：
- 内存：仅存储在内存中；不能在给定的外部函数调用后继续存在
- 存储：存储在合约的永久链上存储中
- 调用数据：只读数据，使用这个可以避免复制
---v
## 数据位置示例

```Solidity
contract DataLocationSample {
    struct Foo {
        int i;
    }

    Foo storedFoo;

    // 数据位置限定符会影响函数参数和返回值……
    function test(Foo memory val) public returns (Foo memory) {
        // ……也会影响函数内的变量
        Foo memory copy = val;

        // 存储变量在使用前必须先赋值。
        Foo storage fooFromStorage = storedFoo;
        fooFromStorage.i = 1;
        require(storedFoo.i == 1, "对存储变量的写入会影响存储");

        // 内存变量可以从存储变量初始化
        // （但反过来不行）
        copy = fooFromStorage;

        // 但它们是一个独立的副本
        copy.i = 2;
        require(copy.i == 2);
        require(storedFoo.i == 1, "对内存变量的写入不能影响存储");

        return fooFromStorage;
    }
}
```
---v
## 枚举

```Solidity
contract Foo {
    enum Suite {
        Hearts,
        Diamonds,
        Clubs,
        Spades
    }

    function getHeartsSuite() public returns (Suite) {
        Suite hearts = Suite.Hearts;
        return hearts;
    }
}
```
---v
## 结构体

```Solidity
contract Foo {
    struct Ballot {
        uint32 index;
        string name;
    }

    function makeSomeBallot() public returns (Ballot memory) {
        Ballot memory ballot;
        ballot.index = 1;
        ballot.name = "John Doe";
        return ballot;
    }
}
```
---
<!--.slide: data-background-color="#4A2439" -->
## Solidity 实践
---v
## 开发环境
我们将使用在线的 [Remix IDE](https://remix.ethereum.org) 进行示例编码。
它在浏览器中提供了编辑器、编译器、EVM 和调试器，使入门变得轻而易举。
> <https://remix.ethereum.org>
---v
## 翻转器示例
一边编写代码一边解释。
---v
<!--.slide: data-background-color="#4A2439" -->
## 练习：乘法器
- 编写一个具有`uint256`存储值的合约。
- 编写函数将其与用户指定的值相乘。
- 与之交互：你能引发溢出吗？
> Solidity 中已添加溢出检查。
> 你可以通过指定较旧的编译器来禁用这些检查。
> 在你的`.sol`文件的最顶部添加以下内容：
>
> ```Solidity
> pragma solidity ^0.7.0;
> ```
---v
<!--.slide: data-background-color="#4A2439" -->
#### 额外内容：
- 防止你的乘法函数溢出。
- 将此预防措施重写为`modifier noOverflow()`。
> 常量`constant public MAX_INT_HEX = 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff;`可能会有所帮助。
---
## Vyper
- 也为 EVM 而设计。
- 类似于 Python。
- 有意地缺少一些特性，如继承。
- 可审计：“对读者来说简单比对作者来说简单更重要。”
---v
## 与 Solidity 相比
Vyper 大多缺少 Solidity 中存在的特性，都是为了提高可读性。
一些例子：
- 无继承。
- 无修饰符。
- 无函数重载。
- 无递归调用（！）。
- 无无限循环。
---v
## 基础

```Python
# 没有`contract`关键字。
# 像 Python 模块一样，合约由其所在的文件隐式地限定范围。

# 存储变量在任何函数之外声明。
bar: uint

# init 用于部署合约并初始化其状态。
@external
def __init__(val):
    self.bar = val
```
---v
## 函数

```Python
@external
def doSomething() -> bool:
    return True
```
---v
## 装饰器和可支付

```Python
# Vyper 包含用于限制函数的装饰器：

@external # 函数只能从外部调用
@internal # 函数只能在当前上下文中调用
@pure # 不能读取状态或环境变量
@view # 不能改变合约状态
@payable # 函数可以接收以太币

# 还有，涵盖 Solidity 修饰符最常见的用例：
@nonreentrant(<unique_key>) # 防止给定 ID 的重入
```

Notes:
来源：<https://docs.vyperlang.org/en/stable/control-structures.html#decorators-reference>
---v
## 类型

```Python
# 值类型较小和/或大小固定，会被复制。
@external
def valueTypes():
    b: bool = False

    # 有符号和无符号整数
    i: int128 = -1
    i2: int256 = -10000
    u: uint128 = 42
    u2: uint256 = 42

    # 定点（十进制）小数，精度为 10 位小数
    # 这样的优点是可以精确表示字面量
    f: decimal = 0.1 + 0.3 + 0.6
    assert f == 1.0, "十进制字面量是精确的！"

    # 用于 20 字节以太坊地址的地址类型
    a: address = 0x1010101010101010101010101010101010101010
    b = a.balance

    # 固定大小的字节数组
    selector: bytes4 = 0x12345678

    # 有界字节数组
    bytes: Bytes[123] = b"\x01"

    # 动态长度、有界字符串
    name: String[16] = "Vyper"

# 引用类型可能很大和/或动态大小。
# 它们是按引用复制的。
@external
def referenceTypes():
    # 固定大小的列表。
    # 它也可以是多维的。
    # 所有元素必须初始化。
    list: int128[4] = [1, 2, 3, -4]

    # 有界、动态大小的数组。
    # 它们有最大大小，但初始化为空。
    dynArray: DynArray[int128, 3]
    dynArray.append(1)
    dynArray.append(5)
    val: int128 = dynArray.pop() # == 5

    map: HashMap[int128, int128]
    map[0] = 0
    map[1] = 10
    map[2] = 20
```
---v
## 枚举

```Python
enum Suite {
    Hearts,
    Diamonds,
    Clubs,
    Spades
}

# "hearts" 会被视为值类型
hearts: Suite = Suite.Hearts
```
---v
## 结构体

```Python
struct Ballot:
    index: uint256
    name: string

# "someBallot" 会被视为引用类型
someBallot: Ballot = Ballot({index: 1, name: "John Doe"})
name: string = someBallot.name
```
---
<!--.slide: data-background-color="#4A2439" -->
## Vyper 实践
---v
## Remix 插件
Remix 通过一个插件支持 Vyper，可以在 IDE 中轻松启用。
首先，在插件选项卡中搜索“Vyper”：

<img rounded style="width: 500px;" src="./img/RemixInstallVyper1.png" />
---v
## Remix 插件
通过新的 Vyper 选项卡使用 Vyper，并使用“远程编译器”。

<img rounded style="width: 500px" src="./img/RemixInstallVyper2.png" />
---
## 重入
DAO 漏洞

```Java
    function withdraw() public {
        // 检查用户的余额
        require(
            balances[msg.sender] >= 1 ether,
            "资金不足。无法提取"
        );
        uint256 bal = balances[msg.sender];

        // 提取用户的余额
        (bool sent, ) = msg.sender.call{value: bal}("");
        require(sent, "无法提取发送者的余额");

        // 更新用户的余额。
        balances[msg.sender] = 0;
    }
```
我们在更新内部状态之前调用了提取用户余额的操作。
---v
## 如何避免这种情况？
- 在合约调用前提交状态。
- 防止重入的修饰符（Solidity）。
- `@nonreentrant` 装饰器（Vyper）。
---
## 在链上存储秘密
我们可以在链上存储秘密吗？
如果我们想对特定的合约调用进行密码保护会怎样？
显然我们不能在链上存储任何明文秘密，因为这样会泄露它们。
<!--.element: class="fragment" -->
---v
## 在链上存储哈希后的秘密
在链上存储密码的哈希值并使用它来验证用户输入怎么样？
接受预哈希也会泄露秘密。
这种泄露可能会在交易执行和结算之前发生，允许他人抢先操作。
<!--.element: class="fragment" -->
---v
## 使用提交-揭示验证
一种可能的解决方案是提交-揭示方案，我们将要揭示的内容与一些盐值一起哈希，然后稍后揭示。

```
// 存储在链上：
secret_hash = hash(secret)
```

```
// 第一笔交易，必须在最终揭示之前在链上执行并结算。
// 这将用户与即将揭示的秘密关联起来。
commitment = hash(salt, alleged_secret)
```

```
// 最终揭示，在承诺被记录之前不得公开。
reveal = alleged_secret, salt
verify(alleged_secret == secret)
verify(commitment == hash(salt, alleged_secret))
```
---v
## 另一种方法：签名
另一种方法是使用公钥密码学。
我们可以存储某个密钥对的公钥，然后要求使用相应私钥的签名。
这可以通过多重签名方案等进行扩展。
这与提交-揭示方案有何不同？

Notes:
提交-揭示方案要求在某个时刻揭示特定的秘密以进行验证。
签名方案为保护秘密提供了更多的灵活性。