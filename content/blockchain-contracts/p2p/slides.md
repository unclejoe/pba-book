---
title: Peer-to-Peer (P2P) Networking
description: Peer-to-Peer (P2P) networking for web3 builders
---

# 点对点网络

---

## 引言/议程

- 点对点网络（P2P)的历史 <!-- .element: class="fragment" data-fragment-index="1" -->
- 讨论区块链运行的网络层和网络条件（大多数情况下） <!-- .element: class="fragment" data-fragment-index="2" -->
- 探讨传统Web2网络覆盖层与Web3网络覆盖层的优缺点 <!-- .element: class="fragment" data-fragment-index="3" -->
- 讨论攻击方式以及如何应对，还有底层的威胁模型 <!-- .element: class="fragment" data-fragment-index="4" -->

---

## 阿帕网（ARPANET）

- 第一个投入运行的数据包交换网络 <!-- .element: class="fragment" data-fragment-index="2" -->
- 20世纪60年代末由美国国防部高级研究计划局（DARPA）开发 <!-- .element: class="fragment" data-fragment-index="3" -->
- 为现代互联网奠定了基础 <!-- .element: class="fragment" data-fragment-index="4" -->

Notes:

全面信息感知（TIA）：21世纪初，DARPA启动了TIA项目，旨在开发用于大规模监视和数据分析的技术。该项目引发了对隐私和公民自由的担忧，最终由于公众的强烈反对，于2003年被取消。

---

## 数据包交换

- 一种数据传输模式，在这种模式下，消息被分解为多个部分（数据包）并独立发送 <!-- .element: class="fragment" data-fragment-index="2" -->
- 数据包通过最优路线发送 <!-- .element: class="fragment" data-fragment-index="3" -->
- 数据包在目的地重新组装 <!-- .element: class="fragment" data-fragment-index="4" -->

---

## 数据包交换

<img style="width: 600px" src="./img/message_packet.svg" />

Notes:

需要提及的是，报头通常包含一些寻址、目的地信息和排序信息，具体取决于……

---

## 数据包交换

<img style="width: 600px" src="./img/packet_switching_1.svg"  />

---

## 数据包交换

<img  style="width: 600px" src="./img/packet_switching_2.svg" />

---

## 数据包交换

<img style="width: 600px" src="./img/packet_switching_3.svg" />

---

## 数据包交换

<img style="width: 600px" src="./img/packet_switching_4.svg" />

---

## 点对点网络（P2P）

- P2P是一种去中心化的网络结构形式 <!-- .element: class="fragment" data-fragment-index="2" -->
- 与客户端 - 服务器模型不同，所有节点（对等节点）都是平等的参与者 <!-- .element: class="fragment" data-fragment-index="3" -->
- 数据直接在系统之间共享，无需中央服务器 <!-- .element: class="fragment" data-fragment-index="4" -->
- 对等节点贡献资源，包括带宽、存储空间和处理能力 <!-- .element: class="fragment" data-fragment-index="5" -->

---

## 历史上的P2P应用

Notes:

Napster、Limewire

---

## Napster

- 1999年推出，流行的P2P平台 <!-- .element: class="fragment" data-fragment-index="2" -->
- 有中央服务器用于索引，P2P用于文件传输 <!-- .element: class="fragment" data-fragment-index="3" -->
- 2001年因法律问题关闭 <!-- .element: class="fragment" data-fragment-index="4" -->

Notes:

Napster的故事与Metallica乐队密切相关。
2000年，Metallica发现他们的歌曲《I Disappear》的演示版本在正式发行前通过Napster传播。
这导致Metallica对Napster提起版权侵权诉讼。
Napster不得不封禁数十万在其平台上分享Metallica音乐的用户。
这是数字版权法的一个转折点，对Napster于2001年最终关闭起到了重要作用。

---

## Napster设置

<img style="width: 600px" src="./img/napster_1.svg" />

---

## Napster设置

<img style="width: 600px" src="./img/napster_2.svg" />

---

## Napster设置

<img style="width: 600px" src="./img/napster_3.svg" />

---

## Napster设置

<img style="width: 600px" src="./img/napster_4.svg" />

---

## Gnutella（Limewire）

