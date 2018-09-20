---
title: "Job list"
date: 2018-09-19T14:53:45.885+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

Create three new components:

- home
- nav
- job-list

## Routes

Before we implement the code for job list let's setup the routes. Create a file **/src/app/routes.ts**

```typescript
import { Routes } from '@angular/router';
import { AuthGuard } from './_guards/auth.guard';
import { HomeComponent } from './home/home.component';
import { JobListComponent } from './job-list/job-list.component';

export const ROUTES: Routes = [
  { path: 'home', component: HomeComponent },
  {
    path: '',
    runGuardsAndResolvers: 'always',
    canActivate: [AuthGuard],
    children: [
      {
        path: 'jobs',
        component: JobListComponent
      }
    ]
  },
  { path: '**', redirectTo: 'home', pathMatch: 'full' }
];
```

Open **app.modulte.ts** and import and configure the router

```typescript
import { RouterModule } from '@angular/router';
import { ROUTES } from './routes';
...
   imports: [
      BrowserModule,
      BrowserAnimationsModule,
      RouterModule.forRoot(ROUTES),
      ...
```

## Navigation

Open **app.component.html** and move the markup of the *mat-toolbar* in **nav.component.html**

```html
<app-nav></app-nav>

<router-outlet></router-outlet>
```

Remove the constructor, the properties and the unused imports from **app.component.ts**

Open **nav.component.ts** and add DI for **AuthService**

```typescript
...
constructor(public auth: AuthService) {}
...
```

Change the code in **nav.component.html**

```html
<mat-toolbar color="primary">
  <mat-toolbar-row>
    <span>JobSearchLog</span>
    <span class="toolbar-spacer"></span>

    <div *ngIf="(this.auth.user | async) as user; else showLogin">
      <span>Hello {{ user.displayName }}!</span>
      <mat-icon class="toolbar-icon">mail_outline</mat-icon>
      <mat-icon class="toolbar-icon" (click)="auth.logout()">exit_to_app</mat-icon>
    </div>
    <ng-template #showLogin>
      <p>Please login</p>
      <mat-icon class="toolbar-icon" (click)="auth.googleLogin()">vpn_key</mat-icon>
    </ng-template>
  </mat-toolbar-row>
</mat-toolbar>
```

## Jobs

Implemnt a dialog form to insert new job offers

Register **MatDialogModule** inside **material.module.ts**

Create a new component **job-dialog**


Open **app.module.ts** and register the new component to both *declarations* and [entryComponent](https://angular.io/guide/entry-components)

```typescript
...
declarations: [
  ...
  JobDialogComponent
],
...
bootstrap: [
  AppComponent
],
entryComponents: [JobDialogComponent]
...
```

This is the first table that we implement so we need to import/export **MatTableModule** inside **material.module.ts**

```typescript
import { NgModule } from '@angular/core';
import {
  MatSidenavModule,
  MatToolbarModule,
  MatIconModule,
  MatListModule,
  MatTableModule
} from '@angular/material';

@NgModule({
  imports: [
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatListModule,
    MatTableModule
  ],
  exports: [
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatListModule,
    MatTableModule
  ]
})
export class MaterialModule {}
```

Open **job-list.component.ts** add DI for **AuthService** and implement paginated loading.

```typescript

```
