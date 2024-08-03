+++
title = 'Git 基础用法'
date = 2024-07-28T12:00:00-07:00
draft = true
+++

在[上一篇文章](/blog-zh-cn/git-basics-zh-cn/)中，我们讨论了版本控制系统的通用概念和
Git 的设计。在本文中，我们将介绍 Git 的基本用法。

<!--more-->

由于 Git 的功能过于丰富，因此我们只会在本文中介绍每个 Git
用户都应该牢记的基础功能。我们会在之后的文章中介绍高级用法。

本文会在配置 Git 部分尽可能列出所有可行的选择。如果你并不熟悉这些选择，你可以直接采用推荐的方法。如果你发现这些选择对你来说过于复杂，请忽略除推荐方案以外的选择，不必在这些部分上浪费时间。

## 配置 Git

### 安装 Git

在 Windows 上，如果你安装了 WSL（Windows Subsystem for Linux），强烈建议你在 WSL 中使用 Git。

对于 Linux（或 WSL）用户，你可以通过系统的包管理器安装 Git。例如，在 Ubuntu 上，你可以执行：

```bash
sudo apt update && sudo apt install git
```

对于 macOS 用户，你可以通过 Homebrew 安装 Git：

```bash
brew install git
```

你可以通过执行以下命令检查 Git 是否已安装：

```bash
git --version
```

执行后，你应当可以看到你已安装的 Git 版本。

### 配置身份信息

在你使用 Git 之前，你需要设置你的身份信息。这些信息会被记录在你的每次提交中。请注意，一旦你将提交推送到公共仓库，这些信息就会公开。如果你不想暴露你的个人信息，建议你不要使用真实姓名。

要设置你的身份信息，请执行：（**请将 `Your Name` 和 `someone@example.org`
替换为你的姓名和电子邮件地址！**）

```bash
git config --global user.name "Your Name"
git config --global user.email "someone@example.org"
```

上述两个命令将分别设置你的姓名和电子邮件地址。

### 设置 Git 托管平台

通常，我们会使用 Git 托管平台（例如 GitHub、GitLab
等）来托管我们的仓库。你可以通过这些服务与他人分享你的代码、备份你的代码，并与他人合作。

你可以使用 SSH 协议或 HTTPS 协议与远程仓库交互。这里推荐使用 SSH
协议，因为它更方便。如果你选择使用 SSH 协议，你需要使用类似
`git@github.com:user/repo` 的链接，而不是类似
`https://github.com/user/repo` 的链接。

#### 设置 SSH 密钥对

在 SSH 协议中，用于验证的信息是密钥对，而不是密码。因此，要使用 SSH
协议，你需要生成一个 SSH 密钥对。请执行以下代码以生成一个 SSH 密钥对：（**请将
`someone@example.org` 替换为你的邮箱地址！**）

```bash
ssh-keygen -t ed25519 -C "someone@example.org"
```

> 注意：如果你使用的系统不支持 Ed25519 算法，你可以使用 RSA 算法执行：（同样，请将
> `someone@example.org` 替换为你的邮箱地址！）
>
> ```bash
> ssh-keygen -t rsa -b 4096 -C "someone@example.org"
> ```

执行上述命令后，它会询问你密钥对的保存位置。请**直接按下回车**，使用默认路径，不要指定保存密钥对的路径；否则，后续使用密钥对会非常不方便。

接着，它会询问你是否要设置一个密码。你可以设置一个密码，也可以留空。你可以之后随时更改密码。

#### 将 SSH 密钥添加到 Git 托管平台

在你注册了 Git 托管平台的账户后，你可以将你的 SSH 公钥添加到该服务。要获取你的
SSH 公钥，请执行：

```bash
cat ~/.ssh/id_ed25519.pub
```

> 注意：如果你使用的是 RSA 算法，请执行：
>
> ```bash
> cat ~/.ssh/id_rsa.pub
> ```

