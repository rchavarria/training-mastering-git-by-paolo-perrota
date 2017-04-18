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

## The four areas: git rest

## The four areas: more tools

## History: exploring the past

## History: fixing mistakes

## Finding your workflow

