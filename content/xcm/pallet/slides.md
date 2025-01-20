---
title: XCM Pallet
description: Introduction to `pallet-xcm`, its interface and features implemented here.
duration: 1 hour
---

# XCM Pallet

---

#### 课程目标：

- 理解该Pallet的接口及其实现。
- 版本发现是如何工作的。
- 接收响应是如何工作的。
- 理解如何在 FRAME Pallet中编写 XCM。

---

## XCM Pallet

我们现在已经了解了 XCVM 和 FRAME。

XCM Pallet是 XCVM 子系统和 FRAME 子系统之间的桥梁。

**它还允许我们发送/执行 XCM 并与 XCM 执行器进行交互**。

---

## XCM 的预期使用方式

XCM 并非供终端用户编写。

相反，**开发者** 编写 XCVM 程序，并将其打包成 FRAME 外部调用。

Notes:

钱包提供商如何使用 XCM ？

在探索 `teleport_assets` 和 `reserve_transfer_assets` 外部调用时，我们将看到在运行时构建 XCM 的示例。

---

### `pallet-xcm` 的关键作用

<pba-flex center>

1. 允许通过执行 XCM 消息与 `xcm-executor` 进行交互。
   这些消息可以通过 `XcmExecuteFilter` 进行过滤。
1. 允许为某些来源向其他链发送任意消息。
   允许发送消息的来源可以通过 `SendXcmOrigin` 进行过滤。
1. 提供了一个更简单的接口来进行基于储备的转移和传送。
   能够执行这些操作的来源可以通过 `XcmTeleportFilter` 和 `XcmReserveTransferFilter` 进行过滤。
1. 处理 XCM 版本协商职责。
1. 处理资产陷阱/认领职责。
1. 以及 XCVM 的其他基于状态的要求。

</pba-flex>

Notes:

- 即使 XCM Pallet允许任何 FRAME 来源发送 XCM，它也会区分根调用和其他任何来源的调用。
  在后一种情况下，它会附加 `DescendOrigin` 指令，以确保非根来源不能代表平行链行事。

---

## XCM Pallet

`pallet-xcm` 为 `XcmConfig` 所需的许多特征提供了默认实现。

`pallet-xcm` 还提供了一个包含 10 个不同外部调用的接口，这些外部调用可以分为三类：

- 用于本地 `execute` 或 `send` 一个 XCM 的基本函数。
- 用于系统间资产转移的高级函数，例如传送和储备资产转移。
- 专门用于版本协商的外部调用。

---

## `pallet-xcm` 基本外部调用

- `execute`

  直接访问 XCM 执行器。
  代表 FRAME 的签名来源执行。

<diagram class="mermaid limit size-40">
flowchart TD
subgraph paraA[平行链 A              .]
  executor --"成功？"--> palletxcm
  palletxcm("pallet-xcm") --"execute"--> executor("xcm-executor")
end
execute("execute(xcm)") --> palletxcm
</diagram>

Notes:

它会检查来源，以确保配置的 `SendXcmOrigin` 过滤器不会阻止执行。
它会在本地执行该消息，并将结果作为一个事件返回。

---v

## `pallet-xcm` 基本外部调用

- `send`

向提供的目的地发送一条消息。

<diagram class="mermaid" style="display: flex; width: 150%; justify-content: center; transform: translateX(-17%);">
flowchart LR
subgraph paraA[平行链 A]
  palletxcma("pallet-xcm") --"deliver"--> routera("xcm-router")
  routera --> mqueuea("消息队列")
end
subgraph paraB[平行链 B]
mqueueb("消息队列") --> executorb("xcm-executor")
end
send("send(xcm)") --> palletxcma
mqueuea --> mqueueb
</diagram>

Notes:

这个外部调用是一个向目的地发送消息的函数。
它会检查来源、目的地和消息。
然后它会让 `XcmRouter` 处理消息的转发。

---

## `pallet-xcm` 资产转移外部调用

<pba-cols>
<pba-col>
<img rounded style="width: 400px;" src="../intro/img/teleport.png" />
</pba-col>

<pba-col>
<img rounded style="width: 400px;" src="../intro/img/reserve-tx.png" />
</pba-col>
</pba-cols>

Notes:

我们在第 7.1 课中已经了解了传送和储备转移的含义；这里快速回顾一下。

---v

## `pallet-xcm` 资产转移外部调用

`limited_teleport_assets`

这个外部调用允许用户执行资产传送。

<diagram class="mermaid">
flowchart LR
subgraph paraA[平行链 A]
  palletxcma("pallet-xcm") --"1. 执行"--> executora("xcm-executor")
  executora --"发送"--> sendera("xcm-sender")
