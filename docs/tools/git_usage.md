---
comments: true
counter: true
tags:
  - tools
---

# Git 用法

## Git 配置

### 配置文件

Git 提供了一个叫做 `git config` 的命令，用来配置或读取相应的工作环境变量，这些环境变量，决定了 Git 在各个环节的具体工作方式和行为。

Git 有三个主要的配置文件，分为三个等级，即 `system`、`global` 和 `local`，优先级为: `system` < `global` < `local`。

- `system` 是系统级别的配置，被所有用户共享，优先级最低。在 Ubuntu 系统中，`system` 级别配置文件位于 `/etc/gitconfig`，在 Windows 系统中，`system` 级别配置文件位于自己的安装目录。使用 `git config --system` 命令可对 `system` 级配置进行查询和修改。
- `global` 是全局级别的配置，被当前用户所使用。在 Ubuntu 系统中，`global` 级配置文件位于 `${XDG_CONFIG_HOME}/git/config` 或 `~/.gitconfig`（如前者不存在）。如果环境变量 `XDG_CONFIG_HOME` 未设置或为空值，则默认为 `~/.config`。在 Windows 系统中，global 级别配置文件位于 `C:\Users\<system user_name>\.gitconfig`。使用 `git config --global` 命令可对 global 级别配置进行查询和修改。
- `local` 是仅限某一个 Git 仓库的设置，优先级最高，`local` 级别配置文件的路径为 `<REPO_DIR>/.git/config`。使用 `git config --local` 命令（设置 local 级别选项时可省略 `--local`）可对 local 级别配置进行查询和修改。

我们需要配置个人的用户名称和电子邮件地址，为了在每次提交代码时记录提交者的信息，使用如下的命令：

```bash
# 使用 --global 参数选项设置全局配置的用户名称和电子邮件地址
git config --global user.name "JunXu"
git config --global user.email jeffery.xu.cn@outlook.com
```

**仓库配置查询**：仓库里的配置是上面多个配置文件的集合

```bash
$ git config --list
user.name=JunXu
user.email=jeffery.xu.cn@outlook.com
alias.co=checkout
alias.ci=commit
alias.s=status -sb
alias.st=status
alias.br=branch
alias.lg=log --graph --pretty=format:'%C(bold yellow)%h%C(reset) -%C(auto)%d%C(reset) %s %C(bold green)(%ad)%C(reset) %C(bold cyan)[%cn]' --abbrev-commit --date=short
alias.type=cat-file -t
alias.dump=cat-file -p
i18n.logoutputencoding=utf-8
i18n.commitencoding=utf-8
core.autocrlf=false
core.safecrlf=true
core.editor=nvim
http.proxy=http://127.0.0.1:7890
https.proxy=https://127.0.0.1:7890
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.symlinks=false
core.ignorecase=true
remote.origin.url=git@github.com:xjjeffery/jeffery-learn.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
```

### 设置代理

在国内 `clone` Github 上的项目可能是出现超时，此时需要设置代理才能成功将项目代码拉到本地。Git 支持 SOCKS/HTTP/HTTPS 代理协议，但与大多数终端应用程序不同，我们无法通过设置 Shell 环境变量 `HTTP_PROXY` 或 `HTTPS_PROXY` 来让 Git 使用代理。正确的方法是通过 Git 配置选项来设定，例如：

```bash
git config --global http.proxy 'socks5://127.0.0.1:7890'
git config --global https.proxy 'socks5://127.0.0.1:7890'
```

### 全局配置（推荐）

此处推荐使用的全局设置（本人常用），为方便操作，对一些常用命令设置了别名，并且规定了换行符在 `checkin/checkout` 时的转换规则。

```ini
[user]
  name = jeffery
  email = jeffery.xu.cn@outlook.com
[alias]
  co = checkout
  ci = commit
  s = status -sb
  st = status
  br = branch
  lg = log --graph --pretty=format:'%C(bold yellow)%h%C(reset) -%C(auto)%d%C(reset) %s %C(bold green)(%ad)%C(reset) %C(bold cyan)[%cn]' --abbrev-commit --date=short
  type = cat-file -t
  dump = cat-file -p
[i18n]
  logOutputEncoding = utf-8
  commitEncoding = utf-8
[core]
  autocrlf = false
  safecrlf = true
  editor = vim
; [url "https://ghproxy.net/https://github.com/"]
;   insteadOf = https://github.com/
[http]
  proxy = http://127.0.0.1:7890
[https]
  proxy = https://127.0.0.1:7890
```

对于篇幅较长或者对排版有要求的提交信息，很难在命令行直接输入，因此需使用文本编辑器进行撰写。`editor` 选项指定了用于撰写提交信息的文本编辑器，可根据喜好自行修改。如果 `editor` 选项未设定，Git 会默认打开由环境变量 `${EDITOR}` 设定的文本编辑器。

配置中的倒数 5、6 行意在使用 `ghproxy.net` 镜像站点来下载 Github 资源。对于任何以 `https://github.com/` 开头的 URL，Git 会自动将其转换成以 `https://ghproxy.net/` 开头的镜像地址。例如，`https://github.com/foo/bar.git` 会被自动转换成 `https://ghproxy.net/https://github.com/foo/bar.git`。ghproxy 会因访问量太大而暂时无法连接，也可能被一些网关屏蔽而完全无法访问，如果你所处的网络环境允许你畅通无阻地访问 Github，又或者你已使用稳定的代理来访问 Github，那么可注释掉该设定。

!!! info

    Git 配置文件使用 `;` 或 `#` 作为注释起始字符。通常，`#` 用于描述性文字，`;` 用于取消选项设定。

    如果需要在 Ubuntu 系统的自带 Terminal 中显示 git 分支信息，可以在 `.bashrc` 文件中添加如下内容：

    ```bash
    parse_git_branch() {
      git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1) /'
    }
    export PS1="$PS1\[\e[91m\]\$(parse_git_branch)\[\e[00m\]\n> "
    ```

## 基本概念

使用 Git 版本管理项目，都要先在项目中初始化一个 Git 仓库，使用命令 `git init`。执行完成 `git init` 命令后，Git 仓库会生成一个 `.git` 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变。或者直接通过 `git clone <remote-repo>` 的方式将一个现有 Git 仓库拷贝到本地中。

在 Git 管理的项目中有以下概念需要提前理解：

- **本地工作区（workspace）**：宿主机中的某一个由 Git 管理的项目目录
- **暂存区（stage 或 index）**：用于临时记录已追踪文件/目录的状态变化和新文件/目录的添加，实际上只是一个文件（`.git/index`），保存待提交的文件列表信息（暂存区保存了将被包括在下一个提交中的更改），所以我们把暂存区有时也叫作索引
- **本地仓库（repository）**：工作区有一个隐藏目录 `.git`，管理本地项目版本的数据库，记录文件/目录状态的地方，包含项目的所有版本历史记录，可以进行提交、回退等操作
- **远程仓库（remote repository）**：服务端的版本仓库，为多人共享提供服务，承担中心服务器的角色，通过 `git push` 指令将本地版本库推送到服务端版本库，简单理解为在一个公共服务器上的仓库
- **分支（branch）**：分支类似一个指针，指向分出时的提交记录。并且可以独立操作而与原分支互不干扰，仓库初始化以后，一般默认主分支为 `master`
- **头（HEAD）**：`HEAD` 类似一个指针，指向仓库当前活动分支的当前所在提交记录（可以在本地提交记录中任意切换）
- **版本号**：是由 40 位 sha1 哈希表示，表示某一次的提交记录
- **标签（tags）**：是某一个版本号的别名，一旦创建标签，就不能改变这个标签的标记版本，只能进行删除操作

