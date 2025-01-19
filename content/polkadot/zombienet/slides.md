---
title: Zombienet
description: Zombienet workshop
duration: 1 hour
---

<style>
    .colored {
        color: var(--r-link-color);
    }

    .colored-green {
        color: #18ffb2;
    }

    .colored-light-green {
        color: #c2ff33;
    }
</style>

# Zombienet

---

## 什么是 Zombienet？

Zombienet 是一个 <span class="colored">集成测试工具</span>，允许用户 _<span class="colored">创建</span>_ 和 _<span class="colored">测试</span>_ 临时的基于 Substrate 的网络。

---

## 为什么要使用 Zombienet？

集成测试总是 <span class="colored">复杂的</span>：

<br />

- 配置设置

<!-- .element: class="fragment" -->

- 端口管理

<!-- .element: class="fragment" -->

- 所有工件的就绪状态

<!-- .element: class="fragment" -->

- 可观测性

<!-- .element: class="fragment" -->

- 资源泄漏

<!-- .element: class="fragment" -->

---v

## 需要解决的麻烦

<br />

- 配置灵活性
- 本地环境
- 维护
- 对 CI 友好
- 扩展性
- 测试运行器

---v

## 目标

<br />

<pba-cols style="align-items:normal">
    <pba-col>

##### 轻松设置

- Toml / json
- 良好的默认值
- 模板语言

</pba-col>
<pba-col>

##### 多种环境

- **本地**
- **k8s**
- **podman**

</pba-col>
<pba-col>

##### 可扩展

- 自定义断言
- 直观的 <span class="colored">领域特定语言（D.S.L）</span>
- 模板语言

  </pba-col>
  </pba-cols>

---

<!-- .slide: data-background-color="#4A2439" -->

# 阶段

---

## 阶段

<pba-cols style="align-items:normal">
<pba-col>

创建

- 自定义链规范
- 自定义命令
- 端口映射
- 平行链注册

</pba-col>
<!-- .element: class="fragment" -->
<pba-col>

测试

- 自定义 <span class="colored">领域特定语言（D.S.L）</span>
- 多种断言
- 可扩展
- 自定义报告

</pba-col>
<!-- .element: class="fragment" -->
</pba-cols>

---

## Zombienet 选项

<pba-flex center>

