+++
title = 'Git 基礎用法'
date = 2024-08-05T12:00:00-07:00
draft = false
tags = ["git", "git-zh-hk", "tutorial", "tutorial-zh-hk"]
showToc = true
+++

在[上一篇文章](/blog-zh-hk/git-basics-zh-hk/)中，我們討論了版本控制系統的通用概念和
Git 的設計。在本文中，我們將介紹 Git 的基礎用法。

<!--more-->

*本文章有其他語言的版本：[English（英文）](/blog/git-basic-usage-en)及[简体中文（簡體中文）](/blog-zh-cn/git-basic-usage-zh-cn)。如果你更熟悉這些語言，建議閱讀這些語言的版本。*

由於 Git 的功能過於豐富，因此我們只會在本文中介紹每個 Git
用戶都應該牢記的基礎功能。我們會在之後的文章中介紹高級用法。

本文會在配置 Git 部分盡可能列出所有可行的選擇。如果你並不熟悉這些選擇，你可以直接採用推薦的方法。如果你發現這些選擇對你來說過於複雜，請忽略除推薦方案以外的選擇，不必在這些部分上浪費時間。

## 配置 Git

### 安裝 Git

在 Windows 上，如果你安裝了 WSL（Windows Subsystem for Linux），強烈建議你在 WSL 中使用 Git。

對於 Linux（或 WSL）用戶，你可以透過系統的包管理器安裝 Git。例如，在 Ubuntu 上，你可以執行：

```bash
sudo apt update && sudo apt install git
```

對於 macOS 用戶，你可以透過 Homebrew 安裝 Git：

```bash
brew install git
```

你可以透過執行以下命令檢查 Git 是否已安裝：

```bash
git --version
```

執行後，你應當可以看到你已安裝的 Git 版本。

### 配置身份信息

在你使用 Git 之前，你需要設定你的身份信息。這些信息會被記錄在你的每次提交中。請注意，一旦你將提交推送到公共倉庫，這些信息就會公開。如果你不想暴露你的個人信息，建議你不要使用真實姓名。

要設定你的身份信息，請執行：（**請將 `Your Name` 和 `someone@example.org`
替換為你的姓名和電子郵件地址！**）

```bash
git config --global user.name "Your Name"
git config --global user.email "someone@example.org"
```

上述兩個命令將分別設定你的姓名和電子郵件地址。

### 設定 Git 託管平台

通常，我們會使用 Git 託管平台（例如 GitHub、GitLab
等）來託管我們的倉庫。你可以透過這些服務與他人分享你的代碼、備份你的代碼，並與他人合作。

你可以使用 SSH 協議或 HTTPS 協議與遠程倉庫交互。這裡推薦使用 SSH
協議，因為它更方便。如果你選擇使用 SSH 協議，你需要使用類似
`git@github.com:user/repo` 的連結，而不是類似
`https://github.com/user/repo` 的連結。

#### 設定 SSH 密鑰對

在 SSH 協議中，用於驗證的信息是密鑰對，而不是密碼。因此，要使用 SSH
協議，你需要生成一個 SSH 密鑰對。請執行以下代碼以生成一個 SSH 密鑰對：（**請將
`someone@example.org` 替換為你的電子郵件地址！**）

```bash
ssh-keygen -t ed25519 -C "someone@example.org"
```

> [!NOTE]
> 如果你的系統不支援 Ed25519 算法，你可以使用 RSA 算法執行：（同樣，請將
> `someone@example.org` 替換為你的電子郵件地址！）
>
> ```bash
> ssh-keygen -t rsa -b 4096 -C "someone@example.org"
> ```

執行上述命令後，它會詢問你密鑰對的保存位置。請**直接按下回車**，使用默認路徑，不要指定保存密鑰對的路徑；否則，後續使用密鑰對會非常不方便。

接著，它會詢問你是否要設定一個密碼。你可以設定一個密碼，也可以留空。你之後可以隨時更改密碼。

#### 將 SSH 密鑰添加到 Git 託管平台

在你註冊了 Git 託管平台的帳戶後，你可以將你的 SSH 公鑰添加到該服務。要獲取你的
SSH 公鑰，請執行：

```bash
cat ~/.ssh/id_ed25519.pub
```

> [!NOTE]
> 如果你使用的是 RSA 算法，請執行：
>
> ```bash
> cat ~/.ssh/id_rsa.pub
> ```

然後，複製輸出的內容，並將其粘貼到 Git 託管平台的 SSH
密鑰設定頁面。例如，你可以將其添加到[GitHub 的 SSH 密鑰設定頁面][github-ssh-key-settings]。

