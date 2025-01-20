---
title: Common Security Risks in Polkadot SDK Development # Also update the h1 header on the first slide to the same name
description: A session to explain common security risks in the development using Polkadot SDK.
duration: 60 minutes.
separator: "\r?\n---\r?\n"
verticalSeparator: "\r?\n---v\r?\n"
revealOptions:
  transition: "slide" # animation between slides = none/fade/slide/convex/concave/zoom
	backgroundTransition: "fade" # background swap between slides = none/fade/slide/convex/concave/zoom
	slideNumber: true
	controls: true
	progress: true
  
---

# Polkadot SDK 开发中的常见安全风险

---

<pba-cols>
<pba-col left>

本演示文稿讨论了 Polkadot SDK（Polkadot、Substrate、Cumulus 等）开发中的常见安全风险，以及减轻这些风险的方法。

每个安全风险由以下部分组成：**挑战、风险、案例研究、缓解措施和要点总结**。

</pba-col>
<pba-col left>

<!-- .element: class="fragment" data-fragment-index="1" -->

## 安全风险

1. 不安全的随机性
2. 存储耗尽
3. 基准测试不足
4. 过时的 crate
5. XCM 配置错误
6. 不安全的数学运算
7. 重放问题
8. 无界解码
9. 冗长问题
10. 不一致的错误处理

</pba-col>
</pba-cols>

---

## 免责声明

> 除了此处建议的缓解措施外，始终重要的是**确保进行适当的审计和严格的测试**。
>
> 特别是，如果你的系统处理真实价值的资产，要考虑到如果这些资产被利用，可能会损害其所有者的利益。

---

## 不安全的随机性

---v

### 挑战

**在任何公共、去中心化和确定性的系统（如区块链）上实现链上随机性是很困难的！**

<pba-cols>
<pba-col>

- 在系统中使用**弱加密算法**或**不安全的随机性**可能会损害关键功能的完整性。
- 这可能会使攻击者能够预测或操纵任何依赖于安全随机性的功能的结果。

</pba-col>
<pba-col>

<img rounded style="height: 450px" src="./img/cloudflare.webp" />

</pba-col>
</pba-cols>

---v

### 风险

<pba-cols>
<pba-col>

- 对关键功能的操纵或预测，导致完整性和安全性受损。
- 攻击者可能获得不公平的优势，从而破坏对系统的信任。

</pba-col>
<pba-col>

<img rounded style="height: 350px" src="./img/hack-casino.webp" />

</pba-col>
</pba-cols>

---v

### 案例研究 - 随机数集体翻转

- Substrate 的随机数集体翻转 pallet 提供了一个随机函数，该函数基于前 81 个区块的区块哈希生成低影响的随机值。
- 低影响的随机性在抵御相对较弱的对手时可能很有用。
- 建议主要在低安全性的情况下（如测试）使用此 pallet 作为随机数源。

---v

### 案例研究 - 随机数集体翻转

<div style="font-size: smaller">

```rust [1-12|14-36]
/// substrate/frame/insecure-randomness-collective-flip/src/lib.rs
fn on_initialize(block_number: BlockNumberFor<T>) -> Weight {
    let parent_hash = <frame_system::Pallet<T>>::parent_hash();
    /// ...
    <RandomMaterial<T>>::mutate(|ref mut values| {
        if values.try_push(parent_hash).is_err() {
            let index = block_number_to_index::<T>(block_number);
            values[index] = parent_hash;
        }
    });
    /// ...
}
/// ...
fn random(subject: &[u8]) -> (T::Hash, BlockNumberFor<T>) {
    let block_number = <frame_system::Pallet<T>>::block_number();
    let index = block_number_to_index::<T>(block_number);
    /// ...
    let hash_series = <RandomMaterial<T>>::get();
    let seed = if !hash_series.is_empty() {
        // 第 1 个区块初始化后总是这种情况。
        hash_series
            .iter()
            .cycle()
            .skip(index)
          // RANDOM_MATERIAL_LEN = 81
            .take(RANDOM_MATERIAL_LEN as usize)
            .enumerate()
            .map(|(i, h)| 
              (i as i8, subject, h)
              .using_encoded(T::Hashing::hash)
          ).triplet_mix()
    } else {
        T::Hash::default()
    };
    (seed, block_number.saturating_sub(RANDOM_MATERIAL_LEN.into()))
}
```

</div>

---v

### 案例研究 - VRF

