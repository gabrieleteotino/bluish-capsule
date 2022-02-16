---
title: "Firestore security"
date: 2018-09-14T15:31:27.962+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: false
---

<!--more-->

## Server configuration

Go in the [Firebase Console](https://console.firebase.google.com/?hl=en)

Go to *Database - Rules* and add the following rule to require that access to all database objects requires a logged in user.

```html
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth.uid != null;
    }
  }
}
```

## Client configuration

Inside **src/app** create a new folder **_services**.

Generate a new service ins **_services** named **auth**

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';
import { AngularFireAuth } from '@angular/fire/auth';
import { auth } from 'firebase';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private _isLogIn = new BehaviorSubject(false);
  private _user: firebase.User;

  constructor(public afAuth: AngularFireAuth) {
    afAuth.authState.subscribe(user => {
      const loggedIn = !!user;
      this._user = user;

      console.log(`user is ${loggedIn ? '' : 'not '}logged in`);
      this._isLogIn.next(loggedIn);
    });
  }

  get isLogIn(): Observable<boolean> {
    return this._isLogIn.asObservable();
  }

  get user() {
    return this._user;
  }

  login() {
    this.afAuth.auth.signInWithPopup(new auth.GoogleAuthProvider()).then(
      userCredential => {
        this._user = userCredential.user;
        if (this._user) {
          console.log('Succesfull login');
        } else {
          console.log('Failed login');
        }
      },
      error => {
        console.log('Error during login');
        console.log(error);
        this._isLogIn.next(false);
      }
    );
  }

  logout() {
    this.afAuth.auth.signOut();
  }
}
```

Change **app.component.ts** remove the direct usage of **AngularFireAuth** and use the new service

```typescript
import { Component } from '@angular/core';
import { Observable } from 'rxjs';
import { AngularFirestore } from '@angular/fire/firestore';
import { AuthService } from './_services/auth.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'JobSearchLog';
  items: Observable<any[]>;

  constructor(db: AngularFirestore, public authService: AuthService) {
    authService.isLogIn.subscribe(loggedIn => {
      if (loggedIn) {
        this.items = db.collection('items').valueChanges();
      } else {
        this.items = null;
      }
    });
  }
}
```

Change **app.component.html**

```html
<mat-toolbar color="primary">
  <mat-toolbar-row>
    <span>JobSearchLog</span>
    <span class="toolbar-spacer"></span>

    <div *ngIf="authService.isLogIn | async; else showLogin">
      <span>Hello {{ authService.user.displayName }}!</span>
      <mat-icon class="toolbar-icon">mail_outline</mat-icon>
      <mat-icon class="toolbar-icon" (click)="authService.logout()">exit_to_app</mat-icon>
    </div>
    <ng-template #showLogin>
      <p>Please login</p>
      <mat-icon class="toolbar-icon" (click)="authService.login()">vpn_key</mat-icon>
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
