+++
title = 'Git 进阶用法：子模块'
date = 2024-09-11T12:00:00-07:00
draft = false
tags = ["git", "git-zh-cn", "tutorial", "tutorial-zh-cn"]
showToc = true
+++

在[上一篇文章](/blog-zh-cn/git-usage-merging-zh-cn)中，我们介绍了 Git
分支合并相关用法。在本文中，我们将介绍如何在 Git 中管理子模块。

<!--more-->

*本文章有其他语言的版本：[English（英文）](/blog/git-usage-submodule-en)及[繁體中文（繁体中文）](/blog-zh-hk/git-usage-submodule-zh-hk)。如果你更熟悉这些语言，建议阅读这些语言的版本。*

## 为什么需要子模块？

如果你从没有听说过子模块，你可能会想，为什么我们需要它们。一个常见的用例是你的项目依赖于其他项目，而这些项目应该被单独管理，这时，使用子模块来分割你的项目和项目依赖就显得非常重要了。

比如说，你正在开发一个 Android 应用，其中包含一些 C/C++ 库。这些库是由其他团队开发的，你希望它们以某种方式出现在你的项目中。显然，你可以使用脚本来下载（或更新）这些库，每次构建项目时都需要这样做。然而，这并不是一个好的办法。你可能会遇到诸如版本冲突、忘记更新库等问题。通过使用子模块，你可以更有条理地管理这些库。

另一个例子是使用细粒度控制来管理你的项目。你可能为每个库使用一个仓库，为你的项目使用一个仓库。通过子模块，你可以管理这些仓库之间的依赖关系。其他人可以很容易地使用你的库，而不需要复制整个庞大的项目。

## 子模块和类似工具

有很多工具提供类似子模块的功能。然而，Git 的设计原则使得方便地管理子模块变得具有挑战性。不同的工具有不同的权衡。以下是一些流行的工具：

- [Git Submodule][git-submodule];
- [Git Subtree][git-subtree];
- [Gitslave][gitslave].

[git-submodule]: https://git-scm.com/docs/git-submodule
[git-subtree]: https://github.com/apenwarr/git-subtree
[gitslave]: https://gitslave.sourceforge.io/

### Git Submodule

Git Submodule 是 Git 官方的子模块管理工具。

优点：

- 几乎所有用户均安装了 git submodule（因为是在 Git 官方项目中的）。
- 修改者容易使用。

缺点：

- 用户使用起来困难（需要手动更新或初始化）。
- 如果子模块仓库不可用，你将无法更新或初始化子模块。这使得仓库更像是一个「中心化」仓库，因为它依赖于每个子模块的单个 URL。

### Git Subtree

[Git Subtree][git-subtree] 是一个将子项目包含在主项目的子目录中的项目，可选择包含子项目的整个历史记录。

优点：

- 使得无需更改的用户更容易使用。
- 子模块包含在仓库中。

缺点：

- 在主仓库中占用更多空间。
- 修改者使用起来困难。

### Gitslave

[Gitslave][gitslave] 是一个项目，它可以帮助从多个单独的 Git 仓库中组装一个元项目。

优点：

- 修改者使用起来非常容易。

缺点：

- 用户必须安装 gitslave 才能使用子模块。

### 工具总结

如你所见，所有可能的解决方案都有其缺点。对我来说，强制每个用户安装一个非官方的
Git 工具是不可接受的。如果用户可以简单地通过 README
中的说明来管理，那么这是可以接受的。此外，我不希望工具显著增加开发者的负担。因此，我更倾向于使用
Git 子模块。你可以选择适合你需求的工具。在本文中，我们仅介绍 Git 子模块的用法。

## 子模块的常见需求

### 用户

对于用户来说，他们可能希望：