### 工作区、暂存区和仓库之间的关系

![](../assets/tools/git/getting_started_1.png)

如上图所示，当对工作区修改（或新增）的文件执行 `git add <files>` 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的 ID 被记录在暂存区的文件索引中。当执行 `git commit` 时，暂存区的目录树写到版本库（对象库）中，`master` 分支会做相应的更新，即 `master` 指向的目录树就是提交时暂存区的目录树，同时也更新 `HEAD` 的指向为最新的提交记录。

简单的理解：在工作区中对项目文件或目录进行增删改操作，将这些操作临时放入到暂存区进行管理，可以方式修改操作的失误。再确认修改操作正确后，将暂存临时存放的操作提交到仓库中进行管理，如此就可以在历史提交记录中查看到这一个操作。也可以从历史提交记录中，将某一个提交记录逐步回退到暂存区、工作区。

![](../assets/tools/git/getting_started_2.png)

如上图，为对应本地仓库目录的结构关系：

- `myblogs` 为项目目录，也就是 Git 工作区；
- 项目根目录下隐藏的 `.git` 目录就是 Git 仓库目录了，存放了所有 Git 管理的信息；
- `.git/config` 为该仓库的配置文件，可通过指令修改或直接修改；
- `index` 文件就是存放的是暂存区内容。

## 工作流程

Git 管理项目的工作流程有几个核心的步骤，只有熟悉这些步骤，才能避免在多人协作开始的项目中避免出错：

1. 准备 Git 仓库（`git init` 或 `git clone`）：手动创建或从服务端克隆一个仓库；
2. 编辑：在项目目录中进行添加、修改、删除等操作；
3. 暂存（`git add`）：将需要进行版本管理的文件添加到暂存区；
4. ==拉取更新（`git fetch/git pull`）：如果是多人协作开发，一定要在每次提交之前都要拉取更新，确保在提交之前当前的提交节点是最新的==；
5. 提交（`git commit`）：将暂存区的文件提交到 Git 仓库，可能会出现冲突，此时需要解决冲突；
6. 合并（`git merge`）：解决完冲突后直接合并，此时会有一个新的提交记录产生；
7. 推送（`git push`）：将本地仓库推送到远程仓库，同步本地仓库的提交记录。

大致的流程如下图所示：

![](../assets/tools/git/getting_started_3.png)

## `.gitignore`

项目目录中的文件并不是全都需要纳入版本管理，如日志、临时文件、私有配置文件等不需要也不能纳入版本管理，那该怎么办呢？在工作区根目录下创建 `.gitignore` 文件，文件中配置不需要进行版本管理的文件、文件夹。 `.gitignore` 文件本身是被纳入版本管理的，可以共享。有如下规则：

- `#` 符号开头为注释。
- 可以使用 Linux 通配符。
    - 星号（`*`）代表任意多个字符，
    - 问号（`?`）代表一个字符，
    - 方括号（`[abc]`）代表可选字符范围，
    - 大括号（`{string1,string2,...}`）代表可选的字符串等。
- 感叹号（`!`）开头：表示例外规则，将不被忽略。
- 路径分隔符（`/f`）开头：`,` 表示要忽略根目录下的文件f。
- 路径分隔符（`f/`）结尾：`,` 表示要忽略文件夹f下面的所有文件。

```bash
# 为注释
*.txt # 忽略所有“.txt”结尾的文件
!lib.txt # lib.txt除外
/temp # 仅忽略项目根目录下的temp文件,不包括其它目录下的temp，如不包括“src/temp”
build/ # 忽略build/目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

!!! note

    各种语言项目的常用 .gitignore 文件配置：[https://github.com/github/gitignore](https://github.com/github/gitignore)。

## 仓库创建

在对项目进行 Git 管理前，首先在项目目录中创建 Git 仓库，创建本地仓库的方式有两种：

- 通过 `git init` 在本地创建一个全新的仓库；

```bash
# 进入到项目目录中，如 learn_git 目录
cd /tmp/learn_git
git init
# 会出现 Initialized empty Git repository in /tmp/learn_git/.git/ 表示创建成功
```

创建完成以后，就能在项目目录下看到被隐藏 `.git` 目录，此目录就是一个完整的 Git 仓库。

![](../assets/tools/git/common_operator_1.png)

- 通过克隆远程仓库在本地添加一个仓库。

```bash
# 进入到需要保存项目目录的父级目录中，如 /tmp 目录
$ git clone git@github.com:xjjeffery/learn_git.git
Cloning into 'learn_git'...
remote: Enumerating objects: 23, done.
remote: Counting objects: 100% (23/23), done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 23 (delta 3), reused 23 (delta 3), pack-reused 0
Receiving objects: 100% (23/23), 16.94 KiB | 3.39 MiB/s, done.
Resolving deltas: 100% (3/3), done.
```

克隆完成后会在当前目录下创建一个 `learn_git` 目录。

!!! warning

    Git 指令的执行，都需要在 Git 管理的项目目录下。

## 状态查看及比较

刚进入一个项目或帮助别人等，并不一定知道此时仓库中的状态，如果想要知道此时仓库的状态，需要使用 `git status` 命令来显示变化文件的状态。一般有四种状态：

- untracked：是指新添加的文件还没有加入到 Git 仓库进行跟踪
- modified：是指已跟踪的文件发生了变化，需要确认变化并提交
- staged：是指将未跟踪或发生变化的文件放入到暂存区进行跟踪
- committed：将暂存区跟踪的内容提交到了仓库中

上面四个状态中，在任何一个状态下，都有可能出现忘记当前文件与上一个提交之间的变化，此时如何才能知道这两个之间的变化 —— 使用 `git diff` 命令来输出两者之间的变化。

| **指令** | **描述** |
| --- | --- |
| `git diff` | 查看当前工作区文件的变化与暂存区的区别，如果暂存区没有临时跟踪的文件，则与最新的提交记录 `HEAD` 进行比较 |
| `git diff <file>` | 同上，此处指定了比较的文件 |
| `git diff --cached` | 查看已暂存的文件变化与最新的提交记录 `HEAD` 的区别， |
| `git diff --staged` | 同上 |
| `git diff --cached <file>` | 同上，此处指定了比较的文件 |
| `git diff <commit-id>` | 查看当前工作区文件的变化与指定提交记录之间的区别，也可以用 `HEAD` 来表示 |
| `git diff <id> <id>` | 查看两次提交之间的差异，后一个 id 相对于前一个 id 的修改 |
| `git diff <branch>` | 查看工作区和指定分支之间的区别 |

通过下图能够更加清晰的了解具体比较情况

![](../assets/tools/git/common_operator_2.png)

```bash
# 查看文件修改
git diff README.md

