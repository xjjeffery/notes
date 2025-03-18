---
comments: true
counter: true
tags:
  - tools
---

# MkDocs 使用记录

## 基本介绍

[MkDocs](https://www.mkdocs.org/) 是一款快速、简单且非常漂亮的静态站点生成器，旨在构建项目文档。文档源文件以 Markdown 编写，并使用单个 YAML 配置文件进行配置。

MkDocs 页面必须用 [Markdown](https://daringfireball.net/projects/markdown/) 编写，这是一种轻量级标记语言，可以生成易于阅读、易于编写的纯文本文档，并可以可预测的方式转换为有效的 HTML 文档。

MkDocs 使用 [Python-Markdown](https://python-markdown.github.io/) 库将 Markdown 文档呈现为 HTML。Python-Markdown 几乎完全符合[参考实现](https://daringfireball.net/projects/markdown/)，尽管存在一些非常细微的差异。

除了所有 Markdown 实现中通用的基本 [Markdown 语法](https://daringfireball.net/projects/markdown/syntax)之外，MkDocs 还支持使用 Python-Markdown 扩展来扩展 Markdown 语法。请参阅 MkDocs 的 [markdown_extensions](https://www.mkdocs.org/user-guide/configuration/#markdown_extensions) 配置设置以了解如何启用扩展的详细信息。

使用过 Python 的都知道，Python 的依赖很容易因为版本的不同而造成环境的冲突，因此 Python 项目的依赖管理强烈建议使用虚拟环境。用于 Python 虚拟环境创建与管理的工具众多，除了 Python 自带的 `venv` 模块加上 `pip` 命令，还有 [Anaconda](https://www.anaconda.com/docs/main)/[Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main) 和一众第三方工具。

## 安装和使用

MkDocs 是 Python 的一个包，直接 `pip install mkdocs` 就可以了。

入门非常简单。要创建新项目，请从命令行运行以下命令：

```shell
$ mkdocs new test    # 创建一个名为 test 的文件夹，存储代码
$ cd test
```

此时的目录结构如下:

```test
test/
 ├── docs/            # 存放markdown文档
 │     └── index.md   # 主页
 └── mkdocs.yml       # 配置文件
```

打开实时渲染服务（默认端口 8000），并且使用 watchdog 监控文件夹内的更改，命令如下:

```shell
$ mkdocs serve
```

在浏览器中输入 `127.0.0.1:8000` 预览，终端键入 ++ctrl+c++ 关闭服务器。也可以自己编译整个项目，生成一堆静态网页代码，命令如下:

```shell
$ mkdocs build         # 生成静态网页代码
```

这时已经生成了 `site/` 文件夹，可以将里面的内容部署到网站上了。

```shell 
$ mkdocs gh-deploy
```

自动根据 `mkdocs.yml` 中设置的项目地址部署到 GitHub 的 `gh-pages` 分支中。

## 常用设置

此博客就是基于 Material for MkDocs 来设计的，[Material for MkDocs 官方文档](https://squidfunk.github.io/mkdocs-material/)详细地描述如何使用该主题来设计自己的文档，但是官方文档内容对于我个人的使用来说是比较啰嗦的。下面将自己用到的一些内容做一个简单的描述，大家可以简单参考，如有需要更多的内容，请自行阅读官方文档。

### 站点 UI 设置

#### 颜色设置

Material for MkDocs 支持 Google 的原始调色板，可以通过 `mkdocs.yml` 轻松配置，示例如下:

```yaml
theme:
  name: material # 主题名称
  palette:
    # 下面的方式可以实现调色板的切换
    - media: "(prefers-color-scheme: light)" # 通过使用媒体查询，可以将每个调色板链接到用户对明暗外观的系统偏好设置
      scheme: default     # 配色方案
      primary: light blue # 用于标题、侧边栏、文本链接和其他几个组件的颜色
      accent: teal        # 强调色用于表示可以交互的元素，如悬停链接、按钮和滚动条
      toggle:
        icon: material/weather-sunny  # 调色板的图标
        name: Light Mode              # 用作切换按钮的 title 属性
    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      primary: black
      accent: teal
      toggle:
        icon: material/weather-night
        name: Dark Mode
```
#### 字体设置

Material for MkDocs 可直接与 Google Fonts 集成，可以轻松更改项目文档的字体。或者，如果出于数据隐私原因而希望使用自托管或应使用其他目的，则可以自定加载字体。使用示例如下:

```yaml
theme:
  font:
    text: Noto Sans SC
    code: Martian Mono
```

如果想要从另一个目标加载（附加）字体或覆盖系统字体，则可以使用附加样式表来添加相应的 `@font-face` 定义:

=== ":octicons-file-code-16: `docs/stylesheets/extra.css`"

    ``` css
    @font-face {
      font-family: "<font>";
      src: "...";
    }
    ```

=== ":octicons-file-code-16: `mkdocs.yml`"

    ``` yaml
    extra_css:
      - stylesheets/extra.css
    ```

然后可以将字体应用于特定元素，例如仅标题，或全局用作站点范围的常规或等宽字体:

=== "Regular font"

    ``` css
    :root {
      --md-text-font: "<font>"; /* (1)! */
    }
    ```

    1. 始终通过 CSS 变了而不是 `font-family` 来定义字体，因为这会禁用系统字体后备。

=== "Monospaced font"

    ``` css
    :root {
      --md-code-font: "<font>";
    }
    ```

#### 网站图标设置

在 `mkdocs.yml` 中可以设置网站中的各种图标，如 logo、favicon、site icon，示例如下:

```yaml
theme:
  favicon: assets/images/favicon.png        # 网站站点的图标
  icon:
    logo: material/notebook-outline         # 网站站点的图标
    repo: fontawesome/brands/github         # 仓库的图标
    previous: fontawesome/solid/angle-left  # 上一页的图标
    next: fontawesome/solid/angle-right     # 下一页的图标
```

### 导航栏设置

Material for MkDocs 提供了多种选项来配置导航元素，包括选项卡、section 等多个内容，示例如下:

```yaml
theme:
  features:
    - navigation.tracking # 启用锚点跟踪后，地址栏中的 URL 会自动更新为目录中突出显示的活动锚点
    - navigation.tabs # 启用选项卡以后，顶级部分将在 1220px 以上的视口的标题下方的菜单层中呈现，但在移动设备中保持原样
    - navigation.tabs.sticky # 导航标签将固定在标题下方，并在向下滚动时始终保持可见
    - navigation.sections # 顶级部分将作为组呈现在侧边栏中，以适应 1220px 以上的视口，但在移动设备中保持原样
    - navigation.expand # 默认展开所有可折叠的子部分
    - navigation.path # 每个标题上方都会显示一个导航路径
    - navigation.prune # 呈现的 HTML 中仅半酣可见的导航项，与 navigation.expand 不兼容
    - navigation.indexes # 可以将文档直接附加到 section 上，无需提供额外的页面
    - toc.follow # 侧边栏会自动滚动，以便活动锚点始终可见
    - navigation.top # 当用户向下滚动后再次开始向上滚动时，可以显示返回顶部按钮
```

### 搜索设置

Material for MkDocs 提供了出色的客户端搜索实现，无需集成第三方服务，因为第三方服务可能不符合隐私法规。此外，搜索甚至可以离线进行，允许用户下载您的文档。

内置搜索插件与 Material for MkDocs 无缝集成，使用[lunr](https://lunrjs.com/) 和[lunr-languages](https://github.com/MihaiValentin/lunr-languages) 添加多语言客户端搜索。它默认启用，但在使用其他插件时必须重新添加到 `mkdocs.yml`:

```yaml
plugins:
  - search
```

此外还能启用搜索突出显示，用户点击搜索结果时，Material for MkDocs 将在点击链接后突出显示所有匹配项。也可以进行搜索共享，当用户点击分享按钮时，URL 会自动复制到剪贴板。

```yaml
theme:
  features:
    - search.highlight
    - search.share
```

对于中文文本，可以使用 [jieba](https://pypi.org/project/jieba/) 对中文进行分词，我们可以使用以下的配置对搜索进行搜索分段:

```yaml
plugins:
  - search:
      jieba_dict: dict.txt  # 指定 jieba 用于分词的自定义字典，以替换默认字典
      jieba_dict_user: user_dict.txt # 指定 jieba 用于分段文本的附加用户字典，以扩充默认字典。
```

### 添加评论系统

本人常用的是 giscus 实现的评论系统，[giscus 官网](https://giscus.app/)已经将具体用法都描述清楚，在 [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/setup/adding-a-comment-system/) 中也描述了如何在 MkDocs 中集成这个评论系统，可自行阅读理解。

### 内部链接

MkDocs 支持在文档中的页面之间进行链接跳转，需要使用 [toc](https://python-markdown.github.io/extensions/toc/) 扩展为 Markdown 文档中的每个标题生成一个 ID。可以使用该 ID 通过锚链接链接到目标文档中的某个部分，生成的 HTML 将正确转换链接的路径部分，并保持锚部分完整。

```markdown
Please see the [project license](about.md#license) for further details.
```

!!! note

    ID 是根据标题的文本创建的，所有文本都转换为小写，任何不允许的字符（包括空格）都转换为破折号。然后，连续的破折号将缩减为单个破折号。

    如果使用的是中文标题，所产生的 ID 可能是一个如 `_1` 的值，要想生成中文的 ID，可以在 `mkdocs.yml` 文件中添加以下的内容:

    ```yaml
    markdown_extensions:
    - toc:
      slugify: !!python/object/apply:pymdownx.slugs.slugify {}
    ```

toc 扩展提供了一些配置设置，您可以在 `mkdocs.yml` 配置文件中设置这些配置设置来改变默认行为：

```yaml
markdown_extensions:
  - toc:
      permalink: "#" # 设置标题后的链接文本
      baselevel: 2   # 设置标头的基本级别，默认为 1
      separator: "_" # 单词分隔符，替换生成的 ID 中的空格的字符
      toc_depth: 3   # 子目录的胜读
```

### Metadata

在 MkDocs 中，元数据是一种强大的工具，用于增强文档的描述性、可定制性和功能性。它本质上是 Markdown 文件与生成 HTML 之间的桥梁，通过键值对的形式提供额外信息，提供了一种灵活的方式来增强文档的功能或自定义其表现形式。

元数据一般以 YAML 格式（有时也叫 Front Matter）写在 Markdown 文件的开头，用一对 `---` 分隔符包裹。例如：

```markdown
---
title: 我的页面标题
description: 这是一个简单的描述
tags: [标签1, 标签2]
---

# 正文内容
```

- 作用：元数据是键值对的形式，键（如 `title`、`description`）和对应的值会被 MkDocs 或其主题、插件解析，用于影响页面生成、显示或搜索等功能。
- 位置：通常位于文件顶部，但某些插件也支持在其他位置定义元数据。

## 自定义设置

MkDocs 提供了多种自定义主题的方法，简单的方法就是在 `docs` 目录中创建一个新的样式表文件:

``` { .sh .no-copy }
.
├─ docs/
│  └─ stylesheets/
│     └─ extra.css
└─ mkdocs.yml
```

然后，将这个样式表文件添加到 `mkdocs.yml` 文件中:

```yaml
extra_css:
  - stylesheets/extra.css
```

如果想要集成另一个语法高亮器或在主题添加一些自定义逻辑，请在 `docs` 目录中创建一个新的 JavaScript 文件：

``` { .sh .no-copy }
.
├─ docs/
│  └─ javascripts/
│     └─ extra.js
└─ mkdocs.yml
```

然后，将这个 JS 文件添加到 `mkdocs.yml` 文件中:

```yaml
extra_javascript:
  - javascripts/extra.js
```

### 扩展主题

如果想更改 HTML 源代码（例如添加或删除某些部分），可以展主题。MkDocs 支持主题扩展，像往常一样在 `mkdocs.yml` 中启用 MkDocs 的 Material，并创建一个新的文件夹用于 `overrides`，然后使用 `custom_dir` 设置引用该文件夹：

```yaml
theme:
  name: material
  custom_dir: overrides
```

`overrides` 目录中的结构必须与原始主题的目录结构一致，因为 `overrides` 目录中的任何文件都将替换原始主题中同名的文件。此外，还可以将其他资源放入 `overrides` 目录中：

``` { .sh .no-copy }
.
├─ .icons/                             # Bundled icon sets
├─ assets/
│  ├─ images/                          # Images and icons
│  ├─ javascripts/                     # JavaScript files
│  └─ stylesheets/                     # Style sheets
├─ partials/
│  ├─ integrations/                    # Third-party integrations
│  │  ├─ analytics/                    # Analytics integrations
│  │  └─ analytics.html                # Analytics setup
│  ├─ languages/                       # Translation languages
│  ├─ actions.html                     # Actions
│  ├─ alternate.html                   # Site language selector
│  ├─ comments.html                    # Comment system (empty by default)
│  ├─ consent.html                     # Consent
│  ├─ content.html                     # Page content
│  ├─ copyright.html                   # Copyright and theme information
│  ├─ feedback.html                    # Was this page helpful?
│  ├─ footer.html                      # Footer bar
│  ├─ header.html                      # Header bar
│  ├─ icons.html                       # Custom icons
│  ├─ language.html                    # Translation setup
│  ├─ logo.html                        # Logo in header and sidebar
│  ├─ nav.html                         # Main navigation
│  ├─ nav-item.html                    # Main navigation item
│  ├─ pagination.html                  # Pagination (used for blog)
│  ├─ palette.html                     # Color palette toggle
│  ├─ post.html                        # Blog post excerpt
│  ├─ progress.html                    # Progress indicator
│  ├─ search.html                      # Search interface
│  ├─ social.html                      # Social links
│  ├─ source.html                      # Repository information
│  ├─ source-file.html                 # Source file information
│  ├─ tabs.html                        # Tabs navigation
│  ├─ tabs-item.html                   # Tabs navigation item
│  ├─ tags.html                        # Tags
│  ├─ toc.html                         # Table of contents
│  ├─ toc-item.html                    # Table of contents item
│  └─ top.html                         # Back-to-top button
├─ 404.html                            # 404 error page
├─ base.html                           # Base template
├─ blog.html                           # Blog index page
├─ blog-archive.html                   # Blog archive index page
├─ blog-category.html                  # Blog category index page
├─ blog-post.html                      # Blog post page
└─ main.html                           # Default page
```

为了覆盖内容，我们可以用 `overrides` 目录中同名且位于同一位置的文件替换它。例如，要替换原始的 `footer.html` 部分内容，请在 `overrides` 目录中创建一个新的 `footer.html` 部分内容。

## 常用插件

### 文档日期

[git-revision-date-localized](https://github.com/timvink/mkdocs-git-revision-date-localized-plugin) 插件增加了在每个页面底部添加上次更新和创建文档的日期的支持。使用 pip 安装它：

```base
pip install mkdocs-git-revision-date-localized-plugin
```

将以下内容添加到 `mkdocs.yml`:

```yaml
plugins:
  - git-revision-date-localized:
      enabled: !ENV [CI, false] # 在构建时是否启用此插件
      type: datetime  # 显示日期的格式
      timezone: Asia/Shanghai # 时区
      locale: zh
      fallback_to_build_date: false # 是否启用回退到执行 mkdocs build 时间
      enable_creation_date: true # 是否启用在页面底部的最后更新日期旁边显示与页面管理的文件的创建日期
      exclude:  # 不显示文档日期的文件
        - index.md
      enabled: false
```

### 文档贡献者

[git-committers^2^](https://github.com/ojacques/mkdocs-git-committers-plugin-2) 插件会呈现所有贡献者的 GitHub 头像，并在每个页面的底部链接到他们的 GitHub 个人资料。与往常一样，可以使用 pip 安装：

```bash
pip install mkdocs-git-committers-plugin-2
```

将以下内容添加到 `mkdocs.yml`:

```yaml
plugins:
  - git-committers:
      enabled: !ENV [CI, false]
      repository: xjjeffery/notes
      branch: dev
      exclude:
        - index.md
```

### 时间轴

[mkdosc-changelog-plugin](https://github.com/TonyCrane/mkdocs-changelog-plugin?tab=readme-ov-file) 是由博主 [TonyCrane](https://note.tonycrane.cc/) 自己开发的一个可以显式更新日志的插件，展示效果可以到该博主的博客[更新记录](https://note.tonycrane.cc/changelog/)中欣赏。

可以通过 pypi 直接安装:

```bash
pip install mkdocs-changelog-plugin
```

在 `mkdocs.yml` 中添加以下内容即可直接使用:

```yaml
plugins:
  - changelog
```

`changelog` 从外部的 yaml 文件读取，默认在 `docs/changelog.yml` 中，可以通过 file 选项来选择其他位置：

```yaml
plugins:
  - changelog
      file: changelog.yml
```

在需要插入 `changelog` 的页面 `meta` 部分中添加：

```markdown
---
changelog: True
---
```

在页面需要插入对应部分的位置添加:

```markdown
{{ placeholder }}
```

!!! example "changelog.yml 格式"

    ```yaml
    - "placeholder1":
      - "time1":
        - "type": text
        - "type": text
    - "placeholder":
      - "time2":
        - "type":
            text: text
            href: /link/to/page/
        - "type":
            text: text
            href: /link/to/page/
      - "time3":
        - "type": text
    ```

    - `placeholder` 是在 md 文件插入位置写入 `{{ }}` 的内容
    - `time` 是时间标题
    - `type` 是更改类型
        - 插件内自带三种：
            - `newpage`：新建页面
            - `pageupdate`：页面更新
            - `function`：功能性更新
        - 可以自定义
            - 插入 custom css 即可，例如自定义 refactor type：
                ```css
                .changelog-type-refactor {
                    background-color: #c63f94b0;
                }

                .changelog-type-refactor::before {
                    content: "文档重构";
                }
                ```
            - 如果没有对应 css，则显示为蓝色的“更新”
    - `type` 后可以直接写文本，会直接写在更新类型后面（不支持 markdown，但可以 html）
    - `type` 后也可以按如上写 text 和 href，此时会给 text 加上 href 指定的 link（利用 a 元素）

### statistics

一个用于 mkdocs 文档统计的插件，包括全局页面数、字数、代码块行数，单页字数、代码行数、图片张数以及预计阅读时间等。此插件也是由博主 [TonyCrane](https://note.tonycrane.cc/) 自己开发，本人觉得很好用，就拿来使用，大家可以直接去 [mkdocs-statistics-plugin](https://github.com/TonyCrane/mkdocs-statistics-plugin/tree/master) 仓库详细了解。

直接通过 pypi 直接安装：

```bash
pip install mkdocs-statistics-plugin
```

在 `mkdocs.yml` 中启用插件：

```yaml
plugins:
  - statistics
```

配置选项及解释：

| 选项                   | 类型 | 默认值                 | 解释                                                                 |
| :--------------------- | :--- | :--------------------- | :------------------------------------------------------------------- |
| `pages_placeholder`    | str  | `\{\{\s*pages\s*\}\}`  | 全局统计页面中页面数占位符（正则）                                   |
| `words_placeholder`    | str  | `\{\{\s*words\s*\}\}`  | 全局统计页面中字数占位符（正则）                                     |
| `codes_placeholder`    | str  | `\{\{\s*codes\s*\}\}`  | 全局统计页面中代码行数占位符（正则）                                 |
| `images_placeholder`   | str  | `\{\{\s*images\s*\}\}` | 全局统计页面中图片张数占位符（正则）                                 |
| `page_statistics`      | bool | `True`                 | 是否在单页中显示统计信息                                             |
| `page_check_metadata`  | str  |                        | 如果为空，则所有页面都显示；否则包含指定 metadata 才显示单页统计信息 |
| `page_read_time`       | bool | `True`                 | 是否显示单页预计阅读时间                                             |
| `page_images`          | bool | `True`                 | 是否显示单页图片张数                                                 |
| `page_template`        | str  |                        | 单页统计信息模板相对路径（相对 docs）                                |
| `words_per_minute`     | int  | `300`                  | 每分钟预计阅读字数                                                   |
| `codelines_per_minute` | int  | `80`                   | 每分钟预计阅读代码行数                                               |
| `ignore_languages`     | list | `["mermaid", "math"]`  | 不计入代码行数的语言列表                                             |
| `include_path`         | str  | ` `                    | 只统计匹配的路径（正则，路径相对 docs，为空则不启用）                |
| `exclude_path`         | str  | ` `                    | 不统计匹配的路径（正则，路径相对 docs，为空则不启用）                |

#### 全局统计页

例如在首页显示全局统计信息。需要在该页面的元数据中添加：

```yaml
---
statistics: True
---
```

然后在该页中需要的部分添加占位符，例如：

```markdown
本站共有 {{ pages }} 个页面，{{ words }} 个字，{{ codes }} 行代码，{{ images }} 张图片。
```

#### 单页统计

需要按照上述选项填写好配置（默认开启单页统计）。如果 `page_check_metadata` 为空，则所有页面都显示单页统计信息；否则包含指定 metadata 才显示单页统计信息。例如在我自己的设置中，包含 `comment` 的页面才显示单页统计信息（且包含 `nostatistics` 的页面不显示统计信息）：

```yaml
plugins:
  - statistics:
      page_check_metadata: counter
```

#### 自定义单页统计模板

可以通过 `page_template` 选项指定单页统计模板的相对路径（相对 docs）。这个模板会被插入到 markdown 源码的一级标题下方，会传入 `words`、`code_lines`、`images`、`page_images`、`read_time`、`page_read_time` 六个模板参数。

自定义的话可以参考提供的模板。

#### 阅读时间

可以通过 `page_read_time` 选项控制是否显示单页预计阅读时间。默认开启。可以通过 `page_images` 选项控制是否显示单页图片张数。默认开启。

阅读时间的计算方式是分别计算字数和代码行数的阅读时间，然后取二者之和，图片不计入阅读时间。可以通过 `words_per_minute` 和 `codelines_per_minute` 选项分别设置每分钟预计阅读字数和代码行数。默认情况下分别为 300 和 80，对于技术类文章这样的设置基本合理，对于其他类型例如文学类文章每分钟阅读字数应该提高到 400~600 左右较为合理。

#### 字数统计细节

本插件的字数统计细节为：一个英文单词（包括数字）算一个字，一个中文汉字算一个字，标点都不算字；代码块（带语言的篱笆型语法）中所有内容都不计入字数，而是计入代码块行数统计，可以通过设置 `ignore_languages` 选项来指定不计入代码行数的语言列表（默认不计 `mermaid` 和 `math`），自定义配置写法如下：

```yaml
plugins:
  - statistics:
      ignore_languages:
        - mermaid
        - markdown
```
