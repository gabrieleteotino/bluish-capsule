---
title: "Paging in the SPA"
date: 2018-07-23T19:05:24+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Call the API with pagination parameters

Create a new interface **models/Pagination** to store the pagination info received in the headers with the same properties of **PaginationHeader.cs**

```typescript
export interface Pagination {
  currentPage: number;
  itemsPerPage: number;
  totalItems: number;
  totalPages: number;
}
```

Create a new class **models/PaginatedResult** to store the paginated data and the pagination info.

```typescript
export class PaginatedResult<T> {
    result: T;
    pagination: Pagination;
}
```

Modify **user.service.ts** method **getUsers** to accept optional pagination parameters and put them in the query string. Then *map* the resulting **User[]** in a **PaginatedResult**

```typescript
getUsers(
  page?: string,
  itemsPerPage?: string
): Observable<PaginatedResult<User[]>> {
  let params = new HttpParams();
  if (page != null && itemsPerPage != null) {
    params = params.append('pageNumber', page);
    params = params.append('pageSize', itemsPerPage);
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
          paginatedResults.pagination = JSON.parse(
            response.headers.get('Pagination')
          );
        }
        return paginatedResults;
      })
    )
    .pipe(catchError(this.handleError));
}
```

Fix **member-list.resolver.ts** to make use of the *Observable<PaginatedResult<User[]>>* add two new properties (fixed for testing purposes)

```typescript
// ...
pageNumber = 1;
pageSize = 5;
// ...
resolve(route: ActivatedRouteSnapshot): Observable<PaginatedResult<User[]>> {
  return this.userService.getUsers(this.pageNumber, this.pageSize).pipe(
    catchError(error => {
      this.alertify.error('Problem retrieving data');
      this.router.navigate(['/home']);
      return empty();
    })
  );
}
// ...
```

Finally in **member-list.component.ts** use the new *PaginatedResult<User[]>*

```typescript
ngOnInit() {
  this.route.data.subscribe(data => {
    this.users = data['users'].result;
  });
}
```

## Test the application

Navigate to **Matches** only the first 5 users are visible.

## ngx-bootstrap pagination

To handle the pagination we use the Valorsoft [ngx bootstrap pagination](https://valor-software.com/ngx-bootstrap/#/pagination)

Register the component in the *imports* section of **app.module.ts**

```typescript
PaginationModule.forRoot(),
```

Open **member-list.component.html** and add the code from the section [Custom links content](https://valor-software.com/ngx-bootstrap/#/pagination#custom-links-content)

```html
<div class="container">
  <div class="row">
    <div *ngFor="let user of users" class="col-lg-2 col-md-3 col-sm-6">
      <app-member-card [user]="user"></app-member-card>
    </div>
  </div>
</div>

<div class="text-center">
  <pagination [boundaryLinks]="true" [totalItems]="77" previousText="&lsaquo;" nextText="&rsaquo;" firstText="&laquo;" lastText="&raquo;">
  </pagination>
</div>
```

Open **member-list.component.ts** and add a property for the *Pagination* object.

```typescript
// ...
pagination: Pagination;
// ...
ngOnInit() {
  this.route.data.subscribe(data => {
    this.users = data['users'].result;
    this.pagination = data['users'].pagination;
  });
}
// ...
```

Back in the *html* and bind it to the attributes of *pagination*

```html
<pagination [boundaryLinks]="true" [totalItems]="pagination.totalItems" [itemsPerPage]="pagination.itemsPerPage"
  [(ngModel)]="pagination.currentPage" (pageChanged)="pageChanged($event)"
  previousText="&lsaquo;" nextText="&rsaquo;" firstText="&laquo;" lastText="&raquo;">
</pagination>
```

Now create the **pageChanged** method and restore the **loadUsers** method with the new pagination features

```typescript
pageChanged(event: any): void {
  this.pagination.currentPage = event.page;
  this.loadUsers()
}

loadUsers() {
  this.userService.getUsers(this.pagination.currentPage, this.pagination.itemsPerPage).subscribe(
    result => {
      this.users = result.result;
      this.pagination = result.pagination;
    },
    error => {
      this.alertify.error(error);
    }
  );
}
```
