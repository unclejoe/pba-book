---
title: XCM in Use # Also update the h1 header on the first slide to the same name
description: Leverage HRMP to remote compound on Substrate-based DEX
duration: 2 hours
---

# XCM的实际应用

### 使用XCM构建去中心化应用（dApps），借助polkadot.js.org/apps和node.js

Notes:

正如我们在第三章中所学，XCM模块（pallet）充当XCVM子系统和FRAME子系统之间的桥梁。
它使我们能够发送和执行XCM，并与XCM执行器进行交互。
在本章中，我作为OAK Network的创始人兼平行链开发者，将演示如何使用XCM构建产品，并从polkadot.js应用程序和Javascript代码中进行调度。

---

### _在本次讲座结束时，你将能够：_

<pba-flex center>

1. 为平行链HRMP消息配置XCM。
1. 理解XCM消息的构建和执行。
1. 使用模板在平行链之间执行HRMP交易。
1. 熟练使用xcm-tools调试XCM。

</pba-flex>

---

# 概述

<pba-flex center>

1. 定义产品
1. 准备工作
1. 编写XCM消息
1. 构建客户端代码
1. 实时调试

</pba-flex>

Notes:

在本次会议中，我将引导你完成构建XCM实际用例的过程，具体是从平行链开发者的角度出发。
我们的主要重点将是为平行链进行开发，而不是像Polkadot这样的中继链。
因此，我们将主要关注HRMP消息，实现两个平行链之间的横向通信。

1. 定义你的产品：我们将从定义我们想要构建的产品或应用程序开始，明确其目标和功能。
1. 准备链配置：接下来，我们将准备必要的链配置，确保我们的应用程序与目标区块链环境良好集成。
1. 编写XCM消息：我们将深入编写XCM消息，这对于我们应用程序的不同组件之间的通信和交互至关重要。
1. 构建客户端代码：这一步将涉及为我们的应用程序实际开发客户端代码，实现我们之前设计的逻辑和功能。
1. 实时调试：最后，我们将探索如何在实时环境中调试我们的应用程序，确保它正确且高效地运行。

在本次演示结束时，你将对XCM框架有全面的了解，并具备有效构建自己的应用程序的能力。

让我们开始吧！

---

# 定义产品

目标：在Moonriver平行链上建立无缝的MOVR月度定期支付。

Notes:

在本次演示中，我们的主要目标是在Moonriver平行链上建立无缝的MOVR月度定期支付。
为了实现这一目标，我们将利用一个强大的外部调用 `automationTime.scheduleXcmpTask`，该调用将在Turing Network上远程执行。
这将在每个月末触发一次支付，确保支付过程顺利且自动化。

---v

## 我们需要做什么

我们需要执行一项基本操作，即在Turing Network上远程执行 `automationTime.scheduleXcmpTask`。

为了执行此操作，我们将与以下组件进行交互：

- 源链：Moonriver
- 源XCM版本：V3
- 源外部调用：`xcmTransactor.transactThroughSigned`

<br /> 因此，它将启动以下调用的远程执行：

- 目标链：Turing Network
- 目标XCM版本：V3
- 目标外部调用：`automationTime.scheduleXcmpTask`

---v

在XCM成功执行后，Turing Network上将触发一个 `TaskScheduled` 事件，表明远程调用已成功执行，从而创建了一个自动化任务。

<figure>
  <img rounded style="width: 900px;" src="./img/high-level-product-flow.jpg" />
  <figcaption>Moonriver和Turing Network之间的高级产品流程</figcaption>
</figure>

Notes:

解释 - XCM调用设置了一个定期任务，该任务将在每个月末自动转移MOVR。
Turing Network负责在满足条件时触发该操作。
整个产品的整体流程如下图所示。

---

# 准备工作

为了开启我们的旅程，我们将首先与Moonriver的 `xcmTransactor` 模块进行交互，该模块类似于Polkadot/Kusama的 `xcmPallet`。
在深入研究实际的XCM消息之前，确保我们满足某些先决条件至关重要：

Notes:

对于本次演示，我们使用的是Polkadot和Kusama中内置的现有 `xcmPallet`。
该模块提供了通用的外部接口，开发人员可以使用这些接口轻松编写XCM消息。
Moonriver进一步封装了该功能，创建了自己的 `xcmTransactor`。

---v

1. 确保接收链上的屏障（Barriers）配置正确。<br />
   在这种情况下，需要在Turing Network的XCM配置中配置一个允许屏障 `WithComputedOrigin<Everything>`。
   此屏障将允许XCM中的 `DescendOrigin` 指令，该指令将把Turing Network上的交易发起方从Moonriver的主权账户重新分配给用户的代理账户。
