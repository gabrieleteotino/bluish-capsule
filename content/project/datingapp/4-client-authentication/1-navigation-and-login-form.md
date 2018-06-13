---
title: "1 Navigation and Login form"
date: 2018-06-13T15:01:33+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Navigation bar

Copy from the template at [getbootstrap.com](http://getbootstrap.com/), switch to bootstrap 3.3.7, navigate to *Getting started*  click on the [Jumbotron example](https://getbootstrap.com/docs/3.3/examples/jumbotron/) and then inspect the code. Select all the code from the tag **nav** class **navabar**.

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

Open **app.component.html**, remove some unused code and add `<app-nav></app-nav>` at the top of the page.

```html
<app-nav></app-nav>
<h1>
  Dating APP
</h1>
<app-values></app-values>
```

## Login form


We desire that when the user click the submit of the form it will call the **login** method. We also want a simple form validation that siables the login button if the fields are not valorized.

In **app.component.ts**

- add a field `model: any = {};` (without a specific type for now)
- add a function
```
login() {
  console.log(this.model);
}
```

In **app.component.html** add a templete reference variable to the **form** named **loginForm**.

```html
<form #loginForm="ngForm" class="navbar-form navbar-right">
```

We also need to tell the app to use import the **FormsModule** to able to use the *ngForm* directive. Open **app.module.ts** and add the import.

```javascript
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

Now that the directive is imported the *form* can bind the controls to the model. Inside each *input* add (using Angular Snippets *a-ngModel*) the ngModel two way binding directive.

Add the html5 *required*, *name* attributes to both inputs.

Bind the form *ngSubmit* event to the **login** function.

Bind the *disabled* attribute of the **button** to *!loginForm.valid*.

*loginForm.valid* is automatically populated by Angular like the *dirty* and *touched* property.

```html
<form #loginForm="ngForm" class="navbar-form navbar-right" (ngSubmit)="login()">
  <div class="form-group">
    <input type="text" placeholder="Email" class="form-control" required name="username" [(ngModel)]="model.username">
  </div>
  <div class="form-group">
    <input type="password" placeholder="Password" class="form-control" required name="password" [(ngModel)]="model.password">
  </div>
  <button type="submit" [disabled]="!loginForm.valid" class="btn btn-success">Sign in</button>
</form>
```
