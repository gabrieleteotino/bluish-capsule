---
title: "Auth service"
date: 2018-09-18T13:57:19.237+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

Let's refactor the authentication mechanism.

Create a **User** model to store user settings and preference in **/src/app/_models/user.model.ts**

```typescript
export interface User {
  uid: string;
  email: string;
  photoURL?: string;
  displayName?: string;
}
```

Then use the new implementation of the auth service with persistence of the user preference in Firestore and localstorage.

```typescript
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { Observable, of } from 'rxjs';
import { switchMap, tap, startWith } from 'rxjs/operators';

import { User } from '../_models/user.model';

import { auth } from 'firebase';
import { AngularFireAuth } from '@angular/fire/auth';
import {
  AngularFirestore,
  AngularFirestoreDocument
} from '@angular/fire/firestore';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  user: Observable<User>;

  constructor(
    private afAuth: AngularFireAuth,
    private afs: AngularFirestore,
    private router: Router
  ) {
    // Get auth data, then get firestore user document || null
    this.user = this.afAuth.authState.pipe(
      switchMap(user => {
        if (user) {
          return this.afs.doc<User>(`users/${user.uid}`).valueChanges();
        } else {
          return of(null);
        }
      }),
      // Set/read the user data to local storage
      // this avoids flickering on application startup
      tap(user => localStorage.setItem('user', JSON.stringify(user))),
      startWith(JSON.parse(localStorage.getItem('user')))
    );
  }

  googleLogin() {
    const provider = new auth.GoogleAuthProvider();
    return this.oAuthLogin(provider);
  }

  private oAuthLogin(provider) {
    this.afAuth.auth.signInWithPopup(provider).then(
      userCredential => {
        // Sets user data to firestore on login
        this.updateUserData(userCredential.user);
      },
      error => {
        console.log('Error during login');
        console.log(error);
      }
    );
  }

  logout() {
    this.afAuth.auth.signOut().then(() => {
      this.router.navigate(['/']);
    });
  }

  private updateUserData(fbUser: firebase.User) {
    const userRef: AngularFirestoreDocument<any> = this.afs.doc(
      `users/${fbUser.uid}`
    );

    const user: User = {
      uid: fbUser.uid,
      email: fbUser.email,
      displayName: fbUser.displayName,
      photoURL: fbUser.photoURL
    };

    return userRef.set(user, { merge: true });
  }
}
```
