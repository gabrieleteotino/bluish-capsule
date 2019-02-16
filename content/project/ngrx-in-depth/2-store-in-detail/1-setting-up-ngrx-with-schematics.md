---
title: "Setting up NgRx with schematics"
date: 2019-02-15T17:16:35.275+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

NgRx libraries installation, configuring store, actions and reducers.

<!-- more -->

## Basic NgRx libraries

The required NgRx libraries are already configured in the the branch **1-auth**.

They are from [NgRx repository](https://github.com/ngrx/platform) and the [official documentation](https://ngrx.io/guide/store/install)

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

## NgRx Schematics

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

## Store instead of Service

Without NgRx it is custom to inject a Service in the constructor and consume the data directly.

Instead we inject a *Store<StateClass>* to retrieve observables to consume and we delegate to the Store the task to retrieve data from the server.

For example modify **src/app/courses/home/home.component.ts**

```js
constructor(private coursesService: CoursesService, private store: Store<AppState>) { }
```

Then to use the store we **subscribe** to it's data and **dispatch** to it so that the store can internally do the data manipulation and eventually emit new data to the subscribers.

## Define an Action

In our application the *Login* functionality is inside the *Feature Module* **AuthModule** so we need to generate the action inside the module.

```shell
ng generate action auth/Auth
```

Open the newly created **src/app/auth/auth.actions.ts**

```js
export enum AuthActionTypes {
  LoginAuths = '[Auth] Login',
  LogoutAuths = '[Auth] Logout'
}

export class Login implements Action {
  readonly type = AuthActionTypes.LoginAuths;
  constructor(public payload: { user: User }) {}
}

export type AuthActions = Login;
```

Then to use it edit **auth/login/login.component.ts**

```js
login() {
  const val = this.form.value;
  this.auth.login(val.email, val.password)
    .pipe(
      tap(user => {
        this.store.dispatch(new Login({user}));
        this.router.navigateByUrl('/courses');
      })
    )
    .subscribe(
      noop,
      () => alert('Login error')
    );
}
```

Note that when we receive the **user** from the service we send it as a *payload* of the *action*.

## Reducer

In **reducers/index.ts** we need to define a type and a state property to contain the **auth** state. Then we need to create a reducer function and register it as a handler for that property and handle appropriately the actions. We also define an initial state for the state to be used at the application startup.

```js
interface AuthState {
  logged: boolean;
  user: User;
}

export interface AppState {
  auth: AuthState;
}


const initialAuthState: AuthState = {
  logged: false,
  user: undefined
};

function authReducers(state: AuthState = initialAuthState, action): AuthState {
  switch (action.type) {
    case AuthActionTypes.LoginAuths:
      return {
        logged: true,
        user: action.payload.user
      };
    default:
      return state;
  }
}

export const reducers: ActionReducerMap<AppState> = {
  auth: authReducers
};

export const metaReducers: MetaReducer<AppState>[] = !environment.production
  ? []
  : [];

```

# Use Schematics to implement reducers

The reducer we just implemented is working but it has a few problems. First it was a lot of boilerplate code to write for a few properties. The other more subtle problem is that the reducer for the authentication module was not contained in the feature module and this is not good.

Ideally we want each feature module able to leave the application untouched until it is loaded. This problem may not be apparent for a module like the **Auth** because this module i loaded by default, but even in this case this approach limits the testability of the module itself and it's portability.

To create the skeleton we use schematics to create a reducer named **Auth** inside the **auth** module.

```shell
ng generate reducer Auth --flat false --module auth/auth.module.ts
```

The new reducer is added in the imports of **auth.module.ts** and loaded *forFeature** using a property of the application state named **auth**.

```js
StoreModule.forFeature('auth', fromAuth.reducer),
```

And a new file **auth/auth.reducer.ts** was created (with his specs), now we move the code from **reducers/index.ts** to the new file.

Note that we renamed the interface to **AuthState** and changed the type from **Action** to **AuthActions**.

```js
export interface AuthState {
  logged: boolean;
  user: User;
}

export const initialState: AuthState = {
  logged: false,
  user: undefined
};

export function reducer(state = initialState, action: AuthActions): AuthState {
  switch (action.type) {
    case AuthActionTypes.LoginAuths:
      return {
        logged: true,
        user: action.payload.user
      };
    default:
      return state;
  }
}
```

If we look at the Redux Dev Tools in Chrome now there is a new event **@ngrx/store/update-reducers** after the initial **type: '@ngrx/store/init'**. And a similar event will be emitted each time a new module is lazily loaded.
