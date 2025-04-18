+++
title = 'Git 進階用法：其他'
date = 2024-09-15T12:00:00-07:00
lastmod = 2025-04-10T20:00:00+08:00
draft = false
tags = ["git", "git-zh-hk", "tutorial", "tutorial-zh-hk"]
series = "git-tutorial"
seriesAnnotation = "5/5"
showToc = true
categories = ['software']
+++

在[上一篇文章](/blog-zh-hk/git-usage-submodule-zh-hk)中，我們介紹了 Git
子模塊的用法。在本文中，我們將介紹 Git 其他用法。

<!--more-->

*本文章有其他語言的版本：[English（英文）](/blog/git-usage-misc-en)及[简体中文（簡體中文）](/blog-zh-cn/git-usage-misc-zh-cn)。如果你更熟悉這些語言，建議閱讀這些語言的版本。*

## 標籤

標籤用於標記特定的提交，它們通常用於標記版本發布。標籤有兩種類型：輕量標籤
(lightweight tags) 和帶註釋的標籤 (annotated tags)。

輕量標籤只有一個名稱，指向一個提交，而帶註釋的標籤除此之外還有一個附屬的消息。

### 語義化版本標籤

如果你想為一個版本發布添加一個標籤，建議使用[語義化版本][semver]格式，這在軟體工程中被廣泛使用。

[semver]: https://semver.org/

在語義化版本格式中，一個版本標籤的名稱應該是 `vX.Y.Z`，其中 `X`、`Y` 和 `Z` 是整數。`X` 是主版本號，`Y` 是次版本號，`Z` 是修訂版本號。

當你發布一個版本時，

- 如果更改不向下兼容，遞增主版本號 `X`；
- 如果向下兼容地添加新功能，遞增次版本號 `Y`；
- 如果向下兼容地修復錯誤（沒有新功能），遞增修訂版本號 `Z`。

如果你遞增了某個版本號，那麼接下來的幾個數字應該被重置為 0。例如，如果你發布了一個次版本號版本，從 `v1.1.4` 到 `v1.2.Z`，那麼 `Z` 應該是 0。

對於小於 1 的主版本號，即 `v0.Y.Z`，它被認為是一個初始開發階段。在這種情況下，不向下兼容的更改可能隨時發生。但是在 `v1.0.0` 之後，這被認為是一個穩定的里程碑，版本控制應該遵循語義化版本規則，尤其是兼容性。

### 創建標籤

對於輕量標籤，你可以使用以下命令：

```bash
git tag <tag-name> <commit-id>
```

如果你不指定提交 ID，標籤將指向最新的提交。我們經常會這樣給最新的提交創建標籤。

> 例如，如果你想為最新的提交創建一個 `v1.0.0` 的標籤，你可以使用以下命令：
>
> ```bash
> git tag v1.0.0
> ```

對於帶註釋的標籤，你可以使用以下命令：

```bash
git tag -a <tag-name> -m <message> <commit-id>
```

同樣，你可以省略提交 ID 來指向最新的提交。

> 例如，如果你想為最新的提交創建一個 `v1.0.0` 的帶註釋標籤，消息是 `Supporting multiple platforms`，你可以使用以下命令：
>
> ```bash
> git tag -a v1.0.0 -m "Supporting multiple platforms"
> ```

### 推送標籤

默認情況下，標籤不會在 `git push` 命令中被推送。如果你想推送標籤，你應該在
`git push` 命令中添加 `--tags`。例如：

```bash
git push --tags
```

### 列出標籤

你可以使用以下命令列出所有標籤：

```bash
git tag -l
```

### 刪除本地標籤

你可以使用以下命令刪除本地標籤：

```bash
git tag -d <tag-name>
```

> 例如，如果你想刪除 `v1.0.0` 標籤，你可以使用以下命令：
>
> ```bash
> git tag -d v1.0.0
> ```

### 刪除遠端標籤

你可以使用以下命令刪除遠端標籤：

```bash
git push --delete <remote-name> <tag-name>
```

> 例如，如果你想刪除遠端倉庫 `origin` 中的 `v1.0.0` 標籤，你可以使用以下命令：
>
> ```bash
> git push --delete origin v1.0.0
> ```

## 檔案權限

在 Git 中，有兩種檔案權限：可執行和不可執行。