- 作为二进制文件（[发布版本](https://github.com/paritytech/zombienet/releases)）
- 作为库（@zombienet）
- 作为容器（发布在 Docker Hub 上）
- 从源代码（[zombienet](https://github.com/paritytech/zombienet) 仓库）

</pba-flex>

Notes:

- 作为二进制文件：Linux 和 MacOS 的二进制文件可在 GitHub 的每个发布版本中获取。
- npm 包：cli、编排器、实用工具
- 镜像：docker.io/paritytech/zombienet
  代码可在 GitHub 上获取，其中包含有关如何构建和运行 Zombienet 的说明。
  （https://github.com/paritytech/zombienet）

---v

### 下载 Zombienet

```sh
# macOS
curl -L https://github.com/paritytech/zombienet/releases/download/v1.3.63/zombienet-macos
-o ./zombienet

# linux
curl -L https://github.com/paritytech/zombienet/releases/download/v1.3.63/zombienet-linux
-o ./zombienet

# 使其可执行
chmod +x zombienet
```

---

<!-- .slide: data-background-color="#4A2439" -->

# 让我们创建一个新网络！

---v

## 但首先，尝试手动操作……

<br />
<div>
<ul><li>创建链规范（平行链）</li></ul>

```sh
parachain-template-node build-spec --chain local \
--disable-default-bootnode > /tmp/para.json
```

</div>
<!-- .element: class="fragment" -->
<br />
<div>
<ul><li>创建链规范（中继链）</li></ul>

```sh
polkadot build-spec --chain rococo-local \
 --disable-default-bootnode > /tmp/relay.json
```

</div>
<!-- .element: class="fragment" -->

Notes:

教程 <https://docs.substrate.io/tutorials/build-a-parachain/>

---v

### 添加密钥*

<br />

当不使用 `--alice` 或 `--bob` 时，你需要提供额外的 `aura` 和 `grandpa` 密钥，并将它们注入到密钥库中！（**每个节点**）

```sh
./target/release/polkadot \
key insert --base-path /tmp/node01 \
  --chain /tmp/relay.json \
  --scheme Sr25519 \
  --suri <你的秘密种子> \
  --password-interactive \
  --key-type aura
```

<br />

```sh
./target/release/polkadot key insert \
  --base-path /tmp/node01 \
  --chain /tmp/relay.json \
  --scheme Ed25519 \
  --suri <你的秘密密钥> \
  --password-interactive \
  --key-type gran
```

Notes:

如果你使用开发账户（例如 alice、bob、charlie、dave 等），此步骤是可选的。

---v

- 启动中继链节点

```sh[1-2|4-10|12-18]
# 创建节点目录
  mkdir -p /tmp/relay/{alice,bob}

  ./target/release/polkadot \
  --alice \
  --validator \
  --base-path /tmp/relay/alice \
  --chain /tmp/relay.json \
  --port 30333 \
  --ws-port 9944

  ./target/release/polkadot \
  --bob \
  --validator \
  --base-path /tmp/relay/bob \
  --chain /tmp/relay.json \
  --port 30334 \
  --ws-port 9945
```

Notes:

为什么我们需要为 Alice 和 Bob 使用不同的端口？

---v

- 启动校对节点

```sh
# 创建节点目录
mkdir -p /tmp/para/alice

parachain-template-node \
--alice \
--collator \
--force-authoring \
--chain /tmp/para.json \
--base-path /tmp/para/alice \
--port 40333 \
--ws-port 8844 \
-- \
--execution wasm \
--chain /tmp/relay.json \
--port 30343 \
--ws-port 9977
```

---v

- 在中继链上注册 ParaId

1. 修改平行链链规范并创建原始格式
1. 生成创世 wasm 和状态
1. 使用 sudo 调用注册平行链

<br />

```sh[1,2|4,5|7,8]
parachain-template-node build-spec --chain /tmp/para-raw.json \
--disable-default-bootnode --raw > /tmp/para-raw.json

parachain-template-node export-genesis-wasm --chain /tmp/para-raw.json \
para-2000-wasm

parachain-template-node export-genesis-state --chain /tmp/para-raw.json \
para-2000-genesis-state
```

---

<!-- .slide: data-background-color="#4A2439" data-visibility="hidden" -->

# 活动

按照 [连接本地平行链](https://docs.substrate.io/tutorials/build-a-parachain/connect-a-local-parachain/) 的步骤启动你自己的网络。

---

## 非平凡的繁琐工作

<pba-cols style="align-items:normal">
<pba-col>

- 容易出错。
- 多个命令。
- 端口管理。
- 多个进程。

</pba-col>
<pba-col>

<div class="fragment center" style="font-size:150%;">Zombienet 允许你将所有设置都放在 <span style="color: var(--r-link-color);font-weight:bold;">仅</span> 一个文件中。</div>

</pba-col>
</pba-cols>

---v

## Zombienet 网络定义

Zombienet 允许你使用一个简单的配置文件 [定义你的网络](https://paritytech.github.io/zombienet/network-definition-spec.html)。

Notes:

<https://paritytech.github.io/zombienet/network-definition-spec.html>

---v

```toml[1|3-12|14-21]
# examples/0001-small-network.toml

[relaychain]
default_image = "docker.io/parity/polkadot:latest"
default_command = "polkadot"
chain = "rococo-local"

  [[relaychain.nodes]]
  name = "sub"

  [[relaychain.nodes]]
  name = "zero"

[[parachains]]
id = 1001
cumulus_based = true

  [parachains.collator]
  name = "collator01"
  image = "docker.io/parity/polkadot-parachain:latest"
  command = "polkadot-parachain"
```

Notes:

<https://github.com/pepoviola/zombienet-presentation-examples/blob/main/examples/0001-small-network.toml>

---v

### 启动网络

```sh
./zombienet spawn examples/0001-small-network.toml
```

---

<!-- .slide: data-background-color="#4A2439" -->

# 活动

尝试启动一个包含 `2` 个平行链的网络。

<br />

<https://paritytech.github.io/zombienet/>

---

## 使网络配置动态化

网络定义支持使用 [nunjucks](https://mozilla.github.io/nunjucks/) 模板语言（类似于 [tera](https://github.com/Keats/tera)）。
其中 <span class="colored-green">{{变量}}</span> 会被 <span class="colored-green">环境变量</span> 替换，并且你可以使用所有内置功能。

<br />

```toml[2]
[relaychain]
default_image = "{{ZOMBIENET_INTEGRATION_IMG}}"
default_command = "polkadot"
```

---v

## 使网络配置动态化

<img rounded style="width: 1200px" src="./img/zombienet-env-vars.png" />

---

## 提供商

Zombienet <span class="colored">提供商</span> 允许在不同环境中 <span class="colored-green">创建和测试</span> 网络。

---v

<pba-cols style="align-items:normal">

<pba-col>

<span class="colored">Kubernetes</span>
<br />

- 内部使用，与 [Grafana](https://grafana.com/oss/grafana/) 栈集成。
- 你需要提供自己的基础设施栈。

</pba-col>

<pba-col>

<span class="colored">Podman</span>
<br />

- 自动创建并连接 [Grafana](https://grafana.com/oss/grafana/) 栈的实例。
- 如果在网络定义中启用，则附加一个 jaeger 实例。

</pba-col>

<pba-col>

<span class="colored">Native</span>
<br />

- 允许连接到正在运行的 [Grafana](https://grafana.com/oss/grafana/) 栈。
  **（开发中）**

</pba-col>

</pba-cols>

---

<!-- .slide: data-background-color="#4A2439" -->

# 问题

---

## 认识测试运行器

Zombienet 内置的 <span class="colored">测试运行器</span> 允许用户使用简单的 <span class="colored-green">领域特定语言（D.S.L.）</span> 轻松直观地编写测试，使用一组自然语言表达式进行断言。

---v

### 内置断言

<br />

- <span class="colored">Prometheus</span>：查询暴露的指标/直方图并对其值进行断言。

- <span class="colored">Chain</span>：查询/订阅链的存储/事件。

- <span class="colored">Custom scripts</span>：运行自定义的 JavaScript 脚本或 Bash 脚本（在容器内）。

- <span class="colored">Node's logs</span>：在节点日志中匹配正则表达式/全局模式。

- <span class="colored">Integrations</span>：Zombienet 支持多种集成，如 jaeger 跨度、Polkadot 检查器和反向通道。

---v

```[1-3|6|7|14-16|18-20|22-24|26-28]
描述：小型网络平行链
网络：./0002-small-network-paras.toml
凭证：config  # 仅在k8s中使用

# 常用函数
validator: is up  # 检查组中的所有验证者是否正常运行
validator-0: parachain 1000在225秒内完成注册
validator-0: parachain 1001在225秒内完成注册

# 确保平行链正在生成区块
validator-0: parachain 1000的区块高度在300秒内至少达到5
validator-0: parachain 1001的区块高度在300秒内至少达到5

# 指标
validator-0: reports node_roles的值为4
validator-0: reports的区块高度在15秒内至少达到2

# 日志（模式会转换为正则表达式）
validator-1: 日志行在10秒内匹配通配符“*rted #1*”
validator-1: 日志行在10秒内匹配“Imported #[0-9]+”

# 系统事件（模式会转换为正则表达式）
validator-2: 系统事件在10秒内包含“A candidate was included”
validator-2: 系统事件在10秒内匹配通配符“*was backed*”

# 自定义脚本
validator-0: 在200秒内使用“alice”运行js-script ./custom.js
validator-0: 在200秒内运行./custom.sh
```
Notes:
前3行是头部信息。
每一行代表一个断言。
每个断言按顺序执行。
对一个组的断言会检查组中的每个节点。
“within”关键字表示持续尝试，直到时间到期。

---
## DSL扩展
学习新的领域特定语言（DSL）可能会很枯燥，但如果你使用的是Visual Studio Code，我们开发了一个[扩展](https://github.com/paritytech/zombienet-vscode-extension)，可以帮助你轻松编写测试。
Notes:
展示扩展链接：<https://github.com/paritytech/zombienet-vscode-extension>

---
# 演示时间
```sh
./zombienet -p native test examples/0002-small-network-paras.zndsl
```

---
# 可扩展性
<span class="colored">Zombienet</span>允许用户使用自定义js断言来扩展并运行自定义测试。
---v
## 自定义js
```sh
# 自定义脚本
validator-0: 在200秒内使用“alice”运行js-script ./custom.js
```
```js
async function run(nodeName, networkInfo, args) {
  const { wsUri, userDefinedTypes } = networkInfo.nodesByName[nodeName];
  const api = await zombie.connect(wsUri, userDefinedTypes);
  const validator = await api.query.session.validators();
  return validator.length;
}

module.exports = { run };
```
Notes:
Zombienet会加载你的脚本并调用run函数。
它会传递节点名称、网络信息以及断言中的参数数组。
你的函数可以访问zombie对象，该对象提供了connect、ApiPromise、Keyring等工具 。
断言可以验证你脚本的返回值或执行结果。
\*这与在PolkadotJS应用程序的开发者页面编写脚本的方式类似(https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc.polkadot.io#/js)。

---
## 更多可扩展性
<span class="colored">Zombienet</span>还允许用户将其作为库使用，以便与正在运行的网络进行自定义交互。
---v
### 作为库使用
- <span class="colored">@zombienet/orchestrator</span>模块将start函数作为入口点。
- 返回一个[network](https://github.com/paritytech/zombienet/blob/main/javascript/packages/orchestrator/src/network.ts#L77)实例，其中包含有关正在运行的网络拓扑的所有信息。
- 你还可以使用[test](https://github.com/paritytech/zombienet/blob/main/javascript/packages/orchestrator/src/orchestrator.ts#L853)函数，并传入一个回调函数来运行你的测试。
- <span class="colored">@zombienet/utils</span>模块提供了诸如_readNetworkConfig_等各种实用函数。
---v
```js[1|2|6-7|10-12|14|]
import {start} from "@zombienet/orchestrator";
import { readNetworkConfig } from "@zombienet/utils";

const ZOMBIENET_CREDENTIALS = "";

// 可以是toml或json格式
const launchConfig = readNetworkConfig("../examples/0001-small-network.toml");

( async () => {
    const network = await start(ZOMBIENET_CREDENTIALS, launchConfig, {
        spawnConcurrency: 5,
    });

    // 编写你自己的测试，`network`将包含所有网络信息
})();
```

---
## 未来发展方向...
🚧 🚧 <span class="colored"><b>Zombienet v2</b>（又名[SDK](https://github.com/paritytech/zombienet-sdk)）</span>目前正在开发中 🚧 🚧
[SDK](https://github.com/paritytech/zombienet-sdk)将提供一组构建模块，用户可以组合这些模块来创建网络并与之交互，还会提供一个流畅的API，用于为正在运行的网络构建不同的拓扑结构和断言。
Notes:
SDK仓库：https://github.com/paritytech/zombienet-sdk

---
## 致谢与贡献
<span class="colored"><b>Zombienet</b></span>从<span class="colored-light-green">polkadot-launch</span>和<span class="colored-light-green">SimNet</span>中汲取了灵感并借鉴了一些模式。
我们鼓励大家对其进行测试、提供反馈、提出问题并做出贡献。

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题
---
## 活动
- 启动一个包含两个验证者和一个平行链的网络。
- 添加测试以确保：
  - 区块生成
  - 对等节点数量
  - 节点角色

---
## 更多资源！
> 查看演讲者备注（点击“s” 😉）
Notes:
Zombienet相关资源：
- [仓库](https://github.com/paritytech/zombienet/)
- [文档](https://paritytech.github.io/zombienet/)
- [v2路线图](https://github.com/orgs/paritytech/projects/55/)
- [sub0演讲幻灯片](https://docs.google.com/presentation/d/1wPjbrqLg9MCfygvBYV5gDra39cSr5TXg/edit)
- [sub0演讲](https://www.youtube.com/watch?v=QKTZZCpdGH4)
- [演示示例](https://github.com/pepoviola/zombienet-presentation-examples)
- [设置本地测试网](https://hackmd.io/kSFS2ButRESeJ7hu_iKKoA) 


