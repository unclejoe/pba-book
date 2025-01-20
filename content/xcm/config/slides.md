---
title: Parachain XCM Configuration # Also update the h1 header on the first slide to the same name
description: XCM configuration overview and considerations, for parachains.
duration: 1 hour
---

# 平行链 XCM 配置

---v

## _在本讲座结束时，你将能够：_

<pba-flex center>

- 理解链的不同 XCM 可配置部分
- 为有不同需求的链构建不同的 XCM 配置

---

## 🛠️ `XcmConfig` 中的可配置项

Notes:

XCM 配置有许多可配置项

练习：让同学们举手并推测他们认为哪些内容应该是可配置的。

---v

## 🛠️ `XcmConfig` 中的可配置项

```rust [1-2|6-7|8-9|10-11|12-13|14-15|16-31]
// 我们如何将位置转换为账户 ID
type SovereignAccountOf = SovereignAccountOf;

pub struct XcmConfig;
impl Config for XcmConfig {
  // 当前系统的绝对位置
  type UniversalLocation = UniversalLocation;
  // 预执行过滤器
  type Barrier = Barrier;
  // 我们如何提取/存入资产
  type AssetTransactor = LocalAssetTransactor;
  // 我们如何将位置转换为 FRAME 调度原点
  type OriginConverter = LocalOriginConverter;
  // 我们如何将 XCM 路由到本链之外
  type XcmSender = XcmRouter;
  // 我们信任哪些链作为储备链
  type IsReserve = ?;
  // 我们信任哪些链作为传送器
  type IsTeleporter = ?;
  // 我们如何对消息进行加权
  type Weigher = ?;
  // 我们如何收取费用
  type Trader = ?;
  // 我们如何处理响应
  type ResponseHandler = ?;
  // 我们如何处理资产陷阱
  type AssetTrap = ?;
  // 我们如何处理资产声明
  type AssetClaims = ?;
  // 我们如何处理版本订阅
  type SubscriptionService = ?;
}
```

Notes:

- `SovereignAccountOf`：将 `Location` 转换为账户 ID 的方法
  稍后用于：`OriginConverter` 、`AssetTransactor`

- `xcm-pallet` 是一个不仅允许发送和执行 XCM 消息的模块，而且它还实现了几个配置特征，因此可以用于执行几个 XCM 配置操作。

---v

## 🛠️ `xcm-builder`

`xcm-builder` 是一个包含常见配置项的 crate，用于方便 XCM 配置。

大多数预构建的配置项可以在 `xcm-builder` 中找到。

它允许在 FRAME 中使用 XCM 执行器。

---

### 🤔 在开始之前了解你的链的需求

你应该有答案的问题：

- _我的链是否只传输原生代币？_
  _我的链是否会接收几种其他类型的资产？_

- _我的链是否允许免费执行？_
  _也许仅限于某些平行链/中继链？_

- _我的链是 20 字节账户链吗？_
  _是 32 字节账户链吗？_

- _我的链将如何接受费用支付？_
  _用一种资产？_
  _用多种资产？_

Notes:

- 这些问题的一些答案可能意味着你需要使用自己的自定义原语。

---v

### 我们的起始示例设置需求

1. 不向中继链传入的消息收取费用的平行链。
2. 信任中继链作为中继链代币的储备链的平行链。
3. 当接收到中继链代币时在 `pallet-balances` 中进行铸币的平行链。
4. 用户可以在本地执行 XCM。

---

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- 定义我们如何将 `Location` 转换为本地账户 ID。
- 当我们想从 `Location` 定义的原点提取/存入代币时很有用
- 当我们想从 `Location` 定义的原点作为签名原点进行调度时很有用。

<diagram class="mermaid">
graph TD;
  Location("AccountId32 { id: [18, 52, ..., 205, 239], network: Some(Rococo) }")-- SovereignAccountOf -->Account("0x123..def (Alice)")
</diagram>

Notes:

