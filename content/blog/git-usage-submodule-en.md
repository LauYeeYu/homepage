+++
title = 'Git Advanced Usage: Submodule'
date = 2024-09-11T12:00:00-07:00
draft = false
tags = ["git", "git-en", "tutorial", "tutorial-en"]
series = "git-tutorial"
seriesAnnotation = "4/5"
showToc = true
categories = ['software']
+++

In the [previous article](/blog/git-usage-merging-en), we've introduced the
Git usage for merging operations. In this article, we will introduce how
to manage submodules in Git.

<!--more-->

*This article has other versions in different languages:
[简体中文 (Simplified Chinese)](/blog-zh-cn/git-usage-submodule-zh-cn),
[繁體中文 (Traditional Chinese)](/blog-zh-hk/git-usage-submodule-zh-hk).
If you are a native speaker of these languages, it is recommended to read
these versions.*

## Why we Need Something Like Submodule?

If you have never heard of submodules, you may wonder why we need them.
A common use case for submodule is when your project depends on other
projects and these projects should be managed separately.

For example, you are developing an Android application with a couple
C/C++ libraries. These libraries are developed by other teams and
you want them present at a certain place in your project. Obviously,
you can use scripts to download (or update) these libraries, every time
you need to build your project. However, this is not a good practice.
You may meet problems like version conflicts, forgetting to update
the libraries, etc. With submodules, you can manage these libraries
in a more organized way.

Another example is to manage your project with fine-grained control.
You may use a repository for each of your libraries, and a repository
for your project. With submodules, you can manage the dependencies
between these repositories. Other people can easily use your libraries
without cloning the whole large project.

## The Submodule and Alike

There are lots of tools providing similar functionalities as "submodules".
However, the Git design principle makes it challenging to manage submodules
conveniently. Different tools takes different trade-offs. Here are some
popular tools:

- [Git Submodule][git-submodule];
- [Git Subtree][git-subtree];
- [Gitslave][gitslave].

[git-submodule]: https://git-scm.com/docs/git-submodule
[git-subtree]: https://github.com/apenwarr/git-subtree
[gitslave]: https://gitslave.sourceforge.io/

### Git Submodule

Git submodule is the official submodule management tool by Git.

Pros:

- Almost every user has git submodule installed (because this is included in
  the official project).
- Easy for modifiers to use.

Cons:

- Difficult for users to use (need to update or initialize manually).
- If the submodule repo is unavailable, you cannot update or initialize
  the submodule. This makes the repo more of a "centralized" one because
  it depends on a single URL for each submodule.

### Git Subtree

[Git Subtree][git-subtree] is a project that subprojects to be included
within a subdirectory of the main project, optionally including the
subproject's entire history.

Pros:

- Easy for read-only users to use.
- The submodule included in the repo.

Cons:

- Taking up more space in the main repository.
- Difficult for modifiers to use.

### Gitslave

[Gitslave][gitslave] is a project that assists in assembling a meta-project
from a number of individual git repositories which operate as if they were
one git repository instead of many.

Pros:

- Really easy for modifiers to use.

Cons:

- Users must install gitslave to use the submodules.

### Summary of the Tools

As you can see, all the possible solutions have their disadvantages. For me,
I think forcing every user to install a tool other than the official Git
is unacceptable. And I think if users can manage simply with the instructions
in the README, it is acceptable. And I don't want the tools to increase
the burden on developer dramatically. Thus, I prefer to use Git submodule.
You may choose the one that fits your needs. In this article, we will focus
on the Git submodule.

## Typical Demands for Submodule

### User

As a user, you may want to:

