+++
title = 'Git Basic Usage'
date = 2024-08-05T12:00:00-07:00
draft = false
tags = ["git", "git-en", "tutorial", "tutorial-en"]
showToc = true
+++

In the [previous article](/blog/git-basics-en/), we have discussed about the
general concept of version control system and the design of Git. In this
article, we will introduce the basic usage of Git.

<!--more-->

*This article has other versions in different languages:
[简体中文 (Simplified Chinese)](/blog-zh-cn/git-basic-usage-zh-cn),
[繁體中文 (Traditional Chinese)](/blog-zh-hk/git-basic-usage-zh-hk). If you are
a native speaker of these languages, it is recommended to read
these versions.*

Since Git has too many features for learners, we will only cover the most
basic ones that every Git user should always bear in mind. For the advanced
usage, we will have new articles to cover them.

In this article, all of the examples are based on the command line interface,
but you can also use GUI tools. Since most GUI tools use the "plumbing"
interface of Git as a backend, they usually have similar usage (or probably
the same) as the command line interface.

This article will list all possible practical choices when setting up Git.
If you are not familiar with these choices, you can just follow the
recommended approach. No worries, the most important thing is to get started.

## Set Up Git

### Install Git

For Windows users, if you have WSL (Windows Subsystem for Linux) installed,
I strongly recommend you to use Git under WSL.

For Linux (or WSL) users, you can install Git via the package manager of your
distribution. For example, on Ubuntu, you can install Git by running:

```bash
sudo apt update && sudo apt install git
```

For macOS users, you can install Git via Homebrew:

```bash
brew install git
```

Check if Git is installed by running:

```bash
git --version
```

Then, you should see the version of Git you have installed.

### Set Up Your Identity

Before you start using Git, you are required to set up your identity. This
information will be recorded in every commit you make. Please note that this
information is public once you push your commits to a public repository. If
you don't want to expose your personal information, you are recommended to
not use your real name.

To set up your identity, run: (**Please replace `Your Name` and
`someone@example.org` with your name and email address!**)

```bash
git config --global user.name "Your Name"
git config --global user.email "someone@example.org"
```

The two commands above will set up your name and email address respectively.

### Set Up Git Hosting Service

Usually, we will use a Git hosting service (e.g., GitHub, GitLab, etc.) to
host our repositories. You can share your code with others, back up your
code, and collaborate with others via these services.

You can use either ssh protocol or https protocol to interact with
the remote repository. It is recommended to use ssh protocol since it is
more convenient to use. If you choose to use ssh protocol, you need to
use links like `git@github.com:user/repo` instead of
`https://github.com/user/repo`.

#### Set Up SSH Key Pair

In the ssh protocol, the key pair instead of the password is used for
authentication. Hence, to use ssh protocol, you need to generate an ssh
key pair. To generate an ssh key pair, run: (**Please replace
`someone@example.org` with your email address!**)

```bash
ssh-keygen -t ed25519 -C "someone@example.org"
```

> [!NOTE]
> If you are using a system that doesn't support the Ed25519 algorithm,
> you can use the RSA algorithm by running: (Also replace
> `someone@example.org` with your email address!)
>
> ```bash
> ssh-keygen -t rsa -b 4096 -C "someone@example.org"
> ```

After run the command, it will ask you where to save the key pair. Please
**enter directly** without specifying a path to store the key pair in the
default location; otherwise, it will be really inconvenient to use the key
pair.

Then, it will ask you to enter a passphrase. You can either enter a passphrase
or leave it blank. The passphrase can be changed at any time.

#### Add SSH Key to Git Hosting Service

After you have registered an account on the Git hosting service, you can add
your ssh public key to the service. To get your ssh public key, run:

```bash
cat ~/.ssh/id_ed25519.pub
```

> [!NOTE]
> If you are using the RSA algorithm, run:
>
> ```bash
> cat ~/.ssh/id_rsa.pub
> ```

Then, copy the output and paste it to the ssh key setting page of the Git
hosting service. For example, you can add it to the [GitHub ssh key setting
page][github-ssh-key-settings].

