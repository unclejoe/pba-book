---
title: Light Clients and Unstoppable Apps
description: Light Clients and Unstoppable Apps, for web3 builders.
duration: 45+ mins
---

# 轻客户端<br />以及<br />不停机的应用程序

---

#### 传统Web 2

<img rounded style="width: 80%;" src="./img/web2.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

在开始之前，让我们花点时间来看看我们所熟知的万维网的现状。

欢迎来到Web 2.0的领域，目前大多数网络应用都存在于这个领域。
虽然我不会批评任何人，但必须认识到，像Facebook、Twitter、WhatsApp等许多平台都属于这一类别；（描述图片）

---v

#### Web 3愿景

<img rounded style="width: 80%;" src="./img/web3.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

这代表了Web3应该努力成为的愿景：
一个真正互联的网络，来自世界各地的验证者和终端用户可以无缝连接、共享信息并进行协作。

现在，请举手：
- 有多少人认为我们目前已经接近实现这个愿景了？
- 又有多少人认为我们还有很长的路要走？

---v

#### Web 3现实

<img rounded style="width: 80%;" src="./img/web3_reality.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

让我们更仔细地看看实际情况。
目前，我们进入区块链网络是通过一个中央接入点，即JSON-RPC节点。
这个节点是访问整个区块链网络的网关。

虽然许多应用程序声称是去中心化的，但我们必须自问，它们到底有多去中心化？

现在，我想强调一个关键要点——我鼓励大家花点时间思考一下。
我会停顿几秒钟，让大家好好领会一下；

---v

<h1 style="font-size:7rem; font-weight: bold">区块链“去中心化”应用程序仍然是中心化的</h1>

Notes:

我会停顿几秒钟，让大家好好领会一下；

---v

<img rounded style="width: 30%;" src="./img/learn-student.gif" />

Notes:

我会停顿几秒钟，让大家好好领会一下；

---

# 网络中的节点类型

<p style="text-align: left; padding-bottom: 2rem">每种节点的类型取决于不同的特征：</p>

<ul>
  <li>
    <span class="font-bold underline">验证者节点：</span>配置为可能生成区块的节点。
  </li>
<!-- .element: class="fragment" data-fragment-index="1" -->
  <li>
    <span class="font-bold underline">JSON-RPC节点：</span>公开其JSON-RPC端点的节点。
  </li>
<!-- .element: class="fragment" data-fragment-index="2" -->
  <li>
    <span class="font-bold underline">引导节点：</span>其地址可以在链规范文件（chainspec）中找到的节点。
      启动网络所必需的节点。
  </li>
<!-- .element: class="fragment" data-fragment-index="3" -->
  <li>
    <span class="font-bold underline">归档节点：</span>从第0个区块开始存储链的整个状态。
      对于访问历史数据很有用。
  </li>
<!-- .element: class="fragment" data-fragment-index="4" -->
  <li>
    <span class="font-bold underline text-[var(--r-heading-color)]">轻客户端：</span><span class="text-[var(--r-heading-color)]">不存储链的整个状态，而是按需请求。</span>
  </li>
<!-- .element: class="fragment" data-fragment-index="5" -->
</ul>

Notes:

在其他事情之前——让我们记住网络中的节点类型

验证者节点：这些节点负责生成新的区块并验证交易。
它们参与共识机制，在保障网络安全方面发挥着至关重要的作用。

JSON-RPC节点：作为开发人员和应用程序与区块链交互的接口，通过发送JSON格式的请求并接收JSON格式的响应。

引导节点：引导节点是具有众所周知地址的节点，作为新节点加入网络的入口点。
它们帮助新节点发现并连接到网络中的其他对等节点。

轻节点：轻节点是完整节点的轻量级版本，不存储整个区块链，而是依赖完整节点进行交易验证。
对于那些希望与网络进行交互但又不需要下载整个区块链的用户来说非常有用。

(......点击之后......)

