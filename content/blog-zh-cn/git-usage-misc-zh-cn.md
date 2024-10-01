+++
title = 'Git 进阶用法：其他'
date = 2024-09-15T12:00:00-07:00
draft = false
tags = ["git", "git-zh-cn", "tutorial", "tutorial-zh-cn"]
series = "git-tutorial"
seriesAnnotation = "5/5"
showToc = true
+++

在[上一篇文章](/blog-zh-cn/git-usage-submodule-zh-cn)中，我们介绍了 Git
子模块的用法。在本文中，我们将介绍 Git 其他用法。

<!--more-->

*本文章有其他语言的版本：[English（英文）](/blog/git-usage-misc-en)及[繁體中文（繁体中文）](/blog-zh-hk/git-usage-misc-zh-hk)。如果你更熟悉这些语言，建议阅读这些语言的版本。*

## 标签

标签用于标记特定的提交，它们通常用于标记版本发布。标签有两种类型：轻量标签
(lightweight tags) 和带注释的标签 (annotated tags)。

轻量标签只有一个名称，指向一个提交，而带注释的标签除此之外还有一个附属的消息。

### 语义化版本标签

如果你想为一个版本发布添加一个标签，建议使用[语义化版本][semver]格式，这在软件工程中被广泛使用。

[semver]: https://semver.org/

在语义化版本格式中，一个版本标签的名称应该是 `vX.Y.Z`，其中 `X`、`Y` 和 `Z` 是整数。`X` 是主版本号，`Y` 是次版本号，`Z` 是修订版本号。

当你发布一个版本时，

- 如果更改不向下兼容，递增主版本号 `X`；
- 如果向下兼容地添加新功能，递增次版本号 `Y`；
- 如果向下兼容地修复错误（没有新功能），递增修订版本号 `Z`。

如果你递增了某个版本号，那么接下来的几个数字应该被重置为 0。例如，如果你发布了一个次版本号版本，从 `v1.1.4` 到 `v1.2.Z`，那么 `Z` 应该是 0。

对于小于 1 的主版本号，即 `v0.Y.Z`，它被认为是一个初始开发阶段。在这种情况下，不向下兼容的更改可能随时发生。但是在 `v1.0.0` 之后，这被认为是一个稳定的里程碑，版本控制应该遵循语义化版本规则，尤其是兼容性。

### 创建标签

对于轻量标签，你可以使用以下命令：

```bash
git tag <tag-name> <commit-id>
```

如果你不指定提交 ID，标签将指向最新的提交。我们经常会这样给最新的提交创建标签。

> 例如，如果你想为最新的提交创建一个 `v1.0.0` 的标签，你可以使用以下命令：
>
> ```bash
> git tag v1.0.0
> ```

对于带注释的标签，你可以使用以下命令：

```bash
git tag -a <tag-name> -m <message> <commit-id>
```

同样，你可以省略提交 ID 来指向最新的提交。

> 例如，如果你想为最新的提交创建一个 `v1.0.0` 的带注释标签，消息是 `Supporting multiple platforms`，你可以使用以下命令：
>
> ```bash
> git tag -a v1.0.0 -m "Supporting multiple platforms"
> ```

### 推送标签

默认情况下，标签不会在 `git push` 命令中被推送。如果你想推送标签，你应该在
`git push` 命令中添加 `--tags`。例如：

```bash
git push --tags
```

### 列出标签

你可以使用以下命令列出所有标签：

```bash
git tag -l
```

### 删除本地标签

你可以使用以下命令删除本地标签：

```bash
git tag -d <tag-name>
```

> 例如，如果你想删除 `v1.0.0` 标签，你可以使用以下命令：
>
> ```bash
> git tag -d v1.0.0
> ```

### 删除远程标签

你可以使用以下命令删除远程标签：

```bash
git push --delete <remote-name> <tag-name>
```

> 例如，如果你想删除远程仓库 `origin` 中的 `v1.0.0` 标签，你可以使用以下命令：
>
> ```bash
> git push --delete origin v1.0.0
> ```

## 文件权限