# 查看两次提交的差异
git diff 2a96dd6 f6fe775
```

## 暂存文件

可以简单的理解，`git add` 就是将当前项目目录下已经变更以及未跟踪的文件添加到暂存区进行跟踪，然后可以通过 `git commit` 将暂存区的跟踪提交到本地仓库中。

| **指令** | **描述** |
| --- | --- |
| `git add <file>` | 将指定的文件添加到暂存区进行版本管理 |
| `git add <dir>` | 同上，添加目录到暂存区，包括子目录 |
| `git add .` | 同上，添加所有已修改、新添加的文件到暂存区 |
| `git rm <file>` | 删除工作区已跟踪的文件，并且将删除记录放入暂存区 |

示例：

- 修改文件 `test.c`，未暂存

![](../assets/tools/git/common_operator_3.png)

- 执行 `git add .` 暂存

![](../assets/tools/git/common_operator_4.png)

## 提交

### 概述

`git commit` 提交是以时间顺序排列保存到数据库中的，就如游戏关卡一样，每次提交就会产生一条记录：`id + 描述 + 快照内容`：

- `commit id`：根据修改的文件内容采用摘要算法（SHA1）计算出不重复的 40 位字符，这么长是因为 Git 是分布式的，要保证唯一性、完整性，一般本地指令中可以只用前几位(如前7位)；
- 描述：针对本次提交的描述说明，建议准确描述，就跟代码中的注释一样，非常重要；
- 快照：就是完整的版本文件，以对象树的结构存在仓库下 `./.git/objects` 目录里，这也是 Git 高效的秘诀之一。

??? tip

    - SHA1 是一种哈希算法，可以用来生成数据摘要
    - Git 不适合大的非文本文件，会影响计算摘要、快照功能

多个提交就形成一条时间线，每次提交完成，会移动当上 `HEAD`的指针位置。

![](../assets/tools/git/common_operator_5.png)

???+ tip

    一般情况下，每个完成一个功能或一个 bug 就可以提交一次，这样就会形成比较清晰的历史记录。

### 提交指令

| **指令** | **描述** |
| --- | --- |
| `git commit [--allow-empty] -m <description>` | 提交变更，参数 `-m` 设置提交的描述信息，不带参数会进入编辑模式，在默认的编辑模式中编辑提交的描述信息；`--allow-empty` 表示即使没有变更，也允许提交，可以用来生成一个简单的提交记录 |
| `git commit -a` | 参数 `-a`，直接从工作区提交到版本库中，略过 `git add` 步骤（只会提交已经提交过的文件修改记录，新添加的文件不会提交） |
| `git commit <file>` | 提交暂存区的指定文件到仓库中 |
| `git commit --amend` | 在最近一次的提交基础上，再一次提交，会修改 `commit` 的 `hash` 值(id) |

![](../assets/tools/git/common_operator_6.png)

```bash
# 提交暂存区到仓库区
$ git commit -m [message]
# 提交所有修改到仓库
$ git commit -a -m '修改README的版权信息'

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]
```

???+ note "提交空目录"

    在对一个目录进行提交时，是不允许提交一个空目录，一般在此目录下加一个 `.gitkeep` 文件当做占位符，保证原本的空目录能够成功提交。

### 提交消息规范

提交时都应增加一个描述消息，为了在日后开发中查看 log 更加的清晰，建议按照以下的格式书写：

```bash
<category>(module): one-line short description in form of "do something"

Additional long description for the commit...
```

第一行是概述行，便于 `git log --oneline` 以单行形式查看提交历史，应力求简明扼要，点出关键。

`<category>` 用于对提交分类，常见的类型有：

- `chore`：琐碎的小修改
- `docs`：文档更新
- `feat`：添加特性
- `fix`：修复 bug
- `perf`：性能改进
- `refactor`：重构
- `style`：规范代码风格
- `test`：测试

`(module)` 是可选项，用于指示提交是否特定于某个功能模块。

如有必要，使用第二段对提交内容作更为详尽的补充说明，例如 bug 产生的原因和解决办法、新增功能的详细描述等。

??? notes

    概述行句末不应该添加标点符号

以下是一些示例：

- 解决了避障模块中的一个 bug，在第二段中作补充说明

```bash
fix(oa): remove joy subscription in obstcale avoidance node

Both brain node and oa node subscribing to the joy topic randomly mess up robot
status due to callback out-of-sync invocations between multiple processes. The
fix removes the joy topic subscription in the oa node and has brain node order
oa node to respond to joy events.
```

- 规范化源码风格，不针对某一个功能模块：

```bash
style: normalize indentation to 2 spaces for cpp code
```

- 修改文档中的拼写错误或错别字：

```bash
chore(docs): typo
```

## “指针”引用

### 基本介绍

Git 中最重要的就是提交记录，其它如标签、分支、`HEAD` 都是对提交记录的“指针”引用，指向这些提交记录，理解这一点很重要：

- 提交记录之间也存在 “指针” 引用，每个提交会指向其上一个提交
- 标签就是对某一个提交记录的固定 “指针” 引用，取一个别名更容易记忆一些关键节点，存储在工作区根目录下 `.git\refs\tags`
- 分支也是指向某一个提交记录的 “指针” 引用，“指针” 位置可变，如提交、更新、回滚，存储在工作区根目录下 `.git\refs\heads`
- `HEAD`：指向当前活动分支（最新提交）的一个 “指针” 引用，存在 `“.git/HEAD”` 文件中，存储的内容为 `ref: refs/heads/master`

![](../assets/tools/git/common_operator_7.png)

上图中：

- `HEAD` 始终指向当前活动分支，多个分支只能有一个处于活动状态。
- 标签 `t1` 在某一个提交上创建后，就不会变了。而分支、`HEAD` 的位置会改变。

打开这些文件内容看看，就更容易理解这些 “指针” 的真面目了。

```bash
# tag
$ git tag -a 'v1' -m 'v1版本'
$ cat .git/refs/tags/v1
a2e2c9caea35e176cf61e96ad9d5a929cfb82461

# main分支指向最新的提交
$ cat .git/refs/heads/main
8f4244550c2b6c23a543b741c362b13768442090

# HEAD指向当前活动分支
$ cat .git/HEAD
ref: refs/heads/main

