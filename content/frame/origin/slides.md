---
title: FRAME Origin
description: Deep dive into FRAME Origins
duration: 1 hour
---

# Origin

---

## Origin

本次演讲将介绍FRAME中Origin的使用方法，以及如何定制和扩展这些抽象概念。

---

## 什么是Origin？

所有可调度调用都有一个`Origin`，用于描述调用的来源。

```rust
/// 做一些链上备注。
#[pallet::weight(T::SystemWeightInfo::remark(_remark.len() as u32))]
pub fn remark(origin: OriginFor<T>, _remark: Vec<u8>) -> DispatchResultWithPostInfo {
    ensure_signed_or_root(origin)?;
    Ok(().into())
}
```

---

## FRAME系统的`RawOrigin`

这些是FRAME默认包含的来源。

```rust
/// System pallet的来源。
#[derive(PartialEq, Eq, Clone, RuntimeDebug, Encode, Decode, TypeInfo, MaxEncodedLen)]
pub enum RawOrigin<AccountId> {
    /// 系统本身命令此调度发生：这是最高权限级别。
    Root,
    /// 由某个公钥签名，我们提供`AccountId`。
    Signed(AccountId),
    /// 未由任何人签名，可能是以下两种情况：
    /// * 无论如何都被验证者包含并认可，
    /// * 或者是由pallet验证的无签名交易。
    None,
}
```

---

## 它是如何使用的？

运行时Origin由可调度函数用于检查调用的来源。

这类似于Solidity中的`msg.sender`，但FRAME更强大，`Origin`也是如此。

---

## Origin检查

```rust
/// 确保来源`o`代表根。返回`Ok`，否则返回`Err`。
pub fn ensure_root<OuterOrigin, AccountId>(o: OuterOrigin) -> Result<(), BadOrigin>
```

```rust
/// 确保来源`o`代表一个已签名的外部调用（即交易）。
/// 返回`Ok`并附带签名外部调用的账户，否则返回`Err`。
pub fn ensure_signed<OuterOrigin, AccountId>(o: OuterOrigin) -> Result<AccountId, BadOrigin>
```

```rust
/// 确保来源`o`代表一个无签名的外部调用。返回`Ok`，否则返回`Err`。
pub fn ensure_none<OuterOrigin, AccountId>(o: OuterOrigin) -> Result<(), BadOrigin>
```

```rust
/// 确保来源`o`代表一个已签名的外部调用（即交易）或根。
/// 返回`Ok`并附带签名外部调用的账户，如果是根则返回`None`，否则返回`Err`。
pub fn ensure_signed_or_root<OuterOrigin, AccountId>(o: OuterOrigin) -> Result<Option<AccountId>, BadOrigin>
```

---

## 示例：已签名来源

一个简单的余额转移。

```rust
#[pallet::call_index(0)]
#[pallet::weight(T::WeightInfo::transfer())]
pub fn transfer(
    origin: OriginFor<T>,
    dest: AccountIdLookupOf<T>,
    #[pallet::compact] value: T::Balance,
) -> DispatchResultWithPostInfo {
    let transactor = ensure_signed(origin)?;
    // -- snip --
}
```

大多数外部调用使用`Signed`来源。

---

## 示例：根来源

升级链的外部调用。

```rust
/// 设置新的运行时代码。
#[pallet::call_index(2)]
#[pallet::weight((T::BlockWeights::get().max_block, DispatchClass::Operational))]
pub fn set_code(origin: OriginFor<T>, code: Vec<u8>) -> DispatchResultWithPostInfo {
    ensure_root(origin)?;
    Self::can_set_code(&code)?;
    T::OnSetCode::set_code(code)?;
    Ok(().into())
}
```

`Root`可以访问许多可以直接修改区块链的函数。假设根访问可以做任何事情。

---

## 示例：None来源

设置区块的时间戳。

```rust
    /// 设置当前时间。
    #[pallet::call_index(0)]
    #[pallet::weight((T::WeightInfo::set(), DispatchClass::Mandatory))]
    pub fn set(origin: OriginFor<T>, #[pallet::compact] now: T::Moment) -> DispatchResult {
        ensure_none(origin)?;
        // -- snip --
    }
}
```

`None`来源不是很直接。更多细节如下...

---

## None用于固有交易

`None`来源可用于表示由区块作者专门包含的外部调用，也称为固有交易。

在这些情况下，它包含使用`ProvideInherent`的固有检查逻辑。

```rust
#[pallet::inherent]
impl<T: Config> ProvideInherent for Pallet<T> {
    type Call = Call<T>;
    type Error = InherentError;
    const INHERENT_IDENTIFIER: InherentIdentifier = INHERENT_IDENTIFIER;

    // -- snip --
```

---

## None用于无签名交易

`None`还可用于表示“无签名外部调用”，这些调用旨在由任何人在没有密钥的情况下提交。

在这些情况下，它包含使用`ValidateUnsigned`的无签名验证逻辑。

```rust
#[pallet::validate_unsigned]
impl<T: Config> ValidateUnsigned for Pallet<T> {
    type Call = Call<T>;
    fn validate_unsigned(source: TransactionSource, call: &Self::Call) -> TransactionValidity {
        Self::validate_unsigned(source, call)
    }

    fn pre_dispatch(call: &Self::Call) -> Result<(), TransactionValidityError> {
        Self::pre_dispatch(call)
    }
}
```

---

## 自定义来源

来源是可扩展和可定制的。

每个pallet都可以引入新的来源，这些来源可以在整个运行时中使用。

