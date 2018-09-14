---
title: "Firebase authentication"
date: 2018-09-14T14:20:48.208+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

## Server configuration

Go in the [Firebase Console](https://console.firebase.google.com/?hl=en)

Go to *Settings* and under *General - Your project - Public settings* change the **Public-facing name** to **JobSearchLog**, this name will be used in the email templates and other Firebase related component.

Go to *Authentication* and click *Setup sign-in method*, enable some signin providers.

For the application I choose:

- Email/password
- Google

I plan to enable Facebook, Twitter and GitHub but I have to setup application keys for each provider, this will be done later.

## Client configuration

Open **app.component.ts** add a DI for **AngularFireAuth** and implement **login** and **logout** methods

```typescript
constructor(db: AngularFirestore, public auth: AngularFireAuth) {
 this.items = db.collection('items').valueChanges();
}

login() {
 this.auth.auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
}

logout() {
 this.auth.auth.signOut();
}
```

```html
<mat-toolbar color="primary">
  <mat-toolbar-row>
    <span>JobSearchLog</span>
    <span class="toolbar-spacer"></span>

    <div *ngIf="auth.user | async as user; else showLogin">
      <span>Hello {{ user.displayName }}!</span>
      <mat-icon class="toolbar-icon">mail_outline</mat-icon>
      <mat-icon class="toolbar-icon" (click)="logout()">exit_to_app</mat-icon>
    </div>
    <ng-template #showLogin>
      <p>Please login</p>
      <mat-icon class="toolbar-icon" (click)="login()">vpn_key</mat-icon>
    </ng-template>
  </mat-toolbar-row>
</mat-toolbar>

<mat-list>
  <mat-list-item *ngFor="let item of items | async">
    <mat-icon matListIcon>done</mat-icon>
    <h3 matLine> {{item.name}} </h3>
  </mat-list-item>
</mat-list>
```

## Browser configuration

Running from localhost the browser prevents cookies from external domains

Open Chrome settings

```html
chrome://settings/content/cookies
```

Add to the whitelist
```html
https://[*.]firebaseapp.com
```

## Test

Test the application
