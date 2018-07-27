---
title: "Updating to HttpInterceptor"
date: 2018-07-27T10:47:06.018+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## HttpInterceptor

*HttpInterceptor* intercepts http errors globally so can remove any error handling from our services: the **handleError** method is no longer necessary.

Create a new class in the **_service** folder **error.interceptor.ts** (this is not a *service* but is used only by services)

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

## Auth service

Remove the method **handleError** from **auth.service.ts** and all the relative calls to *catchError*

```typescript
register(user: User) {
  return this.http.post(this.baseUrl + 'register', user, this.httpOptions());
}

login(model: any) {
  return this.http.post(this.baseUrl + 'login', model, this.httpOptions()).pipe(
    map((response: any) => {
      if (response) {
        const token = response.tokenString;
        localStorage.setItem(LOCALSTORAGE_TOKEN_KEY, token);
        this.userToken = token;
        this.decodedToken = this.jwt.decodeToken(token);

        this.user = response.user;
        localStorage.setItem(LOCALSTORAGE_USER_KEY, JSON.stringify(this.user));
        if (this.user.profilePhotoUrl !== null) {
          this.mainPhotoUrl.next(this.user.profilePhotoUrl);
        } else {
          this.mainPhotoUrl.next(DEFAULT_USER_PICTURE);
        }
      }
    })
  );
}
```

Fix the unused imports

```typescript
import { map } from 'rxjs/operators';
import { BehaviorSubject } from 'rxjs';
```

## User service

Remove the method **handleError** from **auth.service.ts** and all the relative calls to *catchError*

```typescript
getUsers(page?: any, itemsPerPage?: any, userParams?: any): Observable<PaginatedResult<User[]>> {
  let params = new HttpParams();
  if (page != null && itemsPerPage != null) {
    params = params.set('pageNumber', page).set('pageSize', itemsPerPage);
  }
  if (userParams != null) {
    params = params
      .set('minAge', userParams.minAge)
      .set('maxAge', userParams.maxAge)
      .set('gender', userParams.gender)
      .set('orderBy', userParams.orderBy);
  }

  return this.http
    .get<User[]>(this.baseUrl, {
      params: params,
      observe: 'response'
    })
    .pipe(
      map(response => {
        const paginatedResults = new PaginatedResult<User[]>();
        paginatedResults.result = response.body;
        if (response.headers.get('Pagination') != null) {
          paginatedResults.pagination = JSON.parse(response.headers.get('Pagination'));
        }
        return paginatedResults;
      })
    );
}

getUser(id: number): Observable<User> {
  return this.http.get<User>(this.baseUrl + id);
}

updateUser(id: number, user: User): Observable<any> {
  return this.http.put(this.baseUrl + id, user);
}

setMainPhoto(userId: number, photoId: number) {
  return this.http
    .post(this.baseUrl + userId + '/photos/' + photoId + '/setMain', {});
}

deletePhoto(userId: number, photoId: number) {
  return this.http.delete(this.baseUrl + userId + '/photos/' + photoId);
}
```

Fix the unused imports

```typescript
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
```

## Test the application

TODO
