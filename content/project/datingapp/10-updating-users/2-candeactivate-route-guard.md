---
title: "CanDeactivate route guard"
date: 2018-06-30T15:53:30+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

<!--more-->

## Guard

Create a new guard **prevent-unsaved-changes.guard.ts**

```shell
ng g guard /_guards/prevent-unsaved-changes
```

```typescript
@Injectable({
  providedIn: 'root'
})
export class PreventUnsavedChangesGuard
  implements CanDeactivate<MemberEditComponent> {
  canDeactivate(component: MemberEditComponent) {
    if (component.editForm.dirty) {
      return confirm(
        'Are you sure you want to continue? Any unsaved changes will be lost!'
      );
    } else {
      return true;
    }
  }
}
```

Add the new *guard* to **app.module.ts** in the **providers** section.

In **routes.ts** add the new guard to the route of **member/edit**.

```typescript
{
  path: 'member/edit',
  component: MemberEditComponent,
  resolve: { user: MemberEditResolver },
  canDeactivate: [PreventUnsavedChangesGuard]
},
```

The guard is now active, test the application.
