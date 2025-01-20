---
title: XCM Beyond Asset Transfers
description: Deep dive on advanced XCM use cases beyond asset transfers and bridging
duration: 1 hour
---

# 资产转移和桥接之外的高级XCM用例

---

## 大纲

<pba-flex center>

1. 先决条件
2. XCMultisig
3. XCM通用接口
4. 通用XCM技巧
5. 结论
6. 下一步
7. 参考资料

</pba-flex>

---

## 先决条件

以下是需要具备的条件：

<pba-flex center>

- 了解XCM核心概念
- 了解XCM链配置

</pba-flex>

---

## XCMultisig

[InvArch Network](https://invarch.network/) 有XCMultisig的概念，这些实体存在于InvArch Network运行时中，并为许多其他区块链上的用户提供高级多签功能。

让我们来看看它是如何工作的！

Notes:

这个名称的由来是因为它们的XCM功能，更具体地说，是因为这些实体的逻辑是在InvArch Network运行时中定义的，但在所有其他连接的链中以相同的账户地址存在，从而允许它们通过XCM在这些链中进行交易。

---v

### 概述

<diagram class="mermaid">
stateDiagram-v2
state Polkadot {
    direction LR

    state InvArch {
        direction LR
        v0: 多签ID 0
        sxc: 发送XCM调用
        vacc: 0x123...

        state if_state <<choice>>
        v0 --> if_state

        if_state --> sxc
        if_state --> vacc

        vacc --> [*]: 交易


        sxc --> h0
        sxc --> i0
        sxc --> b0
    }

    state HydraDX {
        direction LR

        h0: 多签ID 0
        hmxs: **XCM转换器**
        hacc: 0x123...

        h0 --> hmxs
        hmxs --> hacc
        hacc --> [*]: 交易
    }

    state Interlay {
        direction LR

        i0: 多签ID 0
        imxs: **XCM转换器**
        iacc: 0x123...

        i0 --> imxs
        imxs --> iacc
        iacc --> [*]: 交易
    }

    state Bifrost {
        direction LR

        b0: 多签ID 0
        bmxs: **XCM转换器**
        bacc: 0x123...

        b0 --> bmxs
        bmxs --> bacc
        bacc --> [*]: 交易
    }

}
</diagram>

---v

### 消息详情

为了更好地理解这一切是如何工作的，让我们来看看发送的消息及其来源。

```rust [0|1-13|15-33|35|37-53|55|57-75]
let multisig: MultiLocation = MultiLocation {
  parents: 1,
  interior: Junctions::X3(
    Junction::Parachain(<T as pallet::Config>::ParaId::get()),
    Junction::PalletInstance(<T as pallet::Config>::INV4PalletIndex::get()),
    Junction::GeneralIndex(0u128),
  ),
};

let multisig_interior = Junctions::X2(
  Junction::PalletInstance(<T as pallet::Config>::INV4PalletIndex::get()),
  Junction::GeneralIndex(0u128),
);

let destination = MultiLocation {
  parents: 1,
  interior: Junctions::X1(
    Junction::Parachain(1234)
  ),
};

let fee_asset_location = MultiLocation {
  parents: 1,
  interior: Junctions::X2(
    Junction::Parachain(1234),
    Junction::GeneralIndex(0),
  ),
};

let fee_multiasset = MultiAsset {
  id: AssetId::Concrete(fee_asset_location),
  fun: Fungibility::Fungible(1000000000000),
};

let call = vec![...];

let message = Xcm(vec![
  Instruction::WithdrawAsset(fee_multiasset.clone().into()),
  Instruction::BuyExecution {
    fees: fee_multiasset,
    weight_limit: WeightLimit::Unlimited,
  },
  Instruction::Transact {
    origin_kind: OriginKind::Native,
    require_weight_at_most: 5000000000,
    call: <DoubleEncoded<_> as From<Vec<u8>>>::from(call),
  },
  Instruction::RefundSurplus,
  Instruction::DepositAsset {
    assets: MultiAssetFilter::Wild(WildMultiAsset::All),
    beneficiary: multisig,
  },
]);

pallet_xcm::Pallet::<T>::send_xcm(multisig_interior, destination, message)?;

// Pallet XCM将在消息的索引0处添加一个DescendOrigin指令。
Instruction::DescendOrigin(multisig_interior)

// 这会改变初始的Origin
MultiLocation {
  parents: 1,
  interior: Junctions::X1(
    Junction::Parachain(<T as pallet::Config>::ParaId::get()),
  ),
}
// 变为
MultiLocation {
  parents: 1,
  interior: Junctions::X3(
    Junction::Parachain(<T as pallet::Config>::ParaId::get()),
    Junction::PalletInstance(<T as pallet::Config>::INV4PalletIndex::get()),
    Junction::GeneralIndex(0u128),
  ),
}
```

---v

### XCM转换器

现在我们了解了消息的来源和结构，让我们来看看那些“XCM转换器”！

<diagram class="mermaid">
stateDiagram-v2
  direction LR

    para: 平行链2125
    pal: 模块51
    m: 多签ID 0
    acc: 0x123...

    para --> if
    pal --> if
    m --> hash

      state Checks {
        if: 平行链 == 2125 && 模块 == 51
        if --> [*]: 不匹配
        if --> Hasher: 匹配
      }

      state Hasher {
        cs: 常量盐

        cs --> hash
      }

    hash --> acc

</diagram>

Notes:

自定义哈希器的作用是复制源链中的账户生成过程。这些检查和哈希器的组合构成了转换器，用于为我们的MultiLocation返回AccountIds和原生Origin。

---v

### 如果我们将AccountId来源映射到其中的确切账户会发生什么？

## 账户冒充！

<!-- .element: class="fragment" data-fragment-index="0" -->

嘿，链B，我正在向你发送一个来自我的一个用户的余额转移请求，他们的地址是“链B的国库” ;)

