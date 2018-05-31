---
title: "Db Browser for Sqlite"
date: 2018-05-31T15:39:04+02:00
subtitle: "A multiplatform manager for Sqlite database"
author: Gabriele Teotino
tags: ["db", "sqlite"]
categories: ["tools"]
draft: false
---

[DB Browser for SQLite](http://sqlitebrowser.org/) is a high quality, visual, open source tool to create, design, and edit database files compatible with SQLite.

<!-- more -->

- Create and compact database files
- Create, define, modify and delete tables
- Create, define and delete indexes
- Browse, edit, add and delete records
- Search records
- Import and export records as text
- Import and export tables from/to CSV files
- Import and export databases from/to SQL dump files
- Issue SQL queries and inspect the results
- Examine a log of all SQL commands issued by the application

![Screenshot](db-browser-fro-sqlite-screenshot.png)


## Installation on Debian

```shell
sudo apt update
sudo apt install sqlitebrowser
```

## Installation on Ubuntu

```shell
sudo add-apt-repository -y ppa:linuxgndu/sqlitebrowser
sudo apt-get update
sudo apt-get install sqlitebrowser
```
