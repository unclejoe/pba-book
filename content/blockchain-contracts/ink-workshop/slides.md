---
title: ink! Workshop
description: An introduction to the ink! workshop.
duration: 20 min
---

<img rounded style="width: 1200px; padding-top:15px;" src="./img/beginners-workshop.jpg" />

Notes:

这个工作坊的想法源于剑桥的第一波 PBA。
它第一次举办是在布宜诺斯艾利斯。
这非常像是 PBA 的一种传统活动，所以今天能够参与其中非常令人兴奋。

---

# 第一天

---

<img rounded style="margin-top: 25px; width: 1200px;" src="./img/beamer.png" />

Notes:

这是我们今年早些时候在布宜诺斯艾利斯的活动。

---

## 组件

<br />

<div class="flex-container fragment">
<div class="left"> <!-- 注意：你需要一个空行才能在 <div> 中渲染 Markdown -->
<div style="text-align: center"> <center><h2><pre> game.contract </pre></h2></center> </div>
<ul>
<li>我们进行部署和运行。</li>
<li>运行游戏循环。</li>
<li>调用每个玩家。</li>
<li>确定得分。</li>
</ul>
</div>

<div class="left fragment"> <!-- 注意：你需要一个空行才能在 <div> 中渲染 Markdown -->
<div style="text-align: center"> <center><h2><pre> player.contract </pre></h2></center> </div>

<ul>
<li>这是你的任务。</li>
</ul>
</div>
<div class="right fragment"> <!-- 注意：你需要一个空行才能在 <div> 中渲染 Markdown -->
<div style="text-align: center"> <center><h2><pre> frontend </pre></h2></center> </div>
<ul>
<li>游戏期间会显示在大屏幕上。</li>
</ul>
</div>
</div>

Notes:

这个游戏有三个组件：

- **点击** 第一个是游戏合约，这部分我们已经处理好了。
  我会将其部署并在 Rococo 上运行。
  游戏合约运行游戏循环。
  它调用每个玩家合约并确定得分。
- **点击** 还有 `player.contract`，这是你的任务。
  我们有一个非常基础的玩家模板，你可以根据自己的喜好进行修改。
  我一会儿会解释得分函数。
- **点击** 最后，我们有一个前端界面，会放在这里的大屏幕上，但它是在线的，你可以在笔记本电脑上打开并本地观看游戏。

---

## 流程

1. 🧠 创建一个代表你进行游戏的合约<!-- .element: class="fragment" data-fragment-index="1" -->
1. 🚀 将合约部署到 Rococo 测试网 <!-- .element: class="fragment" data-fragment-index="2" -->
1. 🤝 将你的合约作为玩家注册到游戏合约中<!-- .element: class="fragment" data-fragment-index="3" -->
1. ️🎮 我们开始游戏<!-- .element: class="fragment" data-fragment-index="4" -->
1. 📺️️ 游戏会运行几分钟，我们在屏幕上观看<!-- .element: class="fragment" data-fragment-index="5" -->

Notes:

流程如下：

- **点击** - 你要进行头脑风暴，创建一个代表你进行游戏的合约，理想情况下要比其他合约表现更好。
- **点击** - 你将合约部署到 Rococo 测试网。
- **点击** - 你将你的合约作为玩家注册到游戏合约中。
  我们会公布地址，这并不复杂，你可以使用合约 UI。
  我一会儿也会演示。
- **点击** - 然后我们开始游戏。
  我们有一个脚本会定期调用游戏合约，前端会显示变化。
- **点击** - 游戏会运行几分钟。
  此时你的合约已经上传，所以你无法再做任何操作。
  合约会代表你进行游戏。
  也就是说，你可以放手不管了。
  当然，你也可以使用可升级合约模式等进行更改，但一般来说不需要。
  你只需观看游戏进展。
  如果你熟悉人工智能代理，这也是一个类似的概念，即有一个代理代表你进行游戏。

---

## 游戏界面

<img rounded src="./img/splash-2.png" />

Notes:

这是游戏棋盘的样子。
你可以看到，这是一个坐标网格。

---

## 游戏界面

<img rounded src="./img/splash-9.png" />

Notes:

这是 X1 Y0 位置。
作为合约开发者，你的目标是尽可能多地在这个画布上涂色。
有一个用于涂色的 API 函数，我一会儿会展示，总之，最后涂色最多的合约获胜。

---

## 游戏界面

<img rounded src="./img/splash-10.png" />

Notes:

是的，需要进行一些计分。
这边还有一个计分板，会显示所有玩家及其排名。
你的玩家合约会被分配一个随机颜色。

---

## 如何得分？