end
subgraph tdestination[可信目的地]
end
lteleport("limited_teleport_assets(\n  dest,\n  beneficiary,\n  assets,\n  fee_asset_item,\n  weight_limit\n)"):::left --> palletxcma
sendera --"2."--> tdestination
classDef left text-align:left
</diagram>

---v

### `limited_teleport_assets`

<ol>

<li> `pallet-xcm` 组合以下 XCM 并将其传递给 `xcm-executor`</li>

```rust
Xcm(vec![
  WithdrawAsset(assets),
  SetFeesMode {jit_withdraw: true},
  InitiateTeleport {assets: Wild(AllCounted(max_assets)), dest, xcm},
])
```

<li>平行链 A 然后向可信目的地发送以下消息</li>

```rust
Xcm(vec![
  ReceiveTeleportedAsset(assets),
  ClearOrigin,
  BuyExecution { fees, weight_limit },
  DepositAsset { assets: Wild(AllCounted(max_assets)), beneficiary },
])
```

</ol>

---v

## `pallet-xcm` 资产转移外部调用

`limited_reserve_transfer_assets`

允许用户从储备链向目的地执行储备支持的转移。

<diagram class="mermaid" style="display: flex; width: 150%; justify-content: center; transform: translateX(-17%);">
flowchart LR
subgraph reserve[储备链]
  palletxcma("pallet-xcm") --"1. 执行"--> executora("xcm-executor")
  executora --"发送"--> sendera("xcm-sender")
end
subgraph destination[目的地]
end
lteleport("limited_reserve_transfer_assets(\n  dest,\n  beneficiary,\n  assets,\n  fee_asset_item,\n  weight_limit\n)"):::left --> palletxcma
sendera --"2."--> destination
classDef left text-align:left
</diagram>

---v

### `limited_reserve_transfer_assets`

<ol>

<li> `pallet-xcm` 组合以下 XCM 并将其传递给 `xcm-executor`</li>

```rust
Xcm(vec![
  SetFeesMode { jit_withdraw: true },
  TransferReserveAsset { assets, dest, xcm },
])
```

<li>储备链然后向目的地发送以下消息</li>

```rust
Xcm(vec![
  ReserveAssetDeposited(assets),
  ClearOrigin,
  BuyExecution { fees, weight_limit },
  DepositAsset { assets: Wild(AllCounted(max_assets)), beneficiary },
])
```

</ol>

---

## 🗣️ 使用 `pallet-xcm` 进行版本协商

XCM 是一种**版本化的消息格式**。

一个版本可能包含比另一个版本更多或不同的指令，因此对于各方通过 XCM 进行通信来说，了解对方使用的版本非常重要。

版本订阅机制允许各方订阅来自其他方的版本更新。

<pba-flex center>

```rust
pub enum VersionedXcm {
  V2(v2::Xcm),
  V3(v3::Xcm),
}
```

Notes:

- 随着 XCM v3 的加入，V0 和 V1 已被移除。

---v

## 🗣️ 使用 `pallet-xcm` 进行版本协商

但链需要知道彼此支持的版本。
`SubscribeVersion` 和 `QueryResponse` 在这里起着关键作用：

<pba-flex center>

```rust
enum Instruction {
  // --snip--
  SubscribeVersion {
        query_id: QueryId,
        max_response_weight: u64,
  },
  QueryResponse {
        query_id: QueryId,
        response: Response,
        max_weight: u64,
  },
  // --snip--
}
```

Notes:

- `query_id` 在 `SubscribeVersion` 和 `QueryResponse` 指令中应该是相同的。
- 同样，`max_response_weight` 也应该与响应中的 `max_weight` 匹配。

---v

## 🗣️ 使用 `pallet-xcm` 进行版本协商

- `ResponseHandler`：负责处理来自其他链的响应消息的组件。
- `SubscriptionService`：负责处理向其他链发送版本订阅通知的组件。

<pba-flex center>

```rust
impl Config for XcmConfig {
 // --snip--
 type ResponseHandler = PalletXcm;
 type SubscriptionService = PalletXcm;
}
```

Notes:

- `PalletXcm` 在收到响应时会跟踪每个链的版本。
- 它还会跟踪每当我们更改版本时需要通知的链。

---

## 订阅服务

任何系统都可以在另一个系统更改其最新支持的 XCM 版本时得到通知。
这是通过 `SubscribeVersion` 和 `UnsubscribeVersion` 指令实现的。

`SubscriptionService` 类型定义了在处理 `SubscribeVersion` 指令时应采取的操作。

Notes:

`pallet-xcm` 提供了这个特征的默认实现。
当收到 `SubscribeVersion` 时，链会发送一个包含 `QueryResponse` 指令的 XCM，其中包含其当前版本。