# 切换到dev分支，HEAD指向了dev
$ git switch dev
Switched to branch 'dev'
$ cat .git/HEAD
ref: refs/heads/dev
```

这里的主分支名字为 `main`，是因为该仓库是从 Github 上克隆的，Github 上创建的仓库默认主分支名字就是 `main`，本地创建的仓库默认主分支名字为 `master`。

???+ tip

    “指针” 引用：之所以用引号的 “指针”，是为了便于统一和理解。和指针原理类似，都是一个指向，只是实际上可能更复杂一点，且不同的 “指针” 引用会有区别。

### 理解 `HEAD`

每一个提交都有一个唯一标识，主要就是提交的 hash 值 commit id，在很多指令中会用到，如版本回退、拣选提交等，需要指定一个提交。`HEAD` 是一个活动的指针，用来指向当前所在分支的某一个 commit id。例如当前在 `dev` 分支，`HEAD` 内容就是 `ref:refs/heads/develop`。

`HEAD` 既可以指向「当前分支」的最新 commit，也可以指向历史中的某一次 commit (「分离头指针」的情况)。归根结底，`HEAD` 指向的就是某个提交点。当我们做分支切换时，`HEAD` 会跟着切换到对应分支。

在历史回退的时候，可能会用 `HEAD` 来表示历史的某一个提交版本，表示方式有：

- `HEAD` 表示当前分支的最新版本，是比较常用的参数。
- `HEAD^`上一个版本，`HEAD^^` 上上一个版本。
- `HEAD~` 或 `HEAD~1` 表示上一个版本，以此类推，`HEAD~10` 为最近第10个版本。
- `HEAD@{2}` 在 `git reflog`日志中标记的提交记录索引。

通过`git log`、`git reflog`可以查看历史日志，可以看每次提交的唯一编号（hash）。区别是 `git reflog` 可以查看所有操作的记录（实际是 `HEAD` 变更记录），包括被撤销回退的提交记录。

```bash
$ git reflog -n10
5acc914 (HEAD -> main) HEAD@{0}: reset: moving to HEAD~
738748b (dev) HEAD@{1}: reset: moving to HEAD~
9312c3e HEAD@{2}: reset: moving to HEAD~
db03fcb HEAD@{3}: reset: moving to HEAD~
1b81fb3 HEAD@{4}: reset: moving to HEAD~
41ea423 HEAD@{5}: reset: moving to HEAD~
d3e15f9 HEAD@{6}: reset: moving to d3e15f9
1b81fb3 HEAD@{7}: reset: moving to HEAD~1
41ea423 HEAD@{8}: reset: moving to HEAD~
d3e15f9 HEAD@{9}: reset: moving to HEAD~
```

## 分支的使用

!!! tip "为什么要用分支"

    分支是从某一个提交节点分离出去的“副本”，分支可以独立开发、独立提交、回滚等操作，也可以和其它分支合并。分支就是指向某一个提交记录的“指针”引用，因此对分支的操作非常的快，不管仓库多大。创建一个分支，实际上就是在 `.git/refs/heads` 下创建一个分支名的引用文件（没有扩展名）。

比如你们项目团队准备 10 月份发布新版本，要新开发一堆黑科技功能，占领市场。你和你的小伙伴（小咪）一起负责开发一个新的功能 A，开发周期为 2 周，这两周你们的代码不能影响其他人（不影响主分支）。这个时候可以为这个新功能创建一个分支，你们两个在这个分支上干活，2 周后代码开发完成、测试通过，就看将此分支合并到主分支上。安全、高效，不影响其他人工作，简直是完美。

![](../assets/tools/git/branch_1.png)

在实际项目中，一般会有以下几个分支：

- **`master`**：永远保持稳定和可发布的状态，每次发布一个新的版本时，都会从 `dev` 分支合并到 `master` 分支，不允许随便修改和合并
- **`dev`**：作为团队开发的主分支，代表了最新的开发进度，功能分支、发布分支和修复分支都从这里分支出去，最终合并回这里
- **`feature`**：开发人员根据自己的功能模块，创建一些临时分支用于特定功能的开发，开发完毕后在合并到主开发分支，并删除该分支，命名规范：`feature/feature-name`
- **`release`**：从 `dev` 分支创建，进行最后的测试和修复，然后合并回 `dev` 和 `master` 分支，并打上版本标签，命名规范：`release/release-name`
- **`hotfix`**：从 `master` 分支创建，用于修复紧急问题，修复完成后合并回 `master` 和 `develop` 分支，并打上版本标签，命名规范：`hotfix/hotfix-name`

![](../assets/tools/git/branch_2.png)

## 操作命令

| 指令 | 描述 |
| --- | --- |
| `git branch` | 列出所有本地分支，加参数 `-v` 显示详细列表，下同 |
| `git branch -r` | 列出所有远程分支 |
| `git branch -a` | 列出所有本地分支和远程分支，用不同颜色区分 |
| `git branch <branch>` | 新建一个分支，但依然停留在当前分支 |
| `git branch -d dev` | 删除 `dev` 分支，`-D`（大写）强制删除 |
| `git branch -m <newname>` | 为当前分支重命名为 newnname |
| `git checkout -b dev` | 从当前分支创建并切换到 `dev` 分支 |
| `git checkout -b feature1 dev` | 从本地 `dev` 分支代码创建一个 `feature1` 分支，并切换到新分支 |
| `git branch <branch> <commit>` | 新建一个分支，指向指定 `commit id` |
| `git branch --track <branch> <remote-branch>` | 新建一个分支，与指定的远程分支建立关联，使用的前提是之前已经添加过远程仓库，并且对该仓库执行过 `pull` 和 `fetch` 操作 |
| `git checkout -b <branch> <remote-branch>` | 基于远程分支新建本地分支，使用的前提是之前已经添加过远程仓库并已经将远程分支 `pull` 下来，并且对该仓库执行过 `pull` 和 `fetch` 操作 |
| `git branch --set-upstream=<remote-branch> <branch>` | 将现有分支与指定的远程分支之间建立跟踪关联 |
| `git checkout <branch-name>` | 切换到指定分支，并更新工作区 |
| `git checkout .` | 撤销工作区的（未暂存）修改，把暂存区恢复到工作区。 |
| `git checkout HEAD .` | 撤销工作区、暂存区的修改，用 HEAD 指向的当前分支最新版本替换 |
| `git switch master` | 切换到已有的 `master` 分支 |
| `git switch -c <branch_name>` | 创建并切换到新的 `branch_name` 分支 |

!!! note

    - 关于 `checkout` 指令：`checkout` 是 Git 的底层指令，比较常用，也比较危险，他会重写工作区。支持的功能比较多，能撤销修改，能切换分支，这也导致了这个指令比较复杂。在 Git 2.23 版本以后，增加了 `git switch`、`git reset` 指令，对于分支的切换和版本回退更加推荐使用这两个命令。
    - `git switch`：专门用来实现分支切换，创建等操作。
    - `git reset`：专门用来实现版本回退以及撤销修改等操作。

```bash
$ git branch -a
* main
# 列出了当前的所有分支，星号“*”开头的“main”为当前活动分支
```
!!! question "切换分支时，修改操作未提交改怎么办"

    - 如果修改（包括未暂存、已暂存）和待切换的分支没有冲突，则切换成功，且未提交修改会一起带过去，所以要注意。
    - 如果有冲突，切换失败，会提示先 `commit` 或 `stash`。

## `stash`

当你正在 `dev` 分支开发一个功能时，代码写了一半，突然有一个线上的 bug 急需要马上修改。`dev` 分支 bug 没写完，不方便提交，就不能切换到主分支去修复线上 bug。Git 提供一个 `stash` 功能，可以把当前工作区、暂存区未提交的内容“隐藏”起来，就像什么都没发生一样。

```bash
# 有未提交修改，切换分支时报错
$ git switch dev
error: Your local changes to the following files would be overwritten by checkout:
        README.md
Please commit your changes or stash them before you switch branches.
Aborting

# 隐藏
$ git stash
Saved working directory and index state WIP on main: 2bc012c s

# 查看被隐藏的内容
$ git stash list
stash@{0}: WIP on main: 2bc012c s

# 比较一下，什么都没有，一切都没有发生过！
$ git diff

