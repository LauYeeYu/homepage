+++
title = 'Git Advanced Usage: Miscellaneous'
date = 2024-09-15T12:00:00-07:00
draft = false
tags = ["git", "git-en", "tutorial", "tutorial-en"]
series = "Git 5/5"
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
to push the tag, you should add `--tags` in your `git push` command. For
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
pointing to something outside the repository. Therefore, you should avoid
using symbolic links pointing to something outside the repository. Also, on
Windows, symbolic links are not supported well, so my suggestion is to avoid
using symbolic links except that you are sure that it won't cause any
problem, e.g., you only use it on Linux and points to a file in the repository.

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

## Git Alias

Are you fed up with typing `git commit --amend` or
`git pull --recurse-submodules` all the time? You can create an alias for the
long commands that you often use or the commands that is hard to remember.

Git alias allows you to create a shorthand for a long command.For example,
you can set `amend` as an alias for `commit --amend`, then `git amend` will
be equivalent to `git commit --amend`. This is similar to the alias in the
shell.

Git alias can be configured either with a command or by editing the
configuration file (`~/.gitconfig`). It is more recommended to use the
command to configure the alias.

To set an alias with a command, you can use the following command:

```bash
git config --global alias.<alias-name> <command>
```

If you want to set the alias for the current repository only, you should
remove the `--global` option.

Here are some aliases that I recommend:

1. `amend`: amend the last commit

    ```bash
    git config --global alias.amend 'commit --amend'
    ```

2. `unstage`: unstage files

    ```bash
    git config --global alias.unstage 'reset HEAD --'
    ```

3. `full-pull`: pull with submodules

    ```bash
    git config --global alias.full-pull 'pull --recurse-submodules'
    ```

4. `full-push`: push with submodules

    ```bash
    git config --global alias.full-push 'push --recurse-submodules=on-demand'
    ```

5. `log-graph`: show the log with a graph one line per commit

    ```bash
    git config --global alias.log-graph 'log --graph --oneline --decorate'
    ```

6. `fpush`: force push (often used after amending a commit)
  
      ```bash
      git config --global alias.fpush 'push --force'
      ```

## Global Git Ignore

Every operating system may have some automatically generated files that you
don't want to include it in the repository, say, the notorious `.DS_Store` in
macOS. Adding these files to `.gitignore` in every repository is really
annoying both for you and your team members. For you, you need to do this
every time you create a new repository. For your team members, this may be
unnecessary if they don't use the same operating system.

Instead, Git allows you to set a global file to ignore files. It is
configured in the `core.excludesFile` variable. You can set it by using the
following command:

```bash
git config --global core.excludesFile <path>
```

The default value is `$XDG_CONFIG_HOME/git/ignore` (if `$XDG_CONFIG_HOME` is not
set, then use `$HOME/.config/git/ignore`) if you don't set `core.excludesFile`.
(This may not take effect on Windows or macOS.)

> [!TIP]
> GitHub's official `.gitignore` templates provides a lot of system-specific
> `.gitignore` files (e.g., the files in the `Global` directory). You can
> find them at the [GitHub gitignore repository][gitignore].

[gitignore]: https://github.com/github/gitignore

## Git Garbage Collection

> [!WARNING]
> Git garbage collection will remove all unreferenced objects (e.g., commits
> that is deprecated because of a rebase or amend). After garbage collection,
> you cannot recover these objects. Therefore, you should be careful when
> using garbage collection. Generally speaking, you should not use garbage
> collection on your own projects; use it only for others' projects or when
> you are sure that you don't need the unreferenced objects.

Git will keep all the objects, even though they are not referenced by any
branches or tags. As I said before, committed files can be retrieved easily
in most situations. However, this will take up a lot of space in the `.git`
directory. Hence, Git allows you to free up the space through garbage
collection when you want to remove these unreferenced objects.

To run garbage collection, you can use the following command:

```bash
git gc
```

## Useful Third-party Tools

Here I recommend some third-party tools that I think are really useful.
You can install them if that helps you a lot. Also, it's okay if you don't
install them, as Git itself is already powerful enough.

### Git Delta

[Git Delta][git-delta] offers you a clear and intuitive view of the changes.
It improves the readability of the diff output dramatically. It can also
identify merge conflicts. With Git Delta configured, you are all set. The
output of `git diff` will be fascinating. Also, don't worry about the patch
format, as it only affects the output when displayed, and when you are
generating a patch, Git will still use the original format.

[git-delta]: https://dandavison.github.io/

Git Delta can be applied to `diff`, `blame`, `grep` and `show` commands.

### Git Searching Tools

There are some tools that can help you search in the Git repository, instead
of using `git grep`. The [ripgrep (rg)][ripgrep] and
[the silver searcher(ag)][ag] are two of them.

[ripgrep]: https://github.com/BurntSushi/ripgrep
[ag]: https://github.com/ggreer/the_silver_searcher

## Recap

Now, let's recap the usage introduced in all the Git tutorial articles (Yes,
this is the last one). The following table should be really helpful and
efficient for you to find the command you need (some command might be omitted
if they are not used frequently, e.g., alias commands).

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
| `git tag <tag-name>` | Add a lightweight tag | |
| `git tag -a <tag-name> -m <message>` | Add an annotated tag | |
| `git push --tags` | Push tags | |
| `git tag -l` | List tags | |
| `git tag -d <tag-name>` | Delete a local tag | |
| `git push --delete <remote> <tag-name>` | Delete a remote tag | |
| File permission | executable (`755`) and non-executable (`644`) | |
| `git gc` | Run garbage collection | All unreferenced objects will be removed, and not retrievable afterwards |

## Conclusion

In this article, we've introduced some other usage of Git that you should
know. We've covered tags, file permissions, symbolic links, files and
directories, renaming a file, Git alias, and some useful third-party tools.
This is the last article in the Git tutorial series. In the future, I may
write some other articles that will dig deeper into Git. I hope you've
learned something useful from this series and enjoy your time with Git.

## Copyright

You may use this article for any purpose as long as the original author
and link (<https://lau.yeeyu.org/blog/git-usage-misc-en>) are clearly noted
at the place you use this article. This copyright notice overrides the
footnote of the website.
