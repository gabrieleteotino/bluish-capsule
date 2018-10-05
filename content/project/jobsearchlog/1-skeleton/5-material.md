---
title: "Material"
date: 2018-09-14T11:10:33.486+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "material"]
categories: ["dev"]
draft: false
---

<!-- more -->

## Install

Use npm to install material, cdk and animations.

**@angular/material** contains the material components.

**@angular/cdk** has predefined behaviour for components, like overlay for menus and popups, table behavoiur and others.

**@angular/animations** contains animation (what else?) and it will use Web Animations API if supported by the browser or fallback to css.

```shell
npm install @angular/material @angular/cdk @angular/animations --save
```

## Configure

Create a new file **src/app/material.module.ts** where we will centralize all material related imports

```typescript
import {NgModule} from '@angular/core';

@NgModule({
  imports: [],
  exports: []
})
export class MaterialModule {}
```
Open **app.module.ts** and import animations and **MaterialModule**

```typescript
...
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { MaterialModule } from './material.module';
...
imports: [
    ...
    BrowserAnimationsModule,
    MaterialModule
    ...
```

Add one of the four predefined themes

- deeppurple-amber.css
- indigo-pink.css
- pink-bluegrey.css
- purple-green.css

Open **styles.css**

```css
/* You can add global styles to this file, and also import other style files */
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';
```

Add Material Icons, open **index.html** and add the font

```html
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

Test the application

```shell
ng serve
```

## Navigation and list test

Open **material.module.ts** and imports for some components

```typescript
import { NgModule } from '@angular/core';
import {
  MatSidenavModule,
  MatToolbarModule,
  MatIconModule,
  MatListModule
} from '@angular/material';

@NgModule({
  imports: [MatSidenavModule, MatToolbarModule, MatIconModule, MatListModule],
  exports: [MatSidenavModule, MatToolbarModule, MatIconModule, MatListModule]
})
export class MaterialModule {}
```

Open **app.component.html** and add a toolbar and change the list of items loaded from Firebase

```html
<mat-toolbar color="primary">
  <mat-toolbar-row>
    <span>JobSearchLog</span>
    <span class="toolbar-spacer"></span>
    <mat-icon class="toolbar-icon">mail_outline</mat-icon>
    <mat-icon class="toolbar-icon">account_box</mat-icon>
  </mat-toolbar-row>
</mat-toolbar>

<mat-list>
  <mat-list-item *ngFor="let item of items | async">
    <mat-icon matListIcon>done</mat-icon>
    <h3 matLine> {{item.name}} </h3>
  </mat-list-item>
</mat-list>
```

Open **app.component.css** and add some styling

```css
.toolbar-icon {
  padding: 0 14px;
}

.toolbar-spacer {
  flex: 1 1 auto;
}

.mat-list-icon {
  color: rgba(0, 0, 0, 0.54);
}
```

Test the application.