# 去其他分支修改bug，修复完成回到当前分支，恢复工作区
$ git stash pop
```

在上面示例中，有未提交修改，切换分支时报错。错误提示信息很明确了，`commit` 提交或 `stash` 隐藏：`Please commit your changes or stash them before you switch branches.`

!!! warning "注意"

    如果切换分支时，未提交修改的内容没有冲突，是可以成功切换的，未提交修改会被带过去。

| **指令** | **描述** |
| --- | --- |
| `git stash` | 把未提交内容隐藏起来，包括未暂存、已暂存。等以后恢复现场后继续工作 |
| `git stash list` | 查看所有被隐藏的内容列表 |
| `git stash pop` | 恢复被隐藏的内容，同时删除隐藏记录 |
| `git stash save "message"` | 同 `git stash`，可以备注说明 message |
| `git stash apply` | 恢复被隐藏的文件，但是隐藏记录不删除 |
| `git stash drop` | 删除隐藏记录 |

!!! warning

    当然这里先提交到本地也是可以的，只是提交不是一个完整的功能代码，而是残缺的一部分，影响也不大。

## 分支合并

Git 中的分支合并是一个常见的使用场景，如：

- 仓库的 `hotfix` 分支修复完 bug 之后，要汇合到主干分支，这个时候两个分支就需要合并处理
- 远端仓库的分支 A 有其他小伙伴合入了代码，这时候，我们需要和远端仓库的分支 A 进行合并

以上只是列举了分支合并的一些常见场景，关于合并的常用指令是 `merge` 和 `rebase`，这两个指令的使用场景会有所不同。

### `fast-forward` 和 `--no-ff` 的区别

假如有一个场景：有两个分支，`master` 分支和 `feature` 分支。现在，`feautre` 分支需要合并回 `master` 分支。

![](../assets/tools/git/branch_3.png)

`fast-forward` 合并方式是条件允许的情况，通过将 `master` 分支的 `HEAD` 位置移动到 `feature` 分支的最新提交点上，这样就实现了快速合并。这种情况，是不会新生成 commit 的。

![](../assets/tools/git/branch_4.png)

`--no-ff` 的方式进行合并，master 分支就会新生成一次提交记录。

![](../assets/tools/git/branch_5.png)

!!! tip

    如果提交满足时，`merge` 默认采用 `fast-forward` 方式进行合并，除非显示的加上 `--no-ff` 选项；而条件不满足时，`merge` 也是无法使用 `fast-forward` 合并成功。

### `merge` 操作

上面用图解的方式介绍了 `fast-forward` 和 `--no-ff` 的区别。下面，结合实际的代码仓进行合并操作，举几个例子理解一下。

!!! note

    `git merge` 操作是区分上下文的，当前分支始终是目标分支，其他一个或多个分支始终合并到当前分支，需要先切到目标分支上。

#### `fast-forward` 合并

刚刚一直在强调条件允许的时候，`fast-forward` 才能合并成功。条件指的是什么呢 —— 其实指的是源分支和目标分支之间没有分叉（diverge），这种情况才可以进行快速合并。如果是下图中的场景，无法通过 `HEAD` 的快速移动实现分支的合并。

![](../assets/tools/git/branch_6.png)

下面进行一个不分叉的场景的示例：

![](../assets/tools/git/branch_7.png)

现在需要将 `feature` 分支合入到 `master` 分支，默认使用 `fast-forward` 方式：

```bash
# 切到目标分支
git switch master
git merge feature
```

命令行里显示了 `Fast-forward` 的提示：

![](../assets/tools/git/branch_8.png)

看一眼 `master` 分支合入的前后对比（注意 `HEAD` 的位置）：

![](../assets/tools/git/branch_9.png)

#### `--no-ff` 合并

不分叉的场景是否可以强制采用 `--no-ff` 方式合并 —— 可以。

```bash
# master 回到合入前的状态
git reset --hard d2fa1ae
git merge feature --no-ff
```

![](../assets/tools/git/branch_10.png)

这次命令行没有 `Fast-forward` 的提示了。看一眼 `master` 分支图：

![](../assets/tools/git/branch_11.png)

这个图和上面讲解 `--no-ff` 命令时的示意图一致，果然会有新 commit 生成。

#### 分叉场景合并

![](../assets/tools/git/branch_12.png)

上面的图展示了我们经常遇到的一个场景，`feature` 分支创建之后，源分支也会有新的提交。这就是形成分叉了。这时候如果我们进行合并呢？

```bash
git merge feautre
```

![](../assets/tools/git/branch_13.png)

可以看到，虽然默认会尝试 `fast-forward` 的方式进行合并，但是因为分叉了，所以此时会采用 `--no-ff` 的方式进行合并，有新的 commit 生成了。

!!! info

    `fast-forward` 方式对应的合并参数是 `--ff`

我们试试这个参数 `--ff-only`，顾名思义，就是强制只使用 `--ff` 方式进行合并：

```bash
# 回到合并前
git reset --hard 3793081
git merge feature --ff-only
```

![](../assets/tools/git/branch_14.png)

经过测试，当分叉时，因为无法使用 `--ff` 方式合并，即使你强制指定使用该方式合并也不行，会提示终止。附上 Git 官方文档中的解释，方便理解：

```txt
With --ff, when possible resolve the merge as a fast-forward (only update the branch pointer to match the merged branch; do not create a merge commit). When not possible (when the merged-in history is not a descendant of the current history), create a merge commit.
```

### `rebase` 操作

`rebase` 命令是一个经常听到，但是大多数人掌握又不太好的一个命令。`rebase` 合并往往又被称为「变基」，「基」的理解很重要，理解了它，其实 `rebase` 命令你就掌握了。

我的描述可能并不准确，只是为了能够帮助你理解。==「变基」就是改变当前分支的起点==（在某一个提交记录创建此分支，这个提交记录就是此分支的起点）。注意，是当前分支，`rebase` 命令后面紧接着的就是「基分支」。

变基前：

![](../assets/tools/git/branch_6.png)

`git reabse master` 变基后：

![](../assets/tools/git/branch_15.png)

#### 变基提交示例

我们接下来进行实际的测试，将代码库状态构造成分叉的状态，状态图如下：

![](../assets/tools/git/branch_16.png)

以 `master` 分支为基，对 `feautre` 分支进行变基：

```bash
git switch feature
git rebase master
```

以上两行命令，其实可以简写为：`git rebase master feature`

> 特性分支 `feature` 向前移植到了 `master` 分支。经常使用 `git rebase` 操作把本地开发分支移植到远端的 `origin/<branch>` 追踪分支上。也就是经常说的，「把你的补丁变基到 xxx 分支的头」

![](../assets/tools/git/branch_17.png)

可以发现，在 `master` 分支的最新节点（576cb7b）后面多了 2 个提交（生成了新的提交记录，仅仅提交信息保持一致），而这两个提交内容就是来自变基前 `feature` 分支，`feature` 分支的提交历史发生了改变。

观察上图还可以发现，变基后，改变的只是 `feature` 分支，基分支（`master` 分支）的 `HEAD` 指针依然在之前的 commit（576cb7b）处。这时候要将 `feature` 分支合入到 `master` 分支上，就满足 `fast-forward` 的条件了，`master` 分支执行快速合并，将 `HEAD` 指针指向刚刚最新合入的提交点。

```bash
git switch master
git merge feature
```

![](../assets/tools/git/branch_18.png)

看下图 `master` 分支图，观察 `HEAD` 指针的位置：

![](../assets/tools/git/branch_19.png)

!!! note

    上述的情况是在 `rebase` 后没有发生冲突的情况，如果发生冲突，还需要先解决冲突，然后再将解决后的修改提交到仓库，再次执行 `git rebase --continue` 指令完成 `rebase`，之后的操作就和上面一样了。

`rebase` 变基操作最适合的是本地分支和远端对应跟踪分支之间的合并。这样理解可能会更清晰一点。比如，远端仓库里有一个特性分支 `feature`，除了你开发之外，还有其他人往这个分支进行合入。当你每次准备提交到远端之前，其实可以尝试变基，这时候基分支就是远端的追踪分支。

下图是仓库的分支图：

![](../assets/tools/git/branch_20.png)

```bash
git fetch
git rebase origin/feature feature
```

![](../assets/tools/git/branch_21.png)

观察上图，我们本地的提交以远端分支的最新提交为「基」，将差异提交重新进行了提交！远端分支的提交记录依然是一条直线。如果分叉的情况，不采用这种「变基操作」，而直接采用 `merge` 的方式合并，就会有如下这种分支提交图：

![](../assets/tools/git/branch_22.png)

因为分叉了，采用 `git pull` 时也没法 `fast-forward` 合并，只能采用 `--no-ff` 方式合并，最后的提交历史就会像上图那样。会产生一个合并提交。同时，分支图也显得稍微杂乱了一点，因为 `feature` 分支不是一条直线了。但是，其实也有好处，可以实际的看出来合并的提交历史。该选择哪个，往往取决于团队的选择策略。

#### `rebase` 总结

`rebase` 命令其实关键在于理解「基」，`git rebase <基分支>`，就是将当前基分支与当前分支的差异提交获取到，然后在「基分支」最新提交点后面将差异提交逐个再次提交，最后将当前分支的 `HEAD` 指针指向最新的提交点。「基分支」的 `HEAD` 位置是不变的。要想完成分支合并，完成变基之后，需要再进行分支间的合并等操作。

`rebase` 命令的用法也不止于此，这里只是做一个简单的介绍进行理解。是否需要 `rebase`，一般根据公司的情况和使用场景来，更多的还是使用 `merge` 来合并分支，可以通过历史提交记录查看每个时间节点干了什么以及先后顺序。

### 冲突处理

冲突一般都是发生在分支合并时，两个分支对同一个地方进行了修改。因此，在有冲突的文件中，都会有标志性的标识来表示冲突的地方。

![](../assets/tools/git/branch_23.png)

`<<<<<<< HEAD` 开头的内容就表示是有冲突的部分，需要人工处理，可以借助一些第三方的对比工具。人工处理完毕后，完成合并提交，才最终完成此次合并。`<<<<<<<` 和 `=======` 之间的所有内容都是当前分支的修改，`=======` 和 `>>>>>>>` 之间的内容是另一个分支或远程仓库中的修改。

如下图所示，修正所有冲突的地方之后，执行提交。

![](../assets/tools/git/branch_24.png)

## 标签管理

标签（Tags）指的是某个分支某个特定时间点的状态，是对某一个提交记录的的固定“指针”引用。一经创建，不可移动，存储在工作区根目录下 `.git\refs\tags`。可以理解为某一次提交（编号）的别名，常用来标记版本。所以发布时，一般都会打一个版本标签，作为该版本的快照，指向对应提交 `commit`。

当项目达到一个关键节点，希望永远记住那个特别的提交快照，你可以使用 `git tag` 给它打上标签。比如我们今天终于完成了 `V1.1` 版本的开发、测试，并成功上线了，那就可给今天最后这个提交打一个标签 `V1.1`，便于版本管理。

默认标签是打在最新提交的 `commit` 上的，如果希望在指定的提交上打标签则带上提交编号（commit id）：`git tag v0.9 f52c633`

![](../assets/tools/git/branch_25.png)

| **指令** | **描述** |
| --- | --- |
| `git tag` | 查看标签列表 |
| `git tag -l 'a*'` | 查看名称是“a”开头的标签列表，带查询参数 |
| `git show <tagname>` | 查看标签信息 |
| `git tag <tagname>` | 创建一个标签，默认标签是打在最新提交的 commit 上的 |
| `git tag <tagname> <commit_id>` | 新建一个 tag 在指定 commit 上 |
| `git tag -a v5.1 -m'v5.1版本'` | 创建标签 v5.1.1039，`-a` 指定标签名，`-m` 指定说明文字 |
| `git tag -d <tagname>` | 删除本地标签 |
| `git checkout v5.1.1039` | 切换标签，同切换分支 |
| `git push <remote> v5.1` | 推送标签，标签不会默认随代码推送推送到服务端 |
| `git push <remote> --tags` | 提交所有 tag |

如果要推送某个标签到远程，使用命令 `git push origin [tagname]`，或者，一次性推送全部到远程：`git push origin --tags`

!!! note

    标签总是和某个 commit 挂钩。如果这个 commit 既出现在 `master` 分支，又出现在 `dev`分支，那么在这两个分支上都可以看到这个标签。

```bash
# tag
$ git tag -a 'v1' -m'v1版本'
$ cat .git/refs/tags/v1
a2e2c9caea35e176cf61e96ad9d5a929cfb82461

