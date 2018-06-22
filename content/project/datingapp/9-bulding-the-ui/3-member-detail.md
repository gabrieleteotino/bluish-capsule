---
title: "Member detail"
date: 2018-06-22T11:26:53.785+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## User service

Open **user.service.ts** and add a method to get a single user.

```typescript
getUser(id: number): Observable<User> {
  return this.http
    .get<User>(this.baseUrl + id)
    .pipe(catchError(this.handleError));
}
```

## Member Detail

Create a new component **members/member-detail**. When we create a component in a sub folder the angular extension doesn't add it reference, so open **app.module.ts** and add it.

Add a property *user*, add DI in the contructor for *HttpClient*, *AlertifyService* and *ActivatedRoute*. Add a method *loadUser*.

```typescript
constructor(
  private userService: UserService,
  private alertify: AlertifyService,
  private route: ActivatedRoute
) {}

ngOnInit() {
  this.loadUser(+this.route.snapshot.params['id']);
}

loadUser(id: number) {
  this.userService.getUser(id).subscribe(
    user => {
      this.user = user;
    },
    error => {
      this.alertify.error(error);
    }
  );
}
```

Open **member-detail.component.html** and add a simple property binding.

```
<p>
  {{user?.knownAs}}
</p>
```

## Router

Open **routes.ts** and a dd a route to the new component with a parameter *:id*.

```typescript
children: [
  { path: 'members', component: MemberListComponent },
  { path: 'members/:id',  component: MemberDetailComponent },
  { path: 'lists', component: ListsComponent },
  { path: 'messages', component: MessagesComponent }
]
```

## Load user from route

Open **member-card.component.html** and add a *a-routerlink*

```html
<button class="btn btn-primary btn-responsive" [routerLink]="['/members/', user.id]">
  <i class="fa fa-user"></i>
</button>
```

## Styling member detail
