+++
title = 'Git Advanced Usage: Submodule'
date = 2024-08-29T12:00:00-07:00
draft = true
tags = ["git", "git-en", "tutorial", "tutorial-en"]
showToc = true
+++

In the [previous article](/blog/git-basic-usage-en), we've introduced the
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

- [Git Submodule][git-submodule]: The official submodule management tool
  provided by Git.

  Pros:
  - Every user has git-submodule installed.
  - Easy for modifiers to use.

  Cons:
  - Difficult for users to use (need to update or initialize manually).
  - If the submodule repo is unavailable, you cannot update or initialize
    the submodule. This makes the repo more of a "centralized" one because
    it depends on a single URL for each submodule.
- [Git Subtree][git-subtree]: A project that subprojects to be included
  within a subdirectory of the main project, optionally including the
  subproject's entire history.

  Pros:
  - Easy for read-only users to use.
  - The submodule included in the repo.

  Cons:
  - Taking up more space in the main repository.
  - Difficult for modifiers to use.
- [Gitslave][gitslave]: A project that assists in assembling a meta-project
  from a number of individual git repositories which operate as if they were
  one git repository instead of many.

  Pros:
  - Really easy for modifiers to use.

  Cons:
  - Users must install gitslave to use the submodules.

[git-submodule]: https://git-scm.com/docs/git-submodule
[git-subtree]: https://github.com/apenwarr/git-subtree
[gitslave]: https://gitslave.sourceforge.io/

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
   `HEAD` to the commit you want);
3. leave the submodule;
4. add the changes in the submodule to the main repository (i.e.,
   `git add <submodule>`).
5. commit the changes in the main repository.

The operations iin the submdule (step 2) are really similar to the operations
in the parent repository. The only difference is that you may be at a detached
`HEAD` state in the submodule.

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
