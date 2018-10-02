---
title: "Firebase Storage"
date: 2018-10-02T17:21:55.085+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

From your [firebase console](https://console.firebase.google.com/) open the settings and click on *Get Started*.

Our documents will be Resumes, CVs and Cover Letters. Similarly to firestore they will be saved under

```shell
/users/{userid}/{resumeid}/{version}/{filename}

eg: /users/E77oXZRvPyZtEjQoS7FeA4g6w6i1/pLbAjzAb7RHxyeZqYH05/1/mymastercv.pdf
eg: /users/E77oXZRvPyZtEjQoS7FeA4g6w6i1/pLbAjzAb7RHxyeZqYH05/2/mymastercv_something.pdf
```
This permits to have multiple version of the same document and mantain the original filename

The security rules will protect the user folder and limit upload size.

```javascript
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read, write: if request.auth != null;
    }
    match /users/{uid}/{allPaths=**} {
      allow read: if isCurrentUser(uid);
      allow write: if isCurrentUser(uid) &&
                      lessThanNMegabytes(n) &&
                      request.resource !=null &&
                      filename.size() < 50;
    }
  }
}

function isCurrentUser(uid) {
    return request.auth.uid == uid;
}

function lessThanNMegabytes(n) {
    return request.resource.size < n * 1024 * 1024;
}
```
