---
title: "Store freeze"
date: 2019-02-21T17:25:52+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

Store freeze is a tool that helps avoid manipulation of the store state.

<!--more-->

Install the library

```shell
npm i --save-dev ngrx-store-freeze
```

Configure the library to be used only in development mode by registering in the metareducers in **reducers/index.ts**.

```js
export const metaReducers: MetaReducer<AppState>[] = !environment.production
  ? [storeFreeze]
  : [];
```

Now to test the library we can try to change a state in the store, for example let's change the logout reducer to

```js
case AuthActionTypes.LogoutAuths:
  state.logged = false;
  state.user = undefined;
  return state;
  /*return {
    logged: false,
    user: undefined
  };*/
```

The compilation is succesful but a soon as we dispatch a logout action in the browser we have an error

```
core.js:14597 ERROR TypeError: Cannot assign to read only property 'logged' of object '[object Object]'
    at authReducer (http://localhost:4200/main.js:511:26)
    at http://localhost:4200/vendor.js:137848:20
    at combination (http://localhost:4200/vendor.js:137806:35)
    at freeze (http://localhost:4200/vendor.js:139915:25)
    at computeNextEntry (http://localhost:4200/vendor.js:137087:21)
    at recomputeStates (http://localhost:4200/vendor.js:137121:15)
    at http://localhost:4200/vendor.js:137405:26
    at ScanSubscriber.StoreDevtools.liftedAction$.pipe.Object.state [as accumulator] (http://localhost:4200/vendor.js:137462:38)
    at ScanSubscriber.push../node_modules/rxjs/_esm5/internal/operators/scan.js.ScanSubscriber._tryNext (http://localhost:4200/vendor.js:148159:27)
    at ScanSubscriber.push../node_modules/rxjs/_esm5/internal/operators/scan.js.ScanSubscriber._next (http://localhost:4200/vendor.js:148152:25)
```
