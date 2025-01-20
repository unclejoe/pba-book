# 贡献者指南

感谢你对为学院做出贡献感兴趣！✨

在做任何其他事情之前，请阅读我们的[行为准则](CODE-OF-CONDUCT.md)，以了解我们社区的指导方针。

本指南旨在帮助学院的贡献者了解此存储库中包含的所有材料是如何组织的，以及如何与之交互和修改它们。
我们为贡献者提供了多种工具，用于制作幻灯片、讲师指导的工作坊和自主活动。

## 安装

学院的开发重度依赖Rust，因此，你需要先[安装Rust](https://www.rust-lang.org/tools/install)。

为了让你的生活更轻松 😉，有一组使用[`cargo make`](https://sagiegurari.github.io/cargo-make/#overview)的任务。

安装了[`cargo make`](https://sagiegurari.github.io/cargo-make/#installation)之后，你可以列出所有包含的任务，以方便进一步进行安装、构建、服务、格式化等操作：

```sh
# 从本存储库的顶级工作目录运行
makers --list-all-steps
```

这些任务应该是不言自明的，如果不是 - 请提交一个问题，以帮助我们改进它们。

```sh
# 安装所有依赖项
makers i
```

<details>
<summary>(不建议) 手动安装</summary>

你可以选择不使用上述的`cargo make`，但至少需要具备以下条件：

### 书籍 - `mdBook`

- [安装`mdBook`](https://rust-lang.github.io/mdBook/guide/installation.html)

离线提供书籍服务：

```sh
# 从本存储库的工作目录运行
mdbook serve --open
```

### 幻灯片和工具 - `bun`

使用[bun](https://bun.sh/)来安装和运行JavaScript和Node工具。
安装了`bun`之后，从学院的顶级目录中：

```sh
if ! $(echo "type bun" | sh > /dev/null ); then
    echo "🥟 正在安装 https://bun.sh ..."
    curl -fsSL https://bun.sh/install | bash
fi
echo "💽 Bun已安装。"

echo "🥟 使用Bun安装幻灯片工具..."
bun install
echo "✅ 幻灯片安装完成！"
```

这应该会在新的浏览器标签页中打开一个简单的幻灯片列表供你选择。

### 嵌入式幻灯片

目前，有一个“技巧”可以将幻灯片嵌入到书籍中，即将幻灯片构建生成的静态HTML资产复制到书籍中，以便它们可以在`iframe`中正常工作。
请查看`Makefile.toml`中的`[tasks.serve]`，以了解手动实现此功能所需的命令。
同样，在这里使用`cargo make`要比手动运行这些命令方便得多！

</details>

## 内容设计

**学院更侧重于我们所涵盖的Web3概念的实际应用，而不仅仅是理解。**

### 组织

书籍的全部内容，包括本书所需的资产（图像、代码等）都位于`./content/*`中。
目录结构如下：

```sh
content
├── <模块>
├── index.md                  # 面向学生的模块概述
├── faculty-guide.md          # 面向教师的模块运行指南
├── README.md -> index.md     # 软链接 `ln -s index.md README.md` - 用于Github网页阅读
│  ├── <课程>               # 与讲座相关，包含幻灯片
│  │  ├── img
│  │  │  ├── <媒体文件>          # 本课程中使用的png、gif、mp4、jpg等文件
│  │  │  ├── ...
│  │  ├── page.md             # 通常是一个用于嵌入`slides.md`的存根文件
│  │  └── slides.md           # 一个采用`reveal-md`格式的文档
│  ├── _materials             # 与工作坊、练习或活动相关
│  │  ├── img
│  │  │  ├── <媒体文件>          # 本课程中使用的png、gif、mp4、jpg等文件
│  │  │  ├── ...
│  │  ├── <材料>.md       # 面向学生的关于某些材料的说明
.  .  .   ...
```

- `<模块>/README.md` - **必填** 指向`index.md`的软链接。
- `<模块>/index.md` - **必填** 书籍页面，**必须** 列在`SUMMARY.md`中。
- `<模块>/faculty-guide.md` - **必填** 不在书籍中使用的页面，**不得** 列在`SUMMARY.md`中。
- `<模块>/<课程>/page.md` - **必填** 书籍页面，**必须** 列在`SUMMARY.md`中。
- `<模块>/<课程>/slides.md` - **可选** 幻灯片，嵌入到`page.md`中，如果使用幻灯片，则**必须** 嵌入到`page.md`中。
- `<模块>/<课程>/img` - **可选** 包含本课程的幻灯片或页面中使用的媒体文件的目录。
- `<模块>/<课程>/_materials` - **可选** 包含幻灯片或页面中引用的内容的目录

## 开发工作流程

通常，课程的大部分工作都集中在幻灯片的开发上。
嵌入幻灯片的页面主要是静态存根，用于在其中托管幻灯片。
工作坊和活动页面是个例外，它们通常没有相关的幻灯片，或者需要幻灯片之外的更多信息。
在实际操作中，查看幻灯片的渲染Markdown比迭代页面更重要。

### 使用`reveal-md`制作幻灯片

幻灯片主要包含用于课堂讲解的讲座材料，并且这些幻灯片必须包含`Notes:`部分，其中包含关于幻灯片内容的详细**面向学生的**信息，而不仅仅是面向演讲者的笔记！
通常，幻灯片笔记应该嵌入所有参考资料、资源以及学生在课堂期间和课后可以使用的进一步考虑内容。

要在**监视**模式下（内容中的任何文件发生更改时立即更新）查看和编辑（仅）幻灯片：

```sh
# 仅用于幻灯片的监视服务器
makers serve-slides
# 或者简单地：
bun s
```

有关`reveal.js`的功能和使用方法的更多详细信息，请参阅[使用本书](./how-to/page.md)页面。

**如果你是第一次使用`reveal.js`，我们建议你浏览[官方演示](https://revealjs.com/demo/#/2)，看看你可以用它做些什么！**
我们正在使用[`reveal-md`](https://github.com/webpro/reveal-md)创建和定制幻灯片：这是一个基于`reveal.js`构建的工具，允许使用[Markdown](https://commonmark.org/help/)制作幻灯片，并有一些额外的语法项，让你可以在风格和视觉设计上尽可能少地花费精力，就能让你的幻灯片看起来很棒。

##### 复制粘贴幻灯片

[复制粘贴幻灯片模板](./copy-paste-slides/page.md)页面和嵌入式幻灯片的源代码展示了如何使用和提供代码片段，以适应**许多常见的幻灯片原型**。
应该可以从这些模板中修改你幻灯片中的示例，包括：

- 多列幻灯片
- 嵌入式媒体
- 图表（mermaid等）

### 使用`mdBook`制作页面

页面嵌入幻灯片，并且可能包含指向材料、参考资料和其他内容的链接，**当这些内容不适合包含在幻灯片的演讲者笔记中时**。
大多数页面只是用于嵌入幻灯片的“存根”文件。

要在**非监视**模式下同时处理嵌入式幻灯片和书籍：

```sh
makers s # 构建幻灯片（覆盖书籍存储库中`./slides`下跟踪的幻灯片），嵌入到书籍中，查看更新后的书籍。

# ... 对书籍和/或幻灯片进行更改 ...
# ... 使用`ctrl+c`终止服务器 ...

makers s # 构建幻灯片（覆盖书籍存储库中`./slides`下跟踪的幻灯片），嵌入到书籍中，查看更新后的书籍。
```

> 😭 目前，这是一个非监视服务器，你必须手动打开页面并**强制刷新**之前提供的页面才能看到更新。
>
> 你必须**重新运行**此命令以在文件更改时进行更新！

### 课程模板

请访问[课程模板](./template/page.md)页面，并在继续之前**仔细阅读源代码**。
整个目录旨在复制并粘贴到正确的模块中，以开始新的课程开发：

```sh
# 复制整个内容 👇😀
└── template
   ├── img
   │  └── REMOVE-ME-example-img.png
   ├── page.md
   └── slides.md
```

`page.md`文件应该**嵌入`slides.html`页面**，在构建过程创建该页面之前，这个嵌入不会生效，但一旦构建完成，它就会存在并进行渲染 😉。

### 文件大小考虑

我们努力避免在本书中加载过大的资产，因此，我们要求所有贡献者在将任何资产提交到本存储库之前：

- 检查图像文件大小，并尽可能进行压缩，使其在全屏显示时看起来还可以，或者使用较小的替代文件。
  示例：
  ```sh
  # 使用imagemagick进行压缩
  convert <输入文件> -quality 20% <输出文件>
  ```
- 将所有视频的大小调整到尽可能小，使其在全屏显示时看起来还可以。
  示例：
  ```sh
  # 视频的比特率是多少？
  ffmpeg -i <输入文件> 2> >(grep -i bitrate)
  # 降低比特率，反复尝试找到最小尺寸的“足够好”的比特率
  ffmpeg -i <输入文件> -b 400k <输出文件>
  ```

### 重构考虑

<details>
<summary>🚧 大多数贡献者通常不需要此工作流程。 点击查看 🚧</summary>

我们选择不使用方便的助手来创建`SUMMARY.md`中链接的缺失文件，因为这可能表明我们在幻灯片 -> 存根页面映射的转换中可能存在问题。

当**彻底**更新幻灯片的路径结构和/或文件名时，重新启用此功能会很有用，因为否则必须**手动**应用更改，以链接到`/slides/.../*-slides.html`中的正确新位置。

你可以通过编辑`book.toml`来选择启用：

```diff,toml
[build]
- create-missing = false # 不创建缺失的页面
+ create-missing = true # 创建缺失的页面
```

### 嵌入式幻灯片的提示

所有模块的结构都如[内容组织](#organization)部分所述。

所有`slides.md`文件都是与`page.md`文件相关联的幻灯片内容的源文件，`page.md`文件会将这些幻灯片嵌入到书籍中。
`page.md`文件通常只是存根，但可以选择添加更多详细信息、说明等。
它们通常与以下内容相同：

```md
# 这里是某个标题

<!-- markdown-link-check-disable -->

<center>
<iframe style="width: 90%; aspect-ratio: 1400/900; margin: 0 0; border: none;" src="slides.html"></iframe>
<br />
<a target="_blank" href="../../contribute/how-to/page.md#-how-to-use-revealjs-slides"><i class="fa fa-pencil-square"></i> 如何使用幻灯片</a> -
<a target="_blank" href="slides.html"><i class="fa fa-share-square"></i> 全屏显示（新标签页）</a>
<br /><br />
<div ><i class="fa fa-chevron-circle-down"></i> 原始幻灯片Markdown</div>
<br />
</center>
{ {#include slides.md} } <!-- 👈 使用时请删除花括号中的空格 ( {{include things}} ) ，否则mdBook会在示例中出现构建错误 😜 -->
<a href="#top" style="position: fixed; right: 11%; bottom: 3%;"><i style="font-size: 1.3em;" class="fa fa-arrow-up"></i></a>

<!-- markdown-link-check-disable -->
```

- `find . -name 'page.md' -exec bash -c 'cat ../tmp  >> "{}"' \;` 以应用嵌入幻灯片的页面内容

</details>

### ⏰ 关键注意事项

- 始终使用正确的MarkDown链接！
  必须使用`<https://parity.io>`这种格式，原始链接**不会**在mdBook中渲染！
- 切勿使用可能会失效的链接。
  此示例位于主分支，而非某个PR分支：<https://github.com/paritytech/polkadot-sdk/blob/5174b9d/polkadot/roadmap/implementers-guide/src/node/backing/prospective-parachains.md>
  使用GitHub链接时，**必须** 是指向提交哈希的永久链接，而不是`main`或其他分支。
- 重复使用图像，不要重复任何图像，尽可能用相近的图像替代。
  支持**相对**路径：`../../<其他课程>/img/<现有图像>`

---

## 约定和辅助工具

本书以及其中的所有内容都有样式和排版约定，在实际可行的情况下，有一些工具可以帮助大家遵守这些约定。

```sh
# 这将安装用于格式化、代码检查等所需的工具。
makers install-dev
```

### 格式化
本仓库中的所有Markdown、TOML、JSON、TypeScript和JavaScript文件都使用 [dprint](https://dprint.dev/) 进行格式化。
该格式化工具的设置在 `.dprint.json` 文件中。
我们使用 `cargo make` 来运行格式化操作：
```sh
# 首次运行缓存后，这将极快地格式化所有文件
makers f
```
如果（且仅当）格式化操作导致Markdown无法正确渲染时，你可以在Markdown代码块前使用 `<!-- prettier-ignore -->` 来跳过格式化，示例如下：
````markdown
<!-- prettier-ignore -->
```html
<pba-cols>
  <pba-col>
    
    ### What's up, yo?
</pba-col>
<pba-col>
  
  - Yo
- Yo
- Yo
</pba-col>
</pba-cols>
```
<!-- prettier-ignore-start -->
Some    text
* other    text
*           testing
<!-- prettier-ignore-end -->
````
更多内容请查看 [dprint关于Markdown的文档](https://dprint.dev/plugins/markdown/#ignore-comments)。

### 链接检查
为确保所有 `*.md` 和 `.html` 文件中不存在损坏的链接，我们使用 [mlc链接检查器](https://github.com/becheran/mlc)。
运行命令如下：
```sh
# 检查所有内容文件中的链接
makers l
# 检查单个文件的链接：
makers links-for <相对于顶级工作目录的文件路径/file.md>
# 递归检查目录中的链接
makers links-for <./content/some-dir/inner-dir>
```
检查器的配置在 `Makefile.rs` 任务中进行设置，可查看相关设置。
`.mlc.toml` 配置文件用于全局忽略特定的、会报错的常见URL，不过有点问题😛……
至少理论上是这样，但目前 [该功能无法正常工作](https://github.com/becheran/mlc/issues/76)。
**注意，被忽略的链接必须定期手动检查！**
**因此，除非明确需要，否则不要使用这类链接，尽可能使用已知有效的URL，比如可以从 <https://archive.org/web/> 获取。**
我们的CI也会在所有分支的每次推送时，对所有文件运行相同的工具进行检查。
详细信息请查看本仓库中的 `.github/workflows/link-check.yml` 文件。
> 你可以在以下位置忽略符合 `regex` 规则的链接检查项：
> 1. `.mlc.toml`
> 2. `.github/workflows/check.yml`
> 3. `Makefile.rs`
> 
> [最终](https://github.com/becheran/mlc/issues/78) 只需要在 `.mlc.toml` 中设置即可。

### 图片检查
为确保本书中的图片没有问题，我们会检查：
1. 构建过程中找不到的图片的损坏链接。
2. 孤立的图片文件，即书中完全没有引用到的图片。
```sh
# 检查所有`<img ...>`标签的链接
makers img
```
请**删除**任何你不需要的资源，之后我们随时可以通过 `git recover` 命令恢复它们💽。

## 持续集成（CI）
1. `.github/workflows/pages.yml` - 每次与 `main` 分支合并时，CI会负责构建书籍并部署其托管版本。
2. `.github/workflows/check.yml` - 每次与 `main` 分支合并时，CI会负责检查书籍的格式、链接和图片是否存在问题。
更多详细信息请查看本仓库中的 `.github/workflows/` 目录。
目前，其他任务大多独立于开发工作流程中建议使用的 `cargo make` 工具，但有些任务需要 `bun` 工具才能正确构建和测试相关内容。 