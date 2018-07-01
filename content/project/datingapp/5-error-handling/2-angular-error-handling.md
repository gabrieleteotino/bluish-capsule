---
title: "Angular error handling"
date: 2018-06-15T14:26:43.420+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

To test the Angular application remember to run the web api in *Production* mode!

```sh
dotnet run --environment=Production
```

## Register component

## Nav component

In **login()** change the *console.log* in

```typescript
console.log(error);
```

Temporarly remove `[disabled]="!loginForm.valid"` from the submit button, so we can send an empty form.

Submitting the form we can see the 400 (Bad request) in the console. The interesting part is the body:

```
"{"Password":["The Password field is required."],"Username":["The Username field is required."]}"
```
 Here we can see the server side validation errors.

## Auth service