[github-ssh-key-settings]: https://github.com/settings/keys/

請記住，**請始終保護好你的私鑰（沒有 `.pub` 後綴的檔案），並且不要將其暴露給他人。只與他人分享公鑰（帶有 `.pub` 後綴的檔案）。**

如果你切換到了新設備、重裝了系統，或者不小心刪掉了密鑰對，你需要重新生成一個 SSH
密鑰對，並將新的公鑰添加到 Git 託管平台。私鑰一旦丟失，無法找回。

你可以透過執行以下命令測試你的密鑰是否已成功添加到 GitHub：（**注意：第一次執行時，你可能會被要求確認
GitHub 的指紋。請確認後輸入 "yes" 並按下回車鍵。**）

```bash
ssh git@github.com
```

## 基礎用法

在本節中，我們將介紹每個 Git 用戶都應該牢記的基本用法，以便更有效、更舒適地使用 Git。

大多數新用戶可能會對 Git 的工作流程感到困惑。在這裡，我們將詳細介紹
Git 的用法，並提供一些練習以測試你是否已經掌握了基本用法。

### 創建倉庫

要創建一個新倉庫，你可以首先創建一個新目錄作為 Git 倉庫。如果你想透過命令行創建目錄，你可以執行：（請將 `<repo-name>` 替換為你的倉庫名！）

```bash
mkdir <repo-name>
```

然後，你可以執行以下命令初始化 Git 倉庫：

```bash
git init
```

### 複製倉庫

我們時常會將現有倉庫複製到本地機器上（例如，使用他人的倉庫，或者先在服務器上創建自己的倉庫）。要複製一個倉庫，你可以執行：（`<repository-url>` 是你想複製的倉庫的 URL）

```bash
git clone <repository-url>
```

> 例如，要複製 GitHub 的 gitignore 倉庫，你可以執行以下命令：
>
> ```bash
> git clone git@github.com:github/gitignore.git
> ```

### 將檔案添加到暫存區

