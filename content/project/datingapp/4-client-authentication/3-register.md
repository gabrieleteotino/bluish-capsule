---
title: "3 Register"
date: 2018-06-14T17:07:28+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Home and register components

Right click on the **app** folder and click *Generate Component*, name it **home**.

Right click on the **app** folder and click *Generate Component*, name it **register**.

Edit **home.component.html** and add the basic structure for the home page.

```html
<div style="text-align: center">
  <h1>Find your match!</h1>
  <p class="lead">Come on in to view your matches... All you nedd to do is sign up!</p>
  <div class="text-center">
    <button class="btn btn-primary btn-lg">Register</button>
    <button class="btn btn-info btn-lg">Learn more</button>
  </div>
</div>
```

In **app.component.html** substitute the *h1* with the **app-home** component.

Check that the site is working fine in the browser.

## Registration

In **register.component.ts** add a few things:

- a prop: *model: any = {}*
- a **register** method that logs the **model**
- a **cancel** method that logs "cancelled"

In **register.component.html** add a new *form* for the registration. The html struscture is simple.

To use the form we do almost the same things done for the **loginForm**

- to the form add *#registerForm="ngForm"*
- to the form add *(ngSubmit)="register()"*
- to both the user input add *required*, *name* and the *[(ngModel)]* binding
- to the **cancel** button add *(click)="cancel()"*

```html
<form #registerForm="ngForm" (ngSubmit)="register()">
  <h2 class="text-center text-primary">Sign up</h2>

  <hr>

  <div class="form-group">
    <input type="text" placeholder="Username" class="form-control" required name="username" [(ngModel)]="model.username">
  </div>

  <div class="form-group">
    <input type="password" placeholder="Password" class="form-control" required name="password" [(ngModel)]="model.password">
  </div>

  <div class="form-group text-center">
    <button type="submit" [disabled]="!registerForm.valid" class="btn btn-success">Register</button>
    <button type="button" class="btn btn-default">Cancel</button>
  </div>
</form>
```

## Put registration in home

In **home.component.html** add a div below the existing one with the **app-register** component. We will switch the visibility of the two divs.

The new div use a *ngIf* on the property **registerMode** and the *register button* will call **registerToggle()**.

```html
...
    <button class="btn btn-primary btn-lg" (click)="toggleRegister()">Register</button>
...

<div class="container" *ngIf="registerMode">
  <div class="col-md-4 col-md-offset-4">
    <app-register></app-register>
  </div>
</div>
```

In **home.component.ts** add a prop **registerMode: bool = false** and the **registerToggle()** function.

Now the child component can't call the parent component to directly call the function.
