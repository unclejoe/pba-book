---
title: XCM in Polkadot # Also update the h1 header on the first slide to the same name
description: XCM in the Polkadot Context for web3 builders
duration: 1 hour
---

# Polkadot中的XCM

---

## _在本次讲座结束时，你将能够：_

<pba-flex center>

- 理解Rococo链的配置
- 在平行链A和Rococo之间发送真实的消息
- 识别XCM消息中的潜在错误

---

## 🤔 注意事项

- 除非有明确要求，否则不应假设链之间存在信任关系。
- 我们不能假设链不会进行恶意操作。
- 发送大量XCM消息会造成拒绝服务（DoS）问题。

---

## 🛠️ Rococo配置

- 障碍（Barriers）
- 传送过滤（Teleport filtering）
- 可信储备（Trusted reserves）
- 资产交易员（Asset transactors）
- 费用支付（Fee payment）
- 正确的XCM指令权重（Proper XCM Instruction Weighting）
- 位置到账户/FRAME起源转换（Location to Account/FRAME Origin conversions）

Notes:

从现在开始，我们将以Rococo运行时作为参考。Rococo是Polkadot和Kusama的测试网，我们将使用它来测试我们的XCM消息。Rococo的大多数配置与Polkadot中的配置相同。

---

## 🚧 Rococo中的XCM `Barrier`

```rust
/// 对于一个XCM消息要被执行，必须通过其中一个障碍。
pub type Barrier = (
  // 支付的权重可以被消耗。
  TakeWeightCredit,
  // 如果消息是立即尝试支付执行费用的消息，则允许执行。
  AllowTopLevelPaidExecutionFrom<Everything>,
  // 来自系统平行链的消息无需支付执行费用。
  AllowUnpaidExecutionFrom<IsChildSystemParachain<ParaId>>,
  // 预期的响应是可以的。
  AllowKnownQueryResponses<XcmPallet>,
  // 用于版本跟踪的订阅是可以的。
  AllowSubscriptionsFrom<Everything>,
);
```

---v

## 🚧 Rococo中的XCM `Barrier`

- `TakeWeightCredit` 和 `AllowTopLevelPaidExecutionFrom` 用于防止本地/远程XCM执行时的垃圾消息攻击。
- `AllowUnpaidExecutionFrom` 允许系统平行链在中继链中免费执行。
- `AllowKnownQueryResponses` 和 `AllowSubscriptionsFrom` ，正如我们已经知道的，主要用于版本控制。

Notes:

- 子系统平行链是包含核心Polkadot功能的平行链，它们的ParaId将小于1000。它们由Polkadot治理分配，并可免费执行。
- `AllowKnownQueryResponses` 将检查pallet-xcm存储以确定响应是否是预期的。
- `AllowSubscriptionsFrom` 确定任何来源都能够订阅版本更改。

---

## 🤝 Rococo中的可信传送器（Trusted teleporters）

```rust [0|2|6-8|18-22]
parameter_types! {
  pub const RocLocation: MultiLocation = Here.into();
  pub const Rococo: MultiAssetFilter =
    Wild(AllOf { fun: WildFungible, id: Concrete(RocLocation::get()) });

  pub const AssetHub: MultiLocation = Parachain(1000).into();
  pub const Contracts: MultiLocation = Parachain(1002).into();
  pub const Encointer: MultiLocation = Parachain(1003).into();

  pub const RococoForAssetHub: (MultiAssetFilter, MultiLocation) =
    (Rococo::get(), AssetHub::get());
  pub const RococoForContracts: (MultiAssetFilter, MultiLocation) =
    (Rococo::get(), Contracts::get());
  pub const RococoForEncointer: (MultiAssetFilter, MultiLocation) =
    (Rococo::get(), Encointer::get());
}

pub type TrustedTeleporters = (
  xcm_builder::Case<RococoForAssetHub>,
  xcm_builder::Case<RococoForContracts>,
  xcm_builder::Case<RococoForEncointer>,
);
```

---v

## 🤝 Rococo中的可信传送器（Trusted teleporters）

- 传送涉及链之间的信任。
- 1000（资产中心）、1001（合约）和1002（Encointer）被允许传送由 **Here** 表示的代币。
- **Here** 表示中继链代币。