- 每个节点既充当客户端又充当服务器，没有中央服务器 <!-- .element: class="fragment" data-fragment-index="2" -->
- 向所有连接的节点查询文件 <!-- .element: class="fragment" data-fragment-index="3" -->
- 通过引导节点（Bootnodes）获得与网络的对等连接 <!-- .element: class="fragment" data-fragment-index="4" -->
- 2010年被美国法院下令关闭 <!-- .element: class="fragment" data-fragment-index="5" -->

Notes:

- 检查本地文件存储中是否有该文件，如果没有，则将请求转发给所有连接的对等节点。
- Gnutella通过向网络发送大量请求，产生了大量的网络流量。

---

<section>
    <h2>客户端 - 服务器网络与点对点网络（P2P）的对比</h2>
    <table>
        <thead>
            <tr>
                <th></th>
                <th>客户端 - 服务器网络</th>
                <th>P2P网络</th>
            </tr>
        </thead>
        <tbody>
            <tr class="fragment">
                <td>结构</td>
                <td>集中式：一个或多个中央服务器控制网络</td>
                <td>去中心化：所有节点（对等节点）平等参与</td>
            </tr>
            <tr class="fragment">
                <td>数据流</td>
                <td>服务器向客户端提供数据</td>
                <td>对等节点之间直接共享数据</td>
            </tr>
            <tr class="fragment">
                <td>资源管理</td>
                <td>服务器管理资源并控制访问权限</td>
                <td>对等节点贡献资源，包括带宽、存储空间和处理能力</td>
            </tr>
            <tr class="fragment">
                <td>可扩展性</td>
                <td>可能受服务器容量限制</td>
                <td>由于资源分布，具有高度可扩展性</td>
            </tr>
            <tr class="fragment">
                <td>安全性</td>
                <td>集中式安全措施，存在单点故障</td>
                <td>可能存在一些安全问题，如恶意软件（取决于实现方式）</td>
            </tr>
        </tbody>
    </table>
</section>

---

## 中心化网络与去中心化网络的对比

<img style="width: 600px" src="./img/client_server_1.svg" />

Notes:

讨论当P2P网络和中心化网络发生分片区时的情况。
在P2P网络中，只要有一个节点拥有完整副本，文件就可以在网络中传播。

---

## 中心化网络与去中心化网络的对比

<img style="width: 600px" src="./img/client_server_2.svg" />

---

## 中心化网络与去中心化网络的对比

<img style="width: 600px" src="./img/p2p_topology_1.svg" />

---

## 中心化网络与去中心化网络的对比

<img style="width: 600px" src="./img/p2p_topology_2.svg" />

---

## 去中心化网络的优势

- 没有特权节点 <!-- .element: class="fragment" data-fragment-index="2" -->
- 带宽瓶颈较少 <!-- .element: class="fragment" data-fragment-index="3" -->
- 抗拒绝服务攻击（DOS） <!-- .element: class="fragment" data-fragment-index="4" -->
- 不需要集中式基础设施（目前除了互联网……） <!-- .element: class="fragment" data-fragment-index="5" -->

Notes:

1. 没有单个节点或节点组（内容分发网络，CDN）可以访问所有内容或文件，也没有节点对网络运行至关重要。
   每个节点都有数据副本。
2. 没有中央节点承担所有流量负载。
   这里可以提及区块生成和区块对等/导入。
3. 难以使网络过载或进行拒绝服务攻击（没有单个节点具有特权）。
4. 虽然许多节点运行在集中式云计算平台上，但并非必须如此（通常情况）。

---

## 困难或劣势

- 由于是无许可的，节点可能会共享恶意资源 <!-- .element: class="fragment" data-fragment-index="2" -->
- 延迟 <!-- .element: class="fragment" data-fragment-index="3" -->
- 难以监管非法活动 <!-- .element: class="fragment" data-fragment-index="4" -->
- 网络受硬件性能最弱的节点限制 <!-- .element: class="fragment" data-fragment-index="5" -->

Notes:

1. 如果我们需要等待许多对等节点从单个节点接收生成的数据，延迟可能会成为问题，因为并非每个人都有直接连接。
   要提及最终确认时间！
2. 没有中央点可以窥探所有用户数据（好坏参半）。
3. 这就是区块链网络有硬件要求的原因。

---

## 流言协议（Gossip Protocol）

