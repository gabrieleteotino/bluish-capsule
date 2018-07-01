---
title: "Alertifyjs"
date: 2018-06-16T16:13:54.000+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

The official [guide](http://alertifyjs.com/guide.html).

## Installation

Stop *ng serve* if it is running.

```shell
npm install alertifyjs --save
```

Open **angular.json** (it was *angular-cli.json* in Angular 5).

Add the new styles and the javascript library.

```json
"styles": [
  "node_modules/bootstrap/dist/css/bootstrap.min.css",
  "node_modules/font-awesome/css/font-awesome.min.css",
  "node_modules/alertifyjs/build/css/alertify.min.css",
  "node_modules/alertifyjs/build/css/themes/bootstrap.min.css",
  "src/styles.css"
],
"scripts": [
  "node_modules/alertifyjs/build/alertify.min.js"
]
```

Restart *ng serve*

## Service wrapper

Implement a new service around alertifyjs to wrap only the features we need and don't use the library globally.

Right click on **_service** click *Generate Service** and call it **alertify**.

Open **alertify.service.md** and add a global variable and a few simple functions.

```typescript
...
declare let alertify: any;
...
confirm(message: string, okCallback: () => any) {
  alertify.confirm(message, okCallback);
}

success(message: string) {
  alertify.success(message);
}

error(message: string) {
  alertify.error(message);
}

warning(message: string) {
  alertify.warning(message);
}

message(message: string) {
  alertify.message(message);
}
```

Add the reference in **app.module.ts** as a *Module*

```typescript
providers: [
  AuthService,
  AlertifyService
],
```

## Use the AlertifyService

Open **navbar.component.ts** and inject the service in the constructor. Then substitute the *console.log* calls with alertify.

```typescript
...
constructor(private authService: AuthService, private alertify: AlertifyService) {}
...
login() {
  console.log(this.model);
  this.authService.login(this.model).subscribe(data => {
    this.alertify.success('Sucessfully logged in');
  }, error => {
    this.alertify.error(error);
  });
}

logout() {
  this.authService.userToken = null;
  localStorage.removeItem('token');
  this.alertify.message('Logged out');
}
...
```

Do the same thing in **register.component.ts**.

Test the application to see the new messages.
