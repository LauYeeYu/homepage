+++
title = 'Git Advanced Usage: Miscellaneous'
date = 2024-09-11T12:00:00-07:00
draft = true
tags = ["git", "git-en", "tutorial", "tutorial-en"]
showToc = true
+++

In the [previous article](/blog/git-usage-submodule-en/), we've covered
the usage of submodules. In this article, we'll introduce some other usage
of Git that that you should know.

<!--more-->

*This article has other versions in different languages:
[简体中文 (Simplified Chinese)](/blog-zh-cn/git-usage-misc-zh-cn),
[繁體中文 (Traditional Chinese)](/blog-zh-hk/git-usage-misc-zh-hk). If you are
a native speaker of these languages, it is recommended to read
these versions.*

## Tag

Tags are used to mark a specific commit. They are often used to mark
releases. There are two types of tags: lightweight tags and annotated tags.

The lightweight tag only has a name and points to a commit, while the
annotated tag has a message alongside its name.

### Conventional Version Tagging

If you want to add a tag for a release, it is recommended to use the
[semantic versioning][semver] format, which is used commonly in the software
industry.

[semver]: https://semver.org/

In semantic versioning format, a release tag name should be like
`vX.Y.Z`, where `X`, `Y`, and `Z` are integers. `X` is the major version,
`Y` is the minor version, and `Z` is the patch version.

When you are making a release, you should increment

- the major version `X` if the changes are not backward compatible,
- the minor version `Y` if you add new features in a backward-compatible way,
- the patch version `Z` if you fix bugs (no new features) in a
  backward-compatible way.

If you increment some version, then the next few numbers should be reset to 0.
For instance, if you make a minor version release, from `v1.1.4` to `v1.2.Z`,
then `Z` should be 0.

For major version less than 1, i.e., `v0.Y.Z`, it is considered as an initial
development phase. In this case, breaking changes may happen anywhere. But
after `v1.0.0`, which is considered as a milestone of stability, the
versioning should follow the semantic versioning rules, especially
compatibility.

### Create a Tag

For a lightweight tag, you can use the following command:

```bash
git tag <tag-name> <commit-id>
```

If you don't specify the commit ID, the tag will point to the latest commit.
(Actually, this is really common.)

> For example, if you want to create a tag `v1.0.0` for the latest commit,
> you can use the following command:
>
> ```bash
> git tag v1.0.0
> ```

For an annotated tag, you can use the following command:

```bash
git tag -a <tag-name> -m <message> <commit-id>
```

Also, you can omit the commit ID to point to the latest commit.

> For example, if you want to create an annotated tag `v0.1.0` for the latest
> commit with a message `Supporting multiple platforms`, you can use the
> following command:
>
> ```bash
> git tag -a v1.0.0 -m "Supporting multiple platforms"
> ```

### Push a Tag

By default, the tag will not be pushed in the `git push` command. If you want
to push the tag, you should add `--tag` in your `git push` command. For
example:

```bash
git push --tags
```

### List Tags

You can list all tags by using the following command:

```bash
git tag -l
```

### Delete a Local Tag

You can delete a local tag by using the following command:

```bash
git tag -d <tag-name>
```

> For example, if you want to delete the tag `v1.0.0`, you can use the
> following command:
>
> ```bash
> git tag -d v1.0.0
> ```

### Delete a Remote Tag

You can delete a remote tag by using the following command:

```bash
git push --delete <remote-name> <tag-name>
```

> For example, if you want to delete the tag `v1.0.0` in the remote
> repository `origin`, you can use the following command:
>
> ```bash
> git push --delete origin v1.0.0
> ```

## File Permission

In Git, there are two types of file permissions: executable and non-executable.

> [!TIP]
> If you don't use a Unix-like system (Windows is not Unix-like and
Linux and macOS), the file permission system may not work as expected.

For executable files, the exact permission is `755` (which means anyone can
read and execute the file, but only the owner can write to it). For
non-executable files, the exact permission is `644` (which means anyone can
read the file, but only the owner can write to it, and nobody can execute it).

## Symbolic Links

In Unix-like systems, symbolic links are commonly used. A symbolic link points
to a file through a path (either relative or absolute), and acts as if it is
the place it points to. If the place is not valid, then it will be a broken
symbolic link, but this won't cause any problem itself.

Git supports symbolic links, but it may not work as expected if it is
pointing to something outside the repository. Therefore, you should avoid using
symbolic links outside the repository. Also, on Windows, symbolic links are
not supported well, so my suggestion is to avoid using symbolic links except
that you are sure that it won't cause any problem, e.g., you only use it on
Linux and points to a file in the repository.

## Files and Directories

In Git, there are no directories, only files. For every file, it has a path
from the root of the repository. For example, `a/b.txt` means a file `b.txt`
is in the directory `a`. Therefore, if you want to add a directory, you should
add a file in that directory.

## Rename a File

If you want to move a file, this is actually equivalent to renaming the file.

You can move the file and add the common ancestor directory of the old and new
one to allow git to track this change. On commit, Git will try to find the
renaming relationship (but sometimes it may fail or have a wrong result).
