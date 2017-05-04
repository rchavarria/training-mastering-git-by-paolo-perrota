# Mastering git

## The four areas: introduction

Four areas:

1. Working area: your current files and folders
2. Repository: contains the complete history of the project
3. Stage or index: where changes live before being committed
4. Stash: temporarily area

Two questions:

1. How does this command move information across the 4 areas?
2. How does this command change the Repository area?

### working area

Git doesn't care too much about it. It looks at it as a temporal space.

### Repository

Objects are stored on `.git/objects`

Do you remember the four kind of objects? tree, blob, commit and tag. All of them are immutable.

Each commit is a snapshot of the working directory at a given point on time

Branches point to a commit, and commits point to other commits (one or several)

HEAD point to a branch, that points to a commit,...

### Index

You don't move changes from working area to repository area. You must pass through index (or staging area)

This area is implemented in a binary file, `.git/index`

In a clean status, where index area is empty, it's not really empty, it contains the same commits that the repository.

`git diff` compares working area with index area

`git diff --cached` compares index area with repo area

## The four areas: basic workflow

Two questions:

1. How does this command move information across the 4 areas?
2. How does this command change the Repository area?

We'll see the basic commands (add, commit, rm,...) under the light of these two questions.

Edit file -> Stage file -> Commit file

`git add` copy files from the working area to the index area

`git diff` shows differences between working and index

`git diff --cached` shows differences between index and repo

`git commit` copy files from index to repo. So it moves data and changes the repo. While `git add` doesn't change the repo

`git checkout` changes the repo, for example, `git checkout <branch>` changes the current commit (HEAD). It also copy changes to the index and the working areas. So it leaves the three areas in the same state, in a clean state.

Let's imagine you have a file in the working and in the index area (but not in the repo), so the file is staged. How can you remove it? Two options:

1. `git rm <file> --cached` to remove from the index and keep it in the working area
2. `git rm <file> -f` to force the removal, and remove from everywhere

`git mv <file>` moves the file in the working area and copy the changes to the index. It's a shortcut for `mv file; git add <new file>; git add <old file>`. You have to *add* the new and the removed files for git to detect the file has been moved. `git mv` saves you some commands. `git mv` moves files in the working and in the index areas at the same time, but it doesn't change the repo

## The four areas: git reset

To understand `reset`, you must understand the three areas and how branches work in git

It's confusing most of the times because it does different things in different contexts

Commands that move branches: `commit`, `merge`, `rebase`, `pull`,... But none of them are specialized in moving branches. They move them becuase they create new commits.

`reset` is specialized in moving branches. That changes the repository area.

But, depending on its options, it does different things on index and working area.

`reset --hard` makes index and working look like the repo. It changes the 3 areas to be the same.

`reset --mixed` changes index, but not working area.

`reset --soft` does not change any area, just move the branch.

With `git reset --hard <commit>` we can undo all our changes back in time until that commit

For example. Let's imagine I have changes in working area and they are staged in the index, but not committed to the repo. With `git reset [--mixed] HEAD <file>`, git moves the current branch to the commit where `HEAD` points, it is, the current commit. With the optional option `--mixed`, git changes the index area, but not the working area. So index and repo are the same, and working is different. It's like we unstaged our changes, so we have to add and commit if we want them to be in the repo.

If you want to undo completely your stated changes, `get reset --hard HEAD`. It doesn't move the current branch (because `HEAD` points already there). But it changes index and working. This is the easiest way to **destroy** things in git.

## The four areas: more tools

Now, we'll learn about the *stash* area.

It doesn't change, unless you explicitly say it will change.

Stash can be used to *hide* temporal changes, to go back to them soon.

`git stash --include-untracked` moves changes from working and index areas to stash area

`git stash list` shows all your changes are on the stash areas

`git stash apply` get changes from the stash and moves them to working area and index, depending on where they were before stashing.

`git stash clear` cleans the stash area.

**Solving conflicts**

If after a merge, we have conflicts, git will modify the conflicted file. We need to edit it manually to fix the conflict. Then, to mark the file as resolved, we `git add` the file. That tells git that the file is ready to be committed.

**Working with paths**

To revert changes in just one file to the index area, we can do: `git reset [--mixed] HEAD <file>`.

So, to revert changes of just one file to the working area, we could thing we shoudl do: `git reset --hard HEAD <file>`. But we get an error `Can not hard reset with paths`. Instead, we shoudl do a checkout: `git checkout HEAD <file>`

## History: exploring the past

`git log --graph --decorate --oneline`: shows the history log, graphically, decorated and each commit is just one line

`git show` shows information about a single commit. How can we refer to that commit?

