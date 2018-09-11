---
title: "Delete messages SPA"
date: 2018-08-06T15:07:46.353+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

<!--more-->

## Service

Add a new method to **user.service.ts**

```typescript
deleteMessage(userId: number, messageId: number) {
 return this.http.post(this.baseUrl + userId + '/messages/' + messageId, {});
}
```

## Component

Open **messages.component.ts** and hook it up to the new **deleteMessage**

```typescript
deleteMessage(messageId: number) {
  this.alertify.confirm('Are you sure?', () => {
    this.userService.deleteMessage(this.authService.getUserId(), messageId).subscribe(
      () => {
        this.messages.splice(this.messages.findIndex(m => m.id === messageId), 1);
        this.alertify.success('Message deleted');
      },
      error => this.alertify.error(error)
    );
  });
}
```

And in the html link the *click* event, also stop the propagation of the click to the *routerLink* of the *tr*

```html
<button class="btn btn-danger" (click)="deleteMessage(message.id)" (click)="$event.stopPropagation()">Delete</button>
```
