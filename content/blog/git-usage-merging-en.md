+++
title = 'Git Advanced Usage: Merging'
date = 2024-08-06T12:00:00-07:00
draft = true
tags = ["git-en", "git", "tutorial", "tutorial-en"]
showToc = true
+++

In the [previous article](/blog/git-basic-usage-en), we've introduced the
basic usage of Git. In this article, we will introduce the advanced usage
on merging branches.

<!--more-->

Using multiple branches, which can make your workflow more flexible and
efficient, is a common practice in Git. However, it is not easy for a
beginner to leverage the features of branches, especially the features
related to merging. If you don't have a good understanding of all the
crucial commands and concepts, then you will get disoriented when you
encounter a problem when handling multiple branches.

This article will assume that you have a good command of the basic usage,
which is introduced in the
[previous article](/blog/git-basic-usage-en). To be specific,
you should be familiar with the stuff listed in the
[recap section](/blog/git-basic-usage-en#recap).

## Merge Branches

**Key rule: Always do your work in your own branch.**

Git is designed for distributed purpose, so your own branch is the branch
that you should focus on. Every time you want to do something (e.g., `merge`
or `rebase`), doing it in your own branch is always a good practice. We
will give more examples in the `git rebase` section.

Git provides two ways to merge branches:

- [Merge](#merge)
- [Rebase](#rebase)

Each method has its own pros and cons, and have difference in terms of the
workflow. You should use the appropriate one based on your situation (e.g.,
personal preference, team rules, etc.).

Conflicts may occur when merging if both sides have modified the same part
of the file. We will discuss about how to resolve conflicts later in this
article.

### Merge

There are basically two behaviours of "merge" command:

- **Fast-forward merge**. If `HEAD` is the ancestor of the branch to be
  merged, Git will only move `HEAD` to the branch to be merged. You can
  use `--ff-only` option to force this behaviour. The graph below depicts
  the fast-forward merge. If we are in the `master` branch and want to
  merge the `feat` branch, the `HEAD` will move to the `feat` branch, that
  is to say, moves from `old HEAD` position to `new HEAD` position.

  ```mermaid
  %%{init: {'gitGraph': {'mainBranchName': 'master'}} }%%
  gitGraph
    commit
    commit tag: "old HEAD"
    branch feat
    commit
    commit tag: "new HEAD"
  ```

- **3-way merge**. If `HEAD` is not the ancestor of the branch to be merged,
  Git will create a new commit to merge the two branches. The graph below
  depicts the 3-way merge. In this case, Git will create a new commit to
  merge the two branches, where the new commit has two parents -- the last
  commit of the branch to be merged and the commit you are previously on.

  ```mermaid
  %%{init: {'gitGraph': {'mainBranchName': 'master'}} }%%
  gitGraph
    commit
    commit tag: "old HEAD"
    branch feat
    checkout master
    commit
    checkout feat
    commit
    commit
    checkout master
    merge feat tag: "new HEAD"
  ```

To merge branches, you can use the following command:

```bash
git merge <commit>...
```

> For example, if you are in the `master` branch and want to merge the
> `feat` branch, you can use the following command:
>
> ```bash
> git merge feat
> ```

Please note that:

- The `<commit>` parameter can be a commit hash or any reference to a commit.
- The number of commits to be merged can be more than one.
- If no commits are specified, Git will merge the remote-tracking counterpart
  of the current branch.
- Use `--ff-only` option to force fast-forward merge (e.g.,
  `git merge --ff-only feat`).
- Use `--squash` option (e.g., `git merge --squash feat`) if you want to
  merge without creating a new commit or to avoid creating a merge commit
  (which means there's more than one parents).

We will discuss later about how to resolve conflicts when merging branches.

Pros:

- Not affecting the previous commits, including committer, author(s) and
  signature(s).

Cons:

- The relationship between commits is complicated and not clear enough.
- When merging a branch with a large number of commits, the conflict
  resolution process may be complicated as opposed to the rebase case.
  This is because rebase process will apply commits one by one, so it is
  finer-grained.

### Rebase

Rebase is a little bit different from merge. If the commit relationship is
like the following graph:

```mermaid
%%{init: {'gitGraph': {'mainBranchName': 'master'}} }%%
gitGraph
  commit id: "A"
  commit id: "B"
  branch feat
  commit id: "C"
  checkout master
  commit id: "D"
  checkout feat
  commit id:"E"
  checkout master
  commit id:"F"
  checkout feat
  commit id: "G" tag: "HEAD"
```

Then, after rebasing the `master` branch on the `feat` branch, the graph
will be like this:

```mermaid
%%{init: {'gitGraph': {'mainBranchName': 'master'}} }%%
gitGraph
checkout master
  commit id: "A"
  commit id: "B"
  commit id: "D"
  commit id: "F"
  branch feat
  commit id: "C1"
  commit id: "E1"
  commit id: "G1" tag: "HEAD"
```

As you can see, the `feat` branch is rebased on the `master` branch, and
the commit `C`, `E`, `G` have been applied to the latest commit of the
`master` branch. The `C1`, `E1`, `G1` are the new commits created by the
rebase operation.

The rebase operation basically applies all the affected branch, which
includes all the branch from the shared ancestor to the current branch,
to the branch-to-be-rebased one by one. This is why it is called rebase,
because it set the base of the current branch to the branch-to-be-rebased.

Please note that you should be the **only** maintainer on the branch on
which you are rebasing. Otherwise, you will probably encounter a lot of
problems.

To rebase branches, you can use the following command:

```bash
git rebase <commit>...
```

> For example, if you are in the `master` branch and want to merge the
> `feat` branch, you can use the following command:
>
> ```bash
> git merge feat
> ```

Please note that:

- The `<commit>` parameter can be a commit hash or any reference to a commit.
- The number of commits to be merged can be more than one.
- If no commits are specified, Git will merge the remote-tracking counterpart
  of the current branch.
- If a conflict occurs, you should resolve it and then use `git rebase
  --continue` to continue the rebase operation. We will discuss about how
  to resolve conflicts later in this article.

If you want to use more features, you can use the `-i` or `--interactive`
option. This option will open an editor and allows you to the following
operations:

- Pick (`p <commit>`): use the commit.
- Reword (`r <commit>`): use the commit, but edit the commit message.
- Edit (`e <commit>`): use the commit, but stop for amending.
- Squash (`s <commit>`): use the commit, but meld into the previous commit.
- Fixup (`f <commit>`): like "squash" but keep only the previous commit's
  log message.
- Execute (`x <command>`): run command (the rest of the line) using shell.
- Break (`b`): stop here (continue rebase later with `git rebase --continue`).
- Drop (`d <commit>`): remove commit.
- Label (`l <label>`): label current `HEAD` with a name.
- Reset (`t <label>`): reset `HEAD` to a label.
- Merge (`m [-C <commit> | -c <commit>] <label> [# <oneline>]`): create a
  merge commit using the original merge commit's message.
- Update-ref (`u <ref>`): track a placeholder for the `<ref>` to be updated
  to this position in the new commits.

Pros:

- The created commits are kept as a chain, which is clear and easy to
  understand compared to the merge case.
- If the commit to be merged has a great difference from the current branch,
  the conflict resolution will be easier in that the rebase process applies
  the commits one by one.

Cons:

- Any commits to be applied during rebasing will lose their original
  committer (as well as their signatures) if their committer are not the
  person who operates the rebase procedure.
- If there are two or more people working on the same branch, the branch
  will probably not be up to date, which means you have to cherry-pick
  the commits manually. (You will not encounter this problem if you are
  the only maintainer of the branch.)
- More conflicts may occur, but the case is rare.

### Resolve Conflicts

> As long as we need to merge, the conflicts will always be there.

When merging branches, Git will tell you if the automatic merge failed,
and you have to resolve the conflicts manually.

You can know the status of the merge with `git status` command. In the
files with conflicts, you will see something like the following message:

```plaintext
<<<<<<< HEAD
...
||||||| b6279c7
...
=======
...
>>>>>>> feat
```

The first `...` is the content of the current `HEAD`; the second `...` is
the content of the shared ancestor; the third `...` is the content to be
merged.

You may use other tools (e.g., [delta project][delta-project]) to show the
conflicts in a more intuitive way, which will be introduced in the coming
articles.

[delta-project]: https://github.com/dandavison/delta

If you use the rebase strategy, it is easier to resolve the conflicts,
because the changes in every commit is finer-grained and has a clear
intention (the changes are applied commit by commit).

After resolving the conflicts, you should use the following command depending
on your merge strategy:

- For merge strategy, add the resolved files (i.e., `git add <file>`) and
  then create a new commit (i.e., `git commit`).
- For rebase strategy, add the resolved files (i.e., `git add <file>`) and
  then continue the rebase operation (i.e., `git rebase --continue`).

There are some other tricks:

- Abort: `git merge --abort`, `git rebase --skip`, `git rebase --abort`
  (depending on the strategy you are using).
- Follow others: `git merge -s theirs ...`.
- Insist on your own: `git merge -s ours ...`.

### Example of Common Workflow

Let's assume that the main branch is `master` while the feature branch is
`feat`. We need to merge the `feat` branch into the `master` branch, but
the `master` branch cannot merge the `feat` branch in a fast-forward way.

- Merge strategy: merge the `feat` branch on the `master` branch.

  It is recommended to switch to the `feat` branch and then merge the
  `master` branch into the `feat` branch in a fast-forward way. This will
  make the `feat` branch up to date with `master`. If you don't do this,
  nothing will happen in the near future, but as the changes are accumulating,
  conflicts or other problems (e.g., not working but no conflicts) will be
  more likely to occur.
- Rebase strategy: rebase the `master` branch on the `feat` branch, then
  switch to the `master` branch and merge the `feat` branch in a fast-forward
  way.

## Relative Commit Reference

When you are working on multiple branches, you may want to specify a commit
relative to a certain commit. Obviously, you can show the git log first and
get the hash, but it is not efficient.

For convenience, Git allows you to specify a commit by the relationship to
another commit (e.g., `HEAD^` means the parent commit and `HEAD~2` means
the parent of HEAD's parent). The rules are as follows:

- `<rev>~<n>`: the `<n>`th generation ancestor commit of `<rev>`. For example:
  - `HEAD~0`: equivalent to `HEAD`.
  - `HEAD~1`: the parent commit of `HEAD`.
  - `HEAD~n`: the `n`th generation ancestor of `HEAD`.
- `<rev>^<n>`: switch among parents (The commits created on `git merge` have
  multiple parents). Note: `^` alone has the meaning of "parent", and the
  `n` is for switching among one or multiple parents (if you exceed the
  number of parents, then an error will occur). `n` means the `n`th parent
  commit of `<rev>`. For example, `HEAD^` (equivalent to `HEAD^1`) and
  `HEAD^2`.

## Stash

Sometimes, you have to switch to another branch, but find yourself not ready
to commit the changes in the current branch. In this case, if you switch to
other branch directly, Git may refuse to switch due to the conflict of
changes (this only happens when the file you have changes is different between
two branches).

Thus, Git allows you to stash the changes temporarily, and retrieve them
later at any time. To be specific, Git has a modification stack, which
records all the stashed changes. You can use the following commands to
use the stash feature:

1. Stash the changes:

   ```bash
   git stash
   ```

2. Retrieve the changes:

   ```bash
    git stash pop
    ```

3. List the stashed changes:

   ```bash
   git stash list
   ```

4. Clear all stashed changes:

   ```bash
    git stash clear
    ```

## Get the common ancestor

In some cases, you may want to get the common ancestor of multiple branches
(normally two). You can use the following command to get the common ancestor:

```bash
git merge-base --all <commit>...
```

> For example, if you want to get the common ancestor of the `master` and
> `feat` branches, you can use the following command:
>
> ```bash
> git merge-base --all master feat
> ```

## Restore Files from Commits to the Working Directory

If you mistakenly delete files or directories in the working directory that
**have been committed**, you can restore them by using the following command:

```bash
git restore <path>...
```

Please note that this operation will drop all the changes of the restored
files, so it might be dangerous. To achieve similar functionality, you can
also [stash the changes](#stash), which is safer.

> For example, if you mistakenly delete the `README.txt` file, you can use
> the following command to restore it:
>
> ```bash
> git restore README.txt
> ```

If you want to restore something from commits other than `HEAD`, you can
specify it with `--source=<tree>` option. The `<tree>` can be a commit hash
or any reference to a commit. For example, if you want to restore the
`README.txt` file from the parent commit of `HEAD`, you can use the following
command:

```bash
git restore --source=HEAD~ README.txt
```

If `git restore` command is not supported in your Git version, you can use
`git checkout` command instead. The usage is similar to `git restore` command:

```bash
git checkout <path>...
```

> For example, in the same case of the previous example, you can use:
>
> ```bash
> git checkout README.txt
> ```

## Unstage Files

If you mistakenly add files to the staging area, you can unstage them by
using the following command:

```bash
git restore --staged <path>...
```

> For example, if you mistakenly add the `README.txt` file to the staging
> area, you can use the following command to unstage it:
>
> ```bash
> git restore --staged README.txt
> ```

If `git restore` command is not supported in your Git version, you can use
`git reset` command instead:

```bash
git reset HEAD <path>...
```

> For example, in the same case of the previous example, you can use:
>
> ```bash
> git reset HEAD README.txt
> ```

## Change the Head Commit of a Branch

Sometimes, we may want to change the head commit of a certain branch to
another commit (e.g., the parent commit of the current head).

If you are not on the branch you want to change, switch to that branch first.
Then you may use the following command to achieve this:

```bash
git reset [--soft | --mixed | --hard | --merge | --keep] <commit>
```

As the [git reset documentation](https://git-scm.com/docs/git-reset) says,
the `--soft`, `--mixed`, `--hard`, `--merge`, and `--keep` options mean:

- `--soft`: Does not touch the index file or the working tree at all (but
  resets the head to `<commit>`, just like all modes do). This leaves all
  your changed files "Changes to be committed", as `git status` would put it.

- `--mixed` (default): Resets the index but not the working tree (i.e., the
  changed files are preserved but not marked for commit) and reports what
  has not been updated.

- `--hard`: Resets the index and working tree. Any changes to tracked files
  in the working tree since `<commit>` are discarded. Any untracked files or
  directories in the way of writing any tracked files are simply deleted.

- `--merge`: Resets the index and updates the files in the working tree that
  are different between `<commit>` and `HEAD`, but keeps those which are
  different between the index and working tree (i.e. which have changes
  which have not been added). If a file that is different between `<commit>`
  and the index has unstaged changes, reset is aborted.

- `--keep`: Resets index entries and updates files in the working tree that
  are different between `<commit>` and `HEAD`. If a file that is different
  between `<commit>` and `HEAD` has local changes, reset is aborted.

> For example, if you want to change the head commit of the `master` branch
> to its parent commit and also to reset the index and the working tree, you
> can use the following command:
>
> ```bash
> git reset --hard HEAD~
> ```

## Show the Difference

There are four common cases to show the difference in Git:

- [Show the Difference of the Working Directory and certain Commits](#show-the-difference-of-the-working-directory-and-certain-commits)
- [Show the Difference of the Staging Area and certain Commits](#show-the-difference-of-the-staging-area-and-certain-commits)
- [Show the Difference of Commits](#show-the-difference-of-commits)
- [Generate a Patch](#generate-a-patch)

### Show the Difference of the Working Directory and certain Commits

Use the following command to show the difference between the working directory
and certain commits:

```bash
git diff <commit>
```

> For example, if you want to show the difference between the working directory
> and the `feat` branch, you can use the following command:
>
> ```bash
> git diff feat
> ```

Specifically, if you want to see the difference between the working directory
and the current `HEAD`, you can just run:

```bash
git diff
```

To show the difference of certain files or directories, you can specify them
by:
  
```bash
git diff <commit> <path>...
```

> For example, if you want to show the difference between the working directory
> and the `feat` branch for the `README.txt` file, you can use the following
> command:
>
> ```bash
> git diff feat README.txt

### Show the Difference of the Staging Area and certain Commits

Use the following command to show the difference between the staging area
and certain commits: (similar to the previous case, just add `--cached` option)

```bash
git diff --cached <commit>
```

Similar to the previous case, you can emit the `<commit>` parameter to show
the difference between the staging area and the current `HEAD`. Also, you can
specify the files or directories to show the difference.

### Show the Difference of Commits

Use the following command to show the difference between two commits:

```bash
git diff <commit> <commit>
```

> For example, if you want to show the difference between the `master` and
> `feat` branches, you can use the following command:
>
> ```bash
> git diff master feat
> ```

Similar to the previous cases, you can specify the files or directories to
show the difference:

```bash
git diff <commit> <commit> <path>...
```

### Generate a Patch

To generate a patch for any difference, you just need to redirect the output
of the previous cases to a file.

> For example, if you want to generate a patch for the difference between the
> current working directory and `HEAD` to a file named `patch.diff`, you can
> use the following command:
>
> ```bash
> git diff > patch.diff
> ```

In the next section, we will introduce how to apply a patch.

## Apply a Patch

To apply a patch, you can run:

```bash
git apply <patch>
```

> For example, if you want to apply the `patch.diff` patch in the `patch/`
> directory, you can use the following command:
>
> ```bash
> git apply patch/patch.diff
> ```

## Show a Commit

To show the details of a commit, you can use the following command:

```bash
git show <commit>
```

> For example, if you want to show the details of the parent commit of the
> current `HEAD`, you can use the following command:
>
> ```bash
> git show HEAD~
> ```

## Revert a Commit

If we made some wrong changes in some commit(s) (e.g., introduced another more
severe bug when solving a bug), we can revert the commit(s) by running:

```bash
git revert <commit>...
```

This will revert every commit. Please not that a new commit will be created
for every reverted commit. If you don't want to create multiple commits, you
can use the `--no-commit` or `-n` option.

> For example, if you want to revert the commit `614b994` and `d4be492`, you
> can use the following command:
>
> ```bash
> git revert 614b994 d4be492
> ```
>
> This will create two new commits, one for each reverted commit.

## Cherry-pick a Commit

Sometimes, we may want to apply commits from other branches to the current
branch. For instance, we have a development branch and a main branch for
production. We may want to apply some commits from the development branch to
the main branch. The development branch may have some commits that fix
critical bugs, and we want to apply them to the main branch. In this case, we
want to apply these changes to the main branch.

Git provides the `cherry-pick` command to apply commits to the current branch.
To cherry-pick commits, you can use the following command:

```bash
git cherry-pick <commit>...
```

This will apply the commits one by one. Similar to the `revert` command, a new
commit will be created for every cherry-picked commit. If you don't want to
create multiple commits, you can use the `--no-commit` or `-n` option.

> For example, if you want to cherry-pick the commit `614b994` and `d4be492`,
> you can use the following command:
>
> ```bash
> git cherry-pick 614b994 d4be492
> ```
>
> This will create two new commits, one for each cherry-picked commit.

## Find Certain Patterns in Files

If you want to find certain patterns (e.g., a string) in files, you can use
the following command:

```bash
git grep <pattern> <path>...
```

If you don't specify the `<path>`, Git will search in the whole repository.
The pattern is similar to the pattern in the `grep` command.

> Note: this command has other alternatives, (e.g. [rip-grep][ripgrep]), and
> we will introduce them in the coming articles.

[ripgrep]: https://github.com/BurntSushi/ripgrep

## Show the Reference Log

In the basic usage article, we mentioned that if the changes can be retrieved
easily if committed. This is because Git has a reference log, which records
all changes about commits. You can see every operation related to commits
with reference log. To show the reference log, you can run:

```bash
git reflog
```

## Manage Remote Repositories

In the basic usage article, we introduced how to add a remote repository. Here,
we will introduce more commands to manage remote repositories.

### List Remote Repositories

To list all the remote repositories, you can use the following command:

```bash
git remote show
```

Use the `--version` or `-v` option to show more details:

```bash
git remote -v show
```

To list a specific remote repository, you can run:

```bash
git remote show <repo>
```

> For example, if you want to show the details of the `origin` repo, you can
> use the following command:
>
> ```bash
> git remote show origin
> ```

### Add New Remote Repositories

This has already been introduced in the basic usage article. Use the following
command to add a new remote repository:

```bash
git remote add <name> <url>
```

### Remove Remote Repositories

To remove a remote repository, you can use the following command:

```bash
git remote remove <name>
```

> For example, if you want to remove the `origin` remote repository, you can
> use the following command:
>
> ```bash
> git remote remove origin
> ```

### Modify Remote Repositories

To modify a remote repository, you can use the following command:

```bash
git remote set-url <name> <url>
```

> For example, if you want to modify the `origin` remote repository to
> `git@github.com:github/gitignore.git`, you can use the following command:
>
> ```bash
> git remote set-url origin git@github.com:github/gitignore.git
> ```

### Rename Remote Repositories

To rename a remote repository, you can use the following command:

```bash
git remote rename <old-name> <new-name>
```

## Recap

Now. let's recap the usage introduced in this article and the previous one.

| Command | Description | Note |
| --- | --- | --- |
| `git init` | Initialize a new repo | The current directory cannot be a existing repo |
| `git clone <url>` | Clone an existing repo | The target directory should not exist |
| `git add <path>` | Add changes in `<path>` to the staging area | Commit will only apply to files added to the staging |
| `git status` | Check the status of the repo | Recommended to check the status before making a commit |
| `git diff` | Show the difference in the working directory | Use `--cached` to show difference in the staging area |
| `git commit` | Make a commit | Use `-m` if the message is short; commit often (changes in files can be retrieved easily in most cases) |
| `git remote add <name> <url>` | Add a remote repo | The default name for the remote repo is `origin`; you might need to following the instructions on first push (e.g., `git push --set-upstream origin master`) |
| `git remote show` | List remote repos | Use `-v` to show more details; you may specify the repo by name |
| `git remote set-url <name> <url>` | Modify a remote repo | |
| `git remote remove <name>` | Remove a remote repo | |
| `git remote rename <old> <new>` | Rename a remote repo | |
| `git push` | Push changes to the remote repo | If the fast-forward strategy fails, use `--force` to force push (this will discard some commits) |
| `git fetch` | Fetch changes from the remote repo | Use `git fetch <repo>` if you want to fetch any non-default branch |
| `git pull` | Fetch and merge changes from the remote repo | The default merging strategy may differ |
| `.gitignore` | Ignore files | Use `.gitignore` to ignore files you don't want to commit |
| `git branch <branch>` | Add a new branch | The new branch name should not exist; alternative solution: `git checkout -b <branch>` |
| `git switch <branch>` | Switch to another branch | Alternative solution: `git checkout <branch>` |
| `git merge <commit>...` | Merge branches | Use `--ff-only` to force fast-forward merge; use `--squash` to merge without creating a new commit |
| `git rebase <commit>...` | Rebase branches | Use `-i` or `--interactive` to use more features |
| `<rev>~<n>` | Get the `<n>`th generation ancestor commit of `<rev>` | |
| `<rev>^<n>` | Get the `<n>`th parent commit of `rev` | `^` alone means "parent" |
| `git stash` | Stash changes | Use `pop`, `clear` and `list` to retrieve, clear all, and list stashed changes |
| `git merge-base --all <commit>...` | Get the common ancestor | |
| `git restore <path>` | Restore files from commits | Use `--source=<tree>` to restore from commits other than `HEAD`; alternative solution: `git checkout <path>` |
| `git restore --staged <path>` | Unstage files | Alternative solution: `git reset HEAD <path>` |
| `git reset <commit>` | Change the head commit of a branch | Options: `--soft`, `--mixed`, `--hard`, `--merge`, or `--keep` |
| `git diff` | Show the difference | Use `--cached` to show the difference in the staging area; specify the paths after the commit(s) |
| `git apply <patch>` | Apply a patch | |
| `git show <commit>` | Show a commit | |
| `git revert <commit>...` | Revert a commit | Use `--no-commit` or `-n` to avoid creating multiple commits |
| `git cherry-pick <commit>...` | Cherry-pick a commit | Use `--no-commit` or `-n` to avoid creating multiple commits |
| `git grep <pattern> <path>...` | Find certain patterns in files | Search the whole repo if path not specified |
| `git reflog` | Show the reference log | |

## Conclusion

In this article, we introduced the advanced usage of Git, focusing on the
commands related to merging branches. I hope this article will help you
a lot in terms of managing multiple branches. We will introduce the usage
of submodules in the next article.

## Copyright

You may use this article for any purpose as long as the original author
and link (<https://lau.yeeyu.org/blog/git-usage-merging-en>) are clearly noted
at the place you use this article. This copyright notice overrides the
footnote of the website.
