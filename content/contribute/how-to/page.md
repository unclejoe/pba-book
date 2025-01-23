# 🪄 使用本书

本书按顺序包含了学院的所有内容。建议从头到尾阅读，通过参看每个模块的 `index.md` 页面了解该模块的概要。

## 📔 如何使用 `mdBook`

本书使用 Rust 原生文档工具 [mdBook](https://rust-lang.github.io/mdBook/) 制作。
请阅读官方文档，了解其中的功能。
列举[几个关键项](https://rust-lang.github.io/mdBook/guide/reading.html#top-menu-bar)：

| 图标                              | 描述                                                                     |
| --------------------------------- | ------------------------------------------------------------------------------- |
| <i class="fa fa-bars"></i>        | 打开和关闭章节列表侧边栏。                                   |
| <i class="fa fa-paint-brush"></i> | 打开主题选择器，用于选择不同的颜色主题。                               |
| <i class="fa fa-search"></i>      | 打开搜索栏，用于在书中搜索。                               |
| <i class="fa fa-print"></i>       | 通过网页浏览器打印整本书。                             |
| <i class="fa fa-github"></i>      | 打开托管本书源代码网站的链接。             |
| <i class="fa fa-edit"></i>        | 打开一个页面，直接编辑你当前正在阅读的页面的源代码（远程仓库）。 |

### <i class="fa fa-search"></i> 搜索

点击菜单栏中的搜索图标 (<i class="fa fa-search"></i>)，或者按下键盘上的 `S` 键，将打开一个输入框，用于输入搜索词。
会根据内容实时显示匹配的章节和小节。

点击任何结果将跳转到该小节。
可以使用上下箭头键浏览结果，按回车键将打开突出显示的小节。

加载搜索结果后，匹配的搜索词将在文本中突出显示。
点击突出显示的单词或按下 `Esc` 键将取消突出显示。

## 🎞️ 如何使用 `reveal.js` 幻灯片

大多数页面都包含具有许多实用功能的嵌入式幻灯片。
这些幻灯片使用的是 [`reveal-md`](https://github.com/webpro/reveal-md)：一个基于 `reveal.js` 构建的工具，允许只使用 [Markdown](https://commonmark.org/help/) 制作幻灯片，还有一些额外的语法项，可以让你的幻灯片看起来很棒，而且几乎不需要费什么力气。

> 📝 要使用幻灯片的快捷键，请确保页面上的幻灯片 `iframe` 处于活动状态（🖱️ 点击它）……
> 否则，这些快捷键将被 `mdbook` 工具捕获！
> （`s` 键用于搜索本书，也用于查看幻灯片的演讲者备注）

通过使用**快捷键**与幻灯片进行交互，成为这些幻灯片的高级用户：

- 使用 `space` 键浏览所有幻灯片：从上到下，从左到右。
  - 使用 `down/up` 箭头键浏览垂直幻灯片。
  - 使用 `left/right` 箭头键浏览水平幻灯片。
- 按 `Esc` 或 `o` 键查看概述视图，可以使用箭头键进行导航。
- 按 `s` 键打开演讲者视图。<br />
  **👀 演讲者备注包含非常重要的信息，不容错过！**

### 💫 幻灯片演示

尝试使用以下快捷键（🖱️ 点击幻灯片开始）：

<!-- markdown-link-check-disable -->
<iframe style="width: 90%; aspect-ratio: 1400/900; margin: 0 5%; border: none;" src="slides.html"></iframe>
<center>
<a target="_blank" href="../../contribute/how-to/page.md#-how-to-use-revealjs-slides"><i class="fa fa-pencil-square"></i> 如何使用幻灯片</a> -
<a target="_blank" href="slides.html"><i class="fa fa-share-square"></i> 全屏幻灯片（新标签页）</a>
</center>
<br />
<details>
<summary>(🖱️ 展开) 幻灯片内容的原始 Markdown</summary>
{{#include slides.md}}
</details>
<!-- markdown-link-check-enable -->

☝️ 所有幻灯片的 `Slides Content` 在所有页面都可用。
这使得**搜索**功能可以在本书中正常工作，以便你可以根据记忆中与课程中所涵盖内容相关的任何关键词**跳转到相应位置** 🚀。

### 📖 了解更多

- <https://revealjs.com/>
  - [官方幻灯片演示](https://revealjs.com/demo/)
- [Markdown ➡ 幻灯片构建工具](https://github.com/webpro/reveal-md/)