# 查看标签列表
$ git tag
v1
```

## `cherry-pick`

出现一个紧急 bug，并在 `dev` 上修复完，此时我们想把在 `dev` 上修复 bug 所做的修改 “复制” 到 `master` 分支，但又不想把整个 `dev` 合并过去。为了方便操作，Git 专门提供了一个 `cherry-pick` 命令，让我们将某一个特定的提交复制到当前分支，不管这个提交在哪个分支。

![](../assets/tools/git/branch_26.png)

如上图，操作过程相当于将该提交导出为补丁文件，然后在当前 `HEAD` 上重放，形成无论内容还是提交说明都一致的提交。

- 希望把 `dev` 分支上的 `v7` 提交的内容合并到 `master`，但不需要其他的内容。
- 在 `master` 分支上执行指令 `git cherry-pick v7`，会产生一个新的 `v7` 提交，内容和 `v7` 相同。
- 同时更新 `master`、`HEAD`，以及工作区。

```bash
# 选择连续的提交合入
$ git cherry-pick <start-commit-id>...<end-commit-id>  # 这种写法是左闭右开
$ git cherry-pick <start-commit-id>^...<end-commit-id> # 这种写法是左闭右闭
```

## `git log`

!!! tip "提交日志"

    查看 Git 提交历史可以帮助我们了解代码的变更情况和开发进度，Git 提供了多种命令和选项来查看提交历史，从简单的日志到详细的差异对比。除此之外，我们可以回退到任何一个指定的历史记录。

`git log` 是常用的查看 Git 仓库中的历史提交记录，其显示了从最新提交到最早提交的所有提交信息，包括提交的哈希值、作者、提交日期和提交消息等。但是单独 `git log` 显示出的历史提交记录内容较多，查看起来十分麻烦。如下所示：

```bash
$ git log
commit d5e9fc2c811e0ca2b2d28506ef7dc14171a207d9 (HEAD -> master)
Merge: c68142b 7774248
Author: runoob <test@runoob.com>
Date:   Fri May 3 15:55:58 2019 +0800

    Merge branch 'change_site'

