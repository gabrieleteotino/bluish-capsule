---
title: "Member detail"
date: 2018-06-22T11:26:53.785+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "bootstrap", "ngx-bootstrap"]
categories: ["dev"]
draft: false
---

<!--more-->

## User service

Open **user.service.ts** and add a method to get a single **User**.

```typescript
getUser(id: number): Observable<User> {
  return this.http
    .get<User>(this.baseUrl + id)
    .pipe(catchError(this.handleError));
}
```

## Member detail component

Create a new component **members/member-detail**. When we create a component in a sub folder the angular extension doesn't add it reference, so open **app.module.ts** and add it.

Add a property **user**, add DI in the constructor for **HttpClient**, **AlertifyService** and **ActivatedRoute**. Add a method **loadUser**.

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

Open **routes.ts** and add a route with a parameter to **MemberDetailComponent**.

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

## Member detail left column

Open **member-detail.component.html** and replace all the content with a two column grid layout. In the left part we have a profile picture and some details about the user.

```html
<div class="container">
  <div class="row">
    <h1>{{user?.knownAs}}</h1>
  </div>
  <div class="row">
    <div class="col-sm-4">
      <div class="panel panel-default">
        <img class="thumbnail profile-image" src="{{user?.profilePhotoUrl}}" alt="{{user?.knownAs}}">
        <div class="panel-body">
          <div>
            <strong>Location:</strong>
            <p>{{user?.city}}, {{user?.country}}</p>
          </div>
          <div>
            <strong>Age:</strong>
            <p>{{user?.age}}</p>
          </div>
          <div>
            <strong>Last active:</strong>
            <p>{{user?.lastActive}}</p>
          </div>
          <div>
            <strong>Member since:</strong>
            <p>{{user?.created}}</p>
          </div>
          <div class="panel-footer">
            <div class="btn-group-justified">
              <div class="btn-group">
                <button class="btn btn-primary">Like</button>
              </div>
              <div class="btn-group">
                <button class="btn btn-success">Message</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="col-sm-8"></div>
  </div>
</div>
```

Add some styles to **member-detail.component.css**

```css
.profile-image {
  margin: 25px;
  width: 85%;
  height: 85%;
}

.panel-body {
    padding: 0 25px;
}

.panel-footer {
    padding: 10 15px;
    background-color: #fff;
    border-top: none;
}

.btn-group-justified>.btn-group .btn {
    width: 95%
}
```

## Member detail right column

In **member-detail.component.html** add into *col-sm-8* a tabbed panel using [NGX bootstrap](https://valor-software.com/ngx-bootstrap/#/tabs). To do so open **app.module.ts** and import *TabModule*.

```typescript
...
BsDropdownModule.forRoot(),
TabsModule.forRoot(),
RouterModule.forRoot(ROUTES)
...
```

Inside the *col-sm-8* add the markup for the tabs. Note the class **members-table**, we will use this class inside the global css.

```html
<div class="col-sm-8">
  <div class="tab-panel">
    <tabset class="member-tabset">
      <tab heading="About {{user?.knownAs}}">
        <h4>Description</h4>
        <p>{{user?.introduction}}</p>
        <h4>Looking for</h4>
        <p>{{user?.lookingFor}}</p>
      </tab>
      <tab heading="Interests">
        <p>{{user?.interests}}</p>
      </tab>
      <tab heading="Photos">
        <p>Photos will go here</p>
      </tab>
      <tab heading="Messages">
        <p>Messages will go here</p>
      </tab>
    </tabset>
  </div>
</div>
```

Open **styles.css** and add the styles.

```css
.tab-panel {
  border: 1px solid #ddd;
  padding: 10px;
  border-radius: 4px;
}

.nav-tabs > li.open,
.member-tabset > .nav-tabs > li:hover {
  border-bottom: 4px solid #e5f0fc;
}

.member-tabset > .nav-tabs > li.open > a,
.member-tabset > .nav-tabs > li:hover > a {
  border: 0;
  background: none !important;
  color: #333333;
}

.member-tabset > .nav-tabs > li.open > a > i,
.member-tabset > .nav-tabs > li:hover > a > i {
  color: #a6a6a6;
}

.member-tabset > .nav-tabs > li.open .dropdown-menu,
.member-tabset > .nav-tabs > li:hover .dropdown-menu {
  margin-top: 0px;
}

.member-tabset > .nav-tabs > li.active {
  border-bottom: 4px solid #1a242f;
  position: relative;
}

.member-tabset > .nav-tabs > li.active > a {
  border: 0 !important;
  color: #333333;
}

.member-tabset > .nav-tabs > li.active > a > i {
  color: #404040;
}

.member-tabset > .tab-content {
  margin-top: -3px;
  background-color: #fff;
  border: 0;
  border-top: 1px solid #eee;
  padding: 15px 0;
}
```
