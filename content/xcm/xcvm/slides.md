---
title: XCVM
description: Learning about the XCVM state machine
duration: 1 hour
---

# XCVM

---

# 🫀 XCVM

XCM的核心是**跨共识虚拟机（XCVM）**。

XCM中的“消息”是一个XCVM程序，它是一系列指令。

XCVM是一个状态机，状态通过**寄存器**进行跟踪。

Notes:

它是一个超高级别的非图灵完备计算机。
消息是一个或多个XCM指令。
程序会一直执行，直到运行结束或遇到错误，此时程序会完成并停止。
Parity提供了一个遵循XCVM规范的XCM执行器，它可以进行扩展或定制，甚至可以完全忽略，用户可以创建自己遵循XCVM规范的结构。

---

# 📜 XCVM指令

XCVM指令可能会改变寄存器，可能会改变共识系统的状态，或者两者都会改变。

---v

## 指令类型

<pba-flex center>

- 命令
- 可信指示
- 信息
- 系统通知

---v

## 示例：TransferAsset

一个用于将资产转移到其他地址的指令。

<pba-flex center>

```rust
TransferAsset {
    assets: Assets,
    beneficiary: Location,
}
```

Notes:

这个指令是一个命令。
它需要知道要转移哪些资产以及要将这些资产转移到哪个账户。

---

# XCVM寄存器

<diagram class="mermaid">
graph LR
    subgraph Registers[ ]
        Holding(Holding)
        Origin(Origin)
        More(...)
    end
</diagram>

Notes:

寄存器**就是**XCVM的状态。
请注意，它们是临时的/瞬态的。
我们将讨论`holding`和`origin`寄存器，但还有更多其他寄存器。

---v

## 📍 原点寄存器

包含一个`Option<Location>`，表示消息的跨共识起源位置。

Notes:

这个`Location`在程序执行过程中可能会发生变化。

它可能是`None`，因为某些指令会清除原点寄存器。

---v

### 💸 持有寄存器

表示XCM执行过程中控制的一些资产的数量，这些资产没有链上表示。

它们不属于任何账户。

可以将其视为持有“未花费资产”的寄存器。

---

# 基本XCVM操作

<diagram class="mermaid">
graph LR
    subgraph Program
        WithdrawAsset-->BuyExecution
        BuyExecution-->DepositAsset
        DepositAsset
    end
    Program-.->Fetch
    Fetch(Fetch Instruction)-->Execute(Execute instruction)
    Execute-->Fetch
    Execute-.->Registers
    subgraph Registers
        Holding(Holding)
        Origin(Origin)
        More(...)
    end
</diagram>

Notes:

XCVM从程序中获取指令并逐个执行。

---v

## XCVM与标准状态机的对比

<pba-flex center>

1. 错误**处理程序**寄存器
2. 附录寄存器

Notes:

1. 在XCM程序失败或出错时执行的代码。
   无论结果如何，当程序完成时，错误处理程序寄存器都会被清除。
   这确保了前一个程序的错误处理逻辑不会影响任何附加代码（即错误处理程序寄存器中的代码不会无限循环，附录寄存器中的代码无法访问错误处理程序中代码执行的结果）。
2. 无论XCM程序的执行结果如何都会执行的代码。

---v

## 更完整的XCVM操作

<diagram class="mermaid">
graph LR
    subgraph Program
        WithdrawAsset-->BuyExecution
        BuyExecution-->DepositAsset
        DepositAsset
    end
    Program-.->Fetch
    Fetch(Fetch Instruction)-->Execute(Execute instruction)
    Execute-->Fetch
    Execute-.->Registers
    subgraph Registers
        Holding(Holding)
        Origin(Origin)
        ErrorRegister(Error)
        ErrorHandler(Error Handler)
        AppendixRegister(Appendix)
        More(...)
    end
    Execute-- Error -->Error(Error Handler)
    Error-.->ErrorHandler
    Error-.->ErrorRegister
    Error-->Appendix
    Appendix-.->AppendixRegister
    Execute-->Appendix
</diagram>

---

# 💁 XCM示例

---v

## `WithdrawAsset`指令

<pba-flex center>

```rust
enum Instruction {
    /* snip */
    WithdrawAsset(Assets),
    /* snip */
}
```

Notes:

有许多指令可以将资产放置到持有寄存器中。
一个非常简单的指令是`WithdrawAsset`指令。

它从原点寄存器中指定位置的账户中提取一些资产。
但它对这些资产做了什么呢？
如果这些资产没有被存入任何地方，那么这就是一个相当无用的操作。
这些资产会被保存在持有寄存器中，直到对它们进行某些操作，例如使用以下指令。