提交是 Git 的基礎操作。在你提交之前，你需要將更改添加到暫存區（暫存區是一個緩衝區，用於更乾淨、更細粒度地提交）。如果你不知道或忘記了什麼是暫存區，你可以閱讀[上一篇文章](/blog-zh-hk/git-basics-zh-hk/#工作區-workspace-和暫存區-staging)中的工作區和暫存區部分。

要將更改添加到暫存區，你可以執行：（一次可以添加多個檔案）

```bash
git add <file>...
```

> 例如，如果你想將 `README.md` 檔案的更改添加到暫存區，你可以執行：
>
> ```bash
> git add README.md
> ```

如果你想將當前目錄及其子目錄中的所有更改添加到暫存區，你可以執行：

```bash
git add .
```

### 檢查狀態

要查看哪些檔案已添加到暫存區，你可以執行以下命令查看已暫存和未暫存的更改：

```bash
git status
```

強烈建議在提交之前檢查狀態，以確保你提交的是正確的更改，尤其是在執行 `git add .` 之後。

`git status` 命令會顯示很多信息和提示。如果你對下一步該做什麼感到困惑，你可以隨時嘗試執行
`git status` 以獲取一些提示，並了解你的 Git 倉庫的狀態，因此，`git status` 基本是 Git
中最經常使用的。

### 顯示工作區與當前提交的差異

在提交之前，你可能想查看工作區和當前提交之間的差異。要顯示所有差異，你可以執行：

```bash
git diff
```

Git 會以高亮顯示的方式顯示每個更改檔案中的差異（以及更改周圍的上下文）。

如果你只想查看特定檔案或目錄中的差異，你可以執行：

```bash
git diff <file>...
```

> 例如，如果你想查看 `README.md` 檔案和 `src` 目錄中的差異，你可以執行：
>
> ```bash
> git diff README.md src
> ```

### 顯示暫存區中的差異

如果你想查看暫存區和當前提交之間的差異，你可以在 `git diff` 命令中添加 `--cached` 選項：

```bash
git diff --cached
```

如果你只想查看特定檔案或目錄中的差異，你可以執行：

```bash
git diff --cached <file>...
```

> 例如，如果你想查看 `README.md` 檔案和 `src` 目錄中的差異，你可以執行：
>
> ```bash
> git diff --cached README.md src
> ```

### 提交更改

在將更改添加到暫存區後，你可以執行：

```bash
git commit
```

這個命令會打開默認的文本編輯器（通常是 Vim 或
Nano），讓你編寫提交訊息。如果你將提交訊息留空，Git
會中止提交。在文本編輯器界面中，Git 會向你展示一些關於提交的提示（如你將要提交的更改）。

如果提交訊息很短，你可以直接在命令行中編寫提交訊息：（**請將 `Your commit message` 替換為你的提交訊息！**）

```bash
git commit -m "Your commit message"
```

> 例如，如果你想提交的信息為 `docs: add README.md`，你可以執行：
>
> ```bash
> git commit -m "docs: add README.md"
> ```

已提交的檔案在大多數情況下都可以輕鬆找回，而未提交的更改可能會丟失。因此，**強烈建議**你**經常提交**。一個好的方法是，如果你對倉庫進行了更改，每天至少提交一次。通常，細粒度的提交在很多方面都比大的提交更好（如遴選、回滾、理解更改等）。

關於提交訊息應該如何編寫，很多人使用
[Conventional Commits][conventional-commits]
風格。這種風格使得更容易、更清晰地理解提交中的更改。

[conventional-commits]: https://www.conventionalcommits.org/

### 顯示提交歷史

要查看提交歷史，你可以執行：

```bash
git log
```

你可以使用以下參數：

- `--oneline`：每個提交僅顯示一行；
- `--graph`：顯示提交關係圖。

### 显示提交历史

要查看提交历史，你可以执行：

```bash
git log
```

你可以使用以下参数：

- `--oneline`：每个提交仅显示一行；
- `--graph`：显示提交关系图。


### 在 Git 倉庫裡的任何地方使用 Git 子命令

只要你在 Git 倉庫中，你就可以在倉庫的任何子目錄中使用 Git
子命令。例如，你可以在倉庫的根目錄執行 `git status`，亦可在其子目錄執行。這對於管理倉庫非常方便。

### 設定遠程倉庫

要設定遠程倉庫，你可以執行：

```bash
git remote add origin <repository-url>
```

這個命令會添加一個名為 `origin` 的遠程倉庫，URL 為
`<repository-url>`。`origin`
是遠程倉庫的默認名稱。你也可以使用其他名字作為遠程倉庫的名稱。

> 例如，如果你想添加一個 URL 為 `git@github.com:github/gitignore.git`
> 的遠程倉庫，你可以執行：
>
> ```bash
> git remote add origin git@github.com:github/gitignore.git
> ```

第一次推送時，你會看到一個錯誤消息，告訴你要設定上游分支。你可以按照錯誤消息中的說明設定上游分支。例如，你可以執行以下命令將上游分支設定為 `master`：

```bash
git push --set-upstream origin master
```

### 推送更改到遠程倉庫

在提交後，你可以執行：

```bash
git push
```

將更改推送到遠程倉庫。在執行命令後，仔細查看輸出。如果看到任何錯誤消息，你應該仔細閱讀並嘗試理解錯誤是什麼。除了錯誤消息外，Git
還會提供一些解決問題的提示。

例如，如果遠程倉庫無法使用快進合併 (fast-forward)
你的更改，這意味著遠程分支的頭提交不是你的提交的祖先。在這種情況下，你需要在本地合併這些更改（Git
不允許你在遠程服務器上合併）或者強制推送你的更改到遠程倉庫。要強制推送你的更改，你可以執行：

```bash
git push --force
```

另一個常見的錯誤是未設定上游分支。在[設定遠程倉庫](#設定遠程倉庫)部分中，已經提過這個問題。

### 從遠程倉庫抓取更改

要從遠程倉庫抓取更改，你可以執行：

```bash
git fetch
```

這個命令會將遠程倉庫的更改抓取到你的本地遠程跟踪分支。如果遠程倉庫不是默認的遠程倉庫，你可以指定遠程倉庫的名稱執行：（請將 `<remote-repo>` 替換為遠程倉庫的名稱）

```bash
git fetch <remote-repo>
```

> 例如，如果遠程倉庫的名稱為 `source`，你可以執行：
>
> ```bash
> git fetch source
> ```

抓取這些修改後，你可以使用你喜歡的合併策略將這些修改合併到你的本地分支。我們將在下一篇文章中介紹合併策略。

抓取更改不會改變你的本地分支（除了遠程跟踪分支）。這對於你只想了解遠程倉庫的更改而不想影響你的本地分支非常有用。

### 從遠程倉庫拉取並合併更改

要從遠程倉庫拉取並合併更改，你可以執行：

```bash
git pull
```

如果沒有手動配置，默認的合併策略取決於 Git 版本。你可以暫時不管合併策略。只要你在同一個本地倉庫上做所有更改，快進合併應該總是適用的。

### Gitignores

如果你嘗試執行 `git add .`
將檔案添加到暫存區，你可能會發現一些你不想提交的檔案被添加到了暫存區。通常，我們不想提交由程序生成的檔案（例如編譯緩存、日誌檔案、可執行檔案等）。Git
允許你透過在任何目錄中創建 `.gitignore` 檔案來忽略這些檔案。`.gitignore`
檔案是一個文本檔案，包含用於匹配檔案名和目錄名的格式。例如，如果你想忽略所有 `.o`
擴展名的檔案（這些是 C/C++ 編譯器生成的目標檔案），你可以在 `.gitignore`
檔案中添加以下行：

```plaintext
*.o
```

這將忽略**目錄和子目錄**中所有擴展名為 `.o` 的檔案。

以下是一些常見的 `.gitignore` 檔案中的模式：（`#` 表示後面的文本是註釋）

```plaintext
# Ignore all files with .o .s extension
*.o
*.S

# Some of the asm files cannot be ignored
# Use ! to negate the ignore rule
!not-generated.S

# ignore the build directory
build/
```

`.gitignore` 檔案的完整語法可以參考[官方文檔][gitignore-doc]（或在終端中輸入 `man 5 gitignore`）。

[gitignore-doc]: https://git-scm.com/docs/gitignore

GitHub 為 `.gitignore` 檔案提供了幾個模板。你可以使用這些模板（需要將這些檔案重命名為
`.gitignore`）。你可能需要將幾個模板合到一起來創建一個適合你的項目的
`.gitignore` 檔案。這些模板可以在[GitHub 的 gitignore 倉庫][github-gitignore]中找到。

[github-gitignore]: https://github.com/github/gitignore/

### 添加新分支

Git 的默認分支是 `master`（GitHub 的默認分支稱為
`main`，不需要額外設定；不過，你可以將默認分支名設定為 `master`
或任何其他名稱）。通常，`master`
分支用於項目的最新版本（這裡的「最新版本」是指它包含了最新合併到項目中的功能）。除了 `master`
分支，一個項目可能還有用於發布的穩定版本、用於正在進行的功能的特性分支和用於修復錯誤的分支。

要添加一個新分支，你可以執行：（請記得將 `<branch-name>` 替換為新分支的名稱）

```bash
git branch <branch-name>
```

上述命令會創建一個名為 `<branch-name>` 的新分支。

> 例如，如果你想創建一個名為 `dev` 的新分支，你可以執行：
>
> ```bash
> git branch dev
> ```

如果你的 Git 版本不支援 `git branch` 命令，你可以執行：

```bash
git checkout -b <branch-name>
```

這會創建一個名為 `<branch-name>` 的新分支並切換到新分支。

> 例如，如果你想創建一個名為 `dev` 的新分支並切換到新分支，你可以執行：
>
> ```bash
> git checkout dev
> ```

### 切換到其他分支

要切換到另一個分支，你可以執行：

```bash
git switch <branch-name>
```

> 例如，如果你想切換到 `dev` 分支，你可以執行：
>
> ```bash
> git switch dev
> ```

如果你的 Git 版本不支援 `git switch` 命令，你可以執行：

```bash
git checkout <branch-name>
```

> 例如，如果你想切換到 `dev` 分支，你可以執行：
>
> ```bash
> git checkout dev
> ```

### 添加標籤

要給當前提交打上標籤，你可以執行：

```bash
git tag <tag-name>
```

> 例如，如果你想給最新的提交打上 `v1.0.0` 標籤，你可以執行：
>
> ```bash
> git tag v1.0.0
> ```

## 回顧

現在，讓我們回顧一下 Git 的基本用法：

| 命令 | 描述 | 備註 |
| --- | --- | --- |
| `git init` | 初始化一個新倉庫 | 當前目錄不能是一個已存在的倉庫 |
| `git clone <url>` | 複製一個已存在的倉庫 | 目標目錄不能存在 |
| `git add <path>` | 將 `<path>` 中的更改添加到暫存區 | 提交只會應用於添加到暫存區的檔案 |
| `git status` | 檢查倉庫的狀態 | 推薦在提交前使用 |
| `git diff` | 顯示工作區和當前提交之間的差異 | 使用 `--cached` 顯示暫存區的差異 |
| `git commit` | 進行一次提交 | 如果消息很短，使用 `-m`；經常提交（檔案的更改在大多數情況下很容易找回） |
| `git log` | 顯示提交歷史 | 使用 `--oneline` 讓每個提交僅佔用一行；使用 `--graph` 顯示提交圖 |
| `git remote add origin <url>` | 添加一個遠程倉庫 | 遠程倉庫的默認名稱是 `origin`；你可能需要遵循第一次推送時的指示（例如，`git push --set-upstream origin master`） |
| `git push` | 將更改推送到遠程倉庫 | 如果快進合併策略失敗，使用 `--force` 強制推送（這會丟棄一些提交） |
| `git fetch` | 從遠程倉庫抓取更改 | 如果你想抓取非默認分支，使用 `git fetch <repo>` |
| `git pull` | 從遠程倉庫抓取並合併更改 | 默認的合併策略可能不同 |
| `.gitignore` | 忽略檔案 | 使用 `.gitignore` 忽略你不想提交的檔案 |
| `git branch <branch>` | 添加一個新分支 | 新分支名稱不能存在；另一種解決方案：`git checkout -b <branch>` |
| `git switch <branch>` | 切換到另一個分支 | 另一種解決方案：`git checkout <branch>` |


## 練習

現在，讓我們來做一些練習，看看你是否已經掌握了基本用法。你可以嘗試在你的電腦上完成以下任務。同時，我們也會提供一個可行的解決方案。上面的表格也可能幫助你解決這個練習。

### Task A

#### Subtask A.1

在一個新目錄中創建一個名為 `my-repo` 的新倉庫。

{{< details summary="點擊查看可行的解決方案" >}}

1. 創建一個新目錄並進到此目錄。
2. 執行 `git init`。

{{< /details >}}

#### Subtask A.2

進行一次初始提交，提交為 `README.txt` 檔案，內容為 `my-repo`。提交訊息為 `initial commit`。

{{< details summary="點擊查看可行的解決方案" >}}

1. 創建一個名為 `README.txt` 的新檔案，內容為 `my-repo`。
2. 執行 `git add README.txt`。（你可以執行 `git status` 檢查檔案是否已添加到暫存區。）
3. 執行 `git commit -m "initial commit"`。

{{< /details >}}

#### Subtask A.3

檢查你的倉庫的狀態。

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git status`。

{{< /details >}}

#### Subtask A.4

*如果你不想使用遠程倉庫，你可以跳過子任務 A.4 和 A.5。*

在 GitHub（或任何其他 Git 託管服務）上創建一個**私有**遠程倉庫。然後將遠程倉庫添加到本地倉庫。

{{< details summary="點擊查看可行的解決方案" >}}

1. 在 GitHub 上創建一個名為 `my-repo` 的新私有倉庫。
2. 執行 `git remote add origin git@github.com:<your-name>/my-repo.git`。

{{< /details >}}

#### Subtask A.5

將更改推送到遠程倉庫。

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git push --set-upstream origin master`。

{{< /details >}}

#### Subtask A.6

創建一個名為 `dev` 的新分支。

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git branch dev`。

{{< /details >}}

#### Subtask A.7

切換到 `dev` 分支。

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git switch dev`。

{{< /details >}}

#### Subtask A.8

忽略將 `.log` 後綴的所有檔案。創建 `1.log` 檔案以測試規則是否生效。

{{< details summary="點擊查看可行的解決方案" >}}

1. 創建一個名為 `.gitignore` 的新檔案。
2. 在 `.gitignore` 檔案中添加以下行：

   ```plaintext
   *.log
   ```

3. 創建一個名為 `1.log` 的新檔案。
4. 執行 `git status` 檢查 `1.log` 檔案是否被忽略。
   如果被忽略，你不應該在未跟踪的更改中看到 `1.log` 檔案。

{{< /details >}}

### Task B

#### Subtask B.1

複製倉庫 `git@github.com:github/gitignore.git`。

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git clone git@github.com:github/gitignore.git`。

{{< /details >}}

#### Subtask B.2

檢查倉庫的狀態。

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git status`。

{{< /details >}}

#### Subtask B.3

抓取遠程倉庫的更改。（遠程倉庫沒有變更是很正常的。）

{{< details summary="點擊查看可行的解決方案" >}}

1. 執行 `git fetch`。

{{< /details >}}

## 總結

在本文中，我們介紹了 Git 的基本用法。我們介紹了每個 Git 用戶都應該牢記的基本命令。在下一篇文章中，我們將介紹關於合併的命令。

## 版權

你可以將本文用於任何目的，只要你在使用的地方明確標註原作者和連結
(<https://lau.yeeyu.org/blog-zh-hk/git-basic-usage-zh-hk>)。請忽略頁腳處的版權聲明。
