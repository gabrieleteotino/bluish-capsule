---
title: "Testing the photos controller"
date:
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Post a photo with Postman

In Postman configure a folder o a collection to send the authorization token.

Create a new *Request* of type *POST*.

In the *Body* tab set it to send *form-data* and add a *Key* named **File** (like the object property) and set it of type *File*. Choose a test picture from your pc. Save the request but don't send it.

In visual studio code attach the debugger and set a breakpoint at the start of **AddPhotoForUser**.

Send the request with Postman.

Follow the code and be sure that everything is ok.

# Check the results

Go to the [Cloudinary dashboard](https://cloudinary.com/console) and find the uploaded image. open the image and check the *PublicId*.

Using *Db Browser fro SQLite* open the **Photos** table; there is a new item with the same *PublicId*.
