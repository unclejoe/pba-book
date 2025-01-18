---
title: Interacting With a Substrate Blockchain
duration: 60 minutes
---

---
title: 与Substrate区块链交互
duration: 60分钟
---

# 与Substrate区块链交互

---

## 与Substrate区块链交互

<img style="width: 1200px;" src="../intro/img/dev-4-1-json.svg" />

Notes:

这些交互中的许多都在一个Wasm二进制大对象（blob）中进行。

那么你需要问自己什么问题呢？使用哪个运行时二进制大对象。

几乎所有的外部通信都是通过JSON-RPC进行的，所以让我们仔细看看。

---

## JSON-RPC

> JSON-RPC是一种用JSON编码的远程过程调用协议。它类似于XML-RPC协议，只定义了少数数据类型和命令。

---v

### JSON-RPC

```json
{
  "jsonrpc": "2.0",
  "method": "subtract",
  "params": { "minuend": 42, "subtrahend": 23 },
  "id": 3
}
```

<br />

```json
{ "jsonrpc": "2.0", "result": 19, "id": 3 }
```

<!-- .element: class="fragment" -->

---v

### JSON-RPC

- 完全与传输协议无关。
- 基于Substrate的链同时支持`websocket`和`http`（如果需要，也支持`wss`和`https`）。

> 通过`--ws-port`和`--rpc-port`，分别为9944和9934。

---v

### JSON-RPC

- JSON-RPC方法通常写成`scope_method`的形式

  - 例如：`rpc_methods`，`state_call`

- &shy;<!-- .element: class="fragment" --> `author`：用于向链提交内容。
- &shy;<!-- .element: class="fragment" --> `chain`：用于获取关于_区块链_数据的信息。
- &shy;<!-- .element: class="fragment" --> `state`：用于获取关于_状态_数据的信息。
- &shy;<!-- .element: class="fragment" --> `system`：关于链的信息。
- &shy;<!-- .element: class="fragment" --> `rpc`：关于RPC端点的信息。

Notes:

回顾：

- <https://paritytech.github.io/substrate/master/sc_rpc_api/index.html>
- <https://paritytech.github.io/substrate/master/sc_rpc/index.html>

完整列表也可以在这里看到：<https://polkadot.js.org/docs/substrate/rpc/>

---v

### JSON-RPC

- 让我们看几个例子：

- `system_name`，`system_chain`，`system_chainType`，`system_health`，`system_version`，`system_nodeRoles`，`rpc_methods`，`state_getRuntimeVersion`，`state_getMetadata`

```sh
wscat \
  -c wss://kusama-rpc.polkadot.io \
  -x '{"jsonrpc":"2.0", "id": 42, "method":"rpc_methods" }' \
  | jq
```

---v

### JSON-RPC: 运行时无关性

- 不用说，RPC方法是与运行时无关的。上述内容并没有告诉你是否使用了FRAME。
- <!-- .element: class="fragment" --> 除了……在某种程度上的元数据。

---v

### JSON-RPC: 运行时API

- 虽然与运行时无关，但许多RPC调用会落在运行时API中。
- &shy;<!-- .element: class="fragment" --> RPC端点有一个`at: Option<hash>`，运行时API也有，真巧！🌈
  - &shy;<!-- .element: class="fragment" --> 还记得`state`这个作用域吗？

---v

### JSON-RPC: 扩展

- 运行时可以扩展更多自定义的RPC方法，但新的趋势是使用`state_call`。

---v

### JSON-RPC: 安全性

- 一些RPC方法是不安全的 😱。

---v

### JSON-RPC: 弹性

RPC服务器与轻客户端

---

### JSON-RPC: 应用

- 在`SCALE`和`JSON-RPC`的基础上，已经构建了大量的库。

- &shy;<!-- .element: class="fragment" --> `PJS-API` / `PJS-APPS`
- &shy;<!-- .element: class="fragment" --> `capi`
- &shy;<!-- .element: class="fragment" --> `subxt`
- &shy;<!-- .element: class="fragment" --> 还有很多！

Notes:

<https://github.com/JFJun/go-substrate-rpc-client>
<https://github.com/polkascan/py-substrate-interface>
更多内容在这里：<https://project-awesome.org/substrate-developer-hub/awesome-substrate>

---

### JSON-RPC: 小活动

在Kusama中：

- 找到创世块哈希。
- 第10,000,000个块的外部交易（extrinsics）数量。
- 块号存储在`twox128("System") ++ twox128("Number")`下。
  - 现在就找，以及在第10,000,000个块时找。

<br />