<!-- .element: class="fragment" data-fragment-index="1" -->

# 信任！

<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:

一定要强调这一点！

---

## XCM通用接口

XCM可以用作多个区块链之上的通用API抽象层。

通过一些巧妙的用法，我们可以构建能够以通用方式被dApp集成的链，也可以构建能够轻松集成多个链而无需任何自定义逻辑的dApp。

---v

## 概念

###### 由XCM驱动的多链NFT市场

想象一个NFT市场，不仅支持多个链，还支持这些链选择实现的任何标准！

---v

### 如何实现？

<diagram class="mermaid">
stateDiagram-v2
  direction TB

    ui: 用户界面
    xcm: XCM API
    indexer: 索引器

    ui --> xcm

    indexer --> ui

    xcm --> axti
    xcm --> mxti
    xcm --> cxti

    state Asset_Hub {
      axti: XCM资产交换器
      apu: 模块Uniques
      apn: 模块NFTs

      axti --> apu
      axti --> apn
    }

    state Moonbeam {
      mxti: XCM资产交换器
      mpe: 模块EVM

      mxti --> mpe
    }

    state Chain_C {
      cxti: XCM资产交换器
      cpu: 模块Uniques
      cpc: 模块Contracts

      cxti --> cpu
      cxti --> cpc
    }

</diagram>

---v

### 匹配NFT

```rust [0|4-15|18-21]
MultiAsset {
  // 在哪里可以找到NFT（NFT模块中的合约或集合）
  id: AssetId::Concrete (
    Multilocation {
      parents: 0,
      interior: Junctions::X3(
        // 平行链ID，以便我们可以预先检查此消息是否是针对此链的
        Junction::Parachain (para_id),
        // 模块ID，以便我们知道应该使用哪个模块来查找NFT
        Junction::PalletInstance(pallet_id),
        // 通用索引，通过整数ID选择特定的集合
        // 或通用键，通过合约ID选择特定的集合
        Junction::GeneralIndex(collection_id) or Junction::GeneralKey(contract_address),
      )
    }
  ),
  // NFT本身
  fun: Fungibility::NonFungible(
    // 集合中特定NFT实例，通过其ID选择
    AssetInstance::Instance(nft_id)
  )
}
```

---v

### 实现资产交换器

```rust [1-20|22-38|40-48]
pub trait AssetExchange {
	/// 资产交换的处理程序。
	///
	/// - `origin`：尝试进行交换的位置；这通常无关紧要。
	/// - `give`：已从调用者处移除的资产。
	/// - `want`：如果发生任何交换，应给予调用者的最小资产数量。如果提供了更多资产，则它们通常应属于相同的资产类别（如果可能的话）。
	/// - `maximal`：如果为`true`，则应尽可能多地进行交换。
	///
	/// 返回`Ok`以及已与`give`交换的新资产集。至少应包含`want`。`give`中的某些资产也可能在这个集合中。在返回`Err`的情况下，将返回`give`。
	fn exchange_asset(
		origin: Option<&MultiLocation>,
		give: Assets,
		want: &MultiAssets,
		maximal: bool,
	) -> Result<Assets, Assets>;
}

struct MyNftStandardExchanger;

impl AssetExchange for MyNftStandardExchanger {
	fn exchange_asset(
		origin: Option<&MultiLocation>,
		give: Assets,
		want: &MultiAssets,
		maximal: bool,
	) -> Result<Assets, Assets> {
    match (give, want) {
      (FUNGIBLE, NONFUNGIBLE) => MyNftPallet::buy(...),
      (NONFUNGIBLE, FUNGIBLE) => MyNftPallet::sell(...),
      (NONFUNGIBLE, NONFUNGIBLE) => MyNftPallet::swap(...),
      (FUNGIBLE, FUNGIBLE) => Err(give),
    }
	}
}

impl xcm_executor::Config for XcmConfig {
  ...
  type AssetExchanger = (
    MyNftStandardExchanger,
    EvmNftExchanger,
    PalletUniquesExchanger
  );
  type AssetTransactor = AssetTransactors;
}
```

