---
title: "Router store"
date: 2019-02-22T14:38:29+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

Router Store is a binding between the Angular Router and the Store that listens for changes in the router state. Configuring this binding enables the rewind feature of Redux Dev Tools and time travel during the debug sessions.

<!--more-->

Register the module in **app.module.ts**. The parameter **stateKey** specify the name of the router inside the store tree.

```js
imports: [
    ...
    StoreRouterConnectingModule.forRoot({stateKey: 'router', serializer: CustomSerializer})
  ],
```

## Custom Serializer

We also need the *Custom Route State Serializer* code from (NgRx docs)[https://ngrx.io/guide/router-store/configuration] to extract only the minimal information from the router (url and params) so that we don't fill the store with useless clutter.

Create a folder **src/app/shared** and new file **custom-route-serializer.ts**

```js
import { Params, RouterStateSnapshot } from '@angular/router';
import { RouterStateSerializer } from '@ngrx/router-store';

export interface RouterStateUrl {
  url: string;
  params: Params;
  queryParams: Params;
}

export class CustomSerializer implements RouterStateSerializer<RouterStateUrl> {
  serialize(routerState: RouterStateSnapshot): RouterStateUrl {
    let route = routerState.root;

    while (route.firstChild) {
      route = route.firstChild;
    }

    const {
      url,
      root: { queryParams }
    } = routerState;

    const { params } = route;

    // Only return an object including the URL, params and query params
    // instead of the entire snapshot
    return { url, params, queryParams };
  }
}
```

Finally in the root reducer **reducers/index.ts** configure the reducer (and the relative custom serializer)

```js
export const reducers: ActionReducerMap<AppState> = {
  router: routerReducer
};
```
