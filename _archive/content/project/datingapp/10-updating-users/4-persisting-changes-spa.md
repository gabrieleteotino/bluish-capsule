---
title: "Persisting changes SPA"
date: 2018-06-30T17:27:16+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

Update the user in the client.

<!--more-->

## User service

Open **user.service.ts** and add a metod **updateUser**

```typescript
updateUser(id: number, user: User): Observable<any> {
  return this.http
    .put(this.baseUrl + id, user)
    .pipe(catchError(this.handleError));
}
```

## User component

In **member-edit.component.ts** make use of the new method.

```typescript
updateUser() {
  this.userService.updateUser(this.authService.getUserId(), this.user).subscribe(
    _ => {
      this.editForm.reset(this.user);
      this.alertify.success('Profile updated succesfully');
    },
    error => {
      this.alertify.success(error);
    }
  );
}
```
