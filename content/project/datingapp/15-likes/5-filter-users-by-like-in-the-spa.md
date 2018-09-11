---
title: "Filter users by like in the SPA"
date: 2018-08-01T15:01:10.879+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

<!--more-->

## Service

Add a new optional parameter to **UserService.getUsers**

```typescript
getUsers(page?: any, itemsPerPage?: any, userParams?: any, likesParam?: string): Observable<PaginatedResult<User[]>> {
\\...
  if (likesParam === 'likers') {
    params = params.set('likers', 'true');
  }
  if (likesParam === 'likees') {
    params = params.set('likees', 'true');
  }
```

## Resolver

Create a new resolver **lists.resolver.ts**

```typescript
@Injectable()
export class ListsResolver implements Resolve<PaginatedResult<User[]>> {
  pageNumber = 1;
  pageSize = 5;
  likesParam: any;

  constructor(private router: Router, private userService: UserService, private alertify: AlertifyService) {}

  resolve(route: ActivatedRouteSnapshot): Observable<PaginatedResult<User[]>> {
    return this.userService.getUsers(this.pageNumber, this.pageSize, null, this.likesParam).pipe(
        catchError(error => {
          this.alertify.error('Problem retrieving data');
          this.router.navigate(['/home']);
          return empty();
        })
      );
  }
}
```

Add this resolver to **route.ts**

```typescript
{
  path: 'lists',
  component: ListsComponent,
  resolve: { users: ListsResolver }
},
```

Register the resolver in **app.module.ts**

```typescript
providers: [
  // ...
  ListsResolver
],
```

## Component

Edit **lists.component.ts**

```typescript
@Component({
  selector: 'app-lists',
  templateUrl: './lists.component.html',
  styleUrls: ['./lists.component.css']
})
export class ListsComponent implements OnInit {
  users: User[];
  pagination: Pagination;
  likesParam: string;

  constructor(
    private route: ActivatedRoute,
    private userService: UserService,
    private alertify: AlertifyService
  ) {}

  ngOnInit() {
    this.route.data.subscribe(data=> {
      this.users = data['users'].result;
      this.pagination = data['users'].pagination;
    });
    this.likesParam = 'Likers';
  }

  pageChanged(event: any): void {
    this.pagination.currentPage = event.page;
    this.loadUsers();
  }

  loadUsers() {
    this.userService
      .getUsers(this.pagination.currentPage, this.pagination.itemsPerPage, null, this.likesParam)
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
}
```

Add the markup in **lists.component.html**

```html
<div class="text-center mt-3">
  <h2>{{likesParam === 'Likers' ? 'Members who like me' : 'Members who I\'ve Liked'}} : {{pagination.totalItems}}</h2>
</div>

<div class="container mt-3">

  <div class="row">
    <div class="btn-group">
      <button class="btn btn-primary" [(ngModel)]="likesParam" btnRadio="Likers" (click)="loadUsers()">Members who like me</button>
      <button class="btn btn-primary" [(ngModel)]="likesParam" btnRadio="Likees" (click)="loadUsers()">Members who I like</button>
    </div>
  </div>

  <br>

  <div class="row">
    <div *ngFor="let user of users" class="col-sm-6 col-md-4 col-lg-4 col-xl-2">
      <app-member-card [user]="user"></app-member-card>
    </div>
  </div>
</div>

<div class="d-flex justify-content-center">
  <pagination [boundaryLinks]="true" [totalItems]="pagination.totalItems" [itemsPerPage]="pagination.itemsPerPage" [(ngModel)]="pagination.currentPage"
    (pageChanged)="pageChanged($event)" previousText="&lsaquo;" nextText="&rsaquo;" firstText="&laquo;" lastText="&raquo;"></pagination>
</div>
```

Note that in the application API the **Gender** is automatically added if not specified in the **UserParams**. In the Members page we can filter for both genders and add a like to anyone. But in this page only likes received from the opposite gender are considered.