“验证者”、“引导节点”和“JSON-RPC节点”的任何组合都是可能的，除了“轻节点”和“归档节点”是互斥的。

---v

#### 当今区块链的现实

<img rounded style="width: 100%;" src="./img/reality_bc_today.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:

这就是目前的实际情况，或者说现在人们可能如何连接到网络

（阅读幻灯片）

请注意：为了简单起见，从现在开始，我将使用“UI”这个词来指代客户端/用户/应用程序等。
提问：如今，像（例如PolkadotJS应用程序或任何自定义应用程序）这样的UI有哪些连接到网络的方式？

---v

#### 用户控制的节点

<pba-cols>
  <pba-col left>
    <div>应用程序连接到用户在其机器上安装的节点客户端</div>
    <div class="bg-green-600 rounded-2xl p-4 !mt-2"><span class="font-bold">安全</span><br />无需信任：连接到多个节点，验证所有内容</div>
    <div class="bg-red-600 rounded-2xl p-4 !mt-2"><span class="font-bold">便捷：</span>透明运行</div>
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="2" -->
   <pba-col left>
      <img rounded src="./img/USER-CONTROLLED-NODE.png" />
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="1" -->
</pba-cols>

Notes:

（阅读幻灯片）

---v

#### 公开访问的节点

<pba-cols>
  <pba-col left>
    <div>应用程序连接到第三方拥有的公开访问的节点客户端</div>
    <div class="bg-red-600 rounded-2xl p-4 !mt-2"><span class="font-bold">中心化且不安全：</span>公开访问的节点可能存在恶意行为</div>
    <div class="bg-green-600 rounded-2xl p-4 !mt-2"><span class="font-bold">便捷：</span>透明运行</div>
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="2" -->
  <pba-col left>
    <img rounded src="./img/PUBLICLY-ACCESSIBLE-NODE.png" />
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="1" -->
</pba-cols>

Notes:

（阅读幻灯片）

---v

#### 为什么这需要修复？

<pba-cols>
  <pba-col left>
      <h4>可靠性</h4>
      <!-- .element: class="fragment" data-fragment-index="1" -->
      <div>“中间人”可能由于某种原因停止工作，导致终端用户无法与区块链进行交互。</div>
      <!-- .element: class="fragment" data-fragment-index="2" -->
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="1" -->
   <pba-col left>
    <h4>审查或劫持的可能性</h4>
    <!-- .element: class="fragment" data-fragment-index="3" -->
    <div>“中间人”可以决定禁止某些终端用户或某些交易，或者可能被攻击者控制。</div>
    <!-- .element: class="fragment" data-fragment-index="4" -->
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="3 " -->
   <pba-col left>
     <h4>抢先交易问题</h4>
     <!-- .element: class="fragment" data-fragment-index="5" -->
     <div>“中间人”在交易实际应用之前就知道所有提交的交易，并且可以提前插入自己的交易以获取经济利益。</div>
     <!-- .element: class="fragment" data-fragment-index="6" -->
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="5" -->
</pba-cols>

Notes:

在第三方的情况下，用户依赖第三方节点进行连接，以便与网络进行通信。
（观众）请举手回答，为什么这需要修复？
（停顿并等待可能的答案）

- （我们需要）可靠性
- （存在）审查或劫持的可能性
- 抢先交易是指在知道未来交易的情况下将交易放入队列中的行为

---v

## 我们想要的区块链现实

<img rounded style="width: 100%;" src="./img/reality_we_want.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

---v

# 解决方案

<img rounded src="./img/feather.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

# 轻客户端

<!-- .element: class="fragment" data-fragment-index="1" -->

---

# 什么是轻客户端？

<p style="font-size:4rem">它是一个客户端（一个节点）...</p>

<!-- .element: class="fragment" data-fragment-index="1" -->

<p style="font-size:1.5rem">...但更轻量级！</p>

<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:

