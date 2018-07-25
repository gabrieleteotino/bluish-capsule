---
title: "Filtering in the SPA"
date: 2018-07-25T10:30:35.989+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Filtering form

Open **member-list.component.html** and add a new row before the list of members with an inline form.

```html
<form class="form-inline" novalidate>
  <div class="form-group">
    <label for="minAge">Age from </label>
    <input type="text" name="minAge" id="minAge" class="form-control" style="width: 70px;">
  </div>
  <div class="form-group">
    <label for="maxAge">Age to </label>
    <input type="text" name="maxAge" id="maxAge" class="form-control" style="width: 70px;">
  </div>
  <div class="form-group">
    <label for="gender">Show </label>
    <select name="gender" id="gender" class="form-control" style="width: 130px;">
      <option></option>
    </select>
  </div>
  <button type="submit" class="btn btn-primary" style="margin-left: 10px">Apply filter</button>
  <button type="button" class="btn btn-info" style="margin-left: 10px">Reset filter</button>
</form>
```

And a custom style to fix the margins between items

```css
.form-inline > .form-group > * {
  margin-left: 3px;
}
```

Open **member-list.component.ts** and add some properties, then initialize the default values for the **userParams**. Add a method to reset the filter to the default values and add **userParams** to the parameters of **getUsers**

```typescript
user: User = this.authService.getUser();
genderList: any = [
  { value: 'male', display: 'Males' },
  { value: 'female', display: 'Females' }
];
userParams: any = {};

ngOnInit() {
  this.route.data.subscribe(data => {
    this.users = data['users'].result;
    this.pagination = data['users'].pagination;
  });

  this.userParams.gender = this.user.gender === 'male' ? 'female' : 'male';
}

loadUsers() {
  this.userService
    .getUsers(this.pagination.currentPage, this.pagination.itemsPerPage, this.userParams)
    .subscribe(
      result => {
        this.users = result.result;
        this.pagination = result.pagination;
      },
      error => {
        this.alertify.error(error);
      }
    );
}

resetFilters() {
  this.userParams.gender = this.user.gender === 'male' ? 'female' : 'male';
  this.userParams.minAge = 18;
  this.userParams.maxAge = 99;
  this.loadUsers();
}
```

Bind the form to the new properties and functions

```html
<form class="form-inline" novalidate #form="ngForm" (ngSubmit)="loadUsers()">
  <div class="form-group">
    <label for="minAge">Age from </label>
    <input type="text" name="minAge" id="minAge" class="form-control" style="width: 70px;" [(ngModel)]="userParams.minAge">
  </div>
  <div class="form-group">
    <label for="maxAge">Age to </label>
    <input type="text" name="maxAge" id="maxAge" class="form-control" style="width: 70px;" [(ngModel)]="userParams.maxAge">
  </div>
  <div class="form-group">
    <label for="gender">Show </label>
    <select name="gender" id="gender" class="form-control" style="width: 130px;" [(ngModel)]="userParams.gender">
      <option *ngFor="let gender of genderList" [value]="gender.value">{{gender.display}}</option>
    </select>
  </div>
  <button type="submit" class="btn btn-primary" style="margin-left: 10px">Apply filter</button>
  <button type="button" class="btn btn-info" style="margin-left: 10px" (click)="resetFilters()">Reset filter</button>
</form>
```

Add a parameter for **userParams** in **user.service.ts** method **getUsers**

```typescript
if (userParams != null) {
  params = params
    .set('minAge', userParams.minAge)
    .set('maxAge', userParams.maxAge)
    .set('gender', userParams.gender);
}
```

Test the application.
