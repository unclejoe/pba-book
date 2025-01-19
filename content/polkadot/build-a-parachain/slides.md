---
title: Build a Parachain
description: Build a simple parachain without Cumulus
duration: 1.5 hours
---

---
标题：构建一条平行链
描述：在不使用 Cumulus 的情况下构建一个简单的平行链
时长：1.5 小时
---

# 构建一条平行链

> _注意_ 这里使用的是 Polkadot v1.0.0 版本的存档仓库。

<!-- FIXME 下次更新为 https://github.com/paritytech/polkadot-sdk/ -->

---

## 议程

- 在不使用 Cumulus 的情况下构建一个简单的收集人节点
- 介绍 Cumulus 以及如何构建一个平行链
- 实践环节：手动注册一个平行链
- 实践环节：如何获取一个平行链插槽

---

## 开始之前：

```sh
# 我们需要一个 Polkadot 二进制文件，例如
git clone https://github.com/paritytech/polkadot/
cd polkadot
cargo build --release

# 提前编译以节省时间：
git clone https://github.com/Polkadot-Blockchain-Academy/cumuless-parachain-PBA-BA-2023
cd cumuless-parachain-PBA-BA-2023
# 为你自己创建一个分支
git checkout -b <这里输入你的 GitHub 用户名>
cargo build --release
```

---

## 构建简单平行链

我们将在不使用 Cumulus 的情况下构建一个简单的平行链！

<pba-flex center>

- PVF
- 收集人节点

<pba-flex>

Notes:

这将是一个不使用 FRAME 构建的平行链。
只会有一个收集人节点，且没有收集人节点选择逻辑。
没有消息处理。
没有交易。
没有运行时升级。
没有平行链全节点。

---

## 平行链要求

一个平行链需要两样东西：

<pba-flex center>

1. 一个暴露了 `validate_block` 函数的 Wasm 运行时
1. 能够同步中继链区块并与中继链通信的节点端

<pba-flex>

Notes:

与中继链通信意味着使用 Polkadot 的网络协议来分发 PoV（证明有效性）。

---

## 最小化平行链运行时（PVF）

```rust
#![no_std]
#![cfg_attr(
	not(feature = "std"),
	feature(core_intrinsics, lang_items, core_panic_info, alloc_error_handler)
)]

// 使 Wasm 二进制文件可用。
#[cfg(feature = "std")]
include!(concat!(env!("OUT_DIR"), "/wasm_binary.rs"));

#[cfg(feature = "std")]
/// 解包后的 Wasm 二进制文件。如果使用 `BUILD_DUMMY_Wasm_BINARY` 构建，该函数会引发 panic。
pub fn wasm_binary_unwrap() -> &'static [u8] {
	Wasm_BINARY.expect(
		"开发用的 Wasm 二进制文件不可用。仅在禁用该标志的情况下支持测试。",
	)
}

#[cfg(not(feature = "std"))]
#[panic_handler]
#[no_mangle]
pub fn panic(_info: &core::panic::PanicInfo) -> ! {
	core::intrinsics::abort()
}

#[cfg(not(feature = "std"))]
#[alloc_error_handler]
#[no_mangle]
pub fn oom(_: core::alloc::Layout) -> ! {
	core::intrinsics::abort();
}

#[cfg(not(feature = "std"))]
#[no_mangle]
pub extern "C" fn validate_block(_params: *const u8, _len: usize) -> u64 {
	loop {}
}
```

Notes:

`panic` 和 `oom` 处理程序是 Rust 特有的，你无需担心。
如果我们在 `validate_block` 函数中实际包含一个无限循环，那么中继链验证者将永远不会支持/包含该平行链区块。

---

## 平行链节点端

<pba-flex center>

- 我们的节点将同步中继链区块
- 当导入新的最佳区块时，<br />我们将连接到支持组
- 然后我们将向该组中的一个验证者宣传我们的区块（“整理”）<br />
- 验证者将使用 `collator-protocol` 从我们这里请求整理后的区块
- 现在就由验证者来决定是否包含我们的区块

<pba-flex>

Notes:

验证者被随机分配到小型支持组中，这些组会按照 `group_rotation_frequency` 定期轮换。
目前，收集人节点只有在其前一个区块被中继链包含后（记住 `CandidateIncluded`）才能生成下一个区块。
由于包含操作是在候选区块被支持后的下一个区块中进行的，这意味着收集人节点每 12 秒才能生成一个区块。异步支持将改变这一点。

---

## 收集人协议

Polkadot 包含了收集人协议的收集人节点端和验证者端的实现。

```rust
/// 参与收集人协议的是哪一方
pub enum ProtocolSide {
	/// 验证者在中继链上运行。
	Validator {
		/// 保存验证者密钥的密钥库。
		keystore: SyncCryptoStorePtr,
		/// 针对不活跃的对等节点或验证者的驱逐策略。
		eviction_policy: CollatorEvictionPolicy,
	},
	/// 收集人节点在平行链上运行。
	Collator(
		PeerId,
		CollatorPair,
		IncomingRequestReceiver<request_v1::CollationFetchingRequest>,
	),
}
```

Notes:

我们将使用配置为收集人节点端的 Polkadot 作为库。

---

## 是时候深入研究代码了

我们的 PBA 平行链是以下版本的精简版：

- <https://github.com/paritytech/polkadot/tree/master/parachain/test-parachains/adder>

---

## 练习

使平行链的状态成为一个固定大小的二维字段（例如 25x25），该字段根据生命游戏的规则在每个区块中演变，并在每个区块中打印状态。

- <https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life>
- <https://www.rosettacode.org/wiki/Conway%27s_Game_of_Life>

---

<!-- .slide: data-background-color="#4A2439" -->

## 问题