- `git show <commit hash>`
- `git show HEAD`
- `git show <branch name>`
- `git show HEAD~2`: two commits before `HEAD`
- `git show HEAD^`: parent commit

`git blame <file>` shows who modified each line of the file

`git diff <commit 1> <commit 2>` shows the difference between two commits

`git log --grep <topic>`: filter commits shown

`git log <branch 1>..<branch 2>` shows all commits in `branch 2` that are not present in `branch 1`

## History: fixing mistakes

The golden rule: **Never rebase shared commits**, because `rebase` (and most commands from this module) changes the history, and it will confuse other users

#### Fixing the latest commit

`git commit --amend`. It will join changes in the latest commit with changes in the index area. It's like a mini-rebase operation.

#### Interactive rebasing

With `git rebase --interactive` we have a powerful history edition tool. You can pick, reword, ammend, edit, remove,... previous commit. But be careful, do not change history of shared commits.

#### The reflog

Every time a reference moves in the repository, git logs that movement. A reference can be `HEAD`, a branch, a tag,...

You can see the log with `git reflog <reference>` (`git reflog HEAD`)

#### Reverting commits

Let's imagine we have some changes in a commit, way down in the history, and it's shared. How we revert those changes? We have several options:

1. Use an interactive rebase, and pick all commits but the one we want to remove. But that will change git history, and commits are already shared, so it's not a good option
2. Revert those changes manually, and create a new commit

Or, we have `git revert <commit hash>`. That will create a new commit, with exactly the opposite changes we had in `commit hash`. 

And, it's more or less safe, because it doesn't modify existing commits.

But be careful. Merge commits are tricky. So, the general rule is **not revert merge commits**.

## Finding your workflow

There are many many ways of working with git within a team. So, you have to make some decissions:

1. Distribution model
2. Branching model
3. Constraints: rebase or merge, push unstable code,...

### Distribution model

This answer questions such as: how many repos do you have? who can access what repo?...

The simplest (but not easiest) model is the **Peer to peer model**, where each developer has a copy of the complete repo. Each dev sees other devs repos as remotes. Each dev pull changes from each other.

In that model, which repo is the reference? who owns the repo where releases are created? Then, a new kind of repo appears, the *blessed* repo. It's in a machine where nobody works. It's a server. And everybody pushes and pulls from that repo. It's kind of central repo. And most people call it *origin*. That *degenerates* in a *Centralized model*.

**Pull request model**

In other models, not all devs can write (push) changes to the blessed repo. There are maintainers and contributors. Contributors can only pull from the blessed repo. Maintainers can pull and push to that repo.

How do contributors contribute? They send a message to a maintainer, they create a *pull request*. GitHub makes this very easy, but it doesn't have to be integrated with the central server.

This is the most common model used in Open Source projects

**Dictator and Lieutenants model**

It's a more complex model than the PR model. In this one, there are one main project, with a maintainer, and several subprojects. Each project is a PR model (maintainer + contributor). Then, each subproject maintainer works as a main project contributor.

Linux uses this model. Subproject maintainers are called Lieutenants. Main project maintainer is called *Benevolent dictator*.

### Branching model

You must differenciate between *stable* and *unstable* branches.

Almost all projects have a branche where everybody integrate their changes. The more stable this branch is, the better.

Another branch to look for is: what branch should we use to release a version? People usually call it `release`. It usually is even more stable than the integration (or `master`) branch.

A very common model is the *Feature branch model*. In this model, a feature is developed in unstable branches. When feature is done, it's integrated (merged) into a more stable branch.

There are another type of branches, *hotfix* branches. When you need a change in two parallel branches, you can do them in one branch and cherry-pick to the second branch. Or, you can develop the changes in a third branch (the `hotfix`) and then merge the hotfix in each parallel branches, doing two merges.

### Constraints

Examples:

1. `rebase`, don't `merge`. Or just the opposite: `merge`, don't `rebase`
2. Only developer *X* can do *Y* on branch *Z*
3. Don't push to a red build (in a project using continuous integration)
4. Squash a feature to a single commiti before you merge it to `master`

### We need to talk about GitFlow

### Growing a workflow

Don't design a workflow on your own. It'll be very difficult to imagine all possible situations.

Instead, **grow** your workflow.

Basic workflow to start with:

1. Distribution model
  
  - Centralized

2. Branching model

  - One integration branch (`master`)
  - One feature branch per feature

3. Constraints

  - Keep `master` stable, fix it ASAP
  - Integrate feature branches every few days
  - Use `merge` over `rebase` by default

> Simple, clear purpose and principles give rise to complex and intelligent behavior. Complex rules and regulations give rise to simple and stupid behavior ~ Dee Hock, Visa company

### Thinking in Git