[github-ssh-key-settings]: https://github.com/settings/keys/

Remember to always keep your private key (the file without `.pub`) safe and
never expose it to others. Only share the public key (with `.pub`) with
others.

if you switch to a new device, reinstall the system, or delete the ssh key
pair accidentally, you should regenerate a new ssh key pair with the command
above and add the new public key to the Git hosting service. There's no way
to retrieve the private key if you lose it.

You may test if your key has been added successfully to GitHub by running:
(Note: when running this for the first time, you may be asked to confirm the
GitHub's fingerprint. Please confirm it by typing "yes" and hitting the enter
key.)

```bash
ssh git@github.com
```

## Basic Usage

In this section, we will introduce the minimal Git usage that every Git user
should bear in mind in order to use Git effectively and comfortably.

Most new Git users may feel confused about the Git workflow. Here, we will
introduce the basic usage in details with examples and also provides you
with a drill to test whether you have understand the basic usage.

### Create a Repository

To create a new repository, you can first create a new directory for the Git
repo. If you want to create the directory with command line, you can run:
(Remember to replace `<repo-name>` with the name of your repository!)

```bash
mkdir <repo-name>
```

Then you can run the following commands to initialize the Git repository:

```bash
git init
```

### Clone a Repository

It is very common to clone an existing repository to your local machine
(e.g., using other's repository, or creating your repository on the server
first). To clone a repository, you can run: (The `<repository-url>` is the
URL of the repository you want to clone)

```bash
git clone <repository-url>
```

> For example, to clone the Git repository, you can run the following command
> to clone the GitHub's gitignore repository:
>
> ```bash
> git clone git@github.com:github/gitignore.git
> ```

### Add Files to Staging Area