1. 在接收链上配置用户的远程钱包。远程钱包或代理钱包充当账户抽象，允许区块链代表用户执行特定代码。

Notes:

1. 我们在上一章中介绍了屏障主题。
   屏障负责为传入的消息创建允许或拒绝规则。
   通过添加此屏障，我们允许XCM中的 `DescendOrigin` 指令，该指令将把Turing Network上的交易发起方从Moonriver的主权账户重新分配给用户的代理账户。
1. 此远程钱包充当账户抽象，使区块链能够代表用户执行特定代码。
   通过使用用户的子钱包进行特定的外部调用，我们创建了细粒度的控制，允许用户的钱包高效且安全地执行必要的操作。

---

# 编写XCM消息

在本节中，我们将通过调用Moonriver上的 `xcmTransactor.transactThroughSigned` 外部调用来启动执行。

Notes:

此外部调用是编写XCM消息的入口，包含了所需跨链消息的所有必要指令。

---v

## XCM配置

在发送XCM消息之前，你需要确定以下参数：<br /><br />

1. **版本号**：检查接收方（Turing Network）和源方（Moonriver）链上的XCM版本。
   确保它们的XCM版本兼容。
1. **权重**：每个链为XCM指令定义了不同的权重，这会影响计算、存储和gas费用。
1. **每秒费用**：如果使用接收链的原生代币（TUR）以外的资产支付费用，请确定MOVR到TUR的转换率。

Notes:

在XCM文档的链配置第4节中，我们回顾了各种链配置。
在本节中，我们将通过演示来说明它们的用法。
虽然有几个变量需要确定，但一旦你熟悉了它们并建立了一些模板，就可以继续使用它们。

1. 例如，V3向后兼容V2，但它的配置需要设置 `safeXcmVersion`。
1. XCM指令的权重在每个链上定义的值不同。
   它指定了执行每个指令所需的计算能力以及存储（PoV大小），并确定了XCM执行的gas费用。
1. 除了权重之外，如果我们使用接收链的原生代币以外的资产（在本例中为TUR）来支付费用，则该资产的价值必须相对于接收链的原生代币进行转换。
   `Fee per Second` 定义了MOVR和TUR之间的转换率，假设我们希望在本次交易中使用MOVR支付所有费用。

确定这些参数后，继续构建XCM消息的指令序列。

---v

## 消息元素

为了构建XCM消息，我们使用Moonriver的 `xcmTransactor.transactThroughSigned` 外部调用。
它需要以下参数：

**目标地址（Destination）**：它指定了目标链，在我们的例子中是Turing Network，在Kusama上通过 `{Relay, 2114}` 标识。

<br />

<figure>
  <img rounded style="width: 900px;" src="./img/xcm-transactor-extrinsic.png" />
  <figcaption>`transactThroughDerivative()` 外部调用中的参数</figcaption>
</figure>

---v

**内部调用（InnerCall）**

这表示目标链上交易的编码调用哈希。
该值将传递给 `Transact` XCM指令。

**费用（Fees）**

`transactRequiredWeightAtMost` 限制了内部调用的gas费用，防止费用代币成本过高。
同样，`overallWeight` 为XCM执行设置了上限，包括 `Transact` 哈希。

---v

## 发起XCM消息

---v

<img rounded style="width: 770px;" src="./img/xcm-send-1.png"/>

