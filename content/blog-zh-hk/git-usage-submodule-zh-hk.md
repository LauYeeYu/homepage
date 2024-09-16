+++
title = 'Git 進階用法：子模塊'
date = 2024-09-11T12:00:00-07:00
draft = false
tags = ["git", "git-zh-hk", "tutorial", "tutorial-zh-hk"]
showToc = true
+++

在[上一篇文章](/blog-zh-hk/git-usage-merging-zh-hk)中，我們介紹了
 Git 分支合併相關用法。在本文中，我們將介紹如何在 Git 中管理子模塊。

<!--more-->

*本文章有其他語言的版本：[English（英文）](/blog/git-usage-submodule-en)及[简体中文（簡體中文）](/blog-zh-cn/git-usage-submodule-zh-cn)。如果你更熟悉這些語言，建議閱讀這些語言的版本。*

## 為甚麼需要子模塊？

如果你從沒有聽說過子模塊，你可能會想，為甚麼我們需要它們。一個常見的用例是你的項目依賴於其他項目，而這些項目應該被單獨管理，這時，使用子模塊來分割你的項目和項目依賴就顯得非常重要了。

比如，你正在開發一個 Android 應用，其中包含一些 C/C++ 庫。這些庫是由其他團隊開發的，你希望它們以某種方式出現在你的項目中。顯然，你可以使用腳本來下載（或更新）這些庫，每次構建項目時都需要這樣做。然而，這並不是一個好的辦法。你可能會遇到諸如版本衝突、忘記更新庫等問題。透過使用子模塊，你可以更有條理地管理這些庫。

另一個例子是使用細粒度控制來管理你的項目。你可能為每個庫使用一個倉庫，為你的項目使用一個倉庫。透過子模塊，你可以管理這些倉庫之間的依賴關係。其他人可以很容易地使用你的庫，而不需要複製整個龐大的項目。

## 子模塊和類似工具

有很多工具提供類似子模塊的功能。然而，Git 的設計原則使得方便地管理子模塊變得具有挑戰性。不同的工具有不同的權衡。以下是一些流行的工具：

- [Git Submodule][git-submodule];
- [Git Subtree][git-subtree];
- [Gitslave][gitslave].

[git-submodule]: https://git-scm.com/docs/git-submodule
[git-subtree]: https://github.com/apenwarr/git-subtree
[gitslave]: https://gitslave.sourceforge.io/

### Git Submodule

Git Submodule 是 Git 官方的子模塊管理工具。

優點：

- 幾乎所有用戶均安裝了 git submodule（因為是在 Git 官方項目中的）。
- 修改者容易使用。

缺點：

- 用戶使用起來困難（需要手動更新或初始化）。
- 如果子模塊倉庫不可用，你將無法更新或初始化子模塊。這使得倉庫更像是一個「中心化」倉庫，因為它依賴於每個子模塊的單個 URL。

### Git Subtree

[Git Subtree][git-subtree] 是一個將子項目包含在主項目的子目錄中的項目，可選擇包含子項目的整個歷史記錄。

優點：

- 使得無需更改的用戶更容易使用。
- 子模塊包含在倉庫中。

缺點：

- 在主倉庫中佔用更多空間。
- 修改者使用起來困難。

### Gitslave

[Gitslave][gitslave] 是一個項目，它可以幫助從多個單獨的 Git 倉庫中組裝一個元項目。

優點：

- 修改者使用起來非常容易。

缺點：

- 用戶必須安裝 gitslave 才能使用子模塊。

### 工具總結

如你所見，所有可能的解決方案都有其缺點。對我來說，強制每個用戶安裝一個非官方的
Git 工具是不可接受的。如果用戶可以簡單地透過 README
中的說明來管理，那麼這是可以接受的。此外，我不希望工具顯著增加開發者的負擔。因此，我更傾向於使用
Git 子模塊。你可以選擇適合你需求的工具。在本文中，我們僅介紹 Git 子模塊的用法。

## 子模塊的常見需求

### 用戶

對於用戶來說，他們可能希望：

