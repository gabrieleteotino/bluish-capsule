---
title: "Introduction"
date: 2019-02-15T15:05:11.331+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: false
---

Clone the cource code

```shell
cd dev
git clone https://github.com/angular-university/angular-ngrx-course.git
cd angular-ngrx-course/
```

The material for the lessons is available in different branches (*git branch -a*)

- 1-auth
- 1-auth-finished
- 2-entity
- 2-entity-finished
- 3-lessons
- 3-lessons-finished

Select the appropriate branch
```shell
git checkout -b 1-auth origin/1-auth
```

Install the dependencies and start the local express server

```shell
npm install
npm run server
```

Then in a new console

```shell
npm start
```

Finally open **Chrome** and navigate to [http://localhost:4200/](http://localhost:4200/)
