---
title: "Photo upload component"
date: 2018-07-08T21:04:47+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["webapi", "netcore", "angular", "cloudinary"]
categories: ["dev"]
draft: false
---

<!--more-->

Create a new controller **app/members/photo-editor.component.ts**. Register the component declaration in **app.modules.ts**.

Add an *Input* prop to store the photos.

```typescript
@Input() photos: Photo[];
```

Open the html template and create a simple grid system. For each photo add two buttons one to set the main photo and the other to delete the image.

```html
<div class="row">
  <div class="col-sm-2" *ngFor="let photo of photos">
    <img src="{{photo.url}}" alt="" class="thumbnail">
    <div class="text-center">
      <button class="btn btn-xs">Main</button>
      <button class="btn btn-xs btn-danger"><i class="fa fa-trash-o"></i></button>
    </div>
  </div>
</div>
```

Edit the component *css*
```css
img.thumbnail {
    width: 100px;
    height: 100px;
    margin-bottom: 2px;
}
```

Open **member-edit.component.html** scroll to the placeholder for the photo gallery and add the new component.

```html
<tab heading="Photos">
  <app-photo-editor [photos]="user.photos"></app-photo-editor>
</tab>
```

Test the application.