<img style="width: 500px" src="./img/3.7-p2p-gossip-1.svg" />

Notes:

- 讨论我们如何以及为什么要将第45个区块传播给其他节点

---v

## 流言协议（Gossip Protocol）

<img style="width: 85s0px" src="./img/3.7-p2p-gossip-2.svg" />

Notes:

讨论广告传播与盲目发送的区别，以及盲目发送如何会导致效率低下

---

<section>
    <h2>结构化P2P网络与非结构化P2P网络的对比</h2>
    <table>
        <thead>
            <tr>
                <th></th>
                <th>结构化P2P网络</th>
                <th>非结构化P2P网络</th>
            </tr>
        </thead>
        <tbody>
            <tr class="fragment">
                <td>组织方式</td>
                <td>节点按照特定协议和结构进行组织（如分布式哈希表）</td>
                <td>节点以临时方式连接，没有特定的组织形式</td>
            </tr>
            <tr class="fragment">
                <td>搜索效率</td>
                <td>由于结构化特性，搜索操作高效</td>
                <td>搜索操作可能效率较低，可能需要向网络发送大量请求</td>
            </tr>
            <tr class="fragment">
                <td>灵活性</td>
                <td>灵活性较低，拓扑结构的变化需要重新组织</td>
                <td>灵活性高，节点可以自由加入、离开和重新组织</td>
            </tr>
            <tr class="fragment">
                <td>隐私性</td>
                <td>由于结构化组织，数据位置可预测</td>
                <td>具有更大的匿名性潜力</td>
            </tr>
        </tbody>
    </table>
</section>

---

## 发现机制

1. 连接到一个对等节点 <!-- .element: class="fragment" data-fragment-index="2" -->
2. 向该对等节点请求其已知节点的列表 <!-- .element: class="fragment" data-fragment-index="3" -->
3. 从列表中随机选择一部分对等节点进行连接 <!-- .element: class="fragment" data-fragment-index="4" -->
4. 重复步骤2和3 <!-- .element: class="fragment" data-fragment-index="5" -->

---

## 应用场景

Notes:

1. 哪些类型的应用适合这种网络拓扑结构？有没有人能想到一些例子？
2. 文件共享（音乐？）
3. 消息传递和通信？

---

## 初始发现

- 引导节点（Bootnode/bootnodes）（在Substrate中会有更多介绍）

Notes:

1. 最初必须知道网络中的一个参与者（引导节点）

---

## 攻击方式

Notes:

- 有没有人能想到利用这些网络的方法？
- 有哪些可以尝试利用的方面？

---

## 攻击方式

<img style="width: 600px" src="./img/eclipse_attack_1.svg"/>

Notes:

1. 扭曲网络的健康、正常、诚实状态的视图
2. 交易确认可能是虚假的

---

## 攻击方式

<img style="width: 600px" src="./img/eclipse_attack_2.svg"/>

---

## 日蚀攻击（Eclipse Attack）的执行过程

1. 向目标节点发送大量恶意对等节点地址 <!-- .element: class="fragment" data-fragment-index="2" -->
2. 目标节点存储这些恶意对等节点地址，并在下次启动重新同步时使用它们 <!-- .element: class="fragment" data-fragment-index="3" -->
3. 对目标节点进行拒绝服务攻击（DOS），使其离线，从而强制其与这些新的恶意对等节点进行重新同步 <!-- .element: class="fragment" data-fragment-index="4" -->
---

## 防止攻击

- 以某种方式限制入站连接 <!-- .element: class="fragment" data-fragment-index="2" -->
- 随机选择要连接的对等节点 <!-- .element: class="fragment" data-fragment-index="3" -->
- 确定性节点选择（引导节点） <!-- .element: class="fragment" data-fragment-index="4" -->
- 限制新节点加入（可能不是我们想要的做法……） <!-- .element: class="fragment" data-fragment-index="5" -->

Notes:

1. 要警惕与其他节点的新连接
1. 不要只接受最新的连接请求，以避免网络拥堵
1. 使用可信度和信任度较高的引导节点（可能会成为瓶颈） - 由于引导节点也容易受到攻击，因此需要轮换使用

---

## 结论

点对点（P2P）网络为我们提供了一条通往更加去中心化和抗审查应用的发展之路。