在 Git 中，有两种文件权限：可执行和不可执行。

> [!TIP]
> 如果你不使用类 Unix 系统（Windows 不是 Unix 系统，Linux 和 macOS 是），文件权限系统未必可以正常工作。

对于可执行文件，确切的权限是
`755`（这意味着任何人都可以读取和执行文件，但只有所有者可以写入）。对于不可执行文件，确切的权限是
`644`（这意味着任何人都可以读取文件，但只有所有者可以写入，没有人可以执行它）。

## 符号链接

在类 Unix 系统中，符号链接被经常使用。符号链接指向一个文件，通过路径（相对或绝对）指定，它表现得就像是它指向的地方。如果指向的地方无效，那么它将是一个损坏的符号链接，但这本身不会引起任何问题。

Git 支持符号链接，但如果它指向仓库之外的东西，这可能会导致指向的文件不存在或权限不正确。因此，你应该避免使用符号链接指向仓库外的内容。此外，在
Windows 上，符号链接可能存在各种各样的问题，因此我的建议是除非你确定它不会引起任何问题，否则应避免使用符号链接，例如，你只在
Linux 上使用它，并且指向仓库中的文件。

## 文件和目录

在 Git 中，没有目录，只有文件。对于每个文件，它都有一个从仓库根目录的路径。例如，`a/b.txt` 表示一个文件 `b.txt` 在目录 `a` 中。因此，如果你想添加一个目录，你应该在该目录中添加一个文件。

## 重命名文件

如果你想移动一个文件，这实际上等同于重命名文件。

你可以移动文件并添加旧文件和新文件的共同祖先目录，以便让 Git 跟踪这个更改。在提交时，Git 将尝试找到重命名关系（但有时可能会失败或有错误的结果）。

## Git 别名

一直输入 `git commit --amend` 或 `git pull --recurse-submodules` 之类的长命令非常麻烦。Git 提供了一种便捷的方式来为你经常使用的长命令或难以记住的命令创建一个别名。

Git 别名允许你为一个长命令创建一个简短的别名。例如，你可以将 `amend` 设置为 `commit --amend`，然后 `git amend` 将等同于 `git commit --amend`。这类似于 shell 中的别名。

Git 别名可以通过命令或编辑配置文件（`~/.gitconfig`）来配置。推荐使用命令来配置别名。

要使用命令设置别名，你可以使用以下命令：

```bash
git config --global alias.<alias-name> <command>
```

如果你只想为当前仓库设置别名，你应该删除 `--global` 选项。

以下是一些我推荐的别名：

1. `amend`：修改最后一次提交

    ```bash
    git config --global alias.amend 'commit --amend'
    ```

2. `unstage`：取消暂存某些文件

    ```bash
    git config --global alias.unstage 'reset HEAD --'
    ```

3. `full-pull`：拉取并更新子模块

    ```bash
    git config --global alias.full-pull 'pull --recurse-submodules'
    ```

4. `full-push`：推送并更新子模块

    ```bash
    git config --global alias.full-push 'push --recurse-submodules=on-demand'
    ```

5. `log-graph`：以图形化的方式显示提交历史

    ```bash
    git config --global alias.log-graph 'log --graph --oneline --decorate'
    ```

6. `fpush`：强制推送（通常在修改提交后使用）

    ```bash
    git config --global alias.fpush 'push --force'
    ```

## 全局 Git Ignore

每个操作系统可能都有一些自动生成的文件，你不想将它们包含在仓库中，比如
macOS 中臭名昭著的 `.DS_Store`。在每个仓库中添加这些文件到
`.gitignore` 中真的很麻烦，对你和你的团队成员来说都是如此。对于你来说，你需要每次创建一个新仓库时都这样做。对于你的团队成员来说，如果他们不使用相同的操作系统，这很可能是不必要的。

Git 允许你设置一个全局文件来忽略文件。它在 `core.excludesFile`
变量中配置。你可以使用以下命令设置它：

```bash
git config --global core.excludesFile <path>
```

