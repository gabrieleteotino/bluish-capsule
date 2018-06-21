---
title: "Members card"
date: 2018-06-21T13:02:06.460+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Organization

Reorganize some components. We are going to add a few components for members, so create a new folder **app/members**.

Move the forder **members-list** inside **members**.

Open **app.module.ts** and **routes.ts** and update the reference for **MemberListComponent**.

Open **member-list.component.ts** and adjust the imports for the services.

Recompile the app by stopping and starting *ng serve* and test in the browser that everithing is ok.

## Member Card

Create a new component **members/member-card**. When we create a component in a sub folder the angular extension doesn't add it reference, so open **app.module.ts** and add it.

```typescript
declarations: [
  AppComponent,
  NavComponent,
  HomeComponent,
  RegisterComponent,
  MemberListComponent,
  ListsComponent,
  MessagesComponent,
  MemberCardComponent
],
```

Open **member-card.component.ts** and add an *input* prop for the user. The parent component will populate this property.

```typescript
@Input() user: User;
```

Open **member-card.component.html** and create a card with a [bootstrap panel](http://getbootstrap.com/docs/3.3/components/#panels). Note that *panel-image* is not a bootstrap class.

```html
<div class="panel panel-default">
  <div class="panel-body">
    <div class="panel-image">
      <img src="{{user.profilePhotoUrl}}" alt="{{user.knownAs}}">
    </div>
    <div class="text-center">
      <h5><i class="fa fa-user"></i> {{user.knownAs}}, {{user.age}}</h5>
    </div>
    <div class="text-canter">
      <h5><small>{{user.city}}</small></h5>
    </div>
  </div>
</div>
```

Open **member-list.component.html** and make use of the new component passing the *user* from the *ngFor*to the input property *user*.

```html
<div class="container">
  <div class="row">
    <div *ngFor="let user of users" class="col-lg-2 col-md-3 col-sm-6">
      <app-member-card [user]="user"></app-member-card>
    </div>
  </div>
</div>
```

## Styling member card

Open **member-card.component.css** and add some styling. We reduce a bit the margins and set the photo to be at 100% of the panel. The *transform* on the image will zoom in when the mouse hovers.

```css
.panel-body {
  padding: 5px;
}

.panel-body img {
  width: 100%;
}

h5 {
  font-size: 16px;
  font-weight: 700;
  color: #3a3c41;
  margin-top: 1px;
  margin-bottom: 1px;
}

h5 small {
  font-size: 12px;
  font-weight: bold;
  font-style: italic;
}

img {
  transform: scale(1, 1);
  transition-duration: 500ms;
  transition-timing-function: ease-out;
}

.panel:hover img {
  transform: scale(1.2, 1.2);
  transition-duration: 500ms;
  transition-timing-function: ease-out;
  opacity: 0.7;
}

.panel-image {
  overflow: hidden;
}
```

## Adding buttons

Open **member-card.component.html** and add buttons just below the image.

```html
...
<img src="{{user.profilePhotoUrl}}" alt="{{user.knownAs}}">
<ul class="list-inline member-icons animate text-center">
  <li>
    <button class="btn btn-primary"><i class="fa fa-user"></i></button>
  </li>
  <li>
    <button class="btn btn-primary"><i class="fa fa-heart"></i></button>
  </li>
  <li>
    <button class="btn btn-primary"><i class="fa fa-envelope"></i></button>
  </li>
</ul>
...
```

Now adjust **member-card.component.css** adding a *position:relative* to *panel-image* so that we can adjust the positioning of the buttons.