- 这将定义我们如何将 `Location` 转换为本地账户 ID。
- 当我们想从 `Location` 定义的原点提取/存入代币或当我们想从 `Location` 定义的原点作为签名原点进行调度时，这很有用。

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `HashedDescription`：对 `Location` 的描述进行哈希处理，并将其转换为 `AccountId`。

```rust
pub struct HashedDescription<AccountId, Describe>(PhantomData<(AccountId, Describe)>);
impl<
  AccountId: From<[u8; 32]> + Clone,
  Describe: DescribeLocation
> ConvertLocation<AccountId> for HashedDescription<AccountId, Describe>
{
	fn convert_location(value: &Location) -> Option<AccountId> {
		Some(blake2_256(&Describe::describe_location(value)?).into())
	}
}
```

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `HashedDescription`。一个转换器定义的示例：

<pba-flex center>

```rust
pub type LocationToAccount =
  HashedDescription<AccountId, (
    LegacyDescribeForeignChainAccount, // 遗留转换 - 必须放在首位！
    DescribeTerminus,
    DescribePalletTerminal
  )>;
```

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `DescribeLocation`：将位置转换为稳定且唯一的描述性标识符的方法。

```rust
pub trait DescribeLocation {
	/// 如果可能，为给定的 `location` 创建一个描述。没有两个位置应该有相同的描述符。
	fn describe_location(location: &Location) -> Option<Vec<u8>>;
}
```

Notes:

