---
title: "Reactive forms"
date: 2018-07-16T15:39:18.508+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

[Reactive Forms](https://angular.io/guide/reactive-forms) is a different philosophy from *FormsModule* template-driven technology.

*ReactiveFormsModule* are defined in the component code and the binding with the template is syncronous.

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
  console.log(JSON.stringify(this.registerForm.value));
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

Open **register.component.ts** and  add some configuration to the *FormControls*. The built-in [Validators](https://angular.io/api/forms/Validators) class has some basic functions usable for the registration form.

In the api in **UserForRegistration.cs** the **Username** and the **Password** are both required and the password must be between 8 and 30 characters.

```typescript
...
ngOnInit() {
  this.registerForm = new FormGroup({
    username: new FormControl('', Validators.required),
    password: new FormControl('', [
      Validators.required,
      Validators.minLength(8),
      Validators.maxLength(30)
    ]),
    confirmPassword: new FormControl('', [
      Validators.required,
      Validators.minLength(8),
      Validators.maxLength(30)
    ])
  });
}
...
```

## Custom validator

Create a validator function **passwordMatchValidator** for the *FormGroup* that compares the two password fields.
A custom validator must return *null* if the validation is succesful or the [ValidationErrors](https://angular.io/api/forms/ValidationErrors).

Syncronous validators (this is obviously syncronous) for the form are added as the second argument to the *FormGroup* constructor.

```typescript
...
ngOnInit() {
 this.registerForm = new FormGroup({
   username: new FormControl('', Validators.required),
   password: new FormControl('', [
     Validators.required,
     Validators.minLength(8),
     Validators.maxLength(30)
   ]),
   confirmPassword: new FormControl('', Validators.required)
 }, this.passwordMatchValidator);
}

private passwordMatchValidator(
  formGroup: FormGroup
): ValidationErrors | null {
  return formGroup.get('password').value === formGroup.get('confirmPassword').value
    ? null
    : { passwordMismatch: true };
}
...
```

## Validation feedback

In the template **register.component.html** we apply a bootstrap css class [has-error](https://getbootstrap.com/docs/3.3/css/#forms-control-validation) to each input when there are error and after the input was *touched*.

Then we add an error message with the bootstrap class [help-block](https://getbootstrap.com/docs/3.3/css/#forms-control-validation) below each input and we also check for the error type, so that we can display the right error message.

```html
<div class="form-group" [ngClass]="{'has-error': registerForm.get('username').errors && registerForm.get('username').touched}">
  <input type="text" placeholder="Username" class="form-control" formControlName="username">
  <span class="help-block" *ngIf="registerForm.get('username').hasError('required') && registerForm.get('username').touched">Username is required</span>
</div>

<div class="form-group" [ngClass]="{'has-error': registerForm.get('password').errors && registerForm.get('password').touched}">
  <input type="password" placeholder="Password" class="form-control" formControlName="password">
  <span class="help-block" *ngIf="registerForm.get('password').hasError('required') && registerForm.get('password').touched">Password is required</span>
  <span class="help-block" *ngIf="registerForm.get('password').hasError('minlength') && registerForm.get('password').touched">Password must be at least 8 characters</span>
  <span class="help-block" *ngIf="registerForm.get('password').hasError('maxlength') && registerForm.get('password').touched">Password cannot exceed 30 characters</span>
</div>

<div class="form-group" [ngClass]="{'has-error': (registerForm.get('confirmPassword').errors || registerForm.hasError('passwordMismatch')) && registerForm.get('confirmPassword').touched}">
  <input type="password" placeholder="Confirm Password" class="form-control" formControlName="confirmPassword">
  <span class="help-block" *ngIf="registerForm.get('confirmPassword').hasError('required') && registerForm.get('confirmPassword').touched">Confirm Password is required</span>
  <span class="help-block" *ngIf="registerForm.hasError('passwordMismatch') && registerForm.get('confirmPassword').touched">Password must be equal to Confirm Password</span>
</div>
```
