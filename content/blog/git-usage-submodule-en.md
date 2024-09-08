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

- clone the main repository with submodules;
- initialize the submodules on an existing repository;
- update the submodules to the version specified by the current commit (Git
  will not update the submodules on pull by default);
- list all the submodules in the repository.

### Developer

As a developer, you may want to:

- add a submodule to the repository;
- update the submodule to a specific version;
- remove a submodule from the repository;
- modify the URL of a submodule;
- list all the submodules in the repository.