然后，复制输出的内容，并将其粘贴到 Git 托管平台的 SSH
密钥设置页面。例如，你可以将其添加到[GitHub 的 SSH 密钥设置页面][github-ssh-key-settings]。

[github-ssh-key-settings]: https://github.com/settings/keys/

请记住，**请始终保护好你的私钥（没有 `.pub` 后缀的文件），并且不要将其暴露给他人。只与他人分享公钥（带有 `.pub` 后缀的文件）。**

如果你切换到了新设备、重装了系统，或者不小心删掉了密钥对，你需要重新生成一个 SSH
密钥对，并将新的公钥添加到 Git 托管平台。私钥一旦丢失，无法找回。

你可以通过执行以下命令测试你的密钥是否已成功添加到 GitHub：（**注意：第一次执行时，你可能会被要求确认
GitHub 的指纹。请确认后输入 "yes" 并按下回车键。**）

```bash
ssh git@github.com
```

## 基础用法

在本节中，我们将介绍每个 Git 用户都应该牢记的基本用法，以便更有效、更舒适地使用 Git。

大多数新用户可能会对 Git 的工作流程感到困惑。在这里，我们将详细介绍
Git 的用法，并提供一些练习以测试你是否已经掌握了基本用法。

### 创建仓库

要创建一个新仓库，你可以首先创建一个新目录作为 Git 仓库。如果你想通过命令行创建目录，你可以执行：（请将 `<repo-name>` 替换为你的仓库名！）

```bash
mkdir <repo-name>
```

然后，你可以执行以下命令初始化 Git 仓库：

```bash
git init
```

### 复制仓库

我们时常会将现有仓库复制到本地机器上（例如，使用他人的仓库，或者先在服务器上创建自己的仓库）。要复制一个仓库，你可以执行：（`<repository-url>` 是你想复制的仓库的 URL）

```bash
git clone <repository-url>
```

> 例如，要复制 GitHub 的 gitignore 仓库，你可以执行以下命令：
>
> ```bash
> git clone git@github.com:github/gitignore.git
> ```

### 将文件添加到暂存区