> [!TIP]
> 如果你不使用類 Unix 系統（Windows 不是 Unix 系統，Linux 和 macOS 是），檔案權限系統未必可以正常工作。

對於可執行檔案，確切的權限是
`755`（這意味著任何人都可以讀取和執行檔案，但只有所有者可以寫入）。對於不可執行檔案，確切的權限是
`644`（這意味著任何人都可以讀取檔案，但只有所有者可以寫入，沒有人可以執行它）。

## 符號連結

在類 Unix 系統中，符號連結被經常使用。符號連結指向一個檔案，透過路徑（相對或絕對）指定，它表現得就像是它指向的地方。如果指向的地方無效，那麼它將是一個損壞的符號連結，但這本身不會引起任何問題。

Git 支援符號連結，但如果它指向倉庫之外的東西，這可能會導致指向的檔案不存在或權限不正確。因此，你應該避免使用符號連結指向倉庫外的內容。此外，在
Windows 上，符號連結可能存在各種各樣的問題，因此我的建議是除非你確定它不會引起任何問題，否則應避免使用符號連結，例如，你只在
Linux 上使用它，並且指向倉庫中的檔案。

## 檔案和目錄

在 Git 中，沒有目錄，只有檔案。對於每個檔案，它都有一個從倉庫根目錄的路徑。例如，`a/b.txt` 表示一個檔案 `b.txt` 在目錄 `a` 中。因此，如果你想添加一個目錄，你應該在該目錄中添加一個檔案。

## 重命名檔案

如果你想移動一個檔案，這實際上等同於重命名檔案。

你可以移動檔案並添加舊檔案和新檔案的共同祖先目錄，以便讓 Git 跟踪這個更改。在提交時，Git 將嘗試找到重命名關係（但有時可能會失敗或有錯誤的結果）。

## Git 別名

一直輸入 `git commit --amend` 或 `git pull --recurse-submodules` 之類的長命令非常麻煩。Git 提供了一種便捷的方式來為你經常使用的長命令或難以記住的命令創建一個別名。

Git 別名允許你為一個長命令創建一個簡短的別名。例如，你可以將 `amend` 設定為 `commit --amend`，然後 `git amend` 將等同於 `git commit --amend`。這類似於 shell 中的別名。

Git 別名可以透過命令或編輯配置檔案（`~/.gitconfig`）來配置。推薦使用命令來配置別名。

要使用命令配置別名，你可以使用以下命令：

```bash
git config --global alias.<alias-name> <command>
```

如果你只想為當前倉庫配置別名，你應該刪除 `--global` 選項。

以下是一些我推薦的別名：

1. `amend`：修改最後一次提交

    ```bash
    git config --global alias.amend 'commit --amend'
    ```

2. `unstage`：取消暫存某些檔案

    ```bash
    git config --global alias.unstage 'reset HEAD --'
    ```

3. `full-pull`：拉取並更新子模塊

    ```bash
    git config --global alias.full-pull 'pull --recurse-submodules'
    ```

4. `full-push`：推送並更新子模塊

    ```bash
    git config --global alias.full-push 'push --recurse-submodules=on-demand'
    ```

5. `log-graph`：以圖形化的方式顯示提交歷史

    ```bash
    git config --global alias.log-graph 'log --graph --oneline --decorate'
    ```

6. `fpush`：強制推送（通常在修改提交後使用）

    ```bash
    git config --global alias.fpush 'push --force'
    ```

## 全局 Git Ignore

每個作業系統可能都有一些自動生成的檔案，你不想將它們包含在倉庫中，比如
macOS 中臭名昭著的 `.DS_Store`。在每個倉庫中添加這些檔案到
`.gitignore` 中真的很麻煩，對你和你的團隊成員來說都是如此。對於你來說，你需要每次創建一個新倉庫時都這樣做。對於你的團隊成員來說，如果他們不使用相同的作業系統，這很可能是不必要的。

Git 允許你設置一個全局檔案來忽略檔案。它在 `core.excludesFile`
變量中配置。你可以使用以下命令設置它：

```bash
git config --global core.excludesFile <path>
```

如果你不設置 `core.excludesFile`，默認值是 `$XDG_CONFIG_HOME/git/ignore`
（如果沒有設置 `$XDG_CONFIG_HOME`，那麼會採用
`$HOME/.config/git/ignore`）。這可能不會在 Windows 或 macOS 上生效。