```rust
/// `#[pallet::origin]`属性允许你为pallet定义一些来源。
#[pallet::origin]
pub struct Origin<T>(PhantomData<(T)>);
```

---

## 示例：Collective Pallet

```rust
/// Collective模块的来源。
pub enum RawOrigin<AccountId, I> {
    /// 它已得到集体中给定数量的成员（相对于总成员数）的认可。
    Members(MemberCount, MemberCount),
    /// 它已得到集体中单个成员的认可。
    Member(AccountId),
    /// 用于处理实例化的虚拟项。
    _Phantom(PhantomData<I>),
}
```

这个自定义来源允许我们表示一组用户，而不是单个账户。
例如：`Members(5, 9)`表示集体pallet逻辑控制下，9个成员中有5个成员对某事达成一致。

---

## 示例：Parachain来源

```rust
/// 平行链的来源。
#[pallet::origin]
pub enum Origin {
    /// 它来自一个平行链。
    Parachain(ParaId),
}
```

这是一个自定义来源，允许我们表示来自平行链的消息。

---

## 重新调度

你实际上可以在一个调用中使用你选择的来源调度另一个调用。

```rust [11]
#[pallet::call_index(0)]
#[pallet::weight({ let dispatch_info = call.get_dispatch_info(); (dispatch_info.weight, dispatch_info.class) })]
pub fn sudo(
    origin: OriginFor<T>,
    call: Box<<T as Config>::RuntimeCall>,
) -> DispatchResultWithPostInfo {
    // 这是一个公共调用，所以我们确保来源是某个已签名的账户。
    let sender = ensure_signed(origin)?;
    ensure!(Self::key().map_or(false, |k| sender == k), Error::<T>::RequireSudo);

    let res = call.dispatch_bypass_filter(frame_system::RawOrigin::Root.into());
    Self::deposit_event(Event::Sudid { sudo_result: res.map(|_| ()).map_err(|e| e.error) });
    // Sudo用户不支付费用。
    Ok(Pays::No.into())
}
```

在这里，Sudo Pallet允许`Signed`来源在逻辑允许的情况下提升为`Root`来源。

---

## 示例：Collective Pallet

在这里，你可以看到Collective Pallet创建并使用我们之前展示的`Members`来源进行调度。

```rust [5-6]
    fn do_approve_proposal(seats: MemberCount, yes_votes: MemberCount, proposal_hash: T::Hash, proposal: <T as Config<I>>::Proposal) -> (Weight, u32) {
        Self::deposit_event(Event::Approved { proposal_hash });

        let dispatch_weight = proposal.get_dispatch_info().weight;
        let origin = RawOrigin::Members(yes_votes, seats).into();
        let result = proposal.dispatch(origin);
        Self::deposit_event(Event::Executed { proposal_hash, result: result.map(|_| ()).map_err(|e| e.error) });
        // 为了安全起见，默认使用调度信息的权重
        let proposal_weight = get_result_weight(result).unwrap_or(dispatch_weight); // P1

        let proposal_count = Self::remove_proposal(proposal_hash);
        (proposal_weight, proposal_count)
    }
```

---

## 自定义来源检查

你可以通过实现`EnsureOrigin`特征来编写仅可通过自定义来源访问的逻辑。

```rust
/// 此对象对来源执行某种检查。
pub trait EnsureOrigin<OuterOrigin> { ... }
```

这些需要在运行时中进行配置，在运行时中可以知道所有自定义来源。

---

## 示例：Alliance Pallet

Pallet可以允许运行时配置各种来源。

```rust
#[pallet::config]
pub trait Config<I: 'static = ()>: frame_system::Config {
    /// 用于管理级操作（如设置联盟规则）的来源。
    type AdminOrigin: EnsureOrigin<Self::RuntimeOrigin>;
    /// 管理联盟的加入和强制退出的来源。
    type MembershipManager: EnsureOrigin<Self::RuntimeOrigin>;
    /// 用于发布公告和添加/删除不诚实项目的来源。
    type AnnouncementOrigin: EnsureOrigin<Self::RuntimeOrigin>;
    // -- snip --
}
```

---

## 示例：Alliance Pallet

Pallet调用可以使用这些自定义来源来限制对逻辑的访问。

```rust [5]
/// 为联盟规则设置新的IPFS CID。
#[pallet::call_index(5)]
#[pallet::weight(T::WeightInfo::set_rule())]
pub fn set_rule(origin: OriginFor<T>, rule: Cid) -> DispatchResult {
    T::AdminOrigin::ensure_origin(origin)?;

    Rule::<T, I>::put(&rule);

    Self::deposit_event(Event::NewRuleSet { rule });
    Ok(())
}
```

---

## 示例：Alliance Pallet

最后，运行时本身是你配置这些来源的地方。

```rust
impl pallet_alliance::Config for Runtime {
    type AdminOrigin = EitherOfDiverse<
        EnsureRoot<AccountId>,
        EnsureProportionAtLeast<AccountId, AllianceCollective, 1, 1>,
    >;
    type MembershipManager = EitherOfDiverse<
        EnsureRoot<AccountId>,
        EnsureProportionMoreThan<AccountId, AllianceCollective, 2, 3>,
    >;
    type AnnouncementOrigin = EitherOfDiverse<
        EnsureRoot<AccountId>,
        EnsureProportionMoreThan<AccountId, AllianceCollective, 1, 3>,
    >;
}
```

如你所见，它们甚至可以支持多种不同的来源！

---

## 费用

费用通常由诸如Transaction Payments Pallet之类的pallet处理。

然而，如果没有`Signed`来源，你实际上无法收取费用。

你应该假设任何不是来自`Signed`来源的交易都是免手续费的，除非你明确编写代码来处理它。

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题
