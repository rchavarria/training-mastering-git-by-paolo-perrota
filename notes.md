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

## The four areas: git rest

## The four areas: more tools

## History: exploring the past

## History: fixing mistakes

## Finding your workflow