---

## 版本协商

订阅服务利用两个系统之间的任何 XCM 交换来开始版本协商过程。

每次系统需要向一个支持的 XCM 版本未知的目的地发送消息时，其位置将存储在 `VersionDiscoveryQueue` 中。
然后，在下一个区块中会检查这个队列，并向队列中存在的那些位置发送 `SubscribeVersion` 指令。

Notes:

SubscribeVersion - 指示本地系统在前者的 XCM 版本升级或降级时通知发送方。
UnsubscribeVersion - 如果发送方之前订阅了本地系统的 XCM 版本更改通知，则此指令告诉本地系统停止在版本更改时通知发送方。

---v

## 🗣️ XCM 版本协商

XCM 版本协商：
<pba-flex center>

1. 链 A 向链 B 发送 `SubscribeVersion`。
1. 链 B 向链 A 发送 `QueryResponse`，使用相同的 `query_id` 和 `max_weight` 参数，并在响应中放入 XCM 版本。
1. 链 A 将链 B 支持的版本存储在存储中。
1. 从链 B 到链 A 也会进行相同的过程。
1. 使用双方都支持的最高版本建立通信。

---v

## 🗣️ XCM 版本协商

在以下场景中，链 A 使用 XCM v2

<diagram class="mermaid limit size-80">
flowchart BT
subgraph registryA[链 A 的注册表]
  chainB("链 B \n\n v2")
  chainC("链 C \n\n v3")
  chainD("链 D \n\n v1")
  chainE("链 E \n\n v3")
end
</diagram>

<diagram class="mermaid limit size-70">
flowchart LR
chainARequest("链 A") --"链 E ？ \n\n v2"--> chainERequest("链 E")
</diagram>

---

## 响应处理器

版本协商只是一条链可以向另一条链发出的众多查询类型中的一个示例。
无论进行的是哪种查询，响应通常都采用 `QueryResponse` 指令的形式。

---v

## 响应处理器

我们已经讨论过 XCM 是不对称的，那么为什么会有响应呢？

---v

## 信息报告

每个用于信息报告的指令都包含 `QueryResponseInfo`。

<pba-flex center>

```rust
pub struct QueryResponseInfo {
    pub destination: MultiLocation,
    pub query_id: QueryId,
    pub max_weight: Weight,
}
```

Notes:

所有信息报告指令都包含一个 `QueryResponseInfo` 结构体，其中包含有关响应的预期目的地、查询的 ID 以及可调度调用函数可以使用的最大权重的信息。

---v

## 信息检索
<pba-flex center>
```rust
enum Instruction {
    // --略--
    QueryResponse {
        query_id: QueryId,
        response: Response,
        max_weight: Weight,
        querier: Option<MultiLocation>,
    },
    // --略--
}
```
Notes:

上述指令用于提供本地系统期望的某些请求信息。

应该检查`querier`参数，以确保请求信息的系统与预期相符。
---

## 使用`pallet-xcm`处理资产陷阱/认领
在执行完所有指令后，如果持有寄存器中仍有资金会怎样？

在XCM消息执行完毕后，只要持有寄存器中还包含资产，就会导致资产陷阱。

这些被“困住”的资产需要被**存储**起来，以便未来认领，FRAME为我们提供了相关方法。

Notes:

- 这在xcm - 执行器的`post_execute`函数中处理。
---v

## 使用`pallet-xcm`处理资产陷阱/认领
- **`pallet-xcm`资产陷阱处理**: 被陷阱困住的资产存储在`AssetTraps`存储项中，并按来源和资产进行索引。
- **`pallet-xcm`资产认领**: `pallet-xcm`也允许认领被陷阱困住的资产，但需满足以下条件：
  - 认领资产的来源与导致资产被困住的来源相同。
  - 被认领的`Asset`与被困住的资产相同。

Notes:
- `AssetTraps`中的每个映射元素都保存着一个计数器，记录该来源困住该`Asset`的次数。
- 每次该`Asset`被重新认领时，计数器就会减一。
---

## 外部调用解析
让我们深入代码，看看`limited_teleport_assets`外部调用。
[查看源代码🔍](https://github.com/paritytech/polkadot-sdk/blob/342d720/polkadot/xcm/pallet-xcm/src/lib.rs#L1099)
---

## 总结
在本课程中，我们学习到：
- XCM模块是什么以及它的用途。
- 钱包开发者和运行时开发者预期如何使用XCM。
- XCM模块中有用的外部调用。
- XCM版本控制的工作原理。
- 如何使用XCM模块接收响应。
- 资产可能如何被困住，以及如何使用XCM模块认领它们。 