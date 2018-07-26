---
title: "Updating Bootstrap"
date: 2018-07-26T12:10:22.702+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Install bootstrap 4

```shell
# Specify the version to force the update from 3 to 4
npm install bootstrap@4.1.1 -save
npm install bootstrap -save
# Update to the latest version
```

Open **angular.json** and remove all the added css in the *styles* section

```json
"styles": [
  "src/styles.css"
],
```

Open **styles.css** and add the styles

```css
@import '../node_modules/bootstrap/dist/css/bootstrap.min.css';
@import '../node_modules/bootswatch/dist/united/bootstrap.min.css';
```

Update *Bootswatch*

```shell
npm install bootswatch@latest --save
```

Fix some security stuff

```shell
npm audit fix
```

## HttpInterceptor

*HttpInterceptor* intercepts http errors globally so can remove any error handling from our services: the **handleError** method is no longer necessary.

Create a new class in the **_service** folder **error.interceptor.ts** (this is not a *service* but is used ony by services)

```typescript
import { Injectable } from '@angular/core';
import {
  HttpInterceptor,
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpErrorResponse,
  HTTP_INTERCEPTORS
} from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(catchError(this.handleError));
  }

  private handleError(error: any) {
    if (error instanceof HttpErrorResponse) {
      if (error.status === 401) {
        return throwError(error.statusText);
      }
      const applicationError = error.headers.get('Application-Error');
      if (applicationError) {
        console.error(applicationError);
        return throwError(applicationError);
      }
      const serverError = error.error;
      let modelStateErrors = '';
      if (serverError && typeof serverError === 'object') {
        for (const key in serverError) {
          if (serverError[key]) {
            modelStateErrors += serverError[key] + '\n';
          }
        }
      }
      return throwError(
        // if there is no model state error we still send a message
        modelStateErrors || serverError || 'Server error'
      );
    }
  }
}

export const ErrorInterceptorProvider = {
  provide: HTTP_INTERCEPTORS,
  useClass: ErrorInterceptor,
  multi: true
};
```