一旦设置了所有参数，我们就可以继续提交并签署交易。
XCM消息可以直接从 [polkadot.js应用程序](https://polkadot.js.org/apps/) 的外部调用选项卡中方便地触发。

---v

<img rounded style="width: 770px;" src="./img/xcm-send-2.png"/>

`DescendOrigin(descend_location)`：XCM数组中的第一条指令是 `DescendOrigin`，它将权限转移到目标链上用户的代理账户。

---v

<img rounded style="width: 770px;" src="./img/xcm-send-3.png"/>

`WithdrawAsset` 和 `BuyExecution`：这两条指令协同工作，从用户的代理钱包中扣除XCM费用并预留用于执行。

---v

<img rounded style="width: 770px;" src="./img/xcm-send-4.png"/>

XCM消息 - 购买执行

---v

<img rounded style="width: 770px;" src="./img/xcm-send-5.png"/>

`Transact(origin_type, require_weight_at_most, call)`：`Transact` 指令在目标链上执行编码的内部调用。
我们通过在调用期间设置 `requireWeightAtMost` 来确保gas成本不超过指定的限制。

---v

<img rounded style="width: 770px;" src="./img/xcm-send-5.png"/>

<div style="font-size: 0.82em;">

`RefundSurplus` 和 `DepositAsset`：如果在 `Transact` 执行后还有剩余的费用代币，这些指令将确保它们被退还并转移到指定的位置，通常是用户的钱包。

成功发送消息后，发送方和接收方平行链的XCM事件应显示在Polkadot.js应用程序的网络选项卡中。

</div>

---v

## 消息检查

上述交易在链上提交并最终确定后，我们可以使用Moonbeam团队构建的xcm-tools来检查XCM消息。
该工具的代码和脚本列在 [此Github仓库](https://github.com/Moonsong-Labs/xcm-tools) 中。
脚本示例如下：

`yarn xcm-decode-para --w wss://wss.api.moonbeam.network --b 1649282 --channel hrmp --p 2000`

---v

<pba-flex center>

脚本的输出反映了我们之前为XCM消息构建的指令序列。

1. `DescendOrigin`
1. `WithdrawAsset`
1. `BuyExecution`
1. `Transact`
1. `RefundSurplus`
1. `DepositAsset`

</pba-flex>

---

# 客户端代码（node.js）

在证明上述XCM消息正确执行后，我们可以从去中心化应用（dApp）的客户端复制该过程。
以下是我们为本次演示创建的node.js代码片段。

👉 [xcm-demo Github仓库](https://github.com/OAK-Foundation/xcm-demo/blob/master/src/moonbeam/moonbase-alpha.js) 👈

要运行该程序，请使用git克隆它并执行以下命令：

```sh
PASS_PHRASE=<PASS_PHRASE> PASS_PHRASE_ETH=<PASS_PHRASE_ETH> npm run moonbase-alpha
```

---v

### 示例

<div style="font-size: 0.7em;">

```text
const tx = parachainHelper.api.tx.xcmTransactor.transactThroughSigned(
        {
            V3: {
                parents: 1,
                interior: {
                    X1: { Parachain: 2114 },
                },
            },
        },
        {
            currency: {
                AsCurrencyId: 'SelfReserve',
            },
            feeAmount: fungible,
        },
        encodedTaskViaProxy,
        {
            transactRequiredWeightAtMost: {
                refTime: transactRequiredWeightAtMost,
                proofSize: 0,
            },
            overallWeight: {
                refTime: overallWeight,
                proofSize: 0,
            },
        },
    );
```

</div>

Notes:

从代码中可以看出，在构建XCM消息的主要代码块之前有几个准备步骤。
借助以下代码，我们可以轻松地重复发送消息并测试不同的输入值。

---

## 实时调试
在处理XCM消息时，可能会在两个方面出现潜在问题：消息构建过程以及在目标链上执行交易的过程。
---v
**消息格式问题**：如果XCM消息格式错误，接收链可能无法正确处理它。要在链上解读XCM消息，我们可以使用第5章介绍的xcm - tool工具。一些常见问题及解决方案如下：
 - **费用和权重输入错误**：确保XCM调用中指定的最大权重准确无误。如果实际权重略超过限制，接收链可能会拒绝该调用。在这种情况下，增大最大权重参数并重新尝试。
 - **版本不匹配**：当接收链不接受目标地址（Destination）或费用资产（FeeAsset）中指定的多位置（Multi - location）版本时，会出现版本不匹配错误。检查接收链的XCM版本，并相应地将多位置版本调整为V2或V3。
---v
**执行编码调用问题**：要检查“执行”（Transact）指令中的编码调用哈希，需在接收链上定位特定交易，该交易是在“XcmMessageQueue.success”事件之后发生的。遗憾的是，没有自动化工具可以直接将“XcmMessageQueue.success”与编码调用事件关联起来。不过，我们可以通过将消息哈希与源链进行匹配来手动分析。
Notes:
有没有人知道有什么好工具可以将“XcmMessageQueue.success”与“执行”哈希关联起来？
---
## 总结
在本节中，我们讲解了利用XCM实现定期支付的去中心化应用（dApp）的核心要点。
---v
### 课程回顾
要在链之间成功创建XCM消息，请确保准备好以下要素：
 - **类型**：确定是垂直中继过程（VRP，Vertical Relay Process）还是水平中继过程（HRMP，Horizontal Relay Process），这代表了通信涉及的双方。
 - **目标**：确定要调用的具体外部函数，或者交易中要包含哪些操作。
 - **细节**：根据需要调整链配置。确定“DescendOrigin”的设置，即选择将权限下放到用户的远程钱包，还是使用平行链的主权账户。另外，指定消息中包含的指令序列。
---v
准备好这些要素后，将它们组合起来形成XCM消息，并仔细排查问题。一旦建立了可靠的模板，可以考虑使用polkadot.js JavaScript库实现构建过程的自动化。或者，你也可以在平行链的Rust代码中编写一个包装器，比如常用的“xTokens.transferMultiasset”，或者Moonriver的“xcmTransactor.transactThroughSigned”。
---
<!-- .slide: data-background-color="#4A2439" -->
# 提问环节 