---

## 通用XCM技巧

在本节中，我们将介绍一些使用XCM进行开发的通用技巧。

---v

### MultiLocations和MultiAssets

决定如何将MultiLocations映射到运行时中的实体非常重要，因为这些MultiLocations最终将在其他XCM连接的链中使用。

```rust [1-4|6-8|10-11|13-14|16-17|19-24|26-32]
// Main runtime token
Junctions::X1(Parachain(para_id));
Junctions::X2(Parachain(para_id), GeneralIndex(main_token_id));
Junctions::X2(Parachain(para_id), PalletInstance(balances_pallet_id));

// Other tokens
Junctions::X2(Parachain(para_id), GeneralIndex(token_id));
Junctions::X3(Parachain(para_id), PalletInstance(tokens_pallet_id), GeneralIndex(token_id));

// Runtime protocols (i.e. Treasury or it's account)
Junctions::X2(Parachain(para_id), PalletInstance(treasury_pallet_id));

// Wasm smart contracts
Junctions::X3(Parachain(para_id), PalletInstance(contracts_pallet_id), AccountId32(wasm_contract_account));

// EVM smart contracts
Junctions::X3(Parachain(para_id), PalletInstance(evm_pallet_id), AccountKey20(evm_contract_account));

// Wasm or EVM contracts
Junctions::X3(
  Parachain(para_id),
  PalletInstance(contracts_pallet_id || evm_pallet_id),
  AccountId32(wasm_contract_account) || AccountKey20(evm_contract_account),
);

// NFTs
MultiAsset {
  // Match collection
  id: Concrete(wasm_or_evm_multilocation || X2(Parachain(para_id), PalletInstance(nft_pallet_id))),
  // Match item
  fun: Fungibility::NonFungible(AssetInstance::Index(nft_id))
};
```

---v

### 消息指令

```rust [1-23|25-45]
// 支付执行费用并退还剩余费用
Xcm(vec![
  // 提取在此消息中使用的资产，将金额存入持有寄存器。
  Instruction::WithdrawAsset(fee_multiasset.into()),
  // 为此消息的执行支付费用。
  Instruction::BuyExecution {
    // 我们在第一条指令中提取的资产和金额。
    fees: fee_multiasset,
    // 我们愿意购买的最大权重数量。
    weight_limit: WeightLimit::Unlimited,
  },
  // 需要支付执行费用的一条或一组指令。
  <支付执行费用的指令>,
  // 将未使用的已购买权重退还到持有寄存器。
  Instruction::RefundSurplus,
  // 将持有寄存器中的资产存入一个账户的余额中。
  Instruction::DepositAsset {
    // 匹配持有寄存器中所有资产的总量。
    assets: MultiAssetFilter::Wild(WildMultiAsset::All),
    // 退还费用的接收者，通常是最初支付费用的来源。
    beneficiary: account_id_multilocation,
  },
]);

// XCM断言

// 如果运行时中不存在所描述的模块，则出错。
ExpectPallet {
  // 模块索引。
  index: 21,
  // 模块名称。
  name: "Referenda".as_bytes().to_vec(),
  // 模块的名称。
  module_name: "pallet_referenda".as_bytes().to_vec(),
  // 库的主版本。
  crate_major: 4,
  // 可接受的最小次版本。
  min_crate_minor: 0,
}

// 如果所描述的资产和数量未出现在持有寄存器中，则出错。
ExpectAsset(MultiAsset {
  id: AssetId::Concrete(asset_multilocation),
  fun: Fungibility::Fungible(1_000_000_000_000u128),
})
```

---

## 结论

在本次展示中，我们介绍了几个现实世界中的XCM用例以及使用消息标准的一些通用技巧，这里的目标是给你一些启发和想法，以便你可以开始摆弄XCM，为你自己的想法提供动力并增强区块链应用程序的能力！

---

## 参考资料

- [XCM源代码](https://github.com/paritytech/polkadot-sdk/tree/master/polkadot/xcm) - paritytech/polkadot 存储库中主要XCM实现的源代码。

<!-- 预计很快会为 SDK 之外的 XCM 建立新的存储库 -->

- [XCM文档](https://paritytech.github.io/xcm-docs/) - XCM（跨共识消息）的官方文档。
- [InvArch 的 Pallet Rings](https://github.com/InvArch/InvArch-Frames/tree/main/pallet-rings) - XCMultisigs 的 XCM 抽象模块的参考实现。