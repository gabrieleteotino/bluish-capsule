---
title: "Expand the register form"
date: 2018-07-18T13:03:35.930+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "ngx-datepicker"]
categories: ["dev"]
draft: false
---

Using Angular [FormBuilder](https://angular.io/api/forms/FormBuilder) to configure a form.

The FormBuilder provides syntactic sugar that shortens creating instances of a FormControl, FormGroup, or FormArray. It reduces the amount of boilerplate needed to build complex forms.

<!--more-->

## Move to FormBuilder

Open **register.component.ts** and add a new DI for *FormBuilder*. Then we move the *FormGroup* initialization from **ngInit** to a new method **createRegistrationForm** and instead of actually initialize the classes we use configuration on the *FormBuilder*

```typescript
...
constructor(
  private auth: AuthService,
  private alertify: AlertifyService,
  private fb: FormBuilder
) {}

ngOnInit() {
  this.createRegistrationForm();
}

private createRegistrationForm(): void {
  this.registerForm = this.fb.group(
    {
      username: ['', Validators.required],
      password: [
        '',
        [
          Validators.required,
          Validators.minLength(8),
          Validators.maxLength(30)
        ]
      ],
      confirmPassword: ['', Validators.required]
    },
    { validator: this.passwordMatchValidator }
  );
}
...
```

Test the application: all the form functinoality is as it was before.

## Adding new fields

Now add some fields to the form. For the moment the date is initialized to null and we initialize the radio button for gender so that no null value is possible.

```typescript
private createRegistrationForm(): void {
  this.registerForm = this.fb.group(
    {
      gender: ['male'],
      username: ['', Validators.required],
      knownAs: ['', Validators.required],
      dateOfBirth: [null, Validators.required],
      city: ['', Validators.required],
      country: ['', Validators.required],
      password: [
        '',
        [
          Validators.required,
          Validators.minLength(8),
          Validators.maxLength(30)
        ]
      ],
      confirmPassword: ['', Validators.required]
    },
    { validator: this.passwordMatchValidator }
  );
}
```

```html
...
<div class="form-group">
  <label for="gender" class="control-label" style="margin-right: 10px;">I am a:</label>
  <label for="" class="radio-inline">
    <input type="radio" formControlName="gender" value="male">Male
  </label>
  <label for="" class="radio-inline">
    <input type="radio" formControlName="gender" value="female">Female
  </label>
</div>
...
<div class="form-group" [ngClass]="{'has-error': registerForm.get('knownAs').errors && registerForm.get('knownAs').touched}">
  <input type="text" placeholder="Known as" class="form-control" formControlName="knownAs">
  <span class="help-block" *ngIf="registerForm.get('knownAs').hasError('required') && registerForm.get('knownAs').touched">Known as is required</span>
</div>

<div class="form-group" [ngClass]="{'has-error': registerForm.get('dateOfBirth').errors && registerForm.get('dateOfBirth').touched}">
  <input type="date" placeholder="Date of birth" class="form-control" formControlName="dateOfBirth">
  <span class="help-block" *ngIf="registerForm.get('dateOfBirth').hasError('required') && registerForm.get('dateOfBirth').touched">Date of birth as is required</span>
</div>

<div class="form-group" [ngClass]="{'has-error': registerForm.get('city').errors && registerForm.get('city').touched}">
  <input type="text" placeholder="City" class="form-control" formControlName="city">
  <span class="help-block" *ngIf="registerForm.get('city').hasError('required') && registerForm.get('city').touched">City is required</span>
</div>

<div class="form-group" [ngClass]="{'has-error': registerForm.get('country').errors && registerForm.get('country').touched}">
  <input type="text" placeholder="Country" class="form-control" formControlName="country">
  <span class="help-block" *ngIf="registerForm.get('country').hasError('required') && registerForm.get('country').touched">Country is required</span>
</div>
...
```

Test that validation is working and that the validation errors are displayed correctly.

## Datepicker

The support for the *input* type *date* is not very good on all the browsers, see this reference table: [caniuse.com](https://caniuse.com/#search=date). Also the interface differs from browser to browser.

To keep things working and a similar user experience on all the browsers we add another extension from *Valor soft* the [ngx-datepicker](https://valor-software.com/ngx-bootstrap/#/datepicker).

Import in **app.config.ts**

```typescript
...
imports: [
...
  BsDatepickerModule.forRoot(),
...
],
...
```

Add the css int **angular.json**

```json
"styles": [
  ...
  "node_modules/ngx-bootstrap/datepicker/bs-datepicker.css",
  "src/styles.css"
],
```

Restart *ng serve*.

Open the template **register.component.html** and change the *input* type from *date* to *text* and add the attribute *bsDatepicker*. Then we bind *bsConfig* to a property of our component (see below).

```html
<input type="text" placeholder="Date of birth" class="form-control" bsDatepicker [bsConfig]="bsConfig" formControlName="dateOfBirth" >
```

Open **register.component.ts** and create a new property **bsConfig** and add some configuration.

```typescript
...
import { BsDatepickerConfig } from 'ngx-bootstrap/datepicker';
...
bsConfig: Partial<BsDatepickerConfig> = { containerClass: 'theme-dark-blue', maxDate: this.yesterday() };
...
private yesterday(): Date {
  const yesterday = new Date();
  yesterday.setDate(yesterday.getDate() - 1);
  return yesterday;
}
...
```
