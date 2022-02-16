---
title: "NG production Build"
date: 2018-08-07T13:48:29.732+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

<!--more-->

Open **environment.prod.ts**

```json
export const environment = {
  production: true,
  apiUrl: 'api/'
};
```

Using visual studio code search for "localhost" to verify that we don't have any harcoded paths.

We should only find **environment.ts** and **app.module.ts** in the *JwtModule* config (wich we can safely ignore).

Production build does the following optimizations:

- AOT
- production environment
- bundling
- minification
- uglification
- dead code elimination

```shell
ng build --prod
```

To avoid some problems with *alertifyjs* css we can disable the build optimizer. Not much is loss in terms of file size but the optimizer is very aggressive and can break some stuff.

```shell
ng build --prod --build-optimizer=false
```
