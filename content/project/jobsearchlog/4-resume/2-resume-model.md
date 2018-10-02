---
title: "Resume model"
date: 2018-10-02T17:23:46.117+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

The **Resume** model is very simple

```typescript
export interface Resume {
  name: string;
  versions: ResumeVersion[];
}

export interface ResumeVersion {
  readonly version: number;
  readonly createdAt: Date;
  readonly filename: string;
  archived: boolean;
}
```

The user will be able to link a resume to a job and later update the resume. The version permits to recover the correct document for each job application.

We will also use some view models

```typescript
export interface ResumeForList {
    name: string;
    createdAt: Date;
    storagePath: string;
}

export interface ResumeUpload {
    name: string;
    file: File;
}
```