> [!TIP]
> GitHub 的官方 `.gitignore` 模板提供了很多系統特定的 `.gitignore`
> 檔案（例如，`Global` 目錄中的檔案）。你可以在[GitHub gitignore 倉庫][gitignore]中找到它們。

[gitignore]: https://github.com/github/gitignore

## Git 垃圾回收

> [!WARNING]
> Git 垃圾回收將刪除所有未引用的對象（例如，因為重置而被棄用的提交）。垃圾回收後，你將無法恢復這些對象。因此，在使用垃圾回收時，你應該格外小心。一般來說，你不應該在自己的項目中使用垃圾回收；只有在他人的項目中使用，或者當你確定不需要未引用的對象時才使用。

通常情况下，Git 將保留所有對象，即使它們沒有被任何分支或標籤引用。正如我之前所說，大多數情況下，提交的檔案是可以輕鬆找回的。然而，這將佔用
`.git` 目錄中的大量空間。因此，當你想要刪除這些未引用的對象時，Git 允許你通過垃圾回收來釋放空間。

## 實用第三方工具

在這裡，我會推薦一些我認為非常有用的第三方工具。如果它們對你有很大幫助，你可以安裝它們。當然，不安裝它們也不會有什麼關係，因為 Git 本身已經足夠強大了。

### Git Delta

[Git Delta][git-delta] 為你提供了一個清晰直觀的變更視圖。它極大地提高了
diff 輸出的可讀性。它還可以識別合併衝突。配置了 Git Delta
之後，就可直接使用。`git diff`
的輸出將會很方便直觀。此外，不用擔心修補檔的格式，因為它只影響顯示時的輸出，當你生成修補檔時，Git
仍然會使用原始格式。

[git-delta]: https://dandavison.github.io/

Git Delta 可以應用於 `diff`、`blame`、`grep` 和 `show` 等命令。

### Git 倉庫內搜索工具

`git grep` 的輸出不是很方便，因為它只顯示檔案名和匹配行。如果你想要更好的搜索工具，你可以使用 [ripgrep (rg)][ripgrep] 和 [the silver searcher (ag)][ag]。

[ripgrep]: https://github.com/BurntSushi/ripgrep
[ag]: https://github.com/ggreer/the_silver_searcher

## 回顧

現在，讓我們回顧一下所有 Git 教程文章中介紹的用法（對，這就是最後一篇）。下表應該對你找到你需要的命令非常有幫助和高效（不常用的命令可能會被省略，例如配置別名命令）。