commit c68142b562c260c3071754623b08e2657b4c6d5b
Author: runoob <test@runoob.com>
Date:   Fri May 3 15:52:12 2019 +0800

    修改代码

commit 777424832e714cf65d3be79b50a4717aea51ab69 (change_site)
Author: runoob <test@runoob.com>
Date:   Fri May 3 15:49:26 2019 +0800

    changed the runoob.php

commit c1501a244676ff55e7cccac1ecac0e18cbf6cb00
Author: runoob <test@runoob.com>
Date:   Fri May 3 15:35:32 2019 +0800

```

因此，需要添加一些选项进行设置，常用的选项如下：

- `-p`：显示提交的补丁（具体更改内容）
- `--oneline`：以简洁的一行格式显示提交信息
- `--graph`：以图形化方式显示分支和合并历史
- `--decorate`：显示分支和标签指向的提交
- `--author=<作者>`：只显示特定作者的提交
- `--since=<时间>`：只显示指定时间之后的提交
- `--until=<时间>`：只显示指定时间之前的提交
- `--grep=<模式>`：只显示包含指定模式的提交消息
- `--no-merges`：不显示合并提交
- `--stat`：显示简略统计信息，包括修改的文件和行数
- `--abbrev-commit`：使用短提交哈希值
- `--pretty=<格式>`：使用自定义的提交信息显示格式
- `--all`：显示所有分支的提交信息

使用示例如下:

```bash
$ git log --oneline --graph
*   d5e9fc2 (HEAD -> master) Merge branch 'change_site'
|\
| * 7774248 (change_site) changed the runoob.php
* | c68142b 修改代码
|/
* c1501a2 removed test.txt、add runoob.php
* 3e92c19 add test.txt
* 3b58100 第一次版本提交
```

这样我们就更清楚明了地看到何时工作分叉、又何时合并。

!!! tip

    如果按照[我的全局配置]方式进行 Git 配置，那么只需要输出简单的 `git lg --all` 就可以查看所有的历史提交记录。

    [我的全局配置]: #全局配置推荐

    ```bash
    $ git lg --all
    *   d5e9fc2 (HEAD -> master) Merge branch 'change_site'
    |\
    | * 7774248 (change_site) changed the runoob.php
    * | c68142b 修改代码
    |/
    * c1501a2 removed test.txt、add runoob.php
    * 3e92c19 add test.txt
    * 3b58100 第一次版本提交
    ```

## 恢复和回退

Git 提供了多种方式来恢复和回退到之前的版本，不同的命令适用于不同的场景和需求。有以下几种常见的方式：

- `git checkout`：切换分支或恢复文件到指定提交（能力太多，不推荐使用）
- `git restore`: 恢复文件状态的修改
- `git reset`：重置当前分支到指定提交（`--soft`、`--mixed`、`--hard`）
- `git revert`：创建一个新的提交以撤销指定提交，不改变提交历史
- `git reflog`：查看历史操作记录，找回丢失的提交

这些方式对应的场景是不同的，在指定的场景使用对应的指令即可，这样也不会出错，从而破坏仓库的提交记录信息。通常情况下有四种情况下的回退：已修改未暂存、已暂存未提交、已提交未推送到远程、已推送到远程。

![](../assets/tools/git/rollback_1.png)

### 已修改未暂存和已暂存未提交

在本地工作区进行了修改，还没有添加到暂存区中（已建立跟踪的 untracked 内容）或者将已修改的文件添加暂存区进行跟踪，都可以使用 `restore` 指令来恢复这些已修改的内容（恢复到前一次提交的内容）。

具体的操作指令如下所示：

| **指令** | **描述** |
| --- | --- |
| `git restore <file>...` | 恢复工作区中未暂存的指定内容（已建立跟踪的，新添加的文件不符合） |
| `git checkout <file>...` | 同上（不推荐） |
| `git checkout <commit-id> <file>` | 同上，但是使用指定的提交记录来覆盖工作区和暂存区的内容（不推荐） |

### 已经 `commit` 尚未 `push`

有时候，我们在本地的修改，可能提交到了本地仓库，但是尚未 `push` 到远程仓库，针对这一种场景的回滚是比较简单的，可以使用 `git reset` 命令来操作。

`reset` 是专门用来撤销修改、回退版本的指令，支持的场景比较多，多种撤销支持，所以参数组合也比较多。简单理解就是移动分支的 `HEAD` 的“指针”地址，理解这一点就基本掌握 `reset` 了。如下图：

- 回退版本 `git reset --hard v4` 或 `git reset --hard HEAD~2`，`master` 的 HEAD 会指向 `v4` 提交，`v5`、`v6` 就被废弃了。
- 也可以重新恢复到 `v6` 版本：`git reset --hard v6`，就是移动 `master`、HEAD 的“指针”地址。

![](../assets/tools/git/rollback_2.png)

`reset` 有三种模式，对应三种参数：`mixed`(默认模式)、`soft`、`hard`，理解这三个参数的区别很简单：

- `--soft`：将当前提交节点的内容恢复到暂存区，并将 `HEAD` 移动指定的提交节点位置，删除之前所有的提交记录
- `--mixed`：将当前提交节点的内容恢复到暂存区，并将 `HEAD` 移动指定的提交节点位置，删除之前所有的提交记录，此为默认模式，参数可以省略
- `--hard`：直接回退到指定提交记录位置（将 `HEAD` 移动指定的提交节点位置），删除之前所有的提交记录

回退前，用 `git log` 可以查看提交历史，以便确定要回退到哪个版本。要重返未来，可以用 `git reflog` 查看所有 `HEAD` 的移动记录，以便确定要回到未来的哪个版本。

```bash
# 撤销暂存区
$ git reset
Unstaged changes after reset:
M       R.md

# 撤销工作区、暂存区修改
$ git reset --hard HEAD

# 回退版本库到上一个版本，并重置工作区、暂存
$ git reset --hard HEAD~

# 回到原来的版本（恢复上一步的撤销操作），并重置工作区、暂存
$ git reset --hard 5f8b961

# 查看所有历史提交记录
$ git reflog
ccb9937 (HEAD -> main, origin/main, origin/HEAD) HEAD@{0}: commit: 报表新增导入功能
8f61a60 HEAD@{1}: commit: bug：修复报表导出bug
4869ff7 HEAD@{2}: commit: 用户报表模块开发
4b1028c HEAD@{3}: commit: 财务报表模块开发完成
```

### 已经 `push` 到远程仓库中

如果我们的提交已经 `push` 到远程仓库了，此时也要分两种情况，就是其他的同事有没有基于我们的提交做了修改并且已经提交到了远程仓库了，因为此时如果我们回退的话，是会把其它同事的修改也一并回退掉，同时也把 git 的历史记录删掉了，这并不是我们所期望的。

如果没有其它同事基于我们的 `commit` 做修改的话，使用 `git revert` 的方法是比较好的，可能也有人认为为什么不能使用 `git reset` 呢？因为 `git reset` 会删掉 `commit` 记录，所以并不是一种良好的做法。

安全的撤销某一个提交记录，基本原理就是生产一个新的提交，用原提交的逆向操作来完成撤销操作。注意，这不同于 `reset`，`reset` 是回退提交记录，`revert` 只是用于撤销某一次历史提交，操作是比较安全的。

![](../assets/tools/git/rollback_3.png)

如上图：

- 想撤销 `v4` 的修改，执行 `git revert v4`，会产生一个新的提交 `v-4`，是 `v4` 的逆向操作
- 同时更新 `maser`、`HEAD`“指针”位置，以及工作区内容
- 如果已 `push` 则重新 `push` 即可

```bash
# revert撤销指定的提交，“-m”附加说明
$ git revert 41ea42 -m '撤销对***的修改'
[main 967560f] Revert "123"
                            1 file changed, 1 deletion(-)
