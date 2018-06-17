---
title: "Bootstrap NGX"
date: 17-06-2018T18:57:19+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

The official [repository](https://github.com/valor-software/ngx-bootstrap) and [documentation](https://valor-software.com/ngx-bootstrap/#/getting-started).

## Installation

Stop *ng serve* if it is running.

```shell
npm install ngx-bootstrap --save
```

Restart *ng serve*

## Configuration

From the [Dropdown docs](https://valor-software.com/ngx-bootstrap/#/dropdowns)

Open **app.module.ts** and dd the import.

```typescript
...
import { BsDropdownModule } from 'ngx-bootstrap';
...
imports: [
  BrowserModule,
  HttpClientModule,
  FormsModule,
  BsDropdownModule.forRoot()
],
...
```

In **nav.component.html** we need to add *dropdown* to the container element, *dropdownToggle* to the *a* element and *\*dropdownMenu* to the *ul* of the dropdown. We also remove some unused classes.

```html
<ul *ngIf="loggedIn()" class="nav navbar-nav navbar-right">
  <li><a (click)="logout()"><i class="fa fa-sign-out"></i> Logout</a></li>
  <li class="dropdown" dropdown>
    <a href="#" class="dropdown-toggle" dropdownToggle>Welcome {{ getUsername() }} <span class="caret"></span></a>
    <ul class="dropdown-menu" *dropdownMenu>
      <li><a href="#"><i class="fa fa-user"></i> Edit profile</a></li>
      <li role="separator" class="divider"></li>
      <li><a (click)="logout()"><i class="fa fa-sign-out"></i> Logout</a></li>
    </ul>
  </li>
</ul>
```

Now that the dropdown works we don't need the external *Logout* link, so we remove that.

We also add an additional *ngIf* to the first three links to display them only to logged users.

The final code for the navbar:

```html
<div id="navbar" class="navbar-collapse collapse">
  <ul class="nav navbar-nav" *ngIf="loggedIn()">
    <li><a href="#">Matches</a></li>
    <li><a href="#">List</a></li>
    <li><a href="#">Messages</a></li>
  </ul>

  <form *ngIf="!loggedIn()" #loginForm="ngForm" class="navbar-form navbar-right" (ngSubmit)="login()">
    <div class="form-group">
      <input type="text" placeholder="Username" class="form-control" required name="username" [(ngModel)]="model.username">
    </div>
    <div class="form-group">
      <input type="password" placeholder="Password" class="form-control" required name="password" [(ngModel)]="model.password">
    </div>
    <button type="submit"  class="btn btn-success">Sign in</button>
  </form>

  <ul *ngIf="loggedIn()" class="nav navbar-nav navbar-right">
    <li class="dropdown" dropdown>
      <a href="#" class="dropdown-toggle" dropdownToggle>Welcome {{ getUsername() }} <span class="caret"></span></a>
      <ul class="dropdown-menu" *dropdownMenu>
        <li><a href="#"><i class="fa fa-user"></i> Edit profile</a></li>
        <li role="separator" class="divider"></li>
        <li><a (click)="logout()"><i class="fa fa-sign-out"></i> Logout</a></li>
      </ul>
    </li>
  </ul>

</div><!--/.navbar-collapse -->
```

Open **nav.component.css** and add a style to make the pointer behave nicely with our menu.

```css
.dropdown-menu li {
    cursor: pointer;
}
```

Test the application: the nvabar has no private links for the anonymous user, the logout is working as before with the correct cursor icon.