- 复制带有子模块的主仓库（参见[复制带有子模块的仓库](#复制带有子模块的仓库)）；
- 初始化现有仓库中的子模块（参见[初始化或更新子模块到当前提交的版本](#初始化或更新子模块到当前提交的版本)）；
- 将子模块更新到当前提交指定的版本（Git 默认不会在拉取时更新子模块）（参见[初始化或更新子模块到当前提交的版本](#初始化或更新子模块到当前提交的版本)）；
- 列出仓库中的所有子模块（参见[列举子模块](#列举子模块)）。

### 开发者

对于开发者来说，他们可能希望：

- 向仓库中添加子模块（参见[添加子模块](#添加子模块)）；
- 将子模块更新到特定版本（参见[将子模块更新到特定版本](#将子模块更新到特定版本)）；
- 从仓库中删除子模块（参见[删除子模块](#删除子模块)）；
- 修改子模块的 URL（参见[修改子模块的 URL](#修改子模块的-url)）；
- 列出仓库中的所有子模块（参见[列举子模块](#列举子模块)）。

## 复制带有子模块的仓库

如果你要复制一个带有子模块的仓库，你可以在原始参数的基础上添加 `--recurse-submodules` 选项：

```bash
git clone <repo> [dir] --recurse-submodules
```

## 初始化或更新子模块到当前提交的版本

> [!TIP]
> 如果你希望在拉取操作时更新子模块，你可以使用 `--recurse-submodules` 选项。

如果你希望将子模块初始化或更新到当前提交指定的版本，你可以使用以下命令：

```bash
git submodule update --init --recursive
```

这将递归地将子模块更新到当前提交指定的版本（即子模块的子模块也将被更新）。

## 列举子模块

要列出仓库中的所有子模块，你可以简单地使用以下命令：

```bash
git submodule
```

## 添加子模块

要向你的仓库添加一个子模块，你可以使用以下命令：

```bash
git submodule add <repo> [dir]
```

为了方便用户，建议使用 `https` 协议而不是 `ssh` 协议。这将允许用户在没有 Git
托管服务帐户的情况下使用子模块。如果你希望在你的仓库中使用 `https`，但在实际开发中使用
`ssh`，你可以在 Git 中设置 URL 替换。例如，如果你使用 GitHub，你可以使用以下命令在
Git 遇到 `https` 协议时设置替换（如果你只想将此规则应用于当前仓库，你可以删除
`--global` 选项）：

```bash
git config --global url.git@github.com:.insteadOf https://github.com/
```

## 将子模块更新到特定版本

作为开发者，你可能希望在新提交中将子模块更新到特定版本。

为了实现这一目的，你应该：

1. 进入子模块；
2. 进行类似主仓库的更改（目的是将 `HEAD` 更改到你想要的提交），请记住如果新提交尚未出现在远程仓库中，请推送更改；
3. 离开子模块；
4. 将子模块中的更改添加到主仓库中（即，`git add <submodule>`）；
5. 提交主仓库中的更改（你可以推送更改）。

子模块中的操作（步骤 2）与主仓库中的操作非常相似。唯一的区别是你可能会在子模块中处于分离的 `HEAD` 状态。

> [!TIP]
> 你可以在 `git push` 中使用 `--recurse-submodules=on-demand`
> 选项来按需推送子模块中的更改，即使用 `git push --recurse-submodules=on-demand`
> 来防止你忘记推送新提交。

如果你要将子模块更新到最新提交，你可以在主仓库中使用以下命令：

```bash
git submodule update --remote
```

## 删除子模块

你可以先[列举子模块](#列举子模块)。然后在主仓库中使用以下命令删除跟踪信息：

```bash
git submodule deinit <path-to-submodule>
```

接着，删除子模块目录：

```bash
git rm <path-to-submodule>
```

你还应该删除子模块的暂存更改：

```bash
git rm --cached <path-to-submodule>
```

最后，在主仓库中提交更改。

## 修改子模块的 URL

如果你想修改子模块的 URL，你应该修改主仓库根目录中的 `.gitmodules` 文件。

然后，使用以下命令更新子模块：

```bash
git submodule sync
```

最后，在主仓库中提交更改。

## 回顾

现在，让我们回顾一下本文和前面文章中介绍的用法。

| 命令 | 描述 | 备注 |
| --- | --- | --- |
| `git init` | 初始化一个新仓库 | 当前目录不能是一个已存在的仓库 |
| `git clone <url>` | 复制一个已存在的仓库 | 目标目录不能存在；使用 `--recurse-submodules` 来一同复制所有子仓库 |
| `git add <path>` | 将 `<path>` 中的更改添加到暂存区 | 提交只会应用于添加到暂存区的文件 |
| `git status` | 检查仓库的状态 | 推荐在提交前使用 |
| `git diff` | 显示差异 | 使用 `--cached` 显示暂存区的差异；可以指定路径 |
| `git commit` | 进行一次提交 | 如果消息很短，使用 `-m`；经常提交（文件的更改在大多数情况下很容易找回） |
| `git log` | 显示提交历史 | 使用 `--oneline` 让每个提交只占用一行；使用 `--graph` 显示提交图 |
| `git remote add origin <url>` | 添加一个远程仓库 | 远程仓库的默认名称是 `origin`；你可能需要遵循第一次推送时的指示（例如，`git push --set-upstream origin master`） |
| `git remote show` | 列出远程仓库 | 使用 `-v` 显示更多细节；你可以通过名称指定仓库 |
| `git remote set-url origin <url>` | 修改远程仓库地址 | |
| `git remote remove origin` | 移除远程仓库 | |
| `git remote rename origin new-origin` | 重命名远程仓库 | |
| `git push` | 将更改推送到远程仓库 | 如果快进合并策略失败，使用 `--force` 强制推送（这会丢弃一些提交） |
| `git fetch` | 从远程仓库抓取更改 | 如果你想抓取非默认分支，使用 `git fetch <repo>` |
| `git pull` | 从远程仓库抓取并合并更改 | 默认的合并策略可能不同 |
| `.gitignore` | 忽略文件 | 使用 `.gitignore` 忽略你不想提交的文件 |
| `git branch <branch>` | 添加一个新分支 | 新分支名称不能存在；另一种解决方案：`git checkout -b <branch>` |
| `git switch <branch>` | 切换到另一个分支 | 对于其他引用，请加上 `--detach`；另一种解决方案：`git checkout <branch>` |
| `git merge <branch>` | 合并分支 | 使用 `--ff-only` 强制快进合并；使用 `--squash` 合并但不创建新提交或避免创建合并提交 |
| `git rebase <branch>` | 变基分支 | 使用 `-i` 或 `--interactive` 使用更多功能 |
| `<rev>~<n>` | 获取 `<rev>` 的第 `n` 代祖先提交 | |
| `<rev>^<n>` | 获取 `<rev>` 的第 `n` 个父提交 | `^` 单独表示「父提交」 |
| `git stash` | 暂时储藏更改 | 使用 `pop`、`clear` 和 `list` 检索、清除所有、列出储藏的更改 |
| `git merge-base --all <commit>...` | 获取共同祖先 | |
| `git restore <path>` | 从提交中恢复文件到工作目录 | 使用 `--source=<tree>` 指定源；另一种解决方案：`git checkout <path>` |
| `git restore --staged <path>` | 取消暂存 | 另一种解决方案：`git reset HEAD <path>` |
| `git reset <commit>` | 更改分支的 HEAD 提交 | 选项：`--soft`、`--mixed`、`--hard`、`--merge` 或 `--keep` |
| `git apply <patch>` | 应用补丁 | |
| `git show <commit>` | 显示提交 | |
| `git revert <commit>...` | 回退提交 | 使用 `--no-commit` 或 `-n` 避免创建多个提交 |
| `git cherry-pick <commit>...` | 遴选提交 | 使用 `--no-commit` 或 `-n` 避免创建多个提交 |
| `git grep <pattern> <path>...` | 在文件中查找特定内容 | 如果未指定路径，将在整个仓库中搜索 |
| `git reflog` | 显示引用日志 | |
| `git submodule update --init --recursive` | 初始化或更新子模块 | 使用 `--recurse-submodules` 在拉取时更新子模块 |
| `git submodule` | 列举子模块 | |
| `git submodule add <repo> [dir]` | 添加一个子模块 | 建议使用 `https` 而不是 `ssh` 以方便用户 |
| `git submodule update --remote` | 将子模块更新到最新提交 | |
| `git submodule deinit <path-to-submodule>` | 删除子模块的跟踪信息 | |
| `git submodule sync` | 根据当前 `.gitmodules` 更新子模块 | |

## 总结

在本文中，我们介绍了 Git 中的子模块管理。在下一篇文章中，我们将介绍 Git 中的其他部分。希望你享受和 Git 一起度过的时光！

## 版权

你可以将本文用于任何目的，只要你在使用的地方明确标注原作者和链接
(<https://lau.yeeyu.org/blog-zh-cn/git-usage-submodule-zh-cn>)。请忽略页脚处的版权声明。