Commits are fundamental in Git. Before you make a commit, you need to add
the changes to the staging area (the area serves as a buffer for making
cleaner and finer-grained commits). If you don't know or forget about what
is the staging area, you can read the
[workspace and staging section](/blog/git-basics-en/#workspace-and-staging)
in the previous article to have a deeper understanding of it.

To add changes to the staging area, you can run: (you can add multiple files
at a time)

```bash
git add <file>...
```

> For example, if you want to add the changes in the `README.md` file to the
> staging area, you can run:
>
> ```bash
> git add README.md
> ```

If you want to add all changes in the current directory and its subdirectory
to the staging area, you can run:

```bash
git add .
```

### Check the Status

To check what files have been added to the staging area, you can run the
following command to see the staged and unstaged changes:

```bash
git status
```

It is strongly recommended to check the status before making a commit to
ensure that you are committing the correct changes, especially when you
have run `git add .` before.

The status command shows a lot of information and hints for you. If you
are confused about what to do next, you can always try `git status` to
get some hints and also know about your Git repo's status. Thus, the
`git status` is probably the most frequently used command in Git.

### Show the Difference in the Working Directory

You may want to see the difference between the working directory and the
current commit. To show all the difference, you can run:

```bash
git diff
```

Git will show you the difference (as well as the context around the changes)
in each changed files with highlighting.

If you want to see the difference only among specific files or directory,
you can run:

```bash
git diff <file>...
```

> For example, if you want to see the difference in the `README.md` file,
> and the `src` directory, you can run:
>
> ```bash
> git diff README.md src
> ```

### Show the Difference in the Staging Area

If you want to see the difference between the staging area and the current
commit, you can add the `--cached` option to the `git diff` command:

```bash
git diff --cached
```

If you want to see the difference only among specific files or directory,
you can run:

```bash
git diff --cached <file>...
```

> For example, if you want to see the difference in the `README.md` file,
> and the `src` directory, you can run:
>
> ```bash
> git diff --cached README.md src
> ```

### Make a Commit

After you have added the changes to the staging area, you can make a commit
by running:

```bash
git commit
```

This command will open the default text editor (usually Vim or Nano) for you
to write the commit message. If you leave the commit message empty, Git will
abort the commit. In the text editor interface, Git will show you some hints
about making a commit, including the changed files you are going to commit.

If the commit message is short, you can directly write the commit message
in the command line by running: (Remember to replace `Your commit message`
with your commit message!)

```bash
git commit -m "Your commit message"
```

> For example, if you want to make a commit with the message `docs: add
> README.md`, you can run:
>
> ```bash
> git commit -m "docs: add README.md"
> ```

Committed files can be retrieved easily in most situations; however,
uncommitted changes may be lost if you accidentally delete them. Hence,
it is **strongly recommended** to **commit often**. A good practice is to
commit at least every day if you have made changes to the repo.
Fine-grained commits are generally better than large commits in a lot of
aspects (e.g., cherry-picking, reverting, understanding the changes, etc.).

A lot of people uses the [Conventional Commits][conventional-commits] style
for their commit messages. This style makes it easier and clearer to
understand the changes in the commit.

[conventional-commits]: https://www.conventionalcommits.org/

### Show the Commit History

To show the commit history, you can run:

```bash
git log
```

You may also use the following options:

- `--oneline`: Show the commit history in one line.
- `--graph`: Show the commit history in a graph.

### Use Git Subcommands anywhere in the Git Repository

As long as you are in the Git repository, you can use Git subcommands
at any subdirectory of the repository. For example, you can run `git status`
at the root directory of the repository, or you can run `git status` at
its subdirectory. This is really convenient for you to manage the repository.

### Set up Remote Repository

To set up a remote repository, you can run:

```bash
git remote add origin <repository-url>
```

This command will add a remote repository with the name `origin` and the
URL `<repository-url>`. The `origin` is the default name for the remote
repository. You can use any name you like for the remote repository.

> For example, if you want to add a remote repository with the URL
> `git@github.com:github/gitignore.git`, you can run:
>
> ```bash
> git remote add origin git@github.com:github/gitignore.git
> ```

For the first push, you will see a error message that tells you to set the
upstream branch. You can follow the instructions in the error message to
set the upstream branch. For example, you can run the following command to
set the upstream branch to `master`:

```bash
git push --set-upstream origin master
```

### Push Changes to Remote Repository

After you have made a commit, you can push the changes to the remote
repository by running:

```bash
git push
```

After running the command, watch carefully the output of the command. If
you see any error messages, you should read them carefully and try to
understand what the error is. Along with the error message, Git will also
provide you with some hints on how to solve the problem.

For example, if the remote repository cannot merge your changes with
fast-forward, this means that the head commit of the remote branch is not
an ancestor of your commit. In this case, you need to either merge these
changes locally (Git doesn't allow you to merge on remote servers) or force
push your changes to the remote repository. To force push your changes, you
can run:

```bash
git push --force
```

Another common error is that the upstream branch is not set. This has been
discussed in the [set up remote repository section](#set-up-remote-repository)
above.

### Fetch Changes from Remote Repository

To fetch changes from the remote repository, you can run:

```bash
git fetch
```

This command will fetch the changes from the remote repository to your local
remote-tracking branches. If the remote repository is not the default one,
you can specify the remote repository by running: (Remember to replace
`<remote-repo>` with the name of the remote repository)

```bash
git fetch <remote-repo>
```

> For example, if the remote repository name is called `source`, you can run:
>
> ```bash
> git fetch source
> ```

After fetching the changes, you can merge the changes to your local branch
with the merging strategy you like. We will introduce the merging strategy
in the next article.

Fetching changes will not change your local branch (except the remote-tracking
branch). This is quite useful if you only want to know about the changes in
the remote repository without interfering with your local branch.

### Pull Changes from Remote Repository

To fetch and merge changes from the remote repository, you can run:

```bash
git pull
```

The default merging strategy depends on the Git version if not configured
manually. You can currently put the stuff about the merging strategy aside.
As long as you make all changes on the same repository, fast-forward merging
should always apply.

### Gitignores

If you tried to add files to the staging area by running `git add .`, you
will probably find that some files you don't want to commit are added to the
that place. Normally, we don't want any files generated by programs (e.g.,
compilation cache, log files, executables, etc.) to be committed. Git allows
you to ignore these files by creating a `.gitignore` file in the any directory.
The `.gitignore` file is a text file that contains the patterns of files or
directory you want to ignore. The pattern applies to the directory of the
`.gitignore` file. For example, if you want to ignore all files with `.o`
extension (which are the object files generated by the C/C++ compiler), you
can add the following line to the `.gitignore` file:

```plaintext
*.o
```

This will ignore all files with `.o` extension in the **directory and
subdirectory** of the `.gitignore` file.

Here's some common patterns you can use in the `.gitignore` file: (`#`
means the text after it in the same line is a comment)

```plaintext
# Ignore all files with .o .s extension
*.o
*.S

# Some of the asm files cannot be ignored
# Use ! to negate the ignore rule
!not-generated.S

# ignore the build directory
build/

# Ignore files starting with a number
[0-9]*
```

For the full syntax of the `.gitignore` file, you can refer to the
[official documentation][gitignore-doc] (or enter `man 5 gitignore`
in the terminal).

[gitignore-doc]: https://git-scm.com/docs/gitignore

GitHub provides several templates for the `.gitignore` file. You can use
these templates (rename these files to `.gitignore`). You can merge
several templates to create a `.gitignore` file that fits your project.
The templates can be found at the
[GitHub gitignore repository][github-gitignore].

[github-gitignore]: https://github.com/github/gitignore/

### Add New Branches

The default branch in Git is `master` (GitHub's default branch is called
`main` without extra settings; however, you can set the default branch
name to `master` or any other names). Normally, the master branch is used
for the latest version of the project. The "latest version" here means
it contains the latest features merged into the project. Apart from the
master branch, a project may have stable versions for releases, feature
branches for ongoing features, and bugfix branches for fixing bugs.

To add a new branch, you can run: (Remember to replace `<branch-name>`
with the name of the new branch)

```bash
git branch <branch-name>
```

The command above will create a new branch with the name `<branch-name>`.

> For example, if you want to create a new branch named `dev`, you can run:
>
> ```bash
> git branch dev
> ```

If `git branch` command is not supported in your Git version, you can run:

```bash
git checkout -b <branch-name>
```

This will create a new branch with the name `<branch-name>` and switch to
the new branch.

> For example, if you want to create a new branch named `dev` and switch to
> the new branch, you can run:
>
> ```bash
> git checkout -b dev
> ```

### Switch to other Branches

To switch to another branch, you can run:

```bash
git switch <branch-name>
```

> For example, if you want to switch to the `dev` branch, you can run:
>
> ```bash
> git switch dev
> ```

For other references (e.g., tags, commits), you should add the `--detach`
option to the `git switch` command. This will switch to the reference in
a detached `HEAD` state, which means you are not on any branch.

If `git switch` command is not supported in your Git version, you can run:

```bash
git checkout <branch-name>
```

> For example, if you want to switch to the `dev` branch, you can run:
>
> ```bash
> git checkout dev
> ```

### Tags

To mark the current commit with a tag, you can run:

```bash
git tag <tag-name>
```

> For example, if you want to mark the latest commit with the tag `v1.0.0`,
> you can run:
>
> ```bash
> git tag v1.0.0
> ```

## Recap

Now. let's recap the basic usage of Git:

| Command | Description | Note |
| --- | --- | --- |
| `git init` | Initialize a new repo | The current directory cannot be a existing repo |
| `git clone <url>` | Clone an existing repo | The target directory should not exist |
| `git add <path>` | Add changes in `<path>` to the staging area | Commit will only apply to files added to the staging |
| `git status` | Check the status of the repo | Recommended to check the status before making a commit |
| `git diff` | Show the difference in the working directory | Use `--cached` to show difference in the staging area |
| `git commit` | Make a commit | Use `-m` if the message is short; commit often (changes in files can be retrieved easily in most cases) |
| `git log` | Show the commit history | Use `--oneline` to show every commit in one line; use `--graph` to show commit graph |
| `git remote add origin <url>` | Add a remote repo | The default name for the remote repo is `origin`; you might need to following the instructions on first push (e.g., `git push --set-upstream origin master`) |
| `git push` | Push changes to the remote repo | If the fast-forward strategy fails, use `--force` to force push (this will discard some commits) |
| `git fetch` | Fetch changes from the remote repo | Use `git fetch <repo>` if you want to fetch any non-default branch |
| `git pull` | Fetch and merge changes from the remote repo | The default merging strategy may differ |
| `.gitignore` | Ignore files | Use `.gitignore` to ignore files you don't want to commit |
| `git branch <branch>` | Add a new branch | The new branch name should not exist; alternative solution: `git checkout -b <branch>` |
| `git switch <branch>` | Switch to another branch; use `--detach` for other references | Alternative solution: `git checkout <branch>` |

## Drill

Now, let's have a drill to test whether you have understood the basic usage.
You can try to do the following tasks on your computer. We will also provide
you with a possible solution to the drill. (The table above may also help you
to solve the it.)

### Task A

#### Subtask A.1

Create a new repository in a new directory named `my-repo`.

{{< details summary="Click to see the possible solution" >}}

1. Create a new directory and enter it.
2. Run `git init`.

{{< /details >}}

#### Subtask A.2

Make an initial commit with a file name `README.txt`. The content is
`my-repo`. The commit message is `initial commit`.

{{< details summary="Click to see the possible solution" >}}

1. Create a new file named `README.txt` with the content `my-repo`.
2. Run `git add README.txt`. (You may run `git status` to check if the file
   is added to the staging area.)
3. Run `git commit -m "initial commit"`.

{{< /details >}}

#### Subtask A.3

Check your repository's status.

{{< details summary="Click to see the possible solution" >}}

1. Run `git status`.

{{< /details >}}

#### Subtask A.4

*If you don't want to use remote repository, you can skip subtask A.4 and A.5.*

Create a **private** remote repository on GitHub (or any other Git hosting
service). Then add the remote repository to the local repository.

{{< details summary="Click to see the possible solution" >}}

1. Create a new private repository on GitHub with name `my-repo`.
2. Run `git remote add origin git@github.com:<your-name>/my-repo.git`.

{{< /details >}}

#### Subtask A.5

Push the changes to the remote repository.

{{< details summary="Click to see the possible solution" >}}

1. Run `git push --set-upstream origin master`.

{{< /details >}}

#### Subtask A.6

Create a new branch named `dev`.

{{< details summary="Click to see the possible solution" >}}

1. Run `git branch dev`.

{{< /details >}}

#### Subtask A.7

Switch to the `dev` branch.

{{< details summary="Click to see the possible solution" >}}

1. Run `git switch dev`.

{{< /details >}}

#### Subtask A.8

Add create a rule to ignore all file with `.log` extension in this repo.
Create a new file named `1.log` to test if the rule works.

{{< details summary="Click to see the possible solution" >}}

1. Create a new file named `.gitignore`.
2. Add the following line to the `.gitignore` file:

   ```plaintext
   *.log
   ```

3. Create a new file named `1.log`.
4. Run `git status` to see whether the `1.log` file is ignored.
   If ignored, you should not see the `1.log` file in the untracked changes.

{{< /details >}}

### Task B

#### Subtask B.1

Clone the repository `git@github.com:github/gitignore.git`.

{{< details summary="Click to see the possible solution" >}}

1. Run `git clone git@github.com:github/gitignore.git`.

{{< /details >}}

#### Subtask B.2

Check the status of the repository.

{{< details summary="Click to see the possible solution" >}}

1. Run `git status`.

{{< /details >}}

#### Subtask B.3

Fetch the changes from the remote repository. (It's okay to have no changes
at all.)

{{< details summary="Click to see the possible solution" >}}

1. Run `git fetch`.

{{< /details >}}

## Conclusion

In this article, we have introduced the basic usage of Git. We have covered
the basic commands that every Git user should always bear in mind. In the
next post, we will introduce commands about merging.

## Copyright

You may use this article for any purpose as long as the original author
and link (<https://lau.yeeyu.org/blog/git-basic-usage-en>) are clearly noted
at the place you use this article. This copyright notice overrides the
footnote of the website.