当我加入Substrate Connect团队时，我也问过同样的问题。
我得到的回答是……（*）
当时我想……“嗯，谢谢吧”

但这确实是真的！

---v

# 什么是轻客户端？

<ul>
  <li>
    它是一个比完整节点更轻量级的客户端，在内存消耗、线程数量和代码大小方面；</li>
<!-- .element: class="fragment" data-fragment-index="1" -->
  <li> 允许去中心化应用程序（dApp）以安全和去中心化的方式访问和与区块链进行交互，而无需同步整个区块链；</li>
<!-- .element: class="fragment" data-fragment-index="2" -->
  <li> 它是一个不存储链的整个状态，而是按需请求的节点；</li>
<!-- .element: class="fragment" data-fragment-index="3" -->
  <li> 它以完全无需信任的方式连接并与网络进行交互；</li>
<!-- .element: class="fragment" data-fragment-index="4" -->
  </li>
  <!-- .element: class="fragment" data-fragment-index="4" -->
</ul>

Notes:

在下一张幻灯片中，我们将以通用的方式解释“什么是轻客户端”，同时我还会添加一些关于Polkadot生态系统实现的解决方案的额外信息；

要点1）“轻客户端”是一种节点实现类型，允许应用程序与网络进行交互，与完整节点相比消耗的资源更少，因此更适合像手机这样的资源受限设备，或者轻到可以在浏览器中运行（参见Substrate Connect）；

要点2）节点不会维护区块链的完整副本，而只携带其操作所需的最少量数据（例如链规范）。
它依赖完整节点或其他网络参与者来提供它所需的额外信息；

要点3）……
根据请求，它要么从现有数据中提供响应（如果有的话），要么将请求传播到完整节点并返回响应；

要点4）轻客户端可以更快地与区块链同步，因为它们只需要获取最新数据，使用证明（我们稍后会谈到），减少了与网络同步所需的时间（只需几秒钟）。
它们从网络中获取的数据更少，消耗的带宽也更少。
这对于使用有限数据计划或网络连接较慢的用户来说尤其有利。

---v

### 真实案例
<video controls width="100%">
    <source src="./img/LightClients.mp4" type="video/mp4">
    抱歉，你的浏览器不支持嵌入视频。
</video>