| 命令 | 描述 | 備註 |
| --- | --- | --- |
| `git init` | 初始化一個新倉庫 | 當前目錄不能是一個已存在的倉庫 |
| `git clone <url>` | 複製一個已存在的倉庫 | 目標目錄不能存在；使用 `--recurse-submodules` 来一併複製所有子倉庫 |
| `git add <path>` | 將 `<path>` 中的更改添加到暫存區 | 提交只會應用於添加到暫存區的檔案 |
| `git status` | 檢查倉庫的狀態 | 推薦在提交前使用 |
| `git diff` | 檢視差異 | 使用 `--cached` 檢視暫存區的差異；可以指定路徑 |
| `git commit` | 進行一次提交 | 如果消息很短，使用 `-m`；經常提交（檔案的更改在大多數情況下很容易找回） |
| `git log` | 檢視提交歷史 | 使用 `--oneline` 讓每個提交僅佔用一行；使用 `--graph` 檢視提交圖 |
| `git remote add origin <url>` | 添加一個遠端倉庫 | 遠端倉庫的默認名稱是 `origin`；你可能需要遵循第一次推送時的指示（例如，`git push --set-upstream origin master`） |
| `git remote show` | 列出遠端倉庫 | 使用 `-v` 檢視更多細節；你可以透過名稱指定倉庫 |
| `git remote set-url origin <url>` | 修改遠端倉庫地址 | |
| `git remote remove origin` | 移除遠端倉庫 | |
| `git remote rename origin new-origin` | 重命名遠端倉庫 | |
| `git push` | 將更改推送到遠端倉庫 | 如果快進合併策略失敗，使用 `--force` 強制推送（這會丟棄一些提交） |
| `git fetch` | 從遠端倉庫抓取更改 | 如果你想抓取非默認分支，使用 `git fetch <repo>` |
| `git pull` | 從遠端倉庫抓取並合併更改 | 默認的合併策略可能不同 |
| `.gitignore` | 忽略檔案 | 使用 `.gitignore` 忽略你不想提交的檔案 |
| `git branch <branch>` | 添加一個新分支 | 新分支名稱不能存在；另一種解決方案：`git checkout -b <branch>` |
| `git switch <branch>` | 切換到另一個分支 | 對於其他引用，請加上 `--detach`；另一種解決方案：`git checkout <branch>` |
| `git merge <branch>` | 合併分支 | 使用 `--ff-only` 強制快進合併；使用 `--squash` 合併但不創建新提交或避免創建合併提交 |
| `git rebase <branch>` | 重定基底分支 | 使用 `-i` 或 `--interactive` 使用更多功能 |
| `<rev>~<n>` | 獲取 `<rev>` 的第 `n` 代祖先提交 | |
| `<rev>^<n>` | 獲取 `<rev>` 的第 `n` 個父提交 | `^` 單獨表示「父提交」 |
| `git stash` | 暫時儲藏更改 | 使用 `pop`、`clear` 和 `list` 檢索、清除所有、列出儲藏的更改 |
| `git merge-base --all <commit>...` | 獲取共同祖先 | |
| `git restore <path>` | 從提交中恢復檔案到工作目錄 | 使用 `--source=<tree>` 指定源；另一種解決方案：`git checkout <path>` |
| `git restore --staged <path>` | 取消暫存 | 另一種解決方案：`git reset HEAD <path>` |
| `git clean` | 清理工作目錄 | 使用 `-i` 交互模式；使用 `-f` 強制刪除 |
| `git reset <commit>` | 更改分支的 HEAD 提交 | 選項：`--soft`、`--mixed`、`--hard`、`--merge` 或 `--keep` |
| `git apply <patch>` | 應用修補檔 | |
| `git show <commit>` | 檢視提交 | |
| `git revert <commit>...` | 回退提交 | 使用 `--no-commit` 或 `-n` 避免創建多個提交 |
| `git cherry-pick <commit>...` | 遴選提交 | 使用 `--no-commit` 或 `-n` 避免創建多個提交 |
| `git grep <pattern> <path>...` | 在檔案中查找特定內容 | 如果未指定路徑，將在整個倉庫中搜索 |
| `git reflog` | 檢視引用日志 | |
| `git submodule` | 列舉子模塊 | |
| `git submodule update --init --recursive` | 初始化或更新子模塊到當前提交的版本 | |
| `git submodule add <repo> [dir]` | 添加子模塊 | |
| `git submodule update --remote` | 將子模塊更新到最新提交 | |
| `git submodule deinit <path-to-submodule>` | 刪除子模塊跟踪信息 | |
| `git submodule sync` | 同步子模塊 | |
| `git tag <tag-name>` | 添加一個輕量標籤 | |
| `git tag -a <tag-name> -m <message>` | 添加一個帶註釋的標籤 | |
| `git push --tags` | 推送標籤 | |
| `git tag -l` | 列出標籤 | |
| `git tag -d <tag-name>` | 刪除本地標籤 | |
| `git push --delete <remote> <tag-name>` | 刪除遠端標籤 | |
| 檔案權限 | 可執行（`755`）和不可執行（`644`） | |
| `git gc` | 執行垃圾回收 | 所有未引用的對象均會被移除，且無法恢復 |

## 總結

在本文中，我們介紹了 Git 中的其他用法。這是 Git 教程系列的最後一篇文章。在未來，我可能會寫一些更深入
Git 的文章。我希望你從這個系列中學到了一些有用的東西，享受你和 Git
一起的時光。如果你在之後遇到了一些 Git
的問題，你可以查看官方檔案來解決問題（如果你掌握了這個系列中的內容，你應該能夠輕鬆地理解官方檔案）。

## 版權

你可以將本文用於任何目的，只要你在使用的地方明確標註原作者和連結
(<https://lau.yeeyu.org/blog-zh-hk/git-usage-misc-zh-hk>)。請忽略頁腳處的版權聲明。
