---
title: "Git cheatsheet"
date: 2019-11-17T22:55:22+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["git", "github"]
categories: ["dev"]
draft: true
---

## Basic

    git init
    git fetch
    git merge
    # fetch+merge
    git pull
    git push
    git commit -m "message"

## Cloning

    git clone <repository> <directory>
    git clone git://host.xz[:port]/path/to/repo.git/
    git clone git://host.xz[:port]/path/to/repo.git/ ./repo-alternative-name

## Branches

    # View branches
    git branch

    # Create a new branch
    git branch [branch-name]

    # Switch to branch
    git checkout [branch-name]

    # Combine branch-name into the current branch
    git merge [branch-name]

## Graph

    git log --all --decorate --oneline --graph | head

    alias git-graph='git log --all --decorate --oneline --graph | head'

## Reset

    # Remove all commits after <commit>
    git reset <commit>

    # Remove all commits after [commit] and alters the working directory
    # ALL LOCAL CHANGES WILL BE LOST
    git reset --hard [commit]

## Submodules

    git clone --recurse-submodules -j8 <repository> <directory>
    git submodule add -b <branch-name> <repository> <directory>
    git submodule update --remote --merge
    git submodule status