---v

## `BuyExecution`指令

<pba-flex center>

```rust
enum Instruction {
    /* snip */
    BuyExecution {
        fees: Asset,
        weight_limit: WeightLimit,
    },
    /* snip */
}
```

Notes:

这个指令使用持有寄存器中指定的资产来为后续指令的执行购买权重。
它用于需要支付费用的系统中。

`weight_limit`是一个合理性检查，以确保如果购买的权重超过该限制，执行会出错。
权重的估计必须来自使用接收方的权重计算器，而不是发送方的。
接收方是实际执行消息的一方。

---v

## `DepositAsset`指令

<pba-flex center>

```rust
enum Instruction {
    /* snip */
    DepositAsset {
        assets: AssetFilter,
        beneficiary: Location,
    },
    /* snip */
}
```

Notes:

从持有寄存器中取出资产并将其存入受益人账户。
通常，之前会执行一个将资产放入持有寄存器的指令。

---v

## 综合运用

<pba-flex center>

```rust
Xcm(vec![
    WithdrawAsset((Here, amount).into()),
    BuyExecution {
        fees: (Here, amount).into(),
        weight_limit: Limited(sanity_check_weight_limit)
    },
    DepositAsset { assets: All.into(), beneficiary: AccountId32 { ... }.into() },
])
```

Notes:

这些幻灯片中的所有示例都使用最新的XCM版本。

---v

## 良好的模式

<pba-flex center>

```rust
Xcm(vec![
    WithdrawAsset((Here, amount).into()),
    BuyExecution {
        fees: (Here, amount).into(),
        weight_limit: Limited(sanity_check_weight_limit)
    },
    DepositAsset { assets: All.into(), beneficiary: AccountId32 { ... }.into() },
    RefundSurplus,
    DepositAsset { assets: All.into(), beneficiary: sender }
])
```

---

# 储备资产转移

<pba-flex center>

```rust
Xcm(vec![
    WithdrawAsset(asset),
    InitiateReserveWithdraw {
        assets: All.into(),
        reserve: reserve_location,
        xcm: /* ...如何处理储备中的资金... */,
    },
])
```

Notes:

此消息在本地执行。
然后，会向`reserve`位置发送一条消息。
该消息包含提供的自定义`xcm`以及其他指令。

---v

## 储备收到的消息

<pba-flex center>

```rust
Xcm(vec![
    WithdrawAsset(reanchored_asset),
    ClearOrigin, // <- 为什么需要这个？
    /* ...自定义指令... */
])
```

Notes:

这是储备收到的消息。

`ClearOrigin`指令会删除原点寄存器的内容。
这是必要的，因为我们不相信原点可以做除了转移自己的资产之外的任何事情。

---v

## 自定义XCM

<pba-flex center>

```rust
let xcm_for_reserve = Xcm(vec![
    DepositReserveAsset {
        assets: All.into(),
        dest: location,
        xcm: Xcm(vec![
            DepositAsset {
                assets: All.into(),
                beneficiary: AccountId32 { ... }.into(),
            },
        ]),
    },
]);
```

Notes:

对于简单的储备资产转移，此消息将起作用。

---v

## 目的地收到的消息

<pba-flex center>

```rust
Xcm(vec![
    ReserveAssetDeposited(reanchored_asset),
    ClearOrigin, // <- 为什么需要这个？
    /* ...自定义指令... */
])
```

Notes:

如果这里没有`ClearOrigin`，一个非常明显的漏洞就是从目的地的储备主权账户中抽走所有资金。
目的地不能完全相信储备可以代表源，只能相信其代表资产。

---

# 总结

<pba-flex center>

- XCVM
- 指令类型
- 寄存器
  - 原点
  - 持有
  - 错误处理程序
  - 附录
- 指令
  - WithdrawAsset、BuyExecution、DepositAsset
  - RefundSurplus
  - InitiateReserveWithdraw、ReserveAssetDeposited

---v

## 下一步

<pba-flex center>

1. 介绍XCM的博客系列：第[1](https://medium.com/polkadot-network/xcm-the-cross-consensus-message-format-3b77b1373392)、[2](https://medium.com/polkadot-network/xcm-part-ii-versioning-and-compatibility-b313fc257b83)和[3](https://medium.com/polkadot-network/xcm-part-iii-execution-and-error-management-ceb8155dd166)部分。
2. XCM格式[仓库](https://github.com/paritytech/xcm-format)
3. XCM [文档](https://paritytech.github.io/xcm-docs/)