如果你不设置 `core.excludesFile`，默认值是 `$XDG_CONFIG_HOME/git/ignore`
（如果没有设置 `$XDG_CONFIG_HOME`，那么会采用
`$HOME/.config/git/ignore`）。这可能不会在 Windows 或 macOS 上生效。

> [!TIP]
> GitHub 的官方 `.gitignore` 模板提供了很多系统特定的 `.gitignore`
> 文件（例如，`Global` 目录中的文件）。你可以在[GitHub gitignore 仓库][gitignore]中找到它们。

[gitignore]: https://github.com/github/gitignore

## Git 垃圾回收

> [!WARNING]
> Git 垃圾回收将删除所有未引用的对象（例如，因为重置而被弃用的提交）。垃圾回收后，你将无法恢复这些对象。因此，在使用垃圾回收时，你应该格外小心。一般来说，你不应该在自己的项目中使用垃圾回收；只有在他人的项目中使用，或者当你确定不需要未引用的对象时才使用。

通常情况下，Git 将保留所有对象，即使它们没有被任何分支或标签引用。正如我之前所说，大多数情况下，提交的文件是可以轻松找回的。然而，这将占用
`.git` 目录中的大量空间。因此，当你想要删除这些未引用的对象时，Git 允许你通过垃圾回收来释放空间。

要运行垃圾回收，你可以使用以下命令：

```bash
git gc
```

## 实用第三方工具

在这里，我会推荐一些我认为非常有用的第三方工具。如果它们对你有很大帮助，你可以安装它们。当然，不安装它们也不会有什么关系，因为 Git 本身已经足够强大了。

### Git Delta

[Git Delta][git-delta] 为你提供了一个清晰直观的变更视图。它极大地提高了
diff 输出的可读性。它还可以识别合并冲突。配置了 Git Delta
之后，就可直接使用。`git diff`
的输出将会很方便直观。此外，不用担心补丁的格式，因为它只影响显示时的输出，当你生成补丁时，Git
仍然会使用原始格式。

[git-delta]: https://dandavison.github.io/

Git Delta 可以应用于 `diff`、`blame`、`grep` 和 `show` 等命令。

### Git 仓库内搜索工具

`git grep` 的输出不是很方便，因为它只显示文件名和匹配行。如果你想要更好的搜索工具，你可以使用 [ripgrep (rg)][ripgrep] 和 [the silver searcher(ag)][ag]。

[ripgrep]: https://github.com/BurntSushi/ripgrep
[ag]: https://github.com/ggreer/the_silver_searcher

## 回顾

现在，让我们回顾一下所有 Git 教程文章中介绍的用法（对，这就是最后一篇）。下表应该对你找到你需要的命令非常有帮助和高效（不常用的命令可能会被省略，例如设置别名命令）。

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
| `git clean` | 清理工作目录 | 使用 `-i` 交互模式；使用 `-f` 强制删除 |
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
| `git tag <tag-name>` | 添加一个轻量标签 | |
| `git tag -a <tag-name> -m <message>` | 添加一个带注释的标签 | |
| `git push --tags` | 推送标签 | |
| `git tag -l` | 列出标签 | |
| `git tag -d <tag-name>` | 删除本地标签 | |
| `git push --delete <remote> <tag-name>` | 删除远程标签 | |
| 文件权限 | 可执行（`755`）和不可执行（`644`） | |
| `git gc` | 运行垃圾回收 | 所有未引用的对象将被删除，且无法找回 |

## 总结

在本文中，我们介绍了 Git 中的其他用法。这是 Git 教程系列的最后一篇文章。在未来，我可能会写一些更深入
Git 的文章。我希望你从这个系列中学到了一些有用的东西，享受你和 Git
一起的时光。如果你在之后遇到了一些 Git
的问题，你可以查看官方文档来解决问题（如果你掌握了这个系列中的内容，你应该能够轻松地理解官方文档）。

## 版权

你可以将本文用于任何目的，只要你在使用的地方明确标注原作者和链接
(<https://lau.yeeyu.org/blog-zh-cn/git-usage-misc-zh-cn>)。请忽略页脚处的版权声明。