[元组的实现](https://github.com/paritytech/polkadot-sdk/blob/342d720/polkadot/xcm/xcm-builder/src/location_conversion.rs#L34)

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `DescribeAccountId32Terminal`

```rust
fn describe_location(l: &Location) -> Option<Vec<u8>> {
	match (l.parents, &l.interior) {
		(0, X1(AccountId32 { id, .. })) => Some((b"AccountId32", id).encode()),
		_ => return None,
	}
}
```

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `DescribeTerminus`

```rust
fn describe_location(l: &Location) -> Option<Vec<u8>> {
	match (l.parents, &l.interior) {
		(0, Here) => Some(Vec::new()),
		_ => return None,
	}
}
```

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `DescribePalletTerminal`

```rust
fn describe_location(l: &Location) -> Option<Vec<u8>> {
	match (l.parents, &l.interior) {
		(0, X1(PalletInstance(i))) =>
			Some((b"Pallet", Compact::<u32>::from(*i as u32)).encode()),
		_ => return None,
	}
}
```

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `DescribeAccountKey20Terminal`

```rust
fn describe_location(l: &Location) -> Option<Vec<u8>> {
	match (l.parents, &l.interior) {
		(0, X1(AccountKey20 { key, .. })) => Some((b"AccountKey20", key).encode()),
		_ => return None,
	}
}
```

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `AccountId32Aliases`：将本地 `AccountId32` `Location` 转换为 32 字节的账户 ID。

- `Account32Hash`：对 `Location` 进行哈希处理，并取最低的 32 字节作为账户。

- `ParentIsPreset`：将父级 `Location` 转换为 `b'Parent' + 尾随的 0s` 形式的账户。

---v

### 📁 通过 `xcm-builder` 实现的 `SovereignAccountOf`

- `ChildParachainConvertsVia`：将**子级**平行链 `Location` 转换为 `b'para' + para_id_as_u32 + 尾随的 0s` 形式的账户。

- `SiblingParachainConvertsVia`：将**同级**平行链 `Location` 转换为 `b'sibl' + para_id_as_u32 + 尾随的 0s` 形式的账户。

---

### `UniversalLocation`

正在配置的共识系统的绝对位置。

<pba-flex center>

```rust
parameter_types! {
  pub const UniversalLocation: InteriorLocation = GlobalConsensus(NetworkId::Polkadot).into();
}
```

---

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

- 屏障指定是否允许在本地共识系统上执行 XCM。
- 它们在实际执行 XCM 指令之前进行检查。
- **屏障不应涉及任何繁重的计算。**

Notes:

**在检查屏障时，还没有为其执行支付任何费用**。

---v

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

物理原点与计算原点

- 物理原点：构建此特定 XCM 并将其发送给接收方的共识系统
- 计算原点：最终指示共识系统构建 XCM 的实体

Notes:

如果一个外部账户（EOA）通过 XCM 转移一些资金，那么计算原点将是其账户，但物理原点将是所使用的平台（例如平行链）。

---v

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

对**计算原点**进行操作的屏障必须放在 `WithComputedOrigin` 内部。

允许在开头进行原点更改指令。

<pba-flex center>

```rust
pub struct WithComputedOrigin<InnerBarrier, LocalUniversal, MaxPrefixes>;
```

---v

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

- `TakeWeightCredit`：从可用的权重信用中减去消息可以消耗的最大权重。
  通常为本地 `xcm-execution` 配置。

---v

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

- `AllowTopLevelPaidExecutionFrom<T>`：对于包含在 `T` 中的原点，它确保第一条指令将资产放入持有寄存器，然后是一个能够购买足够权重的 `BuyExecution` 指令。
  **对于避免免费的拒绝服务攻击至关重要**。

Notes:

- 一个没有 `AllowTopLevelPaidExecutionFrom` 的链可能会收到几个无需付费的繁重计算指令。
  检查第一条指令是否确实为执行付费有助于快速丢弃它们。

- 虽然 `BuyExecution` 对于来自其他共识系统的消息至关重要，但本地 XCM 执行费用的支付方式与任何其他 Substrate 外部函数相同。

---v

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

- `AllowExplicitUnpaidExecutionFrom<T>`：如果 `origin` 包含在 `T` 中，并且第一条指令是 `UnpaidExecution`，则允许免费执行。

Notes:

- **这满足了我们的需求**
- 为了满足我们的示例用例，我们只需要中继链能够免费执行。

---v

### 🚧 通过 `xcm-builder` 实现的 `Barrier`

- `AllowKnownQueryResponses`：如果消息仅包含预期的 `QueryResponse`，则允许执行该消息。
- `AllowSubscriptionsFrom<T>`：如果发送消息的 `origin` 包含在 `T` 中，并且消息仅包含 `SubscribeVersion` 或 `UnsubscribeVersion` 指令，则允许执行该消息。

---

### 🪙 通过 `xcm-builder` 实现的 `AssetTransactor`

- 定义我们如何提取和存入资产
- 很大程度上取决于我们希望链转移的资产

<diagram class="mermaid">
graph LR
  Withdraw("WithdrawAsset((Here, 100u128).into())")-->DOT(100 个代币，例如来自 pallet-balances)
</diagram>

Notes:

- 中继链是一个处理**单一代币**的链的明显例子。
- 相反，AssetHub 充当资产储备链，它需要处理**多种资产**。

---v

### 🪙 通过 `xcm-builder` 实现的 `AssetTransactor`

- `FungiblesAdapter`：用于从一组定义的可替代代币中存入/提取。
  这些代币的一个例子是 `pallet-assets` 代币。
- `NonFungiblesAdapter`：用于存入/提取 NFT。例如 `pallet-nfts`。

Notes:

- **匹配器**：将 `Asset` 与一些过滤器进行匹配，并返回要存入/提取的数量
- **账户 ID 转换器**：将 `Location` 转换为账户的方法
- 对于我们的例子来说，使用“CurrencyAdapter”就足够了，因为我们要做的就是在收到中继令牌时铸造一种货币（余额）

---v

## 🪙 通过 `xcm-builder` 实现的 `AssetTransactor`
<pba-flex center>
```rust
fn withdraw_asset(
	what: &Asset,
	who: &Location,
	_maybe_context: Option<&XcmContext>,
) -> result::Result<xcm_executor::Assets, XcmError> {
	let (asset_id, amount) = Matcher::matches_fungibles(what)?;
	let who = AccountIdConverter::convert_location(who)
		.ok_or(MatchError::AccountIdConversionFailed)?;
	Assets::burn_from(asset_id, &who, amount, Exact, Polite)
		.map_err(|e| XcmError::FailedToTransactAsset(e.into()))?;
	Ok(what.clone().into())
}
```
---v
## 🪙 通过 `xcm-builder` 实现的 `AssetTransactor`
<pba-flex center>
```rust
fn deposit_asset(
  what: &Asset,
  who: &Location,
  _context: &XcmContext
) -> XcmResult {
	let (asset_id, amount) = Matcher::matches_fungibles(what)?;
	let who = AccountIdConverter::convert_location(who)
		.ok_or(MatchError::AccountIdConversionFailed)?;
	Assets::mint_into(asset_id, &who, amount)
		.map_err(|e| XcmError::FailedToTransactAsset(e.into()))?;
	Ok(())
}
```
---
## 📍 通过 `xcm-builder` 实现的 `OriginConverter`
- 定义如何将由 `Location` 定义的 XCM 来源转换为 FRAME 调度来源。
- 用于 `Transact` 指令中。

Notes:
- `Transact` 需要从 FRAME 调度来源进行调度。
  然而，`xcm-executor` 使用由 `Location` 定义的 XCM 来源进行工作。
- `OriginConverter` 是将一个来源转换为另一个来源的组件。
---v
## 📍 来源转换器列表
- `SovereignSignedViaLocation`：将 `Location` 来源（通常是平行链来源）转换为已签名的来源。
- `SignedAccountId32AsNative`：将本地 32 字节账户 `Location` 转换为使用相同 32 字节账户的已签名来源。
- `ParentAsSuperuser`：将父级来源转换为根来源。
- `SignedAccountKey20AsNative`：将本地 20 字节账户 `Location` 转换为使用相同 20 字节账户的已签名来源。

Notes:
- `ParentAsSuperuser` 可用于公共利益链，因为它们没有本地根来源，而是允许中继链的根来源充当根来源。
---
## 🛠️ `XcmConfig` 中的 XcmRouter
- `ParentAsUmp` 通过 UMP 将 XCM 路由到中继链。
- `XcmpQueue` 通过 XCMP 将 XCM 路由到其他平行链。

<pba-flex center>
```rust
pub type XcmRouter = (
	// 两个路由器 - 使用 UMP 与中继链通信：
	cumulus_primitives_utility::ParentAsUmp<ParachainSystem, PolkadotXcm>,
	//..和 XCMP 与同级链通信。
	XcmpQueue,
);
```
Notes:
- `ParachainSystem` 是 Cumulus 中的一个模块，它处理传入的 DMP 消息和队列，以及其他各种与平行链相关的事务。
- 如果目标位置符合 `Location { parents: 1, interior: Here }` 的形式，消息将通过 UMP 路由。
  UMP 通道默认可用。
- 如果目标位置符合 `Location { parents: 1, interior: X1(Parachain(para_id)) }` 的形式，消息将通过 XCMP 路由。
  截至目前，在消息能够路由之前应该建立一个 HRMP 通道。
- 该项目的元组实现意味着执行器将按顺序尝试使用这些项目。
---v
## 路由器
```rust
pub trait SendXcm {
  type Ticket;

  fn validate(
    destination: &mut Option<Location>,
    message: &mut Option<Xcm<()>>,
  ) -> SendResult<Self::Ticket>;

  fn deliver(ticket: Self::Ticket) -> Result<XcmHash, SendError>;
}
```
Notes:
在发送消息之前验证消息确实可以发送是很重要的。
这确保你支付发送费用并且确实会发送消息。
---
## 总结
在本讲座中，我们学习了：
- 链如何解释位置并将它们转换为账户和 FRAME 来源。
- 如何设置屏障以保护我们的链免受不需要的消息的影响。
- 如何在 XCM 中处理资产。
- 与 XCM 相关的其他配置项。
