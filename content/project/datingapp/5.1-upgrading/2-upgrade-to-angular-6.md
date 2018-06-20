---
title: "Upgrade to Angular 6"
date: 2018-06-16T13:07:13+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

The project started with Angular 5 using *@angular/cli@1.7.4* as recommended by the course instructor.

Today I realized that, even if I am new to Angular, the latest version of Angular is the way to go.

So, let's upgrade!

<!--more-->

The upgrade [guide](https://update.angular.io/)

## Preparation

Switch from **HttpModule** to **HttpClientModule**.

Open **app.modules.ts** and change the imports

```typescript
...
import { HttpClientModule } from '@angular/common/http';
...
imports: [
  BrowserModule,
  HttpClientModule,
  FormsModule
],
...
```

Remove the old import.

Switch from **Http** to **HttpClient**.

Open **auth.service.ts** chenge the import, the constructor and fix the other methods. The overall solution is almost the same with the notable difference that the response is already the required object without the need to call *.json()*

```typescript
...
import { HttpClient, HttpHeaders, HttpResponse, HttpErrorResponse } from '@angular/common/http';
...
login(model: any) {
  return this.http
    .post(this.baseUrl + 'login', model, this.httpOptions())
    .pipe(
      map((user: any) => {
        if (user) {
          localStorage.setItem('token', user.tokenString);
          this.userToken = user.tokenString;
        }
      }),
      catchError(this.handleError)
    );
}
...
private httpOptions() {
   return {
     headers: new HttpHeaders({ 'Content-type': 'application/json' }),
     oberve: 'response'
   };
 }
private handleError(errorResponse: HttpErrorResponse) {
  const applicationError = errorResponse.headers.get('Application-Error');
  if (applicationError) {
    return _throw(applicationError);
  }
  const serverError = errorResponse.error;
  let modelStateErrors = '';
  if (serverError) {
    for (const key in serverError) {
      if (serverError[key]) {
        modelStateErrors += serverError[key] + '\n';
      }
    }
  }
  return _throw(
    // if there is no model state error we still send a message
    modelStateErrors || 'Server error'
  );
}
...
```

## Upgrade cli

This will update Angular cli globally (this is slightly different from the instruction on the guide)

```shell
npm uninstall -g angular-cli
npm cache verify
npm install -g @angular/cli
```

Move in the **DatingApp.SPA** folder and update the cli locally

```shell
rm -rf node_modules
npm uninstall --save-dev angular-cli
npm install --save-dev @angular/cli
npm install
```

## Upgrade configuration

```shell
ng update @angular/cli
ng update @angular/core
ng update @angular/material
```

Check if all the packages are in updated

```shell
ng update
```

## Fix rxjs

Install lint for version 6 and run the automatic update

```shell
npm install -g rxjs-tslint
rxjs-5-to-6-migrate -p src/tsconfig.app.json
```

Remove the compatibility layer

```shell
npm remove rxjs-compat
```

Open vscode and fix the *_throw* alias.

```typescript
...
import { throwError } from 'rxjs';
...
private handleError(errorResponse: HttpErrorResponse) {
  const applicationError = errorResponse.headers.get('Application-Error');
  if (applicationError) {
    return throwError(applicationError);
  }
  const serverError = errorResponse.error;
  let modelStateErrors = '';
  if (serverError) {
    for (const key in serverError) {
      if (serverError[key]) {
        modelStateErrors += serverError[key] + '\n';
      }
    }
  }
  return throwError(
    // if there is no model state error we still send a message
    modelStateErrors || 'Server error'
  );
}
...
```
