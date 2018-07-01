---
title: "Routing in code"
date: 2018-06-18T12:57:40.211+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

Feature: after the user log in he must be redirected to the *members* page.

## Fix dropdown

The dropdown has a *href* that with the Routing enabled is no longer working. Remove the href and fix the css pointer.

Note the explicit *click* handler set to false.

```html
<a (click)="false" class="dropdown-toggle" dropdownToggle>Welcome {{ getUsername() }} <span class="caret"></span></a>
```

```css
.dropdown-menu li, .dropdown-toggle {
    cursor: pointer;
}
```

## Redirect after login

Open **nav.component.ts** and add a *Router* DI to the constructor and add the third event to the **login** *observable*. When we **logout** redirect to *home*.

```typescript
...
constructor(private authService: AuthService, private alertify: AlertifyService, private router: Router) {}
...
login() {
  console.log(this.model);
  this.authService.login(this.model).subscribe(data => {
    this.alertify.success('Sucessfully logged in');
  }, error => {
    this.alertify.error("Failed to login");
  }, () => {
    this.router.navigate(['/members']);
  });
}

logout() {
  this.authService.logout();
  this.alertify.message('Logged out');
  this.router.navigate(['/home']);
}
...
```
