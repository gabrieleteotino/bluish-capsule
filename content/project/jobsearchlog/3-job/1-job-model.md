---
title: "Job model"
date: 2018-09-17T11:26:54.395+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

Create a folder for the models **src/app/_models**

Create a new file **src/app/_models/Job.ts**

```typescript
export interface Job {
    id: string;
    description: string;
    company: string;
    agency: string;
    lead: string;
    companyUrl: string;
    offerUrl: string;
    status: JobStatus;
    createdAt: Date;
    updatedAt: Date;
    location: string;
    contract: string;
}
```

The basic model needs a bit of refining, for now the only thing we need is the **status** enum.

Create a new file **_models/JobStatus.ts**

```typescript
enum JobStatus {
  Preparation,
  Application,
  Negotiation,
  Closed
}
```
