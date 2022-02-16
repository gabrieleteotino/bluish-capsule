---
title: "angularfire2"
date: 2018-09-13T16:03:33.372+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: false
---

<!--more-->

## Install

Install [angularfire2](https://github.com/angular/angularfire2) the official Angular library for Firebase.

```shell
npm install firebase @angular/fire --save
```

## Firebase config

From the [Firebase Console](https://console.firebase.google.com/?hl=en) go to *Settings* then in the *General* tab click on *Add Firebase to your web app*

Copy only the code in the config, create a new file **src/environments/firebase.ts**

```html
export const firebaseConfig = {
  apiKey: 'your-value',
  authDomain: 'your-value',
  databaseURL: 'your-value',
  projectId: 'your-value',
  storageBucket: 'your-value',
  messagingSenderId: 'your-value'
};
```

Add the file to **.gitignore**

```
# Secret files
src/environments/firebase.ts
```

Open **src/environments/environment.ts**

```typescript
import { firebaseConfig } from './firebase';

export const environment = {
  production: false,
  firebase: firebaseConfig
};
```

Similar in **src/environments/environment.prod.ts**

```typescript
import { firebaseConfig } from './firebase';

export const environment = {
  production: true,
  firebase: firebaseConfig
};
```

## AngularFireModule

Open **src/app/app.module.ts** and add **AngularFireModule** in the imports

```typescript
...
import { AngularFireModule } from '@angular/fire';
import { environment } from '../environments/environment';
...
  imports: [
    BrowserModule,
    AngularFireModule.initializeApp(environment.firebase)
  ],
...
```

## Setup individual modules

Add modules for the individual @NgModules, the full list is

- AngularFireAuthModule
- AngularFireDatabaseModule
- AngularFireFunctionsModule
- AngularFirestoreModule
- AngularFireStorageModule
- AngularFireMessagingModule

In JobSearchLog we will use Auth, Firestore and Storage. The complete **app.module.ts** is

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AngularFireModule } from '@angular/fire';
import { AngularFirestoreModule } from '@angular/fire/firestore';
import { AngularFireStorageModule } from '@angular/fire/storage';
import { AngularFireAuthModule } from '@angular/fire/auth';

import { AppComponent } from './app.component';
import { environment } from '../environments/environment';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AngularFireModule.initializeApp(environment.firebase),
    AngularFirestoreModule,
    AngularFireAuthModule,
    AngularFireStorageModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Test Firestore

Open **app.component.ts** and add a DI for AngularFirestore, bind a Firestore collection to a list

```typescript
import { Component } from '@angular/core';
import { AngularFirestore } from '@angular/fire/firestore';
import { Observable } from 'rxjs';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'JobSearchLog';
  items: Observable<any[]>;

  constructor(db: AngularFirestore) {
    this.items = db.collection('items').valueChanges();
  }
}
```

Change the template to visualize the items

```html
<div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>
</div>

<ul>
  <li *ngFor="let item of items | async">
    {{item.name}}
  </li>
</ul>
```

Run the application

```shell
ng serve
```

Open the browser at the address http://localhost:4200/ and verify that there is no content in the list.

## Create Cloud Firestore database

Open the [Firebase Console](https://console.firebase.google.com/?hl=en), go to *Database* and click *Create Database* in the *Firestore* section.

Create the database in *test mode* (we will configure permission later)

Add a new collection with *Collection ID* **items**.

On the *add first document* step leave *Auto-ID* and add a Field **name** with a value of **test**.

In the browser there is new list item.

Add some new document in the collection and verify that they appear in the application.
