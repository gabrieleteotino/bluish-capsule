---
title: "Using pipes for dates"
date: 2018-07-19T15:40:23.420+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

Formatting the dates in the SPA

<!--more-->

## time-ago-pipe

Install [time-ago-pipe](https://www.npmjs.com/package/time-ago-pipe) for converting a date string into a time ago (eg: "an hour ago" or "5 days ago").

Stop *ng serve**.

```shell
npm install time-ago-pipe --save
```

Open **app.module.ts** and add it to the *declarations*

```typescript
...
import {TimeAgoPipe} from 'time-ago-pipe';
...
@NgModule({
    imports: [... etc ...],
    declarations: [..., TimeAgoPipe, ... ]
})
```

Start *ng serve**.


## Formatting dates

Open **member-detail.component.html** and use the default [angular date pipe](https://angular.io/api/common/DatePipe) for the *Member since* date and the *timeAgo* pipe for *Last active*

```html
<div>
  <strong>Last active:</strong>
  <p>{{user.lastActive | timeAgo}}</p>
</div>
<div>
  <strong>Member since:</strong>
  <p>{{user.created | date:'mediumDate'}}</p>
</div>
```

Make the same adjustments in **member-edit.component.html**

```html
<div>
  <strong>Last active:</strong>
  <p>{{user.lastActive | timeAgo}}</p>
</div>
<div>
  <strong>Member since:</strong>
  <p>{{user.created | date:'mediumDate'}}</p>
</div>
```
