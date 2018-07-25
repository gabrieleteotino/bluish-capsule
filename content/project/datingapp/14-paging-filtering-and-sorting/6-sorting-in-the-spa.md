---
title: "Sorting in the SPA"
date: 2018-07-25T13:10:46.430+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

Add the [Radio Buttons](https://valor-software.com/ngx-bootstrap/#/buttons#radio-button) component from Valor Soft.

Register in **app.module.ts**

```typescript
@NgModule({
  imports: [ButtonsModule.forRoot(),...]
})
```

Open **member-list.component.ts** and add a default value for the **orderBy** property.

```typescript
ngOnInit() {
  this.route.data.subscribe(data => {
    this.users = data['users'].result;
    this.pagination = data['users'].pagination;
  });

  this.userParams.gender = this.user.gender === 'male' ? 'female' : 'male';
  this.userParams.minAge = 18;
  this.userParams.maxAge = 99;
  this.userParams.orderBy = 'lastactive';
}
```

Add the new parameter in **user.service.ts**

```typescript
if (userParams != null) {
  params = params
    .set('minAge', userParams.minAge)
    .set('maxAge', userParams.maxAge)
    .set('gender', userParams.gender)
    .set('orderBy', userParams.orderBy);
}
```

Test the application.
