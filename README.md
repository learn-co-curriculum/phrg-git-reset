# Git Log & Git Reset

`git` is a powerful and complex CLI that manages versions of your code. This lesson explores using the `git log` and `git reset`, commands that help us manipulate our local commits.

## The Manual

It is helpful to utilize [`git` documentation](https://git-scm.com/doc) often as you grow as a developer. Sometimes it is even easier to launch `git` documentation in your shell session. Let's do this right now for `git log` and `git reset`, using the `--help` option. You can pass the `--help` option to any `git` command for more information.

```
git log --help
```

```
git reset --help
```

Take a few minutes to read about ways to use these commands.

---

# Git Log

The `git log` command helps us track what commits we have locally. While Github presents a great interface to review commits that have been `push`ed, Github does not (and cannot) know what is stored locally on our own machines. This makes `git log` an indispensable tool, as we often need to review and/or change our commits before we send them up to Github.

`git log` presents very informative output. Here is the output for a new project we just started. (NOTE: `git log` output [is formattable](https://git-scm.com/docs/git-log#_pretty_formats). This lesson uses the default git log format, which is medium.)

```
commit 39ff2a73e31c7f2016d6374538c4c987e8e24e71 (HEAD -> example_branch)
Author: Garett Arrowood <garettarrowood@gmail.com>
Date:   Thu Sep 6 06:52:34 2018 -0400

    Add doggy image

commit fb9106cdc9ca1fc4772f0319ebd3e0d5aa3b3833
Author: Garett Arrowood <garettarrowood@gmail.com>
Date:   Thu Sep 6 06:52:10 2018 -0400

    Add kitty image

commit aa45b596d39a25d3a1fc32b6d382e5be85eccab1 (origin/master, master)
Author: Garett Arrowood <garettarrowood@gmail.com>
Date:   Thu Sep 6 06:47:06 2018 -0400

    First commit
```

By the look this output, our project has just 3 commits; a "First commit", a kitty image commit, and a doggy image commit. Let's break this down.

- The `commit` line reports the full git [SHA-1 hash](https://blog.thoughtram.io/git/2014/11/18/the-anatomy-of-a-git-commit.html#introducing-sha-1). These are the unique identifiers we can use to grab individual commits.
- The `Author` line informs us of the contributor's git username and email.
- The `Date` reports when the commit was created.
- The text below is the commit's subject. If a commit were to also contain a description, this would also be present.

That leaves the information in parentheses after the commit SHAs. The bottom commit shows `(origin/master, master)`. This indicates that it is the last commit we have from master on our current branch. Moving to the top commit, this shows `(HEAD -> example_branch)`. `example_branch` is the name of our current branch. This branch was created from master after its "First commit". [`HEAD`](https://stackoverflow.com/questions/2304087/what-is-head-in-git) represents the exact commit we are working from at the moment. Thus, our last commit is "Add doggy image", and we currently have the `example_branch` checked out.

# Git Reset

`git reset` is a powerful command that is used to undo local changes to the state of a Git repo. The command takes two specifications. One specification is a flag (e.g. `--this-is-a-flagged-option`) that specifies what mode `git reset` should run. The three modes are `--soft`, `--mixed` and `--hard`. The second is an argument that is a commit reference. When not passed a mode or ref (short for reference), `git reset` uses `--mixed` and `HEAD`. So:

```
git reset
```

is actually performing:

```
git reset --mixed HEAD
```

## Commit Refs

Before diving into modes, let's discuss how we point to different states in our git repo using commit references.

Commit refs come in a few forms. One way to reference a commit is to use the entire SHA-1 hash of a commit. From our `git log` output, the latest commit ref would be `39ff2a73e31c7f2016d6374538c4c987e8e24e71`. But since git SHAs are so unique, it is more common and easier to simply use its first 7 characters, which would be `39ff2a7` for this commit. A third option would be to use `HEAD`. Since `39ff2a7` is where our project is currently pointed (it is the `HEAD`), this is also an option.

These three commands are identical in our project's current state:

```
git reset 39ff2a73e31c7f2016d6374538c4c987e8e24e71
git reset 39ff2a7
git reset HEAD
```

If we want to `reset` to an earlier reference, all we need to do is grab the corresponding SHA. Let's reset back to our out "Add kitty image" commit.

```
git reset fb9106c
```

It is often easier to reset using `HEAD` as a point of reference instead of git SHAs though. Especially if we are only navigating a handfull of commits. The syntax for using `HEAD` to reference a commit is to attach the `~` or `^` after it and specify how many commits to move backwards. `~` or `^` perform identically.

Since our "Add kitty image" is only `1` commit back, instead of using the git SHA, we could use either of these commit refs to accomplish the same result as `git reset fb9106c`.

```
git reset HEAD~1
git reset HEAD^1
```

## Reset modes

`git reset` has three different modes we can pass it. The mode determines what happens to the changes we roll back in the reset.

### `--hard`

Generally speaking, do not use this option. `--hard` will wipe out the changes we reset. It is more destructive than any of the rest of the modes.

Using our `git log` example, let's run `git reset --hard HEAD~1` on our `example_branch`.

1. Our local copy now points to the "Add kitty image" commit.
1. There are no staged changes.
1. There are no untracked changes.

### `--soft`

`--soft` will take the changes we have reset and leave them staged.

Using our `git log` example, let's run `git reset --soft HEAD~1` on our `example_branch`.

1. Our local copy now points to the "Add kitty image" commit.
1. Our "Add doggy image" content is staged.
1. There are no untracked changes.

If we wish add this content back to our project, we would need to `git commit` and `git push --force-with-lease`.

### `--mixed` (the default)

We never really see `--mixed` because it is the default option. `--mixed` will take the changes we have reset and leave them untracked.

Using our `git log` example, let's run `git reset HEAD~1` on our `example_branch`.

1. Our local copy now points to the "Add kitty image" commit.
1. There are no staged changes.
1. Our "Add doggy image" content is untracked, but present.

If we wish add this content back to our project, we would need to `git add`, `git commit`, and `git push --force-with-lease`.

### Why force the push?

When we reset a commit that has been `push`ed, we are removing the uniquely identified commit from our version control system, Github. Even if we end up pushing up the exact same content, git will assign it a new commit SHA with a new datestamp. Github will detect that a commit SHA is missing form our commit history when we attempt to push normally, and require that we force it and recognize we have made a destructive change.

## Resources

- [`git` documentation](https://git-scm.com/doc)
- [`git log` documentation](https://git-scm.com/docs/git-log)
- [`git reset` documentation](https://git-scm.com/docs/git-reset)
- [Undoing Changes](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)
- [The Anatomy of a Git Commit](https://blog.thoughtram.io/git/2014/11/18/the-anatomy-of-a-git-commit.html)