```rust
impl xcm_executor::Config for XcmConfig {
  /* snip */
  type IsTeleporter = TrustedTeleporters;
  /* snip */
}
```

Notes:

- 资产中心、Rococo和Encointer能够传送中继链代币。
- 任何其他链发送 `ReceiveTeleportedAsset` 或传送任何其他代币都将被拒绝，并返回 `UntrustedTeleportLocation` 错误。

---

## 💱 Rococo中的可信储备（Trusted reserves）

- Rococo不认可任何链作为储备。
- Rococo阻止接收任何 **ReserveAssetDeposited** 消息。

```rust
impl xcm_executor::Config for XcmConfig {
  /* snip */
  type IsReserve = ();
  /* snip */
}
```

Notes:

- 信任其他平行链（例如，公共利益平行链）作为中继链原生代币的储备会导致总发行量出现罕见情况。例如，用户可以用传送的资金耗尽主权账户的储备。

---

## 📁 Rococo中的 `LocationToAccountId`

- 从多位置（multilocation）到账户ID（AccountId）的转换是提取/存入资产和发出 `Transact` 操作的关键组件。
- 平行链起源将被转换为其对应的主权账户。
- 本地32字节起源将被转换为定义的32字节账户ID。

```rust
pub type LocationConverter = (
  // 我们可以使用标准的 `AccountId` 转换来转换子平行链。
  ChildParachainConvertsVia<ParaId, AccountId>,
  // 我们可以直接将 `AccountId32` 别名为本地账户。
  AccountId32Aliases<RococoNetwork, AccountId>,
);
```

Notes:

- 任何不是平行链起源或本地32字节账户起源的其他起源都无法转换为账户ID。
- 问题：如果来自平行链的消息以 `DescendOrigin` 开头会发生什么？
  XcmV2将在障碍级别拒绝它（因为 **_AllowTopLevelPaidExecutionFrom_** 期望第一条指令是 **_ReceiveTeleportedAsset_** 、 **_WithdrawAsset_** 、 **_ReserveAssetDeposited_** 或 **_ClaimAsset_** 之一），XcmV3将通过障碍，因为 **_AllowTopLevelPaidExecutionFrom_** 在 **_WithComputedOrigin_** 内部。

---

## 🪙 Rococo中的资产交易员（Asset Transactors）

<div style="font-size: smaller">

```rust
pub type LocalAssetTransactor = XcmCurrencyAdapter<
  // 使用此货币：
  Balances,
  // 当它是与给定位置或名称匹配的可替代资产时，使用此货币：
  IsConcrete<RocLocation>,
  // 我们可以使用上面的转换器转换多位置：
  LocationConverter,
  // 我们链的账户ID类型（我们不能不明确提及它）：
  AccountId,
  // 它是原生资产，因此我们跟踪传送以维持总发行量。
  CheckAccount,
>;

impl xcm_executor::Config for XcmConfig {
  /* snip */
  type AssetTransactor = LocalAssetTransactor;
  /* snip */
}
```

</div>

---v

## 🪙 Rococo中的 `asset-transactors`

- Rococo中只有一个资产交易员。
- 资产交易员将 **Here** 多位置ID与 **Balances** 中定义的货币相匹配，**Balances** 指的是 **_pallet-balances_** 。
- 本质上，这是在配置XCM，使得原生代币（DOT）与多位置 **Here** 相关联。

Notes:

- Rococo在 **CheckAccount** 中跟踪传送，**CheckAccount** 在 **palletXcm** 中定义。这旨在即使资产已传送到另一个链，也能维持总发行量。

---

## 📍 Rococo中的 `origin-converter`

```rust
type LocalOriginConverter = (
  // 使用 `LocationConverter` 转换为签名起源
  SovereignSignedViaLocation<LocationConverter, RuntimeOrigin>,
  // 将子平行链多位置转换为平行链起源
  ChildParachainAsNative<parachains_origin::Origin, RuntimeOrigin>,
  // 将本地32字节多位置转换为签名起源
  SignedAccountId32AsNative<WestendNetwork, RuntimeOrigin>,
  // 将系统平行链起源转换为根起源
  ChildSystemParachainAsSuperuser<ParaId, RuntimeOrigin>,
);
```

```rust
impl xcm_executor::Config for XcmConfig {
  /* snip */
  type OriginConverter = LocalOriginConverter;
  /* snip */
}
```

---v

