---
title: "Angular"
date: 2018-05-30T22:03:22+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "ng", "npm"]
categories: ["dev"]
draft: false
---

A skeleton spa that loads data from the webapi

<!--more-->

## Angular setup
Create a new Angular application inside the DatingApp folder, at the same level of the DatingApp.API.

```shell
cd ~/dev/DatingApp
ng new DatingApp.SPA
```

In the *package.json* file we can see that the default scripts for npm are already configured to launch ng with the appropriate parameters.

Let's run the default Angular application and check that everything is working

```shell
npm start
# equivalent to
# ng serve
```

Navigate with the browser to http://localhost:4200/

Now it is a good time to make a git commit with new files.

## Value component and http request

Note that using the *Angular Files* extension to create a component also adds the registration code inside *app-module.ts*.

Creat a **values** component:

- right click on *src/app*
- click **Generate Component**
- name the new component **values**

Add **HttpModule** to the imports of *app-module.ts*

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';
import { ValuesComponent } from './values/values.component';

@NgModule({
  declarations: [
    AppComponent,
    ValuesComponent
],
  imports: [
    BrowserModule,
    HttpModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Edit the **values** component

- add a property to store the values
- inject a *Http* service into the constructor
- create a new function getValues to make the request

```javascript
...
export class ValuesComponent implements OnInit {
  values: any;

  constructor(private http: Http) {}

  ngOnInit() {
    this.getValues();
  }

  getValues(): any {
    this.http.get('https://localhost:5001/api/values')
    .subscribe(response => {
      // console.log(response);
      this.values = response.json();
    });
  }
}
```

Edit the **values** html

```html
<div>
  <h2>
    Values from db
  </h2>
  <p *ngFor="let value of values">id {{value.id}}: "{{value.name}}"</p>
</div>
```

Add the *app-values* selector inside *app.component.html* and clean up style.

```html
<div>
  <h1>
    Dating APP
  </h1>
  <app-values></app-values>
</div>
```

## Bootstrap and font-awesome

Install with npm in the folder DatingApp.SPA, we use a specific version of bootstrap.

```shell
npm install bootstrap@3.3.7 font-awesome --save
```

Add the styles into *.angular-cli.json*

```json
...
"styles": [
  "../node_modules/bootstrap/dist/css/bootstrap.min.css",
  "../node_modules/font-awesome/css/font-awesome.min.css",
  "styles.css"
],
...
```
