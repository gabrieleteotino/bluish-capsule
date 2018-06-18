---
title: "Routing basics"
date: 2018-06-18T11:05:38.278+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## New components

Create new components in **src/app**

- **member-list**
- **lists**
- **messages**

## Routes

Add a new file **src/app/routes.ts** we have no template for this file.

```typescript
import { Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { ListsComponent } from './lists/lists.component';
import { MessagesComponent } from './messages/messages.component';
import { MemberListComponent } from './member-list/member-list.component';

export const ROUTES: Routes = [
    { path: 'home', component: HomeComponent },
    { path: 'lists', component: ListsComponent },
    { path: 'messages', component: MessagesComponent },
    { path: 'members', component: MemberListComponent },
    { path: '**', redirectTo: 'home', pathMatch: 'full' }
];
```

Open **app.module.ts** and add a *RouterModule* to the *imports*.

```typescript
imports: [
  BrowserModule,
  HttpClientModule,
  FormsModule,
  BsDropdownModule.forRoot(),
  RouterModule.forRoot(ROUTES)
],
```

Open **nav.component.html** and fix the links using the snippet *a-routerLink*

```html
...
<a class="navbar-brand" [routerLink]="['/home']" routerLinkActive="router-link-active" >Dating App</a>
...
<li><a [routerLink]="['/members']" routerLinkActive="router-link-active" >Matches</a></li>
<li><a [routerLink]="['/lists']" routerLinkActive="router-link-active" >List</a></li>
<li><a [routerLink]="['/messages']" routerLinkActive="router-link-active" >Messages</a></li>
...
```

Open **app.component.html** and remove the **app-home** tag and add the **router-outlet**.

```html
<app-nav></app-nav>

<router-outlet></router-outlet>
```

Test the navigation, all the nav items should work and invalid paths should redirect to home.