Notes:
“网络连接缓慢”：让我们来看一个真实案例。时间：2022年波卡解码大会；场景：Talisman钱包的联合创始人乔纳森·邓恩（Jonathan Dunne）上台演示，我们的轻客户端解决方案（smoldot）已集成到钱包中，以及它带来的好处——使用的网络连接状况非常差，因为连接人数过多。当[Talisman钱包](https://www.talisman.xyz/)加载时，请注意加载图标——波卡（Polkadot）使用轻客户端加载，而库萨马（Kusama）则使用常规的JSON - RPC方法加载。
完整视频：<https://www.youtube.com/watch?v=oaidhA5eL_8>

---v
<h2>轻客户端如何知道连接到哪里？</h2>
<pba-cols>
  <pba-col left>
    <img rounded style="width: 100%" src="./img/where_to_1.png" />
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="1" -->
  <pba-col left>
    <img rounded style="width: 100%" src="./img/where_to_2.png" />
  </pba-col>
  <!-- .element: class="fragment" data-fragment-index="2" -->
</pba-cols>

Notes:
你可能已经了解到，链规范是一个配置文件，用于定义区块链网络的参数和初始设置。它是启动和运行新区块链节点的蓝图，为搭建网络提供必要信息；我们的Substrate节点可以生成所谓的链规范，Smoldot随后会基于该链规范启动一个轻客户端节点；（在屏幕上展示链规范）

---v
### 轻客户端如何知道该信任什么/谁？
<img rounded style="margin-top: 150px; width: 70%" src="./img/know-who-to-trust.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:
我们知道，Substrate链提供了“最终性（FINALITY）”这一概念，这对轻客户端非常重要！一旦一个区块被最终确定，它就保证会一直是最优链的一部分。由此延伸，一个已最终确定区块的父区块也总是最终确定的，以此类推。对于最终性，Substrate/波卡节点使用GrandPa算法。授权节点在网络上发出投票，当三分之二或更多的节点对特定区块投票时，该区块就会正式被最终确定。这些投票会被收集到所谓的**证明（justification）**中。
**证明**在为轻客户端提供安全性和有效性保证方面起着至关重要的作用。如前所述，轻客户端是不存储区块链数据的节点，而是依赖其他全节点或网络来验证区块链的状态和交易。虽然轻客户端资源需求较低且同步速度更快，但它们面临着信任从其他节点接收的信息的挑战。
证明通过提供区块最终性和有效性的加密证明，解决了轻客户端的信任问题。当一个区块有了证明，就意味着它已经被绝大多数验证者确认和认可，成为区块链最终状态的一部分。它也被那些可能没有收到所有投票的节点使用，例如如果它们离线了，就可以用证明来验证区块的真实性；轻客户端接收这些证明，从而验证区块的真实性。

---v
<section>
  <diagram class="mermaid limit size-80">
    sequenceDiagram
        网络->>证明: 确定最终性并创建
        网络->>证明: 确定最终性并创建
        应用程序->>轻客户端: 唤醒并同步！
        轻客户端->>证明: 嘿！我在这儿！
        证明-->>轻客户端: 给你
        证明-->>轻客户端: 给你
        证明-->>轻客户端: 给你
        应用程序->>轻客户端: 准备好了吗？！
        轻客户端->>应用程序: 还没！正在同步
        证明-->>轻客户端: 给你
        轻客户端-->>应用程序: 已验证并同步！
        应用程序->>轻客户端: 好！现在给我数据
        轻客户端->>网络: 聊聊！应用程序想要数据
        网络-->>轻客户端: 好的！
        轻客户端-->>应用程序: 给你！
  </diagram>
</section>

---v
<pba-cols>
  <pba-col left>
    <h2 style="text-align: center">全节点</h2>
  </pba-col>
  <pba-col left>
    <h2 style="text-align: center">轻客户端</h2>
  </pba-col>
</pba-cols>
<pba-cols>
  <pba-col left>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">全面验证所有区块（真实性/有效性）</div>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">在其数据库中保存链的所有存储数据</div>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">在其数据库中保存所有过往区块</div>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">初始启动时，可能需要数小时才能准备就绪</div>
  </pba-col>
  <pba-col left>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">仅验证区块的真实性</div>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">按需请求链的状态</div>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">没有任何数据库</div>
    <div class="white, bg-[var(--r-heading-color)] text-base rounded-2xl p-4 !mt-2">几秒钟内即可初始化</div>
  </pba-col>
</pba-cols>

---
<img src="./img/words.png" />
<!-- .element: class="fragment" data-fragment-index="1" -->

Notes:
现在，让我们深入了解波卡针对所有Substrate链的轻客户端解决方案。随着我们浏览这些幻灯片，你可能已经遇到或听说过与轻客户端相关的各种术语和概念。此时，明确区分这些概念至关重要；让我们更有针对性地详细探索波卡生态系统中的轻客户端。

---v
<h1>Smoldot</h1>
<div style="font-size:2.5rem; color: #fff">全新构建的轻客户端实现</div>
  <!-- .element: class="fragment" data-fragment-index="1" -->
## rust
<!-- .element: class="fragment" data-fragment-index="2" -->
<div>
  <div style="font-size:1.75rem; color: #fff">smoldot-light-js (/wasm-node) - npm/deno</div>
  <!-- .element: class="fragment" data-fragment-index="3" -->
  <div style="font-size:1.75rem; color: #fff">smoldot (/lib) - Rust库</div>
  <!-- .element: class="fragment" data-fragment-index="4" -->
  <div style="font-size:1.75rem; color: #fff">smoldot-light (/light-base)</div>
  <!-- .element: class="fragment" data-fragment-index="5" -->
  <div style="font-size:1.75rem; color: #fff">smoldot-full-node (/full-node)</div>
  <!-- .element: class="fragment" data-fragment-index="6" -->
</div>
<!-- .element: class="fragment" data-fragment-index="3" -->
<p class="inline-table">
  <img style="width: 10rem; padding-top: 3rem" src="../../blockchain-contracts/light-clients-bridges/img/tomaka.png" />
  <!-- .element: class="fragment" data-fragment-index="7" -->
  <div style="font-size:1.5rem; color: #fff">皮埃尔·克里格（Pierre Krieger） - tomaka</div>
  <!-- .element: class="fragment" data-fragment-index="7" -->
</p>
<!-- .element: class="fragment" data-fragment-index="7" -->
<a href="https://github.com/smol-dot/smoldot/">https://github.com/smol-dot/smoldot/</a>
<!-- .element: class="fragment" data-fragment-index="8" -->

Notes:
Smoldot是全新构建的轻客户端实现，这意味着我们并没有对Substrate进行轻量化处理。它是用Rust从头开始重写的，并且包含：
- smoldot-light-js (/wasm-node)：一个JavaScript包，可以作为轻客户端连接到基于Substrate的链。它在浏览器和NodeJS/Deno中都能使用。这是这个代码库的主要组件。
- smoldot (/lib)：一个中立的Rust库，包含与Substrate和波卡相关的通用原语。它是其他组件的基础。
- smoldot-light (/light-base)：一个与平台无关的Rust库，可以作为轻客户端连接到基于Substrate的链。它是上述smoldot-light-js组件的基础。
- smoldot-full-node (/full-node)：一个正在开发中的全节点二进制文件原型，可以连接到基于Substrate的链。它目前还不支持官方客户端的许多功能。由皮埃尔·克里格（又名tomaka）开发。

---v
## Substrate Connect
<div style="font-size:1.75rem; color: #fff">将smoldot用作实现细节</div>
<!-- .element: class="fragment" data-fragment-index="2" -->
<div style="font-size:1.5rem; color: #d92f78; margin: 0.5rem 0">javascript/typescript</div>
<!-- .element: class="fragment" data-fragment-index="3" -->
  <img width="900" src="./img/wellknown.png"  style="margin: 1rem 15%" />
  <!-- .element: class="fragment" data-fragment-index="4" -->
<p style="font-size:1.5rem; margin-top: 1rem"><a href="https://github.com/paritytech/substrate-connect/">https://github.com/paritytech/substrate-connect/</a></p>
<!-- .element: class="fragment" data-fragment-index="5" -->

Notes:
- npm包
- 来自polkadotJS的rpc提供程序
- Chrome和Mozilla扩展
- 集成了4个“知名”链（库萨马、波卡、Westend、Rococo）——这意味着使用这些链时无需提供链规范；

---v
## 示意图
<section>
  <diagram class="mermaid">
    stateDiagram-v2
      Smoldot轻客户端 --> Substrate Connect
      Substrate Connect --> PolkadotJS API
      PolkadotJS API --> UI_dAPP
      Smoldot轻客户端 --> 自定义代码\n(使用JSON - RPC API)
      自定义代码\n(使用JSON - RPC API) --> UI_dAPP
</diagram>
</section>

---
## Smoldot轻客户端
- （和Substrate一样）它也支持新开发的JSON - RPC协议；
<!-- .element: class="fragment" data-fragment-index="1" -->
- 足够轻量和快速，因此可以嵌入到移动应用程序或一般应用程序中；
<!-- .element: class="fragment" data-fragment-index="2" -->

Notes:
- 新的JSON - RPC协议：https://github.com/paritytech/json-rpc-interface-spec/
- 正如2023年解码大会上达恩·范德普拉斯（Daan van der Plas）展示的：“移动应用中的Smoldot”（https://www.youtube.com/watch?v=Z7FiFHgotzE&feature=share）
我们将使用Substrate Connect的TS/JS代码作为示例的伪代码

---
## 公开可访问节点
去中心化应用程序（UI）连接到第三方拥有的公开可访问节点客户端
<p class="bg-red-600 rounded-2xl p-4 !mt-2"><span class="font-bold">中心化且不安全：</span>公开可访问节点可能存在恶意行为</p>
<p class="bg-green-600 rounded-2xl p-4 !mt-2"><span class="font-bold">方便：</span>运行透明</p>

---v
## 那么需要做什么
- 找到一个你信任的第三方节点（JSON - RPC节点）的WebSocket URL；
<!-- .element: class="fragment" data-fragment-index="1" -->
- 将其添加到代码中并使用；
<!-- .element: class="fragment" data-fragment-index="2" -->

---v
## 在你的去中心化应用程序中
```javascript[0|1|3-5|7-9]
import { ApiPromise, WsProvider } from "@polkadot/api";
// 可能还有一些其他代码在这里施展魔法
const provider = new WsProvider("wss://westend-rpc.polkadot.io");
const api = await ApiPromise.create({ provider });
// 使用polkadotJS API进行交互
const header = await api.rpc.chain.getHeader();
const chainName = await api.rpc.system.chain();
```
---

## 用户控制的节点
去中心化应用程序（dApp，即UI）连接到用户在自己机器上安装的节点客户端。
<p class="bg-green-600 rounded-2xl p-4 !mt-2"><span class="font-bold">安全且无需信任：</span>连接到多个节点，验证所有内容</p>
<p class="bg-red-600 rounded-2xl p-4 !mt-2"><span class="font-bold">不方便：</span>需要安装过程，让节点保持运行状态，还需要进行维护工作</p>

---v
## 那么需要做什么
<pba-flex center>
1. 安装依赖项<br />
   （例如Rust、OpenSSL、CMake、LLVM等）；
<!-- .element: class="fragment" data-fragment-index="1" -->
1. 从GitHub克隆“polkadot”代码库；
<!-- .element: class="fragment" data-fragment-index="2" -->
1. 在本地构建节点；
<!-- .element: class="fragment" data-fragment-index="3" -->
1. 在本地启动节点；
<!-- .element: class="fragment" data-fragment-index="4" -->
1. 等待节点同步；
   <!-- .element: class="fragment" data-fragment-index="5" -->
</pba-flex>

---v
<p>...等待节点同步...</p>
<!-- .element: class="fragment" data-fragment-index="1" -->
<p>.......</p>
<!-- .element: class="fragment" data-fragment-index="2" -->
<p>..........</p>
<!-- .element: class="fragment" data-fragment-index="3" -->
<p>..................</p>
<!-- .element: class="fragment" data-fragment-index="4" -->
<p>......等一下.......</p>
<!-- .element: class="fragment" data-fragment-index="5" -->
<p>..............................</p>
<!-- .element: class="fragment" data-fragment-index="6" -->
<p>好了</p>
<!-- .element: class="fragment" data-fragment-index="7" -->

---v
## 在你的dApp中
```javascript[|1|3-5|7-9]
import { ApiPromise, WsProvider } from "@polkadot/api";
// 也许还有一些代码在这里实现其他功能
const provider = new WsProvider("wss://127.0.0.1:9944");
const api = await ApiPromise.create({ provider });
// 使用polkadotJS API进行交互
const header = await api.rpc.chain.getHeader();
const chainName = await api.rpc.system.chain();
```

---
## 浏览器中的轻客户端
应用程序（UI）连接到一个集成的轻客户端。
<p class="bg-green-600 rounded-2xl p-4 !mt-2"><span class="font-bold">安全且无需信任：</span>连接到多个节点，验证所有内容</p>
<p class="bg-green-600 rounded-2xl p-4 !mt-2"><span class="font-bold">方便：</span>运行透明</p>

---v
## 那么需要做什么
<pba-flex center>
1. 在dApp内安装并配置轻客户端
</pba-flex>

---v
## 使用PolkadotJS API
```javascript[0|1-2|4-7|9-11]
import { ScProvider } from "@polkadot/rpc-provider/substrate-connect";
import * as Sc from '@substrate/connect';
// 也许还有一些代码在这里实现其他功能
const provider = new ScProvider(Sc, Sc.WellKnownChain.westend2);
await provider.connect();
const api = await ApiPromise.create({ provider });
// 使用polkadotJS API进行交互
const header = await api.rpc.chain.getHeader();
const chainName = await api.rpc.system.chain();
```

---v
### 甚至不使用PolkadotJS API
```javascript[0|1|4|5-10|12-15]
import { createScClient, WellKnownChain } from "@substrate/connect";
// 也许还有一些代码在这里实现其他功能
const scClient = createScClient();
const mainChain = await scClient.addWellKnownChain(
  WellKnownChain.polkadot,
  jsonRpcCallback = (response) => {
    console.log(response);
  }
);
// 与网络进行通信
mainChain.sendJsonRpc(
  '{"jsonrpc":"2.0","id":"1","method":"chainHead_unstable_follow","params":[true]}',
);
```

---v
### 甚至不使用PolkadotJS API且使用自定义链规范
```javascript[0|1|2,4|7|8,13|9|10-12| 15-18]
import { createScClient, WellKnownChain } from "@substrate/connect";
import myLovelyChainspec from './myLovelyChainspecFromSubstrateChain.json';
const myLovelyChainspecStringified = JSON.stringify(myLovelyChainspec);
// 也许还有一些代码在这里实现其他功能
const scClient = createScClient();
const mainChain = await scClient.addChain(
  myLovelyChainspecStringified,
  jsonRpcCallback = (response) => {
    console.log(response);
  }
);
// 与网络进行通信
mainChain.sendJsonRpc(
  '{"jsonrpc":"2.0","id":"1","method":"chainHead_unstable_follow","params":[true]}',
);
```

---v
### 甚至仅使用smoldot
```javascript[0|1|3|5,13|6-12|15|17|18|20|21]
import * as smoldot from "smoldot";
const chainSpec = new TextDecoder("utf-8").decode(fs.readFileSync('./westend-chain-specs.json'));
const client = smoldot.start({
    maxLogLevel: 3,  // 可以增大以获取更详细的日志
    forbidTcp: false,
    forbidWs: false,
    forbidNonLocalWs: false,
    forbidWss: false,
    cpuRateLimit: 0.5,
    logCallback: (_level, target, message) => console.log(_level, target, message)
});
client.addChain({ chainSpec, disableJsonRpc: true });
console.log('JSON-RPC服务器现在在9944端口监听');
console.log('请访问：https://cloudflare-ipfs.com/ipns/dotapps.io/?rpc=ws%3A%2F%2F127.0.0.1%3A9944');
// 现在启动一个WebSocket服务器来处理JSON-RPC客户端。
// 查看JSON-RPC协议：https://github.com/paritytech/json-rpc-interface-spec/
```

---
# 或许可以来点演示…？
<p class="inline-table">
  <img src="./img/code.jpg" />
</p>
---
#### 已知漏洞
- 日食攻击（全节点和轻客户端均受影响）
<!-- .element: class="fragment" data-fragment-index="1" -->
- 远程攻击（全节点和轻客户端均受影响）
<!-- .element: class="fragment" data-fragment-index="2" -->
- 无效最佳区块（仅轻客户端受影响）
<!-- .element: class="fragment" data-fragment-index="3" -->
- 最终性停滞（主要影响轻客户端）
<!-- .element: class="fragment" data-fragment-index="4" -->

Notes:
请继续关注，接下来是最后一部分，但并非最容易理解的部分：
- **日食攻击（全节点和轻客户端均受影响）**。区块链是一个P2P网络，Smoldot试图连接到这个网络中的各种节点（从引导节点开始）。想象一下，如果所有这些节点都拒绝返回数据，这将使Smoldot与网络隔离——Smoldot了解存在哪些节点的方式是通过节点自身（引导节点）。如果Smoldot只连接到恶意节点，它将永远无法连接到非恶意节点——如果引导节点列表中只有恶意节点，Smoldot将永远无法连接到任何非恶意节点。如果引导节点列表中包含一个诚实节点，那么Smoldot将能够连接到整个网络。注意！这种攻击实际上是一种拒绝服务攻击，因为它会阻止Smoldot访问区块链！
- **远程攻击（全节点和轻客户端均受影响）**。如果超过三分之二的验证者合谋，他们可以从自己作为验证者的某个区块开始分叉一条链，即使他们不再是链头部的活跃验证者。如果一些验证者分叉一条链，equivocation系统会通过没收他们质押的代币来惩罚他们。然而，如果他们在分叉前解除质押（在Kusama上需要7天，在Polkadot上需要28天），就不会受到惩罚。如果Smoldot从分叉起点开始就没有上线，它可能会被（通过日食攻击）诱骗而跟随错误的分叉。为了避免受到攻击，Smoldot离线时间不应连续超过解除质押延迟时间（如Kusama上的7天或Polkadot上的28天）。或者，如果链规范中提供的检查点不早于解除质押延迟时间，Smoldot也不会受到攻击。鉴于这种攻击 -> 需要许多验证者合谋， -> 是“孤注一掷”的， -> 可以提前检测到， -> 需要与日食攻击结合，而且它不会提供任何直接奖励，因此被认为不是一个现实的威胁。
- **无效最佳区块（仅轻客户端受影响）**。轻客户端不验证区块的有效性，只验证其真实性。如果一个区块是由合法验证者在其被授权创建区块的时间创建的，那么这个区块就是真实的。一个验证者可能创建一个Smoldot认为真实，但包含完全任意数据的区块。诚实的全节点不会在传播网络中传播无效区块，但验证者有可能将该区块发送给与其直接连接或与其串通的Smoldot实例。虽然这种攻击需要验证者恶意为之，且没有直接奖励，不太可能发生，但仍然是一个现实的威胁。因此，在使用轻客户端时，不要认为来自尚未最终确定的最佳区块的任何存储数据是准确的。一旦一个区块被最终确定，这意味着至少三分之二的验证者认为该区块有效。虽然最终确定的区块仍有可能无效，但这需要三分之二的验证者合谋。如果发生这种情况，那么这条链基本上已被接管，此时Smoldot显示的数据是否准确**其实已经无关紧要了**。
- **最终性停滞（主要影响轻客户端）**。因为任何尚未最终确定的区块在未来都可能成为规范链的一部分，一个节点为了正常运行，需要跟踪其已知存在的所有有效（对于全节点）或真实（对于轻客户端）的未最终确定区块。在正常情况下，此类区块的数量相当少（通常为3个）。然而，如果区块不再被最终确定，但新的区块仍在不断创建，那么节点的内存消耗会随着每个新创建的区块逐渐增加，直到没有更多内存可用，节点被迫停止运行。Substrate通过在最新已知的最终确定区块太过陈旧时，强制区块创建者逐渐减缓区块生成速度来缓解这个问题。由于正常情况下，除非存在错误或链配置错误，否则最终性不太可能停滞，所以这实际上不是一种攻击，而是某种攻击的后果。全节点受这个问题的影响较小，因为它们通常比轻客户端拥有更多的可用内存，并且可以将区块存储在磁盘上。

---
<!-- .slide: data-background-color="#4A2439" -->
# 问题