- 尽可能使用最少的 gas 来涂更多的区域。

<!-- .element: class="fragment" -->

- 保持在你的 gas 预算内。

<!-- .element: class="fragment" -->

- 你越晚还能成功涂色，得分就越高。

<!-- .element: class="fragment" -->

- 不能重复涂色！第一个涂色的玩家拥有该区域。

<!-- .element: class="fragment" -->

Notes:

这个游戏的设计方式是，所有智能合约开发的最佳实践都会让你的玩家表现更好。
我们真的试图将一些关于如何开发智能合约的最佳实践概念游戏化。

- **点击** - 第一个要点是尽可能使用最少的 gas 来涂更多的区域。
  gas 消耗是一个非常重要的因素，因为用户费用由此衍生，而且交易吞吐量也与合约的大小有关，所以你的合约越简单越好。
- **点击** - 每个玩家都有一定的 gas 预算，如果超出这个预算，你在这一轮就无法行动。
  所以你必须保持在最优的 gas 预算内。
- **点击** - 在游戏后期你还能成功涂色，得分就越高，因为随着游戏进行，会有各种区域被涂色，最后只剩下少数区域。
  所以如果你的玩家只是随机尝试涂色，那么在某个时候就不再起作用了，因为它涂的是已经被涂过的区域。
  但为了找到那些未涂色的区域，你需要在合约中加入一些更复杂的逻辑。
- **点击** - 最后，不能重复涂色。
  所以第一个涂色的玩家拥有该区域。
  因此，如果你的合约只是反复涂同一个区域，那是行不通的。
  所以你需要在合约中加入一些逻辑来检查某个区域是否已经被涂过。

---

## 基础玩家合约

```rust [1-2,19|3-4|7-10|12-17|1-19]
#[ink::contract]
mod player {
    #[ink(storage)]
    pub struct Player {}

    impl Player {
        #[ink(constructor)]
        pub fn new() -> Self {
            Self {}
        }

        /// 在每一轮游戏中被调用。
        /// 返回你想要涂色的像素的 `(x, y)` 坐标。
        #[ink(message, selector = 0)]
        pub fn your_turn(&self) -> Option<(u32, u32)> {
            Some(1, 2)
        }
    }
}
```

Notes:

这是一个非常基础的玩家合约。
我们已经设置了一个包含此模板的代码仓库 - 我一会儿会分享链接。
它的样子是 - 这是一个定义玩家模块的合约。

- **点击** - 一个非常简单的玩家合约不需要包含任何存储。
- **点击** - 一个非常简单的玩家合约也不需要包含任何构造函数参数。
- **点击** - 它可以只返回一个随机的常量值。
  所以这是最简单的玩家合约。
  你的玩家合约需要有一个消息，该消息有一个定义好的选择器，所以你不需要过多考虑这个。
  只是它需要有这个函数，并且这个函数在每一轮游戏中都会被游戏调用。
  你从这个函数返回的值就是你在游戏中的行动。
  所以在这种情况下，如果你返回 `Some(1, 2)`，这意味着你要涂 X1 Y2 位置的像素。
  在这种情况下，游戏会非常无聊。
  谁能告诉我这里会发生什么？
  _（一个只进行一轮行动的玩家 - 它总是尝试涂同一个区域。如果有人也选择了相同的神奇数字，那么它甚至根本无法行动。）_
- **点击** - 有几种方法可以改进它，我稍后会给出一些提示，但现在我们要做的是查看代码仓库，看看如何开始。

---

## 如何玩

<img rounded src="./img/github.png" />

Notes:

这是 Squink-Splash-beginner 代码仓库 - 它包含了一些东西。

---

## 如何玩

<img rounded src="./img/github1.png" />
Notes:

它包含 `cargo.toml` 文件和 `lib.rs` 文件，`lib.rs` 就是我刚才展示的玩家合约。

---

## 如何玩

<img rounded src="./img/github2.png" />

Notes:

它包含游戏元数据。
你需要这些数据来与游戏进行交互，比如注册你的玩家等等。
我们会展示具体操作。

---

## 如何玩

<img rounded src="./img/github3.png" />

Notes:

它有两个 `todo` 文件。
第一个是一些关于通用设置的说明，第二个是关于构建你的玩家合约的说明。

---

## 现在（1）

