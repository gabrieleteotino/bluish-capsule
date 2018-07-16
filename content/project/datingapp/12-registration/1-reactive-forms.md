---
title: "Reactive forms"
date: 2018-07-16T15:39:18.508+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

[Reactive Forms](https://angular.io/guide/reactive-forms) is a different philosophy from *FormsModule* template-driven technology. Instead *ReactiveFormsModule* are defined in the component code and the binding with the template is syncronous.

<!--more-->

## Configuration

Open **app.module.ts** and import *ReactiveFormsModule*.

```typescript
...
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
...
imports: [
  BrowserModule,
  HttpClientModule,
  JwtModule.forRoot({
    config: {
      tokenGetter: () => {
        return localStorage.getItem('token');
      },
      whitelistedDomains: ['localhost:5001'],
      blacklistedRoutes: ['localhost:5001/api/auth/']
    }
  }),
  FormsModule,
  ReactiveFormsModule,
  BsDropdownModule.forRoot(),
  TabsModule.forRoot(),
  NgxGalleryModule,
  RouterModule.forRoot(ROUTES),
  FileUploadModule
],
...
```

## FormGroup

Open **register.component.ts** and add a property **registerForm**, comment out the code inside **register** and configure the new form.

```typescript
...
registerForm: FormGroup;
...
ngOnInit() {
  this.registerForm = new FormGroup({
    username: new FormControl(),
    password: new FormControl(),
    confirmPassword: new FormControl()
  });
}
...
register() {
  // this.auth.register(this.model).subscribe(() => {
  //   this.alertify.success('registration complete');
  // }, error => {
  //   this.alertify.error(error);
  // });
  console.log(this.registerForm.value);
}
...
```

## Template

Open **register.component.html** and substitue the *#registerForm* directive with *formGroup* and associate all the items using *formControlName* instead of *ngModel*.

There are also two "debug" paragraphs to view the current state of the form.

```html
<form [formGroup]="registerForm" (ngSubmit)="register()">
  <h2 class="text-center text-primary">Sign up</h2>

  <hr>

  <div class="form-group">
    <input type="text" placeholder="Username" class="form-control" formControlName="username">
  </div>

  <div class="form-group">
    <input type="password" placeholder="Password" class="form-control" formControlName="password">
  </div>

  <div class="form-group">
    <input type="password" placeholder="Confirm Password" class="form-control" formControlName="confirmPassword">
  </div>

  <div class="form-group text-center">
    <button type="submit" [disabled]="!registerForm.valid" class="btn btn-success">Register</button>
    <button type="button" class="btn btn-default" (click)="cancel()">Cancel</button>
  </div>
</form>

<p>Form value: {{ registerForm.value | json }}</p>
<p>Form status: {{ registerForm.status | json }}</p>
```

Now the register page is testable (obviously the registration process is no longer working). When we change the values we can see the value and status change and if we click *Register* we can see the form output in the console.

## Validation
