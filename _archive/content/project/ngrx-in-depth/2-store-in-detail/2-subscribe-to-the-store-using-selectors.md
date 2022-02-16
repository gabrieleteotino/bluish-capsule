---
title: "Setting up NgRx with schematics"
date: 2019-02-18T18:39:07+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

Subscribe to the store directly and using selectors.

<!--more-->

## Direct subscription

A simple subscription can be done directly with pipe operators in **app.component.ts**

```js
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  isLoggedIn$: Observable<boolean>;
  isLoggedOut$: Observable<boolean>;

  constructor(private store: Store<AppState>) {}

  ngOnInit() {
    this.isLoggedIn$ = this.store.pipe(
      map((state: any) => state.auth.logged)
    );
    this.isLoggedOut$ = this.store.pipe(
      map((state: any) => !state.auth.logged)
    );
  }

  logout() {
    this.store.dispatch(new Logout());
  }
}
```

And then use the observables in **app.component.html**

```html
<a mat-list-item routerLink="/login" *ngIf="isLoggedOut$ | async">
    <mat-icon>account_circle</mat-icon>
    <span>Login</span>
</a>

<a mat-list-item (click)="logout()" *ngIf="isLoggedIn$ | async">
    <mat-icon>exit_to_app</mat-icon>
    <span>Logout</span>
</a>
```

## Selectors

A selector does a similar job but is optimized to only extract the part of the store that was modified and avoids useless processing for all the observables when only a part of the data was changed.

Create a new file **auth/auth.selectors.ts**, then we will define a simple *feature* selector and two *memoization* selectors. Note that the second memoization selector uses the first memoization as his input.

```js
export const selectAuthState = (state: any) => state.auth as AuthState;

export const isLoggedIn = createSelector(
    selectAuthState,
    auth => auth.logged
);

export const isLoggedOut = createSelector(
    isLoggedIn,
    loggedIn => !loggedIn
);
```

To use the selectors we use a special rx operator defined in ngrx **select** instead of the **map** operator.

```js
ngOnInit() {
  this.isLoggedIn$ = this.store.pipe(
    select(isLoggedIn)
  );
  this.isLoggedOut$ = this.store.pipe(
    select(isLoggedOut)
  );
}
```