## 📍 Rococo中的 `origin-converter`

- 定义了我们可以将多位置转换为调度起源的方式，通常由 `Transact` 指令使用：
- 子平行链起源通过 **_LocationConverter_** （`OriginKind == Sovereign`）转换为签名起源。
- 子平行链也可以转换为原生平行链起源（`OriginKind == Native`）。
- 本地32字节起源转换为签名32字节起源（`OriginKind == Native`）。

Notes:

- 存在“平行链调度起源”的概念，它用于非常特定的功能（例如，与另一个链打开通道）。这通过 _ensure_parachain!_ 宏进行检查。
- 系统平行链能够以根起源进行调度，因为它们可以被视为Rococo运行时本身的扩展。

---

### 🏋️ Rococo中的 `Weigher`

- 使用带有 `pallet-xcm-benchmarks` 基准值的 `WeightInfoBounds` 。
- 权重的完整列表可以在[这里](https://github.com/paritytech/polkadot-sdk/tree/cc9f812/polkadot/runtime/rococo/src/weights/xcm)查看。

```rust
impl xcm_executor::Config for XcmConfig {
  /* snip */
type Weigher = WeightInfoBounds<
		crate::weights::xcm::RococoXcmWeight<RuntimeCall>,
		RuntimeCall,
		MaxInstructions,
	>;
 /* snip */
}
```

---

## 🔧 Rococo中的 `WeightTrader`

- 权重通过 **_WeightToFee_** 类型转换为费用。
- 我们收取费用的资产是 **_RocLocation_** 。这意味着我们只能使用原生货币支付XCM执行费用。
- 费用将通过 **_ToAuthor_** 支付给区块作者。

```rust
impl xcm_executor::Config for XcmConfig {
  /* snip */
  type Trader = UsingComponents<
    WeightToFee,
	RocLocation,
	AccountId,
	Balances,
	ToAuthor<Runtime>>;
  /* snip */
}
```

Notes:

- 尝试使用与指定的AssetId（在这种情况下，`RocLocation` 表示原生代币）不匹配的任何其他代币进行 `buyExecution` **将失败**。
- **_WeightToFee_** 包含一个关联函数，该函数将用于将所需的权重数量转换为用于执行支付的代币数量。

---

## 🎨 Rococo中的 `XcmPallet`

```rust
impl pallet_xcm::Config for Runtime {
  /* snip */
  type XcmRouter = XcmRouter;
  type SendXcmOrigin =
    xcm_builder::EnsureXcmOrigin<RuntimeOrigin, LocalOriginToLocation>;
  // 任何人都可以在本地执行XCM消息。
  type ExecuteXcmOrigin =
    xcm_builder::EnsureXcmOrigin<RuntimeOrigin, LocalOriginToLocation>;
  type XcmExecuteFilter = Everything;
  type XcmExecutor = xcm_executor::XcmExecutor<XcmConfig>;
  // 任何人都可以使用传送功能，无论他们是谁以及他们想要传送什么。
  type XcmTeleportFilter = Everything;
  // 任何人都可以使用储备转移功能，无论他们是谁以及他们想要转移什么。
  type XcmReserveTransferFilter = Everything;
  /* snip */
}
```

---v

## 🎨 Rococo中的XcmPallet

- 对执行、传送或储备转移的消息没有过滤。
- 只有由 **_LocalOriginToLocation_** 定义的起源才允许发送/执行任意消息。
- **_LocalOriginToLocation_** 定义为允许理事会和常规的32字节账户签名起源调用。

```rust
pub type LocalOriginToLocation = (
  // 我们允许来自Collective pallet的起源在XCM中用作相应的 `Unit` 主体的多数派。
  CouncilToPlurality,
  // 并且通常的签名起源可以在XCM中用作相应的AccountId32。
  SignedToAccountId32<RuntimeOrigin, AccountId, RococoNetwork>,
);
```

Notes:

- **_LocalOrigin_** 允许从FRAME调度起源转换为多位置。这是必要的，因为 **我们使用XCM起源进入xcm-executor，而不是使用FRAME调度起源** 。请注意，这是FRAME pallet中的一个外在调用，因此 **我们使用FRAME起源调用它** 。
- 理事会决策被转换为 `Plurality` 连接多位置。
- 签名起源被转换为 `AccountId32` 连接多位置。

---
```
