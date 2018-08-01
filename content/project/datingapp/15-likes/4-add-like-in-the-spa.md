---
title: "Add like in the SPA"
date: 2018-08-01T13:17:15.647+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Service

Add **sendLike** to **user.service.ts**

```typescript
sendLike(fromUserId: number, toUserId: number) {
  return this.http.post(this.baseUrl + fromUserId + '/like/' + toUserId, {});
}
```

## Component

Open **member-card.component.ts** implement a new method and add the DI in the constructor

```typescript
sendLike(toUserId: number) {
  this.userService
    .sendLike(this.authService.getUserId(), toUserId)
    .subscribe(
      _ => this.alertify.success('You like ' + this.user.knownAs + '!'),
      error => this.alertify.error(error)
    );
}
```

Open **member-card.component.html** and bind the button click

```html
<li class="list-inline-item">
  <button class="btn btn-primary btn-responsive" (click)="sendLike(user.id)">
    <i class="fa fa-heart"></i>
  </button>
</li>
```
