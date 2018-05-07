---
title: "Create a GitHub repo from a local git"
date: 2018-05-07T15:15:56+02:00
subtitle: "Adding a local project to Github from Windows using Visual Studio 2015 and GitHub Desktop."
author: Gabriele Teotino
tags: ["github", "git", "github desktop"]
categories: ["dev"]
draft: false
---
This is **not** the best way to add a local project to github. It is just **a way** that works using *Visual Studio* and *GitHub Desktop* with a small manual intervention.

A shorter path is possible by letting *GitHub Desktop* create the repository.

<!--more-->

## Visual Studio
Using Visual Studio right click on the solution in *Solution Explorer* and select *Add Solution to Source Control*. This is equivalent to: `git init`, creating *.gitattributes* and *.gitignore* files and `git add .`.

## github.com
[Create a new repository](https://help.github.com/articles/creating-a-new-repository/) on GitHub, add a description and do **not** select *Initialize this repository with a README*.

## Command line
Open *Git Bash* and go to the solution directory.

Copy the **https** url from the repository creation page.

` git remote add origin https://github.com/gabrieleteotino/CommandLineExperiments.git`

Verify the new remote

`git remote -v`

## GitHub Desktop
From **GitHub Desktop** select *Add a local repository* browse to the solution folder and select *Add Repository*.

After a few seconds the repository is ready to be pushed. Click on **Publish branch**.
