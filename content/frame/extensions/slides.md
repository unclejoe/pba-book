---
title: Signed Extensions
description: Signed Extensions, Transaction Priority.
---


# 签名扩展

---v

- 在本讲座中，你将学习到FRAME中最先进的概念之一：签名扩展。

* 它们允许为FRAME交易添加多种自定义功能。

---

## 历史

- 签名扩展最初是为了以合理的方式实现小费功能而添加的。

* 最初，你那愚蠢的讲师（@kianenigma）曾有过将其硬编码到 `UncheckedExtrinsic` 中的想法，直到@gavofyork提出了签名扩展的想法。

> [带小费的交易类型。由kianenigma发起的拉取请求 #2930 · paritytech/substrate](https://github.com/paritytech/substrate/pull/2930/files) > [可扩展交易（和小费）由gavofyork发起的拉取请求 #3102 · paritytech/substrate](https://github.com/paritytech/substrate/pull/3102/files)

---v

### 历史

- 本质上，它们是一种扩展交易的通用方式。此外，如果它们有额外的负载，这些负载会被签名，因此称为“签名扩展”。

---

## 结构

一个签名扩展可以是以下内容的任意组合：

- 附加到交易中的一些额外数据。
  - 小费！

<!-- .element: class="fragment" -->

- 在交易执行前后执行的一些钩子。
  - 在每次交易执行之前，必须预先支付费用。
  - 或许可以部分退还费用 🤑。

<!-- .element: class="fragment" -->

---v

### 结构

- 用于验证交易并向交易池提供反馈的一些额外验证逻辑。
  - 根据某些指标设置交易优先级！

<!-- .element: class="fragment" -->

- 每个交易的签名负载中必须存在的一些额外数据。
  - 发送方拥有的数据，链上也有，这些数据本身不会被传输，但它是签名负载的一部分。
  - 规范版本和创世哈希是所有交易签名负载的一部分！

<!-- .element: class="fragment" -->

---v

### 结构：让我们来看看这个特性

```rust [1-100|4|6-7|9-10|12]
pub trait SignedExtension:
	Codec + Debug + Sync + Send + Clone + Eq + PartialEq + StaticTypeInfo
{
	fn additional_signed(&self) -> Result<Self::AdditionalSigned, TransactionValidityError>;

	fn validate(..) -> TransactionValidity;
	fn validate_unsigned(..) -> TransactionValidity;

	fn pre_dispatch() -> Result<Self::Pre, TransactionValidityError>;
	fn pre_dispatch_unsigned() -> Result<(), TransactionValidityError>;

	fn post_dispatch() -> Result<(), TransactionValidityError>;
}
```

---

## 分组签名扩展

- 它本身也是一个签名扩展！

- 你可以自己查看具体实现……但简而言之：

- 主要要点：
  - `type AdditionalSigned = (SE1::AdditionalSigned, SE2::AdditionalSigned)`，
  - 所有钩子：
    - 分别执行每个钩子，然后合并结果

Notes:

待办事项：这里 `TransactionValidity` 是如何 `combined_with` 的非常重要，但可能需要在4.3节中更详细地介绍，然后在这里回顾。

---v

## 在运行时中的使用

- 每个运行时都有一组签名扩展。它们可以组合成一个元组

```rust
pub type SignedExtra = (
	frame_system::CheckNonZeroSender<Runtime>,
	frame_system::CheckSpecVersion<Runtime>,
	frame_system::CheckTxVersion<Runtime>,
	frame_system::CheckGenesis<Runtime>,
	pallet_asset_tx_payment::ChargeAssetTxPayment<Runtime>,
);

type UncheckedExtrinsic = generic::UncheckedExtrinsic<Address, Call, Signature, SignedExtra>;
```

- 签名扩展可能源于一个模块，但会应用于所有外部交易 😮‍💨！

Notes:

我们稍后会详细介绍，但要记住，签名扩展本身并不是一个FRAME/模块的概念。FRAME只是对其进行了实现。这也意味着，与签名扩展相关的所有内容都会在整个运行时应用于**所有交易**。

---

## 编码

```rust
struct Foo(u32, u32);
impl SignedExtension for Foo {
  type AdditionalSigned = u32;
  fn additional_signed(&self) -> Result<Self::AdditionalSigned, TransactionValidityError> {
    Ok(42u32)
  }
}

pub struct UncheckedExtrinsic<Address, Call, Signature, (Foo)>
{
	pub signature: Option<(Address, Signature, Extra)>,
	pub function: Call,
}
```

- 两个 `u32` 被解码，`42u32` 应该在签名负载中。

Notes:

这里是 `CheckedExtrinsic` 的 `check` 函数，有详细的注释来演示这一点：

```rust
// SignedPayload::new
pub fn new(call: Call, extra: Extra) -> Result<Self, TransactionValidityError> {
	// 要求所有签名扩展提供它们的额外签名数据...
	let additional_signed = extra.additional_signed()?;
	// 这本质上意味着：交易签名中需要签名的内容是：
	// 1. 调用
	// 2. 签名扩展数据本身
	// 3. 任何额外的签名数据。
	let raw_payload = (call, extra, additional_signed);
	Ok(Self(raw_payload))
}

// UncheckedExtrinsic::check
fn check(self, lookup: &Lookup) -> Result<Self::Checked, TransactionValidityError> {
	Ok(match self.signature {
		Some((signed, signature, extra)) => {
			let signed = lookup.lookup(signed)?;
			// 这是我们期望被签名的负载，如上所述。
			let raw_payload = SignedPayload::new(self.function, extra)?;
			// 对签名负载进行编码，并与签名进行验证。
			if !raw_payload.using_encoded(|payload| signature.verify(payload, &signed)) {
				return Err(InvalidTransaction::BadProof.into())
			}

			// extra 再次传递给 `CheckedExtrinsic`，见下一节。
			let (function, extra, _) = raw_payload.deconstruct();
			CheckedExtrinsic { signed: Some((signed, extra)), function }
		},
		// 我们根本不关心签名扩展。
		None => CheckedExtrinsic { signed: None, function: self.function },
	})
}
```

---

## 交易池验证

- 每个模块也有 `#[pallet::validate_unsigned]`。
- 这与创建签名扩展并实现 `validate_unsigned` 有一定的重叠。

Notes:

- <https://github.com/paritytech/substrate/issues/6102>
- <https://github.com/paritytech/polkadot-sdk/issues/365>

---v

### 交易池验证

- 记住，交易池验证应该是最小工作量且静态的。
- 在 `executive` 中，我们只做以下事情：
  - 检查签名。
  - 调用 `Extra::validate`/`Extra::validate_unsigned`
  - 如果是无签名交易，调用 `ValidateUnsigned::validate`。
  - Notes:调度 ✅！

Notes:

> 交易队列不是共识系统的一部分。交易验证是**免费**的。在交易验证中做太多工作本质上是为被拒绝服务攻击（DOS）打开了大门。

---v

### 交易池验证

- 关键是，你要确保在调度中也重新执行你在交易池验证中所做的任何操作：

```rust
/// 对有签名的交易进行任何预执行操作。
///
/// 确保执行与 [`Self::validate`] 中相同的检查。
fn pre_dispatch() -> Result<Self::Pre, TransactionValidityError>;
```

- 因为非静态条件可能会随时间变化！

---

## 调度后处理

- 调度结果，加上从 `pre_dispatch` 返回的泛型类型（`type Pre`）会传递给 `post_dispatch`。
- 更多信息请参见 [`impl Applyable for CheckedExtrinsic`](https://github.com/paritytech/polkadot-sdk/blob/bc53b9a/substrate/primitives/runtime/src/generic/checked_extrinsic.rs#L69)。

---

## 值得注意的签名扩展

- 这些是FRAME中一些默认的签名扩展。
- 看看你能否预测它们是如何实现的！

---v

### `ChargeTransactionPayment`

收取费用，如果是 `Pays::Yes` 则退款。

```rust
type Pre = (
  // 小费
  BalanceOf<T>,
  // 谁支付了费用 - 这是一个可选值，以允许默认实现。
  Self::AccountId,
  // 提取费用产生的不平衡
  <<T as Config>::OnChargeTransaction as OnChargeTransaction<T>>::LiquidityInfo,
);
```

<!-- .element: class="fragment" -->

---v

### `check_genesis`

确保你是针对正确的链进行签名。

将创世哈希放入 `additional_signed` 中。

<!-- .element: class="fragment" -->

`check_spec_version` 和 `check_tx_version` 的工作原理非常相似。

<!-- .element: class="fragment" -->

---v

### `check_non_zero_sender`

- 有趣的故事：任何账户都可以代表 `0x00` 账户进行签名。
- 由 [@xlc](https://github.com/xlc) 发现。
- 使用 `pre_dispatch` 和 `validate` 来确保签名账户不是 `0x00`。

Notes:

<https://github.com/paritytech/substrate/pull/10413>

---v

### `check_nonce`

- `pre_dispatch`：检查随机数并实际更新它。
- `validate`：检查随机数，不要写入任何内容，设置 `provides` 和 `requires`。

<!-- .element: class="fragment" -->

<div>

- 记住：
  - `validate` 仅用于轻量级检查，不进行读写操作。
  - 你写入存储的任何内容都会被回滚。

</div>

<!-- .element: class="fragment" -->

---v

### `check_weight`

- 在 `validate` 中检查是否有足够的权重。
- 在 `pre_dispatch` 中检查是否有足够的权重，并更新已消耗的权重。
- 在 `post_dispatch` 中更新已消耗的权重。

<!-- .element: class="fragment" -->

---

## 全局概览：扩展的管道

- 签名扩展（或者至少是 `pre_dispatch` 和 `validate` 部分）让我想起了 `express.js` 的扩展系统，如果你们有人知道那是什么的话。

---v

## 全局概览：扩展的管道

<img rounded src="./img/signed-extensions.svg" />

---

## 练习

- 仔细研究上面提到的值得注意的签名扩展，互相猜测它们是如何工作的。
- 签名扩展是交易编码的重要组成部分。尝试使用任何你想要的语言，仅使用scale-codec库，针对一个模板运行时编码一个正确的交易。
- 一个在每次交易时记录日志的签名扩展
- 一个记录所有交易次数的签名扩展
- 一个记录所有成功/失败交易次数的签名扩展
- 一个尝试为每个账户退还交易费用的签名扩展，只要他们每天提交的交易少于1笔。