- 参考“Substrate; Show Me The Code”讲座来找到正确的RPC端点。
- 你有15分钟时间！

Notes:

```sh
# 10,000,000的十六进制表示
printf "%x\n" 10000000
# 创世块哈希
wscat -c wss://kusama-rpc.polkadot.io -x '{"jsonrpc":"2.0", "id":72, "method":"chain_getBlockHash", "params": ["0x0"] }' | jq
# 高度为10,000,000的块的哈希
wscat -c wss://kusama-rpc.polkadot.io -x '{"jsonrpc":"2.0", "id":72, "method":"chain_getBlockHash", "params": ["0x989680"] }' | jq
# 高度为1,000,000的块
wscat -c wss://kusama-rpc.polkadot.io -x '{"jsonrpc":"2.0", "id":72, "method":"chain_getBlock", "params": ["0xdcbaa224ab080f2fbf3dfc85f3387ab21019355c392d79a143d7e50afba3c6e9"] }' | jq

# 现在的`0x26aa394eea5630e07c48ae0c9558cef702a5c1b19ab7a04f536c519aca4983ac`
wscat -c wss://kusama-rpc.polkadot.io -x '{"jsonrpc":"2.0", "id":72, "method":"state_getStorage", "params": ["0x26aa394eea5630e07c48ae0c9558cef702a5c1b19ab7a04f536c519aca4983ac"] }' | jq
# 在第1,000,000个块时的`0x26aa394eea5630e07c48ae0c9558cef702a5c1b19ab7a04f536c519aca4983ac`
wscat -c wss://kusama-rpc.polkadot.io -x '{"jsonrpc":"2.0", "id":72, "method":"state_getStorage", "params": ["0x26aa394eea5630e07c48ae0c9558cef702a5c1b19ab7a04f536c519aca4983ac", "0xdcbaa224ab080f2fbf3dfc85f3387ab21019355c392d79a143d7e50afba3c6e9"] }' | jq
```

注意，我们得到的这个数字是小端序（SCALE）编码的值，也就是我们最初传入的值。

---

## Polkadot JS API

简要介绍。

优秀的教程在这里：<https://polkadot.js.org/docs/>

---v

## Polkadot JS API

<img src="./img/pjs.png" style="width: 500px" />

---v

### PJS: 概述

- `api.registry`
- `api.rpc`

---v

### PJS: 概述

几乎所有其他内容基本上都是基于`api.rpc`构建的。

- `api.tx`
- `api.query`
- `api.consts`
- `api.derive`

在学习FRAME的同时请复习这些内容，它们会变得非常有意义！

---v

### PJS: 工作坊 🧑‍💻

Notes:

```ts
import { ApiPromise, WsProvider } from "@polkadot/api";
const provider = new WsProvider("wss://rpc.polkadot.io");
const api = await ApiPromise.create({ provider });
api.stats;
api.isConnected;
 // 这个值从哪里来？
api.runtimeVersion;
// 这个值从哪里来？
api.registry.chainDecimals;
api.registry.chainTokens;
api.registry.chainSS58;
// 这个值从哪里来？
api.registry.metadata;
api.registry.metadata.pallets.map(p => p.toHuman());
api.registry.createType();
api.rpc.chain.getBlock()
api.rpc.system.health()
await api.rpc.system.version()
await api.rpc.state.getRuntimeVersion()
await api.rpc.state.getPairs("0x")
await api.rpc.state.getKeysPaged("0x", 100)
await api.rpc.state.getStorage()
await api.rpc.state.getStorageSize("0x3A636F6465"),
```

<https://polkadot.js.org/docs/substrate/rpc#getstoragekey-storagekey-at-blockhash-storagedata>

一些其他随机的内容：

```ts
api.createType("Balance", new Uint8Array([1, 2, 3, 4]));

import { blake2AsHex, xxHashAsHex } from "@polkadot/util-crypto";
blake2AsHex("Foo");
xxHashAsHex("Foo");
```

---

## `subxt`

- 类似于Rust中的`PJS`。
- 真正神奇的是，它在编译时通过获取元数据来生成类型，或者静态链接元数据。
- ……当代码（因此元数据）发生变化时，可能需要手动更新。

---

## 更多资源！😋

> 查看演讲者备注（点击“s” 😉）

Notes:

- 查看“客户端库”部分：<https://project-awesome.org/substrate-developer-hub/awesome-substrate>
- <https://paritytech.github.io/json-rpc-interface-spec/introduction.html>
- 完整的`subxt`指南：<https://docs.rs/subxt/latest/subxt/book/index.html>
```