- 複製帶有子模塊的主倉庫（參見[複製帶有子模塊的倉庫](#複製帶有子模塊的倉庫)）；
- 初始化現有倉庫中的子模塊（參見[初始化或更新子模塊到當前提交的版本](#初始化或更新子模塊到當前提交的版本)）；
- 將子模塊更新到當前提交指定的版本（Git 默認不會在拉取時更新子模塊）（參見[初始化或更新子模塊到當前提交的版本](#初始化或更新子模塊到當前提交的版本)）；
- 列出倉庫中的所有子模塊（參見[列舉子模塊](#列舉子模塊)）。

### 開發者

對於開發者來說，他們可能希望：

- 向倉庫中添加子模塊（參見[添加子模塊](#添加子模塊)）；
- 將子模塊更新到特定版本（參見[將子模塊更新到特定版本](#將子模塊更新到特定版本)）；
- 從倉庫中刪除子模塊（參見[刪除子模塊](#刪除子模塊)）；
- 修改子模塊的 URL（參見[修改子模塊的 URL](#修改子模塊的-url)）；
- 列出倉庫中的所有子模塊（參見[列舉子模塊](#列舉子模塊)）。

## 複製帶有子模塊的倉庫

如果你要複製一個帶有子模塊的倉庫，你可以在原始參數中添加 `--recurse-submodules` 選項：

```bash
git clone <repo> [dir] --recurse-submodules
```

## 初始化或更新子模塊到當前提交的版本

> [!TIP]
> 如果你希望在拉取操作時更新子模塊，你可以在原有參數的基礎上使用 `--recurse-submodules` 選項。

如果你希望將子模塊初始化或更新到當前提交指定的版本，你可以使用以下命令：

```bash
git submodule update --init --recursive
```

這將遞迴地將子模塊更新到當前提交指定的版本（即子模塊的子模塊也將被更新）。

## 列舉子模塊

要列舉倉庫中的所有子模塊，你可以簡單地使用以下命令：

```bash
git submodule
```

## 添加子模塊

要向你的倉庫添加一個子模塊，你可以使用以下命令：

```bash
git submodule add <repo> [dir]
```

為了方便用戶，建議使用 `https` 協議而不是 `ssh` 協議。這將允許用戶在沒有 Git
托管服務帳戶的情況下使用子模塊。如果你希望在你的倉庫中使用 `https`，但在實際開發中使用
`ssh`，你可以在 Git 中配置 URL 替換。例如，如果你使用 GitHub，你可以使用以下命令在
Git 遇到 `https` 協議時配置替換（如果你只想將此規則應用於當前倉庫，你可以刪除
`--global` 選項）：

```bash
git config --global url.git@github.com:.insteadOf https://github.com/
```

## 將子模塊更新到特定版本

作為開發者，你可能希望在新提交中將子模塊更新到特定版本。

為了實現這一目的，你應該：

1. 進入子模塊；
2. 進行類似主倉庫的更改（目的是將 `HEAD` 更改到你想要的提交），請記住如果新提交尚未出現在遠程倉庫中，請推送更改；
3. 離開子模塊；
4. 將子模塊中的更改添加到主倉庫中（即，`git add <submodule>`）；
5. 提交主倉庫中的更改（你可以推送更改）。

子模塊中的操作（步驟 2）與主倉庫中的操作非常相似。唯一的區別是你可能會在子模塓中處於分離的 `HEAD` 狀態。

> [!TIP]
> 你可以在 `git push` 中使用 `--recurse-submodules=on-demand`
> 選項來按需推送子模塊中的更改，即使用 `git push --recurse-submodules=on-demand`
> 來防止你忘記推送新提交。

如果你要將子模塓更新到最新提交，你可以在主倉庫中使用以下命令：

```bash
git submodule update --remote
```

## 刪除子模塊

你可以先[列舉子模塊](#列舉子模塊)。然後在主倉庫中使用以下命令刪除跟踪信息：

```bash
git submodule deinit <path-to-submodule>
```

接著，刪除子模塊目錄：

```bash
git rm <path-to-submodule>
```

你還應該刪除子模塊的暫存更改：

```bash
git rm --cached <path-to-submodule>
```

最後，在主倉庫中提交更改。

## 修改子模塊的 URL

如果你想修改子模塓的 URL，你應該修改主倉庫根目錄中的 `.gitmodules` 檔。

然後，使用以下命令更新子模塊：

```bash
git submodule sync
```

最後，在主倉庫中提交更改。

## 回顧

現在，讓我們回顧一下本文和前面文章中介紹的用法。

| 命令 | 描述 | 備註 |
| --- | --- | --- |
| `git init` | 初始化一個新倉庫 | 當前目錄不能是一個已存在的倉庫 |
| `git clone <url>` | 複製一個已存在的倉庫 | 目標目錄不能存在；使用 `--recurse-submodules` 来一併複製所有子倉庫 |
| `git add <path>` | 將 `<path>` 中的更改添加到暫存區 | 提交只會應用於添加到暫存區的檔案 |
| `git status` | 檢查倉庫的狀態 | 推薦在提交前使用 |
| `git diff` | 檢視差異 | 使用 `--cached` 檢視暫存區的差異；可以指定路徑 |
| `git commit` | 進行一次提交 | 如果消息很短，使用 `-m`；經常提交（檔案的更改在大多數情況下很容易找回） |
| `git log` | 檢視提交歷史 | 使用 `--oneline` 讓每個提交僅佔用一行；使用 `--graph` 檢視提交圖 |
| `git remote add origin <url>` | 添加一個遠程倉庫 | 遠程倉庫的默認名稱是 `origin`；你可能需要遵循第一次推送時的指示（例如，`git push --set-upstream origin master`） |
| `git remote show` | 列出遠程倉庫 | 使用 `-v` 檢視更多細節；你可以透過名稱指定倉庫 |
| `git remote set-url origin <url>` | 修改遠程倉庫地址 | |
| `git remote remove origin` | 移除遠程倉庫 | |
| `git remote rename origin new-origin` | 重命名遠程倉庫 | |
| `git push` | 將更改推送到遠程倉庫 | 如果快進合併策略失敗，使用 `--force` 強制推送（這會丟棄一些提交） |
| `git fetch` | 從遠程倉庫抓取更改 | 如果你想抓取非默認分支，使用 `git fetch <repo>` |
| `git pull` | 從遠程倉庫抓取並合併更改 | 默認的合併策略可能不同 |
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

## 總結

在本文中，我們介紹了 Git 中的子模塊管理。在下一篇文章中，我們將介紹 Git 中的其他部分。希望你享受和 Git 一起度過的時光！

## 版權

你可以將本文用於任何目的，只要你在使用的地方明確標註原作者和連結
(<https://lau.yeeyu.org/blog-zh-hk/git-usage-submodule-zh-hk>)。請忽略頁腳處的版權聲明。