```

## 远程仓库

Git 作为分布式的版本管理系统，每个终端都有自己的 Git 仓库。但团队协作还需要一个中间仓库，作为中心进程同步各个仓库。于是服务器（远程）仓库就来承担这个职责，服务端不仅有仓库，还有配套的相关管理流程。我们可以使用公共的 Git 服务器，也可以自己搭建一套 Git 服务器。

![](../assets/tools/git/remote_repository_1.png)

- 公共的 Git 服务器有：[Github](https://github.com/)、[Gitlab](https://about.gitlab.com/)、[码云 Gitte](https://gitee.com/) 等
- 搭建的私有 Git 服务器，如开源的 Gitlab、[Gitea](https://github.com/go-gitea/gitea)等

### 远程用户登录

Git 服务器一般提供两种登录验证方式：

- HTTS：基于 HTTPS 连接，使用用户名、密码身份验证
    - 每次都要输入用户名、密码，当然可以记住
    - 地址形式：`https://github.com/xjjeffery/learn_git.git`
- SSL：采用 SSL 通信协议，基于公私钥进行身份验证，所以需要额外配置公私秘钥
    - 不用每次输入用户名、密码，比较推荐的方法
    - 地址形式：`git@github.com:xjjeffery/learn_git.git`

#### HTTPS

基于 HTTPS 的地址连接远程仓库，Github 的仓库克隆、拉取是不需要验证的。

```bash
$ git clone 'https://github.com/github/gitignore.git'
Cloning into 'gitignore'...

# 仓库配置文件“.git/config”
[remote "origin"]
  url = https://github.com/github/gitignore.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  pushurl = https://github.com/github/gitignore.git
```

推送代码的时候就会提示输入用户名、密码了，否则无法提交。记住用户密码的方式有两种：

- URL 地址配置：在原本 URL 地址上加上用户名、密码，`https://后加用户名:密码@`

```bash
# 直接修改仓库的配置文件“.git/config”
[remote "origin"]
  url = https://用户名:密码@github.com/xjjeffery/learn_git.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  pushurl = https://github.com/xjjeffery/learn_git.git
```

- 本地缓存：创建一个缓存文件 `.git-credentials`，存储输入的用户名、密码。

```bash
# 参数 “--global” 全局有效，也可以针对仓库设置 “--local”
# store 表示永久存储，也可以设置临时存储
git config --global credential.helper store

# 存储内容如下，打开文件“仓库 \.git\.git-credentials”
https://xjjeffery:xxxx@github.com
```

#### SSH

SSH（Secure Shell，安全外壳）是一种网络安全协议，通过加密和认证机制实现安全的访问和文件传输等业务，多用来进行远程登录、数据传输。SSH 通过公钥、私钥非对称加密数据，所以 SSH 需要生成一个公私钥对，公钥放服务器上，私钥自己留着进行认证。

生成公私钥：通过命令生成公私钥，在此过程中一直回车即可完成。

```bash
ssh-keygen -t rsa -C
```

Linux 是在 `~/.ssh` 目录下，Windows 在 `C:\Users\<user name>\.ssh` 目录下，文件 `id_rsa.pub` 的内容就是公钥。

配置公钥：打开 `id_rsa.pub` 文件，复制内容。Github上，打开 Setting -> SSH and GPG keys -> SSH keys -> 按钮 New SSH key，标题(Title)随意，秘钥内容粘贴进去即可。

![](../assets/tools/git/remote_repository_2.png)

SSH 配置完后，可用 `ssh -T git@github.com` 来检测是否连接成功。

### 操作指令

| **指令** | **描述** |
| --- | --- |
| `git clone <remote_repo>` | 从远程仓库克隆到本地(当前目录) |
| `git clone -b <branch> <remote_repo>` | 从远程仓库克隆指定的分支到本地 |
| `git remote -v` | 查看所有远程仓库，不带参数 -v 只显示名称 |
| `git remote show <remote>` | 显示某个远程仓库的信息 |
| `git remote add <name> <url>` | 增加一个新的远程仓库，并命名 |
| `git remote rename <old> <new>` | 修改远程仓库名称 |
| `git remote rm <remote-name>` | 删除远程仓库 |
| `git pull <remote> <branch>` | 拉取远程仓库的变化，并与本地版本合并 |
| `git pull` | 同上，针对当前分支 |
| `git pull --rebase` | 使用 `rebase` 的模式进行合并 |
| `git fetch <remote>` | 获取远程仓库的所有变动到本地仓库，不会自动合并！需要手动合并 |
| `git push` | 推送当前分支到远程仓库 |
| `git push <remote> <branch>` | 推送本地指定分支到远程仓库的指定分支 |
| `git push <remote> --force/-f` | 强行推送当前分支到远程仓库，即使有冲突，很危险 |
| `git push <remote> --all` | 推送所有分支到远程仓库 |
| `git push –u` | 参数 `–u` 表示与远程分支建立关联，第一次执行的时候用，后面就不需要了 |
| `git push <remote> -d <branch>` | 删除远程仓库上的某个分支 |

!!! warning "注意"

    操作远程仓库时，以下要点需严格遵守：

    - ==每次推送前，先将对应的远程分支 `pull` 到本地分支，即将其他开发者推送的更新合并到本地分支中，确保本地修改与远程分支中的更新没有冲突==
    - ==禁止对公共分支（例如 `dev`）进行 `rebase` 操作==。可以对本地分支进行 `rebase` 操作用于简化提交记录
    - 禁止对公共分支进行 `commit --amend` 操作

    一个重要原则就是严禁任何修改公共分支提交历史的操作！`rebase` 操作只在非公共分支上进行，主要用来清理提交记录（将多个零散对提交记录合并为一个提交）。

### `fetch` 与 `pull` 的不同

两者都是从服务端获取更新，主要区别是 `fetch` 不会自动合并，不会影响当前工作区内容。

> `git pull` = `git fetch` + `git merge`

- 如下面图中，`git fetch` 只获取了更新，并未影响 `master` 、`HEAD` 的位置。
- 要更新 `master`、HEAD 的位置需要手动执行 `git merge`合并。

![](../assets/tools/git/remote_repository_3.png)

```bash
# fetch 只更新版本库
$ git fetch
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 663 bytes | 44.00 KiB/s, done.
From github.com:xjjeffery/learn_git
   2ba12ca..c64f5b5  main       -> origin/main

# 执行合并，合并自己
$ git merge
Updating 2ba12ca..c64f5b5
Fast-forward
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```