提交是 Git 的基础操作。在你提交之前，你需要将更改添加到暂存区（暂存区是一个缓冲区，用于更干净、更细粒度地提交）。如果你不知道或忘记了什么是暂存区，你可以阅读[上一篇文章](/blog-zh-cn/git-basics-zh-cn/#工作区-workspace-和暂存区-staging)中的工作区和暂存区部分。

要将更改添加到暂存区，你可以执行：

```bash
git add <file>
```

> 例如，如果你想将 `README.md` 文件的更改添加到暂存区，你可以执行：
>
> ```bash
> git add README.md
> ```

如果你想将当前目录及其子目录中的所有更改添加到暂存区，你可以执行：

```bash
git add .
```

### 检查状态

要查看哪些文件已添加到暂存区，你可以执行以下命令查看已暂存和未暂存的更改：

```bash
git status
```

强烈建议在提交之前检查状态，以确保你提交的是正确的更改，尤其是在执行 `git add .` 之后。

`git status` 命令会显示很多信息和提示。如果你对下一步该做什么感到困惑，你可以随时尝试执行
`git status` 以获取一些提示，并了解你的 Git 仓库的状态，因此，`git status` 基本是 Git
中最经常使用的。

### 提交更改

在将更改添加到暂存区后，你可以执行：

```bash
git commit
```

这个命令会打开默认的文本编辑器（通常是 Vim 或
Nano），让你编写提交信息。如果你将提交信息留空，Git
会中止提交。在文本编辑器界面中，Git 会向你展示一些关于提交的提示（如你将要提交的更改）。

如果提交信息很短，你可以直接在命令行中编写提交信息：（**请将 `Your commit message` 替换为你的提交信息！**）

```bash
git commit -m "Your commit message"
```

> 例如，如果你想提交的信息为 `docs: add README.md`，你可以执行：
>
> ```bash
> git commit -m "docs: add README.md"
> ```

已提交的文件在大多数情况下都可以轻松找回，而未提交的更改可能会丢失。因此，**强烈建议**你**经常提交**。一个好的方法是，如果你对仓库进行了更改，每天至少提交一次。通常，细粒度的提交在很多方面都比大的提交更好（如遴选、回滚、理解更改等）。

很多人使用
[Conventional Commits][conventional-commits]
风格来编写提交信息。这种风格使得更容易、更清晰地理解提交中的更改。

[conventional-commits]: https://www.conventionalcommits.org/

### 在 Git 仓库里的任何地方使用 Git 子命令

只要你在 Git 仓库中，你就可以在仓库的任何子目录中使用 Git
子命令。例如，你可以在仓库的根目录执行 `git status`，亦可在其子目录执行。这对于管理仓库非常方便。

### 设置远程仓库

要设置远程仓库，你可以执行：

```bash
git remote add origin <repository-url>
```

这个命令会添加一个名为 `origin` 的远程仓库，URL 为
`<repository-url>`。`origin`
是远程仓库的默认名称。你也可以使用其他名字作为远程仓库的名称。

> 例如，如果你想添加一个 URL 为 `git@github.com:github/gitignore.git`
> 的远程仓库，你可以执行：
>
> ```bash
> git remote add origin git@github.com:github/gitignore.git
> ```

第一次推送时，你会看到一个错误消息，告诉你要设置上游分支。你可以按照错误消息中的说明设置上游分支。例如，你可以执行以下命令将上游分支设置为 `master`：

```bash
git push --set-upstream origin master
```

### 推送更改到远程仓库

在提交后，你可以执行：

```bash
git push
```

将更改推送到远程仓库。在执行命令后，仔细查看输出。如果看到任何错误消息，你应该仔细阅读并尝试理解错误是什么。除了错误消息外，Git
还会提供一些解决问题的提示。

例如，如果远程仓库无法使用快进合并 (fast-forward)
你的更改，这意味着远程分支的头提交不是你的提交的祖先。在这种情况下，你需要在本地合并这些更改（Git
不允许你在远程服务器上合并）或者强制推送你的更改到远程仓库。要强制推送你的更改，你可以执行：

```bash
git push --force
```

另一个常见的错误是未设置上游分支。在[设置远程仓库](#设置远程仓库)部分中，已经提过这个问题。

### 从远程仓库抓取更改

要从远程仓库抓取更改，你可以执行：

```bash
git fetch
```

这个命令会将远程仓库的更改抓取到你的本地远程跟踪分支。如果远程仓库不是默认的远程仓库，你可以指定远程仓库的名称执行：（请将 `<remote-repo>` 替换为远程仓库的名称）

```bash
git fetch <remote-repo>
```

> 例如，如果远程仓库的名称为 `source`，你可以执行：
>
> ```bash
> git fetch source
> ```

抓取这些修改后，你可以使用你喜欢的合并策略将这些修改合并到你的本地分支。我们将在下一篇文章中介绍合并策略。

抓取更改不会改变你的本地分支（除了远程跟踪分支）。这对于你只想了解远程仓库的更改而不想影响你的本地分支非常有用。

### 从远程仓库拉取更改

要从远程仓库拉取并合并更改，你可以执行：

```bash
git pull
```

如果没有手动配置，默认的合并策略取决于 Git 版本。你可以暂时不管合并策略。只要你在同一个本地仓库上做所有更改，快进合并应该总是适用的。

### Gitignores

如果你尝试执行 `git add .`
将文件添加到暂存区，你可能会发现一些你不想提交的文件被添加到了暂存区。通常，我们不想提交由程序生成的文件（例如编译缓存、日志文件、可执行文件等）。Git
允许你通过在任何目录中创建 `.gitignore` 文件来忽略这些文件。`.gitignore`
文件是一个文本文件，包含用于匹配文件名和目录名的格式。比如，如果你想忽略所有 `.o`
扩展名的文件（这些是 C/C++ 编译器生成的目标文件），你可以在 `.gitignore`
文件中添加以下行：

```plaintext
*.o
```

这将忽略**目录和子目录**中所有扩展名为 `.o` 的文件。

以下是一些常见的 `.gitignore` 文件中的模式：（`#` 表示后面的文本是注释）

```plaintext
# Ignore all files with .o .s extension
*.o
*.S

# Some of the asm files cannot be ignored
!not-generated.S

# ignore the build directory
build/

# Ignore files starting with a number
[0-9]*
```

`.gitignore` 文件的完整语法可以参考[官方文档][gitignore-doc]（或在终端中输入 `man 5 gitignore`）。

[gitignore-doc]: https://git-scm.com/docs/gitignore

GitHub 为 `.gitignore` 文件提供了几个模板。你可以使用这些模板（需要将这些文件重命名为
`.gitignore`）。你可能需要将几个模板合到一起来创建一个适合你的项目的
`.gitignore` 文件。这些模板可以在[GitHub 的 gitignore 仓库][github-gitignore]中找到。

[github-gitignore]: https://github.com/github/gitignore/

### 添加新分支

Git 的默认分支是 `master`（GitHub 的默认分支称为
`main`，不需要额外设置；不过，你可以将默认分支名设置为 `master`
或任何其他名称）。通常，`master`
分支用于项目的最新版本（这里的「最新版本」是指它包含了最新合并到项目中的功能）。除了 `master`
分支，一个项目可能还有用于发布的稳定版本、用于正在进行的功能的特性分支和用于修复错误的分支。

要添加一个新分支，你可以执行：（请记得将 `<branch-name>` 替换为新分支的名称）

```bash
git branch <branch-name>
```

上述命令会创建一个名为 `<branch-name>` 的新分支。

> 例如，如果你想创建一个名为 `dev` 的新分支，你可以执行：
>
> ```bash
> git branch dev
> ```

如果你的 Git 版本不支持 `git branch` 命令，你可以执行：

```bash
git checkout -b <branch-name>
```

这会创建一个名为 `<branch-name>` 的新分支并切换到新分支。

> 例如，如果你想创建一个名为 `dev` 的新分支并切换到新分支，你可以执行：
>
> ```bash
> git checkout -b dev
> ```

### 切换到其他分支

要切换到另一个分支，你可以执行：

```bash
git switch <branch-name>
```

> 例如，如果你想切换到 `dev` 分支，你可以执行：
>
> ```bash
> git switch dev
> ```

如果你的 Git 版本不支持 `git switch` 命令，你可以执行：

```bash
git checkout <branch-name>
```

> 例如，如果你想切换到 `dev` 分支，你可以执行：
>
> ```bash
> git checkout dev
> ```

### 标签

要给一个提交打上标签，你可以执行：

```bash
git tag <tag-name>
```

> 例如，如果你想给最新的提交打上 `v1.0.0` 标签，你可以执行：
>
> ```bash
> git tag v1.0.0
> ```

## 回顾

现在，让我们回顾一下 Git 的基本用法：

| 命令 | 描述 | 备注 |
| --- | --- | --- |
| `git init` | 初始化一个新仓库 | 当前目录不能是一个已存在的仓库 |
| `git clone <url>` | 复制一个已存在的仓库 | 目标目录不能存在 |
| `git add <path>` | 将 `<path>` 中的更改添加到暂存区 | 提交只会应用于添加到暂存区的文件 |
| `git status` | 检查仓库的状态 | 推荐在提交前使用 |
| `git commit` | 进行一次提交 | 如果消息很短，使用 `-m`；经常提交（文件的更改在大多数情况下很容易找回） |
| `git remote add origin <url>` | 添加一个远程仓库 | 远程仓库的默认名称是 `origin`；你可能需要遵循第一次推送时的指示（例如，`git push --set-upstream origin master`） |
| `git push` | 将更改推送到远程仓库 | 如果快进合并策略失败，使用 `--force` 强制推送（这会丢弃一些提交） |
| `git fetch` | 从远程仓库抓取更改 | 如果你想抓取非默认分支，使用 `git fetch <repo>` |
| `git pull` | 从远程仓库抓取并合并更改 | 默认的合并策略可能不同 |
| `.gitignore` | 忽略文件 | 使用 `.gitignore` 忽略你不想提交的文件 |
| `git branch <branch>` | 添加一个新分支 | 新分支名称不能存在；另一种解决方案：`git checkout -b <branch>` |
| `git switch <branch>` | 切换到另一个分支 | 另一种解决方案：`git checkout <branch>` |

## 练习

现在，让我们来做一个练习，看看你是否已经掌握了基本用法。你可以尝试在你的电脑上完成以下任务。同时，我们也会提供一个可行的解决方案。上面的表格也可能帮助你解决这个练习。

### Task A

#### Subtask A.1

在一个新目录中创建一个名为 `my-repo` 的新仓库。

{{< details summary="点击查看可行的解决方案" >}}

1. 创建一个新目录并进到此目录。
2. 执行 `git init`。

{{< /details >}}

#### Subtask A.2

进行一次初始提交，提交为 `README.txt` 文件，内容为 `my-repo`。提交信息为 `initial commit`。

{{< details summary="点击查看可行的解决方案" >}}

1. 创建一个名为 `README.txt` 的新文件，内容为 `my-repo`。
2. 执行 `git add README.txt`。（你可以执行 `git status` 检查文件是否已添加到暂存区。）
3. 执行 `git commit -m "initial commit"`。

{{< /details >}}

#### Subtask A.3

检查你的仓库的状态。

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git status`。

{{< /details >}}

#### Subtask A.4

*如果你不想使用远程仓库，你可以跳过子任务 A.4 和 A.5。*

在 GitHub（或任何其他 Git 托管服务）上创建一个**私有**远程仓库。然后将远程仓库添加到本地仓库。

{{< details summary="点击查看可行的解决方案" >}}

1. 在 GitHub 上创建一个名为 `my-repo` 的新私有仓库。
2. 执行 `git remote add origin git@github.com:<your-name>/my-repo.git`。

{{< /details >}}

#### Subtask A.5

将更改推送到远程仓库。

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git push --set-upstream origin master`。

{{< /details >}}

#### Subtask A.6

创建一个名为 `dev` 的新分支。

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git branch dev`。

{{< /details >}}

#### Subtask A.7

切换到 `dev` 分支。

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git switch dev`。

{{< /details >}}

#### Subtask A.8

忽略将 `.log` 后缀的所有文件。创建 `1.log` 文件以测试规则是否生效。

{{< details summary="点击查看可行的解决方案" >}}

1. 创建一个名为 `.gitignore` 的新文件。
2. 在 `.gitignore` 文件中添加以下行：

   ```plaintext
   *.log
   ```

3. 创建一个名为 `1.log` 的新文件。
4. 执行 `git status` 检查 `1.log` 文件是否被忽略。
   如果被忽略，你不应该在未跟踪的更改中看到 `1.log` 文件。

{{< /details >}}

### Task B

#### Subtask B.1

复制仓库 `git@github.com:github/gitignore.git`。

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git clone git@github.com:github/gitignore.git`。

{{< /details >}}

#### Subtask B.2

检查仓库的状态。

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git status`。

{{< /details >}}

#### Subtask B.3

抓取远程仓库的更改。（远程仓库没有变更是很正常的。）

{{< details summary="点击查看可行的解决方案" >}}

1. 执行 `git fetch`。

{{< /details >}}

## 总结

在本文中，我们介绍了 Git 的基本用法。我们介绍了每个 Git 用户都应该牢记的基本命令。在下一篇文章中，我们将介绍关于合并的命令。

## 版权

你可以将本文用于任何目的，只要你在第一处使用的地方明确标注原作者和链接
(<https://lau.yeeyu.org/blog-zh-cn/git-basic-usage-zh-cn>)。请忽略页脚处的版权声明。
