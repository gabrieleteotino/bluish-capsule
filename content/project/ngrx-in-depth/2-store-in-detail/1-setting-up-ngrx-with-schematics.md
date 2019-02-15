---
title: "Store in detail"
date:2019-02-15T17:16:35.275+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

The requireq NgRx libraries are already configured in the the brach **1-auth**.

They are from [NgRx repository](https://github.com/ngrx/platform) and the [offical documentation](https://ngrx.io/guide/store/install)

To install manually

```shell
npm install @ngrx/store --save
npm install @ngrx/store-devtools --save
npm install @ngrx/effects --save
npm install @ngrx/router-store --save
npm install @ngrx/entity --save
```

To install and configure automatically a bunch of stuff inside the angular project

```shell
ng add @ngrx/store
ng add @ngrx/store-devtools
ng add @ngrx/effects
ng add @ngrx/router-store
ng add @ngrx/entity
```

**Schematics** is a development only library

```shell
npm install @ngrx/schematics --save-dev
ng config cli.defaultCollection @ngrx/schematics
```

This part is not already installed in the **1-auth** branch

```shell
# Scaffold the initial state manager
ng generate @ngrx/schematics:store State --root --module app.module.ts

ng generate @ngrx/schematics:effect App --root --module app.module.ts
```

Open the newly created file **reducers/index.ts** and rename **State** to **AppState**.
