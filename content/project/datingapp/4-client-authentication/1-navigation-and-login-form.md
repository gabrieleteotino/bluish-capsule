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

Copy from the templates at [getbootstrap.com](http://getbootstrap.com/)

- switch to bootstrap 3.3.7
- navigate to *Getting started*
- click on the [Jumbotron example](https://getbootstrap.com/docs/3.3/examples/jumbotron/)
- inspect the code
- select all the code from the tag **nav** class **navabar**.

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
    <input type="text" placeholder="Email" class="form-control" required name="username" [(ngModel)]="model.username">
  </div>
  <div class="form-group">
    <input type="password" placeholder="Password" class="form-control" required name="password" [(ngModel)]="model.password">
  </div>
  <button type="submit" [disabled]="!loginForm.valid" class="btn btn-success">Sign in</button>
</form>
```

## Login service

Inside **src/app** create a new folder **_services**. (The underscore is just a trick to keep the folder up).

Right click and **Create Service** and name it **auth**.

Register the service in **app.module.ts** **providers** section.

```javascript
import { AuthService } from './_services/auth.service';
...
providers: [
  AuthService
],
```

Edit **auth.service.ts**
Add a prop **baseUrl**.

Inject http in the constructor. Check the import from '@angular/http'.

Implement a login method.

## Use the login service

Back in **nav.component.ts** inject the **AuthService** in the constructor and use it from the **login** function.

## Hide the form for logged Users

Copy from the template at [getbootstrap.com](http://getbootstrap.com/), switch to bootstrap 3.3.7, navigate to *Components*  click on the [Navbar](https://getbootstrap.com/docs/3.3/components/#navbar) and then inspect the code. Select all the code of the *ul* for the *dropdown*.

Paste the code below the *loginForm**

Change the text to "Welcome user", remove a couple links and fix the text for the items in the dropdown. Also add icons.
The dropdown is temporary not working, so we duplicate the logout functionality also in the *Link* just before the dropdown.

Now we add the structural directive *ngIf* to the *ul* of the dropdown using the snippet *a-ngIf* and we bind it to a new function **loggedIn**.
