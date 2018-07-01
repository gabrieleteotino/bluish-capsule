---
title: "1 Navigation and Login form"
date: 2018-06-13T15:01:33+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Navigation bar

Copy from the templates at [getbootstrap.com](http://getbootstrap.com/)

- switch to bootstrap 3.3.7
- navigate to *Getting started*
- click on the [Jumbotron example](https://getbootstrap.com/docs/3.3/examples/jumbotron/)
- inspect the code
- select all the code from the tag **nav** class **navabar**

Inside the **app** component right click and *Generate Component* and call it **nav**.

Open **nav.component.html** and paste the code.

Remove the **navabar-fixed-top** class from the outer **nav**.

Change the project name to *Dating App*.

Inside the *div#navbar* add a new *ul* with a few *a* with a placeholder url.

```html
<ul class="nav navbar-nav">
  <li><a href="#">Matches</a></li>
  <li><a href="#">Lists</a></li>
  <li><a href="#">Messages</a></li>
</ul>
```

Open **app.component.html**, remove some unused code and add the **app-nav** tag at the top of the page.

```html
<app-nav></app-nav>
<h1>
  Dating APP
</h1>
<app-values></app-values>
```

## Login form

### Features

- when the user click the submit of the form it will call the **login** method
- if the fields are not valorized a simple form validation disables the login button

### Implementation

In **nav.component.ts**

 add a **model** field  (without a custom type for now)
 add a **login** function

```typescript
...
export class NavComponent implements OnInit {
  model: any = {};
...
  login() {
    console.log(this.model);
  }
...
```

In **nav.component.html** add a templete reference variable to the **form** named **loginForm**.

```html
<form #loginForm="ngForm" class="navbar-form navbar-right">
```

### Import FormModule

We need to configure the app to import the **FormsModule** to able to use the *ngForm* directive.

Open **app.module.ts** and add the import.

```typescript
...
import { FormsModule } from '@angular/forms';
...
imports: [
  BrowserModule,
  HttpModule,
  FormsModule
],
...
```

Now that the directive is imported the *form* can bind the controls to the model.

### Bind form items

Inside each *input* add (using Angular Snippets *a-ngModel*) the *ngModel* two way binding directive.

Add the html5 *required*, *name* attributes to both inputs.

Bind the form *ngSubmit* event to the **login** function.

Bind the *disabled* attribute of the **button** to *!loginForm.valid*.

The value of *loginForm.valid* is automatically populated by Angular like the *dirty* and *touched* properties.

```html
<form #loginForm="ngForm" class="navbar-form navbar-right" (ngSubmit)="login()">
  <div class="form-group">
    <input type="text" placeholder="Username" class="form-control" required name="username" [(ngModel)]="model.username">
  </div>
  <div class="form-group">
    <input type="password" placeholder="Password" class="form-control" required name="password" [(ngModel)]="model.password">
  </div>
  <button type="submit" [disabled]="!loginForm.valid" class="btn btn-success">Sign in</button>
</form>
```
