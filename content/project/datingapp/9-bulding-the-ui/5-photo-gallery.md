---
title: "Photo gallery"
date: 2018-06-29T21:32:41+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Gallery component

[NgxGallery](https://github.com/lukasz-galka/ngx-gallery) [demo](https://lukasz-galka.github.io/ngx-gallery-demo/)

Stop *ng serve*.

Install with npm

```shell
npm install ngx-gallery --save
```

Restart *ng serve*

Add the *Import* in **app.module.ts**

```typescript
import { NgxGalleryModule } from 'ngx-gallery';
...
imports: [
    ...
    NgxGalleryModule
    ...
],
...
```

## Using ngx-gallery

Open **member-detail.component.ts** and add two fields for the component.

```typescript
galleryOptions: NgxGalleryOptions[];
galleryImages: NgxGalleryImage[];
```

In **ngOnInit** configure the *galleryOptions* and put the images urls into *galleryImages*.

```typescript1`
ngOnInit() {
  this.route.data.subscribe(data => {
    this.user = data['user'];
  });

  this.galleryOptions = [
    {
      width: '500px',
      height: '500px',
      imagePercent: 100,
      thumbnailsColumns: 4,
      imageAnimation: NgxGalleryAnimation.Slide,
      preview: false
    }
  ];

  this.galleryImages = this.getGalleryImages();
}

getGalleryImages() {
  const images = [];
  for (let i = 0; i < this.user.photos.length; i++) {
    const photo = this.user.photos[i];
    images.push({
      small: photo.url,
      medium: photo.url,
      big: photo.url,
      description: photo.description
    });
  }
  return images;
}
```
Open **member-detail.component.html** and substitute the placeholder with *ngx-gallery*

```html
<ngx-gallery [options]="galleryOptions" [images]="galleryImages"></ngx-gallery>
```

Additional configuration is required for adaptivity on different screen sizes.
