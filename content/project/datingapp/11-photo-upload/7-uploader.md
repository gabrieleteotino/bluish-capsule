---
title: "Uploader"
date: 2018-07-08T21:43:10+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

Add a new 3rd party component [ng2-file-upload](https://valor-software.com/ng2-file-upload/) from *Valor software* (the same authors of *Bootstrap NGX* from chapter 6).

We are going to modify the sample code from the component sample page.

Stop *ng serve* and install the component

```shell
npm install ng2-file-upload --save
```

Open **app.module.ts** and add an *import* for **FileUploadModule**.

Restart *ng serve*. (Restart visual studio code to be able to view the new library).

Open **photo-editor.component.ts** and add a prop for the **uploader** and one for the *drop zone*. And a method for detecting when the drop zone is active **fileOverBase**.

```typescript
...
uploader: FileUploader = new FileUploader({});
hasBaseDropZoneOver = false;
...
public fileOverBase(e: boolean): void {
  this.hasBaseDropZoneOver = e;
}
...
```

For now the **uploader** is missing the configuration, will fix it soon.

Open **photo-editor.component.html** and below the existing code add the following. Note: this is a slightly modified version of the official exaple.

```html
...
<div class="row">
  <div class="col-md-3">
    <h3>Add Photos</h3>

    <div ng2FileDrop [ngClass]="{'nv-file-over': hasBaseDropZoneOver}" (fileOver)="fileOverBase($event)" [uploader]="uploader"
      class="well my-drop-zone">
      Drop Photos Here
    </div>

    Multiple
    <input type="file" ng2FileSelect [uploader]="uploader" multiple />
    <br/> Single
    <input type="file" ng2FileSelect [uploader]="uploader" />
  </div>

  <div class="col-md-9" style="margin-bottom: 40px" *ngIf="uploader?.queue?.length">
    <h3>Upload queue</h3>
    <p>Queue length: {{ uploader?.queue?.length }}</p>

    <table class="table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Size</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let item of uploader.queue">
          <td>
            <strong>{{ item?.file?.name }}</strong>
          </td>
          <td *ngIf="uploader.isHTML5" nowrap>{{ item?.file?.size/1024/1024 | number:'.2' }} MB</td>
        </tr>
      </tbody>
    </table>

    <div>
      <div>
        Queue progress:
        <div class="progress">
          <div class="progress-bar" role="progressbar" [ngStyle]="{ 'width': uploader.progress + '%' }"></div>
        </div>
      </div>
      <button type="button" class="btn btn-success btn-s" (click)="uploader.uploadAll()" [disabled]="!uploader.getNotUploadedItems().length">
        <span class="glyphicon glyphicon-upload"></span> Upload
      </button>
      <button type="button" class="btn btn-warning btn-s" (click)="uploader.cancelAll()" [disabled]="!uploader.isUploading">
        <span class="glyphicon glyphicon-ban-circle"></span> Cancel
      </button>
      <button type="button" class="btn btn-danger btn-s" (click)="uploader.clearQueue()" [disabled]="!uploader.queue.length">
        <span class="glyphicon glyphicon-trash"></span> Remove
      </button>
    </div>
  </div>
</div>
```

Add to the *css*

```css
/* Default class applied to drop zones on over */
.nv-file-over {
  border: dotted 3px red;
}
```