[github.com/paritytech/squink-splash-beginner ➜ todo-1.md](https://github.com/paritytech/squink-splash-beginner/blob/main/todo-1.md)

Notes:

我们要做的第一件事是，请大家访问这个链接并按照说明操作。
我们会四处走动，帮助遇到问题的人。
我想你可能已经使用过其中的一些要求，所以可能不会太复杂。

---

## 现在（2）

[github.com/paritytech/squink-splash-beginner ➜ todo-2.md](https://github.com/paritytech/squink-splash-beginner/blob/main/todo-2.md)

Notes:

在这个阶段，你需要游戏的地址。
我们会在聊天中发布。
这是一个简单的例子 - 我们只是让你了解上传玩家合约的流程。

---

## 🕹️🎮 让我们开始玩吧！ 🕹️🎮

[https://splash.use.ink](https://splash.use.ink)

Notes:

接下来的幻灯片会介绍策略。
（也许可以等到你玩完一局游戏再看）

---

## 游戏合约

> [`ink-workshop/game/lib.rs`](https://github.com/paritytech/ink-workshop/blob/main/game/lib.rs)

- `pub fn submit_turn(&mut self)`

<!-- .element: class="fragment" -->

- `pub fn board(&self) -> Vec<Option<FieldEntry>>`

<!-- .element: class="fragment" -->

- `pub fn gas_budget(&self) -> u64`

<!-- .element: class="fragment" -->

- `pub fn dimensions(&self) -> (u32, u32)`

<!-- .element: class="fragment" -->

Notes:

当你部署它时，你已经看到有不同的函数可以调用。
有很多有趣的函数。

- **点击** - 游戏运行器会调用这个函数 - 如果你感兴趣，可以看看它是如何工作的，可能会有一些游戏提示。
- **点击** - 然后有一个函数可以查询棋盘，以确定某些区域是否已经被占用或者仍然是空闲的。
- **点击** - 有一个函数可以获取 gas 预算，这样你就可以知道你的玩家在每一轮中可以使用多少 gas。
  因为最糟糕的情况是，如果你的 gas 使用超出了这个预算，那么你在这一轮就无法执行任何行动。
- **点击** - 还有一个函数可以查询游戏的尺寸。
  同样，如果你的涂色超出了边界，那么你也会错过一轮。

---

## 需要考虑的事情 🧠

- 为你的玩家制定一个策略。<br /><br />
- 尽可能使用最少的 gas 来涂更多的区域。
- 保持在你的 gas 预算内。
- 你越晚还能成功涂色，得分就越高。<br /><br />
- 不能重复涂色！第一个涂色的玩家拥有该区域。
- [paritytech/squink-splash-advanced](https://github.com/paritytech/squink-splash-advanced)

---

## 如何进行本地测试？
[paritytech/squink-splash-advanced](https://github.com/paritytech/squink-splash-advanced)
Notes:
这里面有详细说明。你可以在本地部署进行测试，也有不通过用户界面进行部署的命令。
---
## 提示：游戏区域尺寸
- 要在游戏区域边界内涂色！
- 否则你就浪费了一次行动机会。
---
## 思路
- 你可以随心所欲地多次调用自己的合约！
<!-- .element: class="fragment" -->
- 生成随机数
<!-- .element: class="fragment" -->
- 查询哪些区域空闲
  - 通过跨合约调用查询游戏状态
  - 链下计算
<!-- .element: class="fragment" -->
Notes:
- **点击** 最后，为你在游戏中的智能体提供一些思路，你可以随意调用自己的合约。调用次数不受限制，且不会增加游戏中的gas消耗。在游戏过程中，若你想要调整某些内容，也可以调用合约中的一系列函数。
- **点击** 你还可以生成随机数。市面上有不少相关库，不过如果使用这类库，你得留意它们是否为非标准库。通常，随机数生成器库需要启用特定功能才能与非标准环境兼容。
- **点击** 一个巧妙的策略是查询哪些区域空闲。参考这个高级代码仓库中的代码片段，实现起来会有点复杂，但它能给你一些提示。
---
<!-- .slide: data-background="./img/Questions_2.svg"" -->
---
# 第二天
---
<img rounded style="width: 1400px; padding-top:15px;" src="./img/advanced-workshop.jpg" />
---
<pba-cols>
<pba-col center>
### 现在
我们帮你调试！
</pba-col>
<pba-col center>
### 接着
🕹️🎮🕹️🎮
</pba-col>
<pba-col center>
### 之后
讲解解决方案
</pba-col>
</pba-cols>
<br />
<blockquote style="text-align: left; font-size: 0.9em;">
尽可能用最少的gas涂最多的区域。<br /><br />
保持在gas预算内。<br /><br />
越晚还能涂到区域，得分越高。<br /><br />
不能重复涂色！先涂者拥有该区域。<br />
</blockquote>
<br />
<br />
[paritytech/squink-splash-advanced](https://github.com/paritytech/squink-splash-advanced)
---
## 前端
[https://splash.use.ink](https://splash.use.ink)
---
## 问题
- 获胜者采用了什么策略？
<!-- .element: class="fragment" -->
- 其他人采用了什么策略？
<!-- .element: class="fragment" -->
- 你认为完美的策略是什么？
<!-- .element: class="fragment" -->
---
## 棋盘尺寸
- 最差做法 😱
  - 对`game`进行跨合约调用<br /><br />
```rust
#[ink(message)]
pub fn dimensions(&self) -> (u32, u32)
```
<br /><br />
<!-- .element: class="fragment" -->
- 最佳做法 👍️
  - `const width: u32`
  - `new(width: u32, height: u32)`
<!-- .element: class="fragment" -->
---
## 更多易错点
<img rounded style="margin-top: 25px; width: 400px;" src="./img/oopsie.gif" />
- 忘记使用`--release`
<!-- .element: class="fragment" -->
- 在合约中对数据结构进行迭代
<!-- .element: class="fragment" -->
---
## 避免迭代
<pba-cols>
<pba-col center>
```
#[ink(message)]
fn pay_winner()
  let winner = self.players.find(…);
  self.transfer(winner, …);
}
```
</pba-col>
<!-- .element: class="fragment" -->
<pba-col center>
```rust
#[ink(message)]
fn pay_winner(
    winner: AccountId
) {
  assert!(is_winner(winner));
  self.transfer(winner, …);
}
```
</pba-col>
<!-- .element: class="fragment" -->
</pba-cols>
---
## 策略1<br />返回随机数
<img rounded style="margin-top: 25px; width: 500px;" src="./img/0.png" />
---
## 策略1<br />返回随机数
- 与Wasm兼容的随机数生成器
<!-- .element: class="fragment" -->
- 使用存储来保存随机数种子
<!-- .element: class="fragment" -->
- 📈 消耗较少gas
<!-- .element: class="fragment" -->
- 📉 很快会出现碰撞
<!-- .element: class="fragment" -->
- 📉 得分函数对在游戏后期仍能涂到区域的玩家给予奖励
<!-- .element: class="fragment" -->
---
## 策略2<br />只涂空闲区域
<img rounded style="margin-top: 25px; width: 500px;" src="./img/1.png" />
---
## 策略2<br />只涂空闲区域
- 查询棋盘的空闲区域
- 📈 在游戏后期容易成功
<!-- .element: class="fragment" -->
- 📉 跨合约调用 💰️
- 📉 需要遍历`Mapping`：时间复杂度为`O(n)`
<!-- .element: class="fragment" -->
---
## 策略3<br />将计算转移到链下
<img rounded style="margin-top: 25px; width: 500px;" src="./img/2.png" />
---
## 策略3<br />将计算转移到链下
- 链下脚本
  - 查询棋盘 ➜ 搜索空闲区域<br /><br />
<!-- .element: class="fragment" -->
- ```rust[1-2|1-7]
  #[ink(message)]
  fn set_next_turn(turn: …) {}
  #[ink(message, selector = 0)]
  pub fn your_turn(&mut self) -> {
    self.next_turn
  }
  ```
<!-- .element: class="fragment"  -->
---
## 策略4<br />利用游戏循环中的玩家排序
<img rounded style="margin-top: 25px; width: 500px;" src="./img/3.png" />
---
## 策略4<br />利用游戏循环中的玩家排序
- 基于策略3（链下计算）。
<!-- .element: class="fragment"  -->
- 游戏循环每次以相同顺序调用玩家。
<!-- .element: class="fragment"  -->
```rust
#[ink(message)]
fn submit_turn(&mut self) {
    // -- 略 --
    for (idx, player) in players.iter_mut().enumerate() {
        …
    }
  // -- 略 --
}
```
<!-- .element: class="fragment"  -->
---
## 策略4<br />利用游戏循环中的玩家排序
```rust
impl<T: Config> AddressGenerator<T> for DefaultAddressGenerator {
	fn generate_address(
		deploying_address: &T::AccountId,
		code_hash: &CodeHash<T>,
		input_data: &[u8],
		salt: &[u8],
	) -> T::AccountId {
    // -- 略 --
	}
}
```
➜ 所有输入已知
<!-- .element: class="fragment"  -->
➜ 利用已知输入生成较小的`T::AccountId`
<!-- .element: class="fragment"  -->
---
## 策略5<br />昨天就查看这些幻灯片
<img rounded style="margin-top: 25px; width: 500px;" src="./img/4.png" />
---
<!-- .slide: data-background="./img/Questions_2.svg"" -->