- clone the main repository with submodules (see
  [Clone Repo with Submodules](#clone-repo-with-submodules));
- initialize the submodules on an existing repository (see
  [Initialize or Update Submodules to the Current Commit's Version](#initialize-or-update-submodules-to-the-current-commits-version));
- update the submodules to the version specified by the current commit (Git
  will not update the submodules on pull by default) (see
  [Initialize or Update Submodules to the Current Commit's Version](#initialize-or-update-submodules-to-the-current-commits-version));
- list all the submodules in the repository (see
  [List Submodules](#list-submodules)).

### Developer

As a developer, you may want to:

- add a submodule to the repository (see [Add a Submodule](#add-a-submodule));
- update the submodule to a specific version (see
  [Update a Submodule to a Specific Version](#update-a-submodule-to-a-specific-version));
- remove a submodule from the repository (see
  [Remove a Submodule](#remove-a-submodule));
- modify the URL of a submodule (see
  [Modify the URL of a Submodule](#modify-the-url-of-a-submodule));
- list all the submodules in the repository (see
  [List Submodules](#list-submodules)).

## Clone Repo with Submodules

If you are going to clone a repository with submodules, you can add the
`--recurse-submodules` option to the original parameters:

```bash
git clone <repo> [dir] --recurse-submodules
```

## Initialize or Update Submodules to the Current Commit's Version

> [!TIP]
> If you want to update the submodules on git pull operations, you can use the
> `--recurse-submodules` option.

If you want to initialize or update the submodules to the version specified
by the current commit, you can use the following command:

```bash
git submodule update --init --recursive
```

This will bring the submodules to the version specified by the current commit
recursively (i.e., the submodules' submodules will also be updated).

## List Submodules

To list all the submodules in the repository, you can simply use the following
command:

```bash
git submodule
```

## Add a Submodule

To add a submodule to your repository, you can use the following command:

```bash
git submodule add <repo> [dir]
```

For the convenience of the users, it is recommended to use `https` protocol
instead of `ssh` protocol. This will allow users to use the submodule even
without an account on your Git hosting service. If you want to use the `https`
for your repo, but `ssh` for your actual development, you may set up a
URL substitution in Git. For example, if you are using GitHub, you can use
the following command to set up the substitution when your Git encounters
the `https` protocol (if you want to apply this rule only to the current
repository, you can remove the `--global` option):

```bash
git config --global url.git@github.com:.insteadOf https://github.com/
```

## Update a Submodule to a Specific Version

As a developer, you may want to change the submodule to a specific version
in the new commit.

To achieve this, you should

1. enter the submodule;
2. do the changes similar to the main repository (the aim is to change the
   `HEAD` to the commit you want), and please remember to push the changes
   if the new commit has not been present in the remote repository;
3. leave the submodule;
4. add the changes in the submodule to the main repository (i.e.,
   `git add <submodule>`).
5. commit the changes in the main repository (you can push the changes then).

The operations in the submodule (step 2) are really similar to the operations
in the parent repository. The only difference is that you may be at a detached
`HEAD` state in the submodule.

> [!TIP]
> You can use `--recurse-submodules=on-demand` option for `git push` to push
> the changes in the submodules on demand, i.e., use
> `git push --recurse-submodules=on-demand` to prevent you from forgetting to
> push the new commits.

If you are going to update the submodule to its latest commit, you can use
the following command in the parent repository:

```bash
git submodule update --remote
```

## Remove a Submodule

You may [list the submodules](#list-submodules) first. Then use the following
command in the parent repository to remove the tracking information:

```bash
git submodule deinit <path-to-submodule>
```

After that, remove the submodule directory:

```bash
git rm <path-to-submodule>
```

And you should also remove the staged changes for the submodule:

```bash
git rm --cached <path-to-submodule>
```

Finally, commit the changes in the parent repository.

## Modify the URL of a Submodule

If you want to modify the URL of a submodule, you should modify the
`.gitmodules` file in the root directory of the parent repository.

Then, use the following command to update the submodule:

```bash
git submodule sync
```

Finally, commit the changes in the parent repository.

## Recap

Now, let's recap the usage introduced in this article and the previous ones.

| Command | Description | Note |
| --- | --- | --- |
| `git init` | Initialize a new repo | The current directory cannot be a existing repo |
| `git clone <url>` | Clone an existing repo | The target directory should not exist; use `--recurse-submodules` to clone with submodules ready |
| `git add <path>` | Add changes in `<path>` to the staging area | Commit will only apply to files added to the staging |
| `git status` | Check the status of the repo | Recommended to check the status before making a commit |
| `git diff` | Show the difference | Use `--cached` to show the difference in the staging area; specify the paths after the commit(s) |
| `git commit` | Make a commit | Use `-m` if the message is short; commit often (changes in files can be retrieved easily in most cases) |
| `git log` | Show the commit history | Use `--oneline` to show every commit in one line; use `--graph` to show commit graph |
| `git remote add <name> <url>` | Add a remote repo | The default name for the remote repo is `origin`; you might need to following the instructions on first push (e.g., `git push --set-upstream origin master`) |
| `git remote show` | List remote repos | Use `-v` to show more details; you may specify the repo by name |
| `git remote set-url <name> <url>` | Modify the adress of a remote repo | |
| `git remote remove <name>` | Remove a remote repo | |
| `git remote rename <old> <new>` | Rename a remote repo | |
| `git push` | Push changes to the remote repo | If the fast-forward strategy fails, use `--force` to force push (this will discard some commits) |
| `git fetch` | Fetch changes from the remote repo | Use `git fetch <repo>` if you want to fetch any non-default branch |
| `git pull` | Fetch and merge changes from the remote repo | The default merging strategy may differ |
| `.gitignore` | Ignore files | Use `.gitignore` to ignore files you don't want to commit |
| `git branch <branch>` | Add a new branch | The new branch name should not exist; alternative solution: `git checkout -b <branch>` |
| `git switch <branch>` | Switch to another branch | Use `--detach` for other references; alternative solution: `git checkout <branch>` |
| `git merge <commit>...` | Merge branches | Use `--ff-only` to force fast-forward merge; use `--squash` to merge without creating a new commit |
| `git rebase <commit>` | Rebase branches | Use `-i` or `--interactive` to use more features |
| `<rev>~<n>` | Get the `<n>`th generation ancestor commit of `<rev>` | |
| `<rev>^<n>` | Get the `<n>`th parent commit of `rev` | `^` alone means "parent" |
| `git stash` | Stash changes | Use `pop`, `clear` and `list` to retrieve, clear all, and list stashed changes |
| `git merge-base --all <commit>...` | Get the common ancestor | |
| `git restore <path>` | Restore files from commits | Use `--source=<tree>` to restore from commits other than `HEAD`; alternative solution: `git checkout <path>` |
| `git restore --staged <path>` | Unstage files | Alternative solution: `git reset HEAD <path>` |
| `git clean` | Clean the working directory | Use `-i` to use interactive mode; use `-f` to force the deletion |
| `git reset <commit>` | Change the head commit of a branch | Options: `--soft`, `--mixed`, `--hard`, `--merge`, or `--keep` |
| `git apply <patch>` | Apply a patch | |
| `git show <commit>` | Show a commit | |
| `git revert <commit>...` | Revert a commit | Use `--no-commit` or `-n` to avoid creating multiple commits |
| `git cherry-pick <commit>...` | Cherry-pick a commit | Use `--no-commit` or `-n` to avoid creating multiple commits |
| `git grep <pattern> <path>...` | Find certain patterns in files | Search the whole repo if path not specified |
| `git reflog` | Show the reference log | |
| `git submodule update --init --recursive` | Initialize or update submodules | Use `--recurse-submodules` on pull to update submodules along with the parent repo |
| `git submodule` | List submodules | |
| `git submodule add <repo> [dir]` | Add a submodule | Recommended to use `https` instead of `ssh` for the convenience of users |
| `git submodule update --remote` | Update submodules to the latest commit | |
| `git submodule deinit <path-to-submodule>` | Remove the tracking information of a submodule | |
| `git submodule sync` | Update the submodule according to the current `.gitmodules` | |

## Conclusion

In this article, we've introduced the submodule management in Git. In the
next article, we will cover the miscellaneous topics in Git. Hope you enjoy
the time with Git!

## Copyright

You may use this article for any purpose as long as the original author
and link (<https://lau.yeeyu.org/blog/git-usage-submodule-en>) are clearly noted
at the place you use this article. This copyright notice overrides the
footnote of the website.
