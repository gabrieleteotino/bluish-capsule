---
title: "Side effects"
date: 2019-02-18T22:09:46+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

Ngrx/effects is a middleware for handling side effects in ngrx/store. It listens for dispatched actions in an observable stream, performs side effects, and returns new actions either immediately or asynchronously. The returned actions get passed along to the reducer.

<!--more-->

In our example we want to use a side effects to do the redirect when a user login and also save the user info in localstorage. With the info saved in local store the application will be able to resume the logged state even after a browser refresh.

## Scaffolding

Stop the application.

Use schematics to create an side effect and register it in the auth module

```shell
ng generate effect auth/Auth --module auth/auth.module.ts
```

This will register the effects in **auth.module.ts** and will create two files:

- auth/auth.effects.ts (183 bytes)
- auth/auth.effects.spec.ts (577 bytes)

## Non dispatching side effects

In **auth.effects.ts** we will add tow side effects for the login an the logout actions. Those two effects are not dispatching any new actions, so we inform NgRx in the *@Effect* decorator.

The first operation should always be a filter using the operator **ofType**.

```js
@Injectable()
export class AuthEffects {
  @Effect({ dispatch: false })
  login$ = this.actions$.pipe(
    ofType<Login>(AuthActionTypes.LoginAuths),
    tap(action =>
      localStorage.setItem('user', JSON.stringify(action.payload.user))
    )
  );

  @Effect({ dispatch: false })
  logout$ = this.actions$.pipe(
    ofType<Logout>(AuthActionTypes.LogoutAuths),
    tap(() => {
      localStorage.removeItem('user');
      this.router.navigateByUrl('/login');
    })
  );

  constructor(private actions$: Actions, private router: Router) {}
}
```

Now the navigate action when the user logout is done by an effect, so we can remove it from **app.component.ts**

```js
logout() {
  this.store.dispatch(new Logout());
}
```

## Dispatching side effects

When the user refresh the page we want to intercept the init event with a side effect then check the local storage and dispatch a **Login** or a **Logout** event.

Note that the *init$* effect is named by convenction and has to be the last effect (for some buggy reason in NgRx)

```js
@Effect()
init$ = defer(
  (): Observable<Login | Logout> => {
    const userData = localStorage.getItem('user');
    if (userData) {
      const user = JSON.parse(userData);
      return of(new Login({ user: user }));
    } else {
      return of(new Logout());
    }
  }
);
```