- 目前在生产环境中，区块链随机性有两种主要的安全方法：[RANDAO](https://github.com/randao/randao) 和 VRF。**Polkadot 使用 VRF**。

  > **VRF**：一种数学运算，它接受一些输入并生成一个随机数，同时生成一个真实性证明，证明这个随机数是由提交者生成的。

- 使用 VRF 时，任何挑战者都可以验证该证明，以确保随机数生成是有效的。

---v

### 案例研究 - VRF

<div style="font-size: smaller">

- Babe pallet 随机数选项
  1. 两个纪元前的随机数
     - 用例：适用于需要确定性的共识协议。
     - 时间：使用两个纪元前的数据。
     - 风险：如果对手在特定时间控制区块生产，可能会产生偏差。
  2. 一个纪元前的随机数（**Polkadot 平行链拍卖**）
     - 用例：适用于不需要确定性的链上操作。
     - 时间：使用上一个纪元的数据。
     - 风险：如果对手在一个纪元的开始或结束时控制区块生产，可能会产生偏差。
  3. 当前区块随机数
     - 用例：适用于需要新鲜随机数的操作。
     - 时间：看起来是新鲜的，但基于较旧的数据。
     - 风险：最弱的形式，如果对手不宣布区块，可能会产生偏差。
- 随机数会受到其他输入的影响，例如外部随机数源。

</div>

---v

### 案例研究 - VRF

<div style="font-size: smaller">

```rust [1-19|21-36|37-46]
/// substrate/frame/babe/src/lib.rs
/// 计算一个新纪元的随机数。rho 是前一个纪元中所有 VRF 输出的连接。
/// 可以提供一个可选的大小提示，说明有多少个 VRF 输出。
fn compute_randomness(
  last_epoch_randomness: BabeRandomness,
  epoch_index: u64,
  rho: impl Iterator<Item = BabeRandomness>,
  rho_size_hint: Option<usize>,
) -> BabeRandomness {
  let mut s = Vec::with_capacity(
      40 + rho_size_hint.unwrap_or(0) * RANDOMNESS_LENGTH);
  s.extend_from_slice(&last_epoch_randomness);
  s.extend_from_slice(&epoch_index.to_le_bytes());
  for vrf_output in rho {
    s.extend_from_slice(&vrf_output[..]);
  }
  sp_io::hashing::blake2_256(&s)
}
/// ...
/// 当纪元改变时，恰好调用此函数一次，以更新随机数。返回新的随机数。
fn randomness_change_epoch(next_epoch_index: u64) -> BabeRandomness {
    let this_randomness = NextRandomness::<T>::get();
    let segment_idx: u32 = SegmentIndex::<T>::mutate(|s| sp_std::mem::replace(s, 0));
    // 高估为该段已满。
    let rho_size = (segment_idx.saturating_add(1) * UNDER_CONSTRUCTION_SEGMENT_LENGTH) as usize;
    let next_randomness = compute_randomness(
        this_randomness,
        next_epoch_index,
        (0..segment_idx).flat_map(|i| UnderConstruction::<T>::take(&i)), // VRF (From Digest)
        Some(rho_size),
    );
    NextRandomness::<T>::put(&next_randomness);
    this_randomness // -> Randomness::<T>::put(..);
}
/// substrate/frame/babe/src/randomness.rs
impl<T: Config> RandomnessT<T::Hash, BlockNumberFor<T>> for RandomnessFromOneEpochAgo<T> {
  fn random(subject: &[u8]) -> (T::Hash, BlockNumberFor<T>) {
    let mut subject = subject.to_vec();
    subject.reserve(RANDOMNESS_LENGTH);
    subject.extend_from_slice(&NextRandomness::<T>::get()[..]); 

    (T::Hashing::hash(&subject[..]), EpochStart::<T>::get().1)
  }
}
```

</div>

---v

### 缓解措施

- 所有验证者都可以被信任

  > VRF

- 并非所有验证者都可以被信任
  - 利用随机性获利远大于构建一个区块的获利

    > 可信解决方案（预言机、多方计算、提交 - 重放等）

  - 否则

    > VRF

---v

### 要点总结

- 链上随机性很困难。
- Polkadot 使用 VRF（例如拍卖）。
- 关于 VRF（Babe pallet），随机数只能由区块生产者操纵。如果所有节点都被信任，那么随机数也可以被信任。
- 你还可以通过可信预言机将可信随机数注入链中。
- **不要在生产环境中使用随机数集体翻转！**

---

## 存储耗尽

---v

### 挑战

**你的链可能会耗尽存储空间！**

<pba-cols>
<pba-col left>

- 链上存储的收费机制不足，这使得用户可以在不支付适当的存储押金的情况下占用存储空间。
- 这个漏洞可能会被恶意行为者利用，以低成本填满区块链存储空间，使得运行节点变得不可持续，并影响网络性能。

</pba-col>
<pba-col right>

<img rounded style="height: 550px" src="./img/low-disk-space.webp" />

</pba-col>
</pba-cols>

---v

### 风险

- 区块链存储的不可持续增长，导致节点运营商的成本增加，并可能导致节点故障。
- 更容易受到 DoS 攻击，攻击者利用不充分的存储押金机制来充斥区块链。

---v

### 案例研究 - 存在性押金

- 如果一个账户的余额低于存在性押金，该账户将被清除，其数据将被删除以节省存储空间。
- 存在性押金是为了优化存储而要求的。如果没有存在性押金或其价值被低估，可能会导致 DoS 攻击。
- 永久存储的成本通常不会计入外在调用的权重计算中，这使得攻击者可以通过向许多账户分发少量原生代币来填满区块链存储空间。

---v

### 案例研究 - 存在性押金

```rust [1-10|12-20]
/// relay/polkadot/constants/src/lib.rs
/// 货币相关。
pub mod currency {
  /// 存在性押金。
  pub const EXISTENTIAL_DEPOSIT: Balance = 100 * CENTS;
  /// ...
  pub const UNITS: Balance = 10_000_000_000;
  pub const DOLLARS: Balance = UNITS; // 10_000_000_000
  pub const CENTS: Balance = DOLLARS / 100; // 100_000_000
}

/// relay/polkadot/src/lib.rs
parameter_types! {
  pub const ExistentialDeposit: Balance = EXISTENTIAL_DEPOSIT;
  /// ...
}
impl pallet_balances::Config for Runtime {
  type ExistentialDeposit = ExistentialDeposit;
  /// ...
}
```

---v

### 案例研究 - 通用存储使用系统

<img rounded style="height: 550px" src="./img/general-storage-usage-system-issue.webp" />

[Polkadot SDK 中的问题](https://github.com/paritytech/polkadot-sdk/issues/207)

---v

### 案例研究 - NFT Pallet手动押金
<div style="font-size: smaller">
```rust [1-22|24-49|29-30]
// substrate/frame/nfts/src/lib.rs
/// ...
/// 起源必须是`ForceOrigin`或者已签名，且发送者应该是`collection`的管理员。
///
/// 如果起源是已签名的，那么根据公式`MetadataDepositBase + DepositPerByte * data.len`预留签名者的资金，同时考虑任何已预留的资金。
/// ...
#[pallet::call_index(24)]
#[pallet::weight(T::WeightInfo::set_metadata())]
pub fn set_metadata(
    origin: OriginFor<T>,
    collection: T::CollectionId,
    item: T::ItemId,
    data: BoundedVec<u8, T::StringLimit>,
) -> DispatchResult {
    let maybe_check_origin = T::ForceOrigin::try_origin(origin)
        .map(|_| None)
        .or_else(|origin| ensure_signed(origin).map(Some).map_err(DispatchError::from))?;
    Self::do_set_item_metadata(maybe_check_origin, collection, item, data, None)
}

// substrate/frame/nfts/src/features/attributes.rs
fn do_set_item_metadata {
    /// ...
    let mut deposit = Zero::zero();
    if collection_config.is_setting_enabled(CollectionSetting::DepositRequired) 
        // 下一行是为修复问题而添加的 
        || namespace != AttributeNamespace::CollectionOwner
    {
        deposit = T::DepositPerByte::get()
            .saturating_mul(((data.len()) as u32).into())
            .saturating_add(T::MetadataDepositBase::get());
    }
    
    let depositor = maybe_depositor.clone().unwrap_or(collection_details.owner.clone());
    let old_depositor = old_deposit.account.unwrap_or(collection_details.owner.clone());
    
    if depositor != old_depositor {
        T::Currency::unreserve(&old_depositor, old_deposit.amount);
        T::Currency::reserve(&depositor, deposit)?;
    } else if deposit > old_deposit.amount {
        T::Currency::reserve(&depositor, deposit - old_deposit.amount)?;
    } else if deposit < old_deposit.amount {
        T::Currency::unreserve(&depositor, old_deposit.amount - deposit);
    }
    /// ...
}
```
</div>
[提交记录](https://github.com/paritytech/substrate/commit/e907e15e0bda28b5c5db90e6a0bce3393fbc59f1)
---v
### 缓解措施
- **存在性押金**：确保其值与中继链定义的值相似。
- **存储押金**：实现类似以下的合理逻辑：
<div style="font-size: smaller">
```rust
// 押金计算（字节数 * 每字节押金 + 基础押金）
let mut deposit = T::DepositPerByte::get()
    .saturating_mul(((key.len() + value.len()) as u32).into())
    .saturating_add(T::DepositBase::get());

// 押金预留（动态数据大小）
if old_deposit.account.is_some() && 
    old_deposit.account != Some(origin.clone()) {
    T::Currency::unreserve(
        &old_deposit.account.unwrap(), old_deposit.amount);
    T::Currency::reserve(&origin, deposit)?;
} else if deposit > old_deposit.amount {
    T::Currency::reserve(&origin, deposit - old_deposit.amount)?;
} else if deposit < old_deposit.amount {
    T::Currency::unreserve(&origin, old_deposit.amount - deposit);
}
```
</div>
---v
### 要点总结
- 始终明确要求对链上存储收取押金（以预留余额的形式）。
- 当用户从链上移除数据时，押金会返还给用户。
- 确保存在性押金大于N。确定N时，可以从中继链的类似值开始，并监控用户活动。
- 如果可能，限制一个pallet可以拥有的数据量。否则，确保在存储使用中设置一些限制（预留押金）。
---
## 基准测试不足
---v
### 挑战
**基准测试可能是一项艰巨的任务……**
<pba-cols>
<pba-col>
- 错误或缺失的基准测试可能会导致区块权重过高，造成网络拥塞并影响区块链的整体性能。
- 当对计算复杂性或存储访问估计不足时，就会出现这种情况，从而导致对外在调用的权重设置不准确。
</pba-col>
<pba-col>
<img rounded style="height: 550px" src="./img/benchmarking.webp" />
</pba-col>
</pba-cols>
---v
### 风险
<pba-cols>
<pba-col>
- **权重过高的外在调用**会使网络变慢。
- 导致交易处理延迟并影响用户体验。
</pba-col>
<pba-col>
- **权重过低的外在调用**可能会被利用来向网络发送垃圾信息。
- 可能导致拒绝服务（DoS）攻击。
</pba-col>
</pba-cols>
---v
### 案例研究 - 基准测试输入长度 - 问题
<div style="font-size: smaller">
```rust [0|9-10|27-29]
// substrate/frame/remark/src/lib.rs（已修改）
#[frame_support::pallet]
pub mod pallet {
    /// ...
    #[pallet::call]
    impl<T: Config> Pallet<T> {
        /// 对链下数据进行索引和存储。
        #[pallet::call_index(0)]
        #[pallet::weight(T::WeightInfo::store())]
        pub fn store(origin: OriginFor<T>, remark: Vec<u8>) -> DispatchResultWithPostInfo {
            ensure!(!remark.is_empty(), Error::<T>::Empty);
            let sender = ensure_signed(origin)?;
            let content_hash = sp_io::hashing::blake2_256(&remark);
            let extrinsic_index = <frame_system::Pallet<T>>::extrinsic_index()
                .ok_or_else(|| Error::<T>::BadContext)?;
            sp_io::transaction_index::index(extrinsic_index, remark.len() as u32, content_hash);
            Self::deposit_event(Event::Stored { sender, content_hash: content_hash.into() });
            Ok(().into())
        }
    }
    /// ...
}

// substrate/frame/remark/src/benchmarking.rs（已修改）
benchmarks! {
    store {
        let caller: T::AccountId = whitelisted_caller();
    }: _(RawOrigin::Signed(caller.clone()), vec![])
    verify {
        assert_last_event::<T>(Event::Stored { sender: caller, content_hash: sp_io::hashing::blake2_256(&vec![0u8; l as usize]).into() }.into());
    }

    impl_benchmark_test_suite!(Remark, crate::mock::new_test_ext(), crate::mock::Test);
}
```
</div>
---v
### 案例研究 - 基准测试输入长度 - 缓解措施
<div style="font-size: smaller">
```rust [0|9-10|27-30]
// substrate/frame/remark/src/lib.rs
#[frame_support::pallet]
pub mod pallet {
    /// ...
    #[pallet::call]
    impl<T: Config> Pallet<T> {
        /// 对链下数据进行索引和存储。
        #[pallet::call_index(0)]
        #[pallet::weight(T::WeightInfo::store(remark.len() as u32))]
        pub fn store(origin: OriginFor<T>, remark: Vec<u8>) -> DispatchResultWithPostInfo {
            ensure!(!remark.is_empty(), Error::<T>::Empty);
            let sender = ensure_signed(origin)?;
            let content_hash = sp_io::hashing::blake2_256(&remark);
            let extrinsic_index = <frame_system::Pallet<T>>::extrinsic_index()
                .ok_or_else(|| Error::<T>::BadContext)?;
            sp_io::transaction_index::index(extrinsic_index, remark.len() as u32, content_hash);
            Self::deposit_event(Event::Stored { sender, content_hash: content_hash.into() });
            Ok(().into())
        }
    }
    /// ...
}

// substrate/frame/remark/src/benchmarking.rs
benchmarks! {
    store {
        let l in 1 .. 1024*1024;
        let caller: T::AccountId = whitelisted_caller();
    }: _(RawOrigin::Signed(caller.clone()), vec![0u8; l as usize])
    verify {
        assert_last_event::<T>(Event::Stored { sender: caller, content_hash: sp_io::hashing::blake2_256(&vec![0u8; l as usize]).into() }.into());
    }

    impl_benchmark_test_suite!(Remark, crate::mock::new_test_ext(), crate::mock::Test);
}
```
</div>
---v
### 缓解措施
- 使用最坏情况场景条件运行基准测试。
  > 例如，考虑外在调用中可能发生的最大数量的数据库读写操作。
- 首要目标是确保运行时安全。
- 次要目标是尽可能准确地设置权重，以最大化吞吐量。
- 对于非严格时限的代码，使用**计量**。
---v
### 要点总结
- 基准测试可确保平行链的用户不会使用超出网络可用和预期的资源。
- 权重用于根据执行时间（参考硬件）和创建默克尔证明所需的数据大小来跟踪有限的区块链资源消耗。
- 不同计算机上的1秒计算量允许进行不同数量的计算。
---
## 过时的crate
---v
### 挑战
**依赖项可能会成为噩梦！**
<pba-cols>
<pba-col>
- 在Substrate运行时中使用过时或已知存在漏洞的组件，如pallet或库，可能会使系统面临广泛的安全风险和攻击。
</pba-col>
<pba-col>
<img rounded style="height: 500px" src="./img/dependencies.webp" />
</pba-col>
</pba-cols>
---v
### 风险
- 暴露在已知漏洞中，可能会被攻击者利用。
- 损害网络的完整性和安全性，可能导致数据泄露或财务损失。
---v
### 案例研究 - Serde预编译二进制文件
<pba-cols>
<pba-col>
- Polkadot使用带有`derive`特性的`serde`作为依赖项。
- **问题**：Serde开发者决定将其作为预编译二进制文件发布。[文章](https://www.bleepingcomputer.com/news/security/rust-devs-push-back-as-serde-project-ships-precompiled-binaries/)。
- **缓解措施**：将依赖项修复为不包含预编译二进制文件的版本。
</pba-col>
<pba-col>
<img rounded style="height: 450px" src="./img/serde.webp" />
</pba-col>
</pba-cols>
**像Polkadot这样的无信任系统，不能盲目信任二进制文件。**
---v
### 缓解措施
- 始终使用Polkadot、Substrate、Cumulus以及任何其他第三方crate的最新稳定版本。
- 如果可能，避免使用过多的crate。
- 使用诸如`cargo audit`或`cargo vet`等工具来监控系统依赖项的状态。
- 不要使用包含预编译二进制文件的依赖项。
---v
### 要点总结
- 过时的crate可能会导致系统出现漏洞，即使该crate本身没有漏洞。
- 过时的crate可能包含已知漏洞，这些漏洞很容易在你的系统中被利用。
- 在生产环境中，在crate被宣布为稳定版本之前，不要使用其最新版本。
---

## XCM配置错误
---v
### 挑战
**正确配置XCM需要大量的关注！**
- XCM需要通过不同的pallet和配置进行配置。
- 仔细确定对XCM pallet和传入队列的访问控制。
- 对于新的平行链，很难确定哪些XCM消息是需要的，哪些是不需要的。
- 如果配置未正确设置，链可能容易受到攻击，如果传入的XCM消息未被作为不可信消息处理或未进行适当的清理，链可能成为垃圾信息的目标，甚至在**发送**操作中未实施良好的访问控制时，可能被用作攻击其他平行链的**桥梁**。
---v
### 风险
- 对区块链状态的未经授权的操纵，损害网络的完整性。
- 执行未经授权的交易，导致潜在的财务损失。
- 被用作攻击平行链的通道。
---v
### 案例研究 - 罗科科桥接中心 - 描述
- 罗科科桥接中心XcmConfig中的`MessageExporter`类型（`BridgeHubRococoOrBridgeHubWococoSwitchExporter`）在[`validate`](https://github.com/paritytech/polkadot-sdk/blob/f6560c2b7226ea756ade18df42018c3eaf3be2e0/cumulus/parachains/runtimes/bridge-hubs/bridge-hub-rococo/src/xcm_config.rs#L391)和`deliver`方法中使用了`unimplemented!()`宏，这相当于`panic!()`宏。这使罗科科桥接中心的运行时暴露在任何允许向罗科科桥接中心发送消息的平行链可触发的不可跳过的恐慌中。
- 对于任何可以向桥接中心发送消息的人来说，这个问题都很容易执行，因为只需发送一个有效的XCM消息，其中包含一个试图桥接到未实现的网络的ExportMessage指令即可。
---v
### 案例研究 - 罗科科桥接中心 - 问题
```rust [0|14-21|22-29|30]
// cumulus/parachains/runtimes/bridge-hubs/bridge-hub-rococo/src/xcm_config.rs
pub struct BridgeHubRococoOrBridgeHubWococoSwitchExporter;
impl ExportXcm for BridgeHubRococoOrBridgeHubWococoSwitchExporter {
    type Ticket = (NetworkId, (sp_std::prelude::Vec<u8>, XcmHash));

    fn validate(
        network: NetworkId,
        channel: u32,
        universal_source: &mut Option<InteriorMultiLocation>,
        destination: &mut Option<InteriorMultiLocation>,
        message: &mut Option<Xcm<()>>,
    ) -> SendResult<Self::Ticket> {
        match network {
            Rococo => ToBridgeHubRococoHaulBlobExporter::validate(
                network,
                channel,
                universal_source,
                destination,
                message,
            )
           .map(|result| ((Rococo, result.0), result.1)),
            Wococo => ToBridgeHubWococoHaulBlobExporter::validate(
                network,
                channel,
                universal_source,
                destination,
                message,
            )
           .map(|result| ((Wococo, result.0), result.1)),
            _ => unimplemented!("不支持的网络: {:?}", network),
        }
```
---v
### 案例研究 - 罗科科桥接中心 - XCM配置
<div style="font-size: smaller">
```rust [0|30|7|40-59|48-50|13|61-88|71-73]
// cumulus/parachains/runtimes/bridge-hubs/bridge-hub-rococo/src/xcm_config.rs
pub struct XcmConfig;
impl xcm_executor::Config for XcmConfig {
    type RuntimeCall = RuntimeCall;
    type XcmSender = XcmRouter;
    type AssetTransactor = CurrencyTransactor;
    type OriginConverter = XcmOriginToTransactDispatchOrigin;
    // 桥接中心不识别任何资产的储备位置。用户必须在允许的情况下传送原生代币（例如通过中继链）。
    type IsReserve = ();
    type IsTeleporter = TrustedTeleporters;
    type UniversalLocation = UniversalLocation;
    type Barrier = Barrier;
    type Weigher = WeightInfoBounds<
        crate::weights::xcm::BridgeHubRococoXcmWeight<RuntimeCall>,
        RuntimeCall,
        MaxInstructions,
    >;
    type Trader =
        UsingComponents<WeightToFee, TokenLocation, AccountId, Balances, ToStakingPot<Runtime>>;
    type ResponseHandler = PolkadotXcm;
    type AssetTrap = PolkadotXcm;
    type AssetLocker = ();
    type AssetExchanger = ();
    type AssetClaims = PolkadotXcm;
    type SubscriptionService = PolkadotXcm;
    type PalletInstancesInfo = AllPalletsWithSystem;
    type MaxAssetsIntoHolding = MaxAssetsIntoHolding;
    type FeeManager = XcmFeesToAccount<Self, WaivedLocations, AccountId, TreasuryAccount>;
    type MessageExporter = BridgeHubRococoOrBridgeHubWococoSwitchExporter;
    type UniversalAliases = Nothing;
    type CallDispatcher = WithOriginFilter<SafeCallFilter>;
    type SafeCallFilter = SafeCallFilter;
    type Aliasers = Nothing;
}

// 这是我们用来将（传入的）XCM源转换为本地`Origin`实例的类型，准备好使用Xcm的`Transact`调度事务。有一个`OriginKind`可以影响它将成为的本地`Origin`的类型。
pub type XcmOriginToTransactDispatchOrigin = (
    // 主权账户转换器；这试图从源位置派生一个`AccountId`，使用`LocationToAccountId`，然后将其转换为通常的`Signed`源。对于希望在该链上拥有其控制的本地主权账户的外部链很有用。
    SovereignSignedViaLocation<LocationToAccountId, RuntimeOrigin>,
    // 中继链（父级）位置的本地转换器；当识别到时将转换为`Relay`源。
    RelayChainAsNative<RelayChainOrigin, RuntimeOrigin>,
    // 兄弟平行链的本地转换器；当识别到时将转换为`SiblingPara`源。
    SiblingParachainAsNative<cumulus_pallet_xcm::Origin, RuntimeOrigin>,
    // 中继链（父级）位置的超级用户转换器。这将允许它从Root源发起事务。
    ParentAsSuperuser<RuntimeOrigin>,
    // 本地已签名账户转换器；这只是将一个`AccountId32`源转换为具有相同32字节值的普通`RuntimeOrigin::Signed`源。
    SignedAccountId32AsNative<RelayNetwork, RuntimeOrigin>,
    // Xcm源可以在Xcm pallet的Xcm源下以本地形式表示。
    XcmPassthrough<RuntimeOrigin>,
);

pub type Barrier = TrailingSetTopicAsId<
    DenyThenTry<
        DenyReserveTransferToRelayChain,
        (
            // 允许本地用户购买权重信用。
            TakeWeightCredit,
            // 预期的响应是可以的。
            AllowKnownQueryResponses<PolkadotXcm>,
            WithComputedOrigin<
                (
                    // 如果消息是一个立即尝试支付执行费用的消息，则允许它。
                    AllowTopLevelPaidExecutionFrom<Everything>,
                    // 父级、其多数（即治理机构）和中继库获得免费执行。
                    AllowExplicitUnpaidExecutionFrom<(
                        ParentOrParentsPlurality,
                        Equals<RelayTreasuryLocation>,
                    )>,
                    // 用于版本跟踪的订阅是可以的。
                    AllowSubscriptionsFrom<ParentOrSiblings>,
                ),
                UniversalLocation,
                ConstU32<8>,
            >,
        ),
    >,
>;
```
</div>
---v
### 缓解措施
- 经常验证所有配置并与其他链进行比较。
- 在XMC pallet中，在能够确保XCM安全保证之前，限制**执行**和**发送**的使用。
- 在XCM执行器中，确保在`XcmConfig`中进行正确的访问控制：
  - 只允许可信来源。使用`OriginConverter`过滤源。
  - 只接受平行链需要接收的特定消息结构。使用`Barrier`（通用）、`SafeCallFilter`（事务）、`IsReserve`（储备）、`IsTeleporter`（传送）、`MessageExporter`（导出）、`XcmSender`（发送）等过滤消息结构。
- 在XCMP队列中，确保只打开可信通道。
---v
### 要点总结
- XCM的开发和审计仍在进行中，因此目前其安全保证无法确保。
- 允许任何用户使用执行和发送功能可能会对您的平行链产生严重影响。
- 传入的XCM需要作为不可信消息处理并进行适当的清理。
- 平行链中访问控制不足会使恶意行为者通过您的系统攻击其他平行链。
---
## 不安全的数学运算
---v
### 挑战
- 代码库中的不安全数学运算可能导致整数溢出/下溢、除以零、转换截断/溢出和错误的最终结果，攻击者可以利用这些问题操纵计算并获得不公平的优势。
- 这主要涉及基本算术运算的使用。
<img rounded style="height: 300px" src="./img/overflow.webp" />
---v
### 风险
- 对账户余额的操纵，导致未经授权的转账或账户余额的人为膨胀。
- 可能破坏依赖准确算术计算的网络功能。
- 错误的计算，导致诸如错误的账户余额或交易费用等意外后果。
- 攻击者利用该漏洞操纵结果以使其对自己有利的可能性。
---v

### 案例研究 - Frontier余额模块 - 描述
- Frontier的[CVE-2022-31111](https://github.com/paritytech/frontier/security/advisories/GHSA-hc8w-mx86-9fcj)披露指出，在从EVM到Substrate的余额转换过程中发现了问题。该模块没有正确处理转换，导致转账金额显示异常，可能引发溢出。
- 这存在风险的原因有两个：
  1. 它可能导致错误的计算，比如账户余额混乱。
  2. 恶意人员可能利用这个错误获取不公平的优势。
- 为了解决这个问题，仔细检查这些转换的实现方式，确保数字准确至关重要。
---v
### 案例研究 - Frontier余额模块 - 问题
<div style="font-size: smaller">
```rust [0|10|16]
// substrate/frame/evm/src/lib.rs
#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig {
    fn build(&self) {
        for (address, account) in &self.accounts {
            let account_id = T::AddressMapping::into_account_id(*address);

            // 假设：在单个EVM交易中，随机数（nonce）的增加不会超过`u128::max_value()`。
            for _ in 0..account.nonce.low_u128() {
                frame_system::Pallet::<T>::inc_account_nonce(&account_id);
            }

            T::Currency::deposit_creating(
                &account_id,
                account.balance.low_u128().unique_saturated_into(),
            );
```
</div>
---v
### 案例研究 - Frontier余额模块 - 缓解措施
<div style="font-size: smaller">
```rust [0|15-18|24]
// substrate/frame/evm/src/lib.rs
#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig
where
    U256: UniqueSaturatedInto<BalanceOf<T>>,
{
    fn build(&self) {
        const MAX_ACCOUNT_NONCE: usize = 100;

        for (address, account) in &self.accounts {
            let account_id = T::AddressMapping::into_account_id(*address);

            // 假设：在单个EVM交易中，随机数（nonce）的增加不会超过`u128::max_value()`。
            for _ in 0..min(
                MAX_ACCOUNT_NONCE,
                UniqueSaturatedInto::<usize>::unique_saturated_into(account.nonce),
            ) {
                frame_system::Pallet::<T>::inc_account_nonce(&account_id);
            }

            T::Currency::deposit_creating(
                &account_id, 
                account.balance.unique_saturated_into()
            );
```
</div>
---v
### 缓解措施
- **算术运算**
  - **简单但（有时）成本较高的解决方案**：使用像`checked_div`这样的检查型/饱和型函数。
  - **复杂但（有时）成本较低的解决方案**：在执行基本函数之前进行验证。例如：`balance > transfer_amount`。
- **类型转换**
  - 避免向下转型。否则，使用像`unique_saturated_into`这样的方法，而不是`low_u64`这类方法。
  - **系统设计应避免向下转型！**
---v
### 要点总结
- 在测试模块时，如果基本算术运算导致溢出、下溢或除以零，系统会发生恐慌（崩溃）。然而，在发布（生产）环境中，模块在发生溢出时不会恐慌。始终要确保不会出现意外的溢出或下溢。
- 检查型操作比基本操作需要略多的计算资源。
- 在测试模块时，如果转换导致溢出、下溢或截断，系统会发生恐慌（崩溃）。然而，在发布（生产）环境中，模块不会恐慌。始终要确保不会出现意外的溢出、下溢或截断。
- 在类型转换中，新类型越小，发生溢出、下溢或截断的可能性就越大。
---
## 重放问题
---v
### 挑战
<pba-cols>
<pba-col>
- 重放问题，最常见的是由未签名的外部调用引起的，可能导致垃圾信息攻击，在某些情况下还会引发双花攻击。
- 当随机数（nonce）管理不当，交易就有可能被重放。
</pba-col>
<pba-col>
<img rounded style="height: 500px" src="./img/nonce.webp" />
</pba-col>
</pba-cols>
---v
### 风险
- 用重复交易向网络发送垃圾信息，导致网络拥塞和性能下降。
- 存在双花攻击的可能性，这会损害区块链的完整性。
---v
### 案例研究 - Frontier状态转换函数（STF） - 描述
- [CVE-2021-41138](https://github.com/paritytech/frontier/security/advisories/GHSA-vj62-g63v-f8mf)描述了[Frontier #482](https://github.com/paritytech/frontier/pull/482)中的更改所引发的安全问题。在这次更新之前，`validate_unsigned`函数用于检查交易是否有效，该函数是**状态转换函数**（STF）的一部分，在生成区块时非常重要。更新之后，新的`validate_self_contained`函数承担了这个工作，但它不属于STF。这意味着恶意验证者可以提交无效交易，甚至重复使用来自不同链的交易。
- 在[Frontier](https://github.com/paritytech/frontier/blob/0a8e696fdfb9ce73a7f99941a2f2ec22eefd4f38/frame/ethereum/src/lib.rs#L249)的以下示例中，可以看到更新前使用的`do_transact`函数，其中未使用`validate_self_contained`。
- 在后来的[一次提交](https://github.com/paritytech/frontier/commit/146bb48849e5393004be5c88beefe76fdf009aba)中，通过在区块生成时添加验证来修复此问题。从这些更改中可以看到，`do_transact`被`validate_transaction_in_block`取代，后者包含了之前在`validate_self_contained`中的交易验证逻辑。
---v
### 案例研究 - Frontier余额模块 - 问题
<div style="font-size: smaller">
```rust [0|]
fn on_initialize(_: T::BlockNumber) -> Weight {
    Pending::<T>::kill();
    // 如果区块摘要中包含一个现有的以太坊区块（编码为PreLog），则首先执行导入的区块，并禁用交易调度功能。
    if let Ok(log) = fp_consensus::find_pre_log(&frame_system::Pallet::<T>::digest()) {
        let PreLog::Block(block) = log;
        for transaction in block.transactions {
            let source = Self::recover_signer(&transaction).expect(
                "pre-block transaction signature invalid; the block cannot be built",
            );

            Self::do_transact(source, transaction).expect(
                "pre-block transaction verification failed; the block cannot be built",
            );
        }
    }

    0
}
```
</div>
---v
### 案例研究 - Frontier余额模块 - 缓解措施
<div style="font-size: smaller">
```rust [0|12-14|32-39]
fn on_initialize(_: T::BlockNumber) -> Weight {
    Pending::<T>::kill();
    // 如果区块摘要中包含一个现有的以太坊区块（编码为PreLog），则首先执行导入的区块，并禁用交易调度功能。
    if let Ok(log) = fp_consensus::find_pre_log(&frame_system::Pallet::<T>::digest()) {
        let PreLog::Block(block) = log;
        for transaction in block.transactions {
            let source = Self::recover_signer(&transaction).expect(
                "pre-block transaction signature invalid; the block cannot be built",
            );

            Self::validate_transaction_in_block(source, &transaction).expect(
                "pre-block transaction verification failed; the block cannot be built",
            );
            Self::apply_validated_transaction(source, transaction).expect( // do_transact
                "pre-block transaction execution failed; the block cannot be built",
            );
        }
    }

    0
}

// 由交易池和状态转换函数（STF）以相同方式执行的通用控制。
// 除了与随机数（nonce）相关的控制之外，所有控制都是这种情况。
fn validate_transaction_common(
    origin: H160,
    transaction: &Transaction,
) -> Result<U256, TransactionValidityError> {
    // ...
    if let Some(chain_id) = transaction.signature.chain_id() {
        if chain_id != T::ChainId::get() {
            return Err(InvalidTransaction::Custom(
                TransactionValidationError::InvalidChainId as u8,
            )
           .into());
        }
    }
    // ...
}
```
</div>
---v
### 缓解措施
- 确保系统从不可信来源接收的数据：
  - 通过实施随机数机制，使其无法被重复使用。
  - 通过检查任何身份标识类型，如ID、哈希等，确认数据是针对本系统的。
---v
### 要点总结
- 重放问题可能会造成严重破坏。
- 即使链确保运行时交易无法被重放，但如果没有正确验证，外部参与者仍可能通过传入相似的输入来重放相似的输出。
---
## 无界解码
---v
### 挑战
<pba-cols>
<pba-col>
- 对对象进行解码时没有设置嵌套深度限制可能会导致栈溢出，攻击者可能会精心构造高度嵌套的对象来引发栈溢出。
- 这可能被利用来破坏区块链网络的正常运行。
</pba-col>
<pba-col>
<img rounded style="height: 350px" src="./img/recursion.webp" />
</pba-col>
</pba-cols>
---v
### 风险
- 栈溢出，这可能导致网络不稳定和崩溃。
- 存在利用栈溢出漏洞进行拒绝服务（DoS）攻击的可能性。
---v
### 案例研究 - 白名单模块 - 描述
- 在[Substrate #10159](https://github.com/paritytech/substrate/pull/10159)中，引入了`whitelist-pallet`模块。该模块包含`dispatch_whitelisted_call`外部调用，用于调度之前列入白名单的调用。
- 为了进行调度，需要对调用进行解码，而之前是使用`decode`方法进行解码的。
- 审计人员发现这个方法可能导致栈溢出，并建议开发人员使用`decode_with_depth_limit`来缓解这个问题。
- 由于源有特定的限制，风险是有限的，但如果触发了这个问题，由此产生的栈溢出可能会导致整个区块无效，链可能会陷入无法生成新区块的困境。
---v
### 案例研究 - 白名单模块 - 问题
<div style="font-size: smaller">
```rust [0|18-19]
// 易受攻击的白名单模块重制版
pub fn dispatch_whitelisted_call(
    origin: OriginFor<T>,
    call_hash: PreimageHash,
    call_encoded_len: u32,
    call_weight_witness: Weight,
) -> DispatchResultWithPostInfo {
    T::DispatchWhitelistedOrigin::ensure_origin(origin)?;

    ensure!(
        WhitelistedCall::<T>::contains_key(call_hash),
        Error::<T>::CallIsNotWhitelisted,
    );

    let call = T::Preimages::fetch(&call_hash, Some(call_encoded_len))
        .map_err(|_| Error::<T>::UnavailablePreImage)?;

    let call = <T as Config>::RuntimeCall::decode(&mut &call[..])
        .map_err(|_| Error::<T>::UndecodableCall)?;
```
</div>
---v
### 案例研究 - 白名单模块 - 攻击示例（PoC）
<div style="font-size: smaller">
```rust [0|5-8|14-20|22-31|37-47]
// 易受攻击的白名单模块重制版
#[test]
fn test_unsafe_dispatch_whitelisted_call_stack_overflow() {
    new_test_ext().execute_with(|| {
        let mut call = 
            RuntimeCall::System(
                frame_system::Call::remark_with_event { remark: vec![1] }
            );
        let mut call_weight = call.get_dispatch_info().weight;
        let mut encoded_call = call.encode();
        let mut call_encoded_len = encoded_call.len() as u32;
        let mut call_hash = <Test as frame_system::Config>::Hashing::hash(&encoded_call[..]);

        // 需要创建的嵌套调用数量
        // 由于以下值小于导致栈溢出的最小调用数量，此测试不会崩溃
        let nested_calls = sp_api::MAX_EXTRINSIC_DEPTH;

        // 以下行用于在解码（模块）时获取栈溢出错误
        let nested_calls = nested_calls*4;

        // 创建嵌套调用
        for _ in 0..=nested_calls {
            call = RuntimeCall::Whitelist(crate::Call::dispatch_whitelisted_call_with_preimage {
                call: Box::new(call.clone()),
            });
            call_weight = call.get_dispatch_info().weight;
            encoded_call = call.encode();
            call_encoded_len = encoded_call.len() as u32;
            call_hash = <Test as frame_system::Config>::Hashing::hash(&encoded_call[..]);
        }

        // 将调用列入白名单以能够调度它
        assert_ok!(Preimage::note(encoded_call.into()));
        assert_ok!(Whitelist::whitelist_call(RuntimeOrigin::root(), call_hash));

        // 发送要调度的调用
        // 如果嵌套调用数量过高，这将引发栈溢出
        println!("Dispatching with {} nested calls", nested_calls);
        assert_ok!(
            Whitelist::dispatch_whitelisted_call(
                RuntimeOrigin::root(),
                call_hash,
                call_encoded_len,
                call_weight
            ),
        );
    });
}
```
</div>
---v
### 案例研究 - 白名单模块 - 攻击结果
<img rounded style="height: 500px" src="./img/stack-overflow.webp" />
---v
### 案例研究 - 白名单模块 - 缓解措施
<div style="font-size: smaller">
```rust [0|18-22]
// 易受攻击的白名单模块重制版
pub fn dispatch_whitelisted_call(
    origin: OriginFor<T>,
    call_hash: PreimageHash,
    call_encoded_len: u32,
    call_weight_witness: Weight,
) -> DispatchResultWithPostInfo {
    T::DispatchWhitelistedOrigin::ensure_origin(origin)?;

    ensure!(
        WhitelistedCall::<T>::contains_key(call_hash),
        Error::<T>::CallIsNotWhitelisted,
    );

    let call = T::Preimages::fetch(&call_hash, Some(call_encoded_len))
        .map_err(|_| Error::<T>::UnavailablePreImage)?;

    let call = 
        <T as Config>::RuntimeCall::decode_all_with_depth_limit(
            sp_io::MAX_EXTRINSIC_DEPTH,
            &mut &call[..],
        ).map_err(|_| Error::<T>::UndecodableCall)?;
```
</div>
---v
### 缓解措施
- 使用`decode_with_depth_limit`方法代替`decode`方法。
- 使用`decode_with_depth_limit`时，设置的深度限制要低于可能导致栈溢出的深度。
---v
### 要点总结
- 解码不可信对象可能会导致栈溢出。
- 栈溢出可能会导致网络不稳定和崩溃。
- 在模块中解码数据时，始终要确保设置最大深度。
---
## 日志记录问题
---v
### 挑战
<pba-cols>
<pba-col>
- 收集器、节点或RPC缺乏详细的日志记录，这可能会使问题诊断变得困难，尤其是在发生崩溃或网络中断的情况下。
- 这种日志记录的不足会阻碍维护系统健康和及时解决问题的工作。
</pba-col>
<pba-col>
<img rounded style="height: 400px" src="./img/logs.webp" />
</pba-col>
</pba-cols>
---v
### 风险
- 难以诊断和解决系统问题，导致停机时间延长。
- 识别和缓解危害网络完整性的安全威胁的能力下降。
---v
### 案例研究
<pba-cols>
<pba-col>
- 在[最近的Kusama问题](https://kusama.polkassembly.io/referenda/263)期间，链停止生成区块数小时。
- 工程师们需要检查所有日志，以了解问题的原因或触发事件。
- 日志系统使他们能够检测到原因是一个最终确定的区块中存在争议。
</pba-col>
<pba-col>
<img rounded style="height: 500px" src="./img/kusama-incident.webp" />
<pba-cols>
<pba-col>
- 共识系统很复杂，几乎不会停止运行，但一旦停止，就很难重现导致其停止的场景。
- 因此，一个良好的日志系统有助于减少停机时间。
</pba-col>
<pba-col>
<img rounded style="height: 500px" src="./img/kusama-incident.webp" />
</pba-col>
</pba-cols>

---v
### 缓解措施
- 定期审查日志，识别任何可疑活动，并确定日志记录是否足够详细。
- 在你的模块关键部分添加日志记录。
- 实施仪表板来检测日志和指标中的异常模式。Grafana就是一个很好的例子，一些节点维护者用它来了解近期发生的问题。
---v
### 要点总结
- 日志对于诊断和解决系统问题极其重要。
- 日志记录不够详细可能会导致停机时间延长。
---
## 不一致的错误处理
---v
### 挑战
<pba-cols>
<pba-col>
- 错误/异常需要以一致的方式处理，以避免在系统关键部分产生攻击漏洞。
- 在处理一组项目时，如果其中一个出现故障，整个批次都可能失败。这可能会被想要阻止执行的攻击者利用。如果这种处理发生在像钩子这样的特权外部调用中，这可能会成为一个严重的问题。
</pba-col>
<pba-col>
<img rounded style="height: 600px" src="./img/inconsistent-error-handling.webp" />
</pba-col>
</pba-cols>
---v
### 风险
- 特权外部调用的拒绝服务（DoS）攻击。
- 系统出现意外行为。
---v
### 案例研究 - 解码连接数据 - 问题
```rust [0|6-11]
fn decode_concatenated_extrinsics(
  data: &mut &[u8],
) -> Result<Vec<<T as Config>::RuntimeCall>, ()> {
  let mut decoded_extrinsics = Vec::new();
  while !data.is_empty() {
    let extrinsic =
      <T as Config>::RuntimeCall::decode_with_depth_limit(
        sp_api::MAX_EXTRINSIC_DEPTH, 
        data
      ).map_err(|_| ())?;
    decoded_extrinsics.push(extrinsic);
  }
  Ok(decoded_extrinsics)
}
```
---v
### 案例研究 - 解码连接数据 - 问题
```rust [0|6-14]
fn decode_concatenated_extrinsics(
  data: &mut &[u8],
) -> Result<Vec<<T as Config>::RuntimeCall>, ()> {
  let mut decoded_extrinsics = Vec::new();
  while !data.is_empty() {
    if let Ok(extrinsic) =
      <T as Config>::RuntimeCall::decode_with_depth_limit(
        sp_api::MAX_EXTRINSIC_DEPTH, 
        data
      ) {
        decoded_extrinsics.push(extrinsic);
    } else {
      /// 处理损坏的外部调用...
    } 
  }
  Ok(decoded_extrinsics)
}
```
---v
### 缓解措施
- 验证错误处理与外部调用逻辑是否一致。
- 在批处理过程中：
  - 如果所有项目都必须始终被处理，直接传播错误以停止批处理。
  - 如果只需要处理部分项目，则处理错误并继续批处理。
---v
### 要点总结
- 确保进行错误处理。
- 优化批处理，以便处理错误，而不是浪费执行时间。
---
<!-- .slide: data-background-color="#4A2439" -->
### 问题