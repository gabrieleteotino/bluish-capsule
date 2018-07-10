---
title: "Cloudinary transformation"
date: 2018-07-10T20:59:24+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "cloudinary"]
categories: ["dev"]
draft: true
---

<!--more-->

With cloudinary is possible to apply an [image transformation](https://cloudinary.com/documentation/image_transformations) uploading an image.

This feature transform images on-the-fly to any required format, style and dimension, and also optimizes images to have the minimal file size

Open **PhotosController** and change the **uploadParams** of **AddPhotoForUser**.

While cropping a picture to a square aspect ratio the gravity parameter decides how to center the crop. With the parameter *faces* it detects all the faces in an image and uses the rectangle containing all face coordinates as the basis of the transformation (*faces:center* defaults to center gravity if no face is detected).

```csharp
...
var uploadParams = new ImageUploadParams()
{
    File = new FileDescription(file.FileName, stream),
    Transformation = new Transformation()
        .Width(500).Height(500).Crop("fill").Gravity("faces:center")
};
...
```
