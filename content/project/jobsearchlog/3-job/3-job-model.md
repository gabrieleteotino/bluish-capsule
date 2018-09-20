---
title: "Job model"
date: 2018-09-18T14:56:00.862+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

Create a folder for the models **src/app/_models**

Create a new file **src/app/_models/job.model.ts**

```typescript
import { JobStatus } from './job-status.model';

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

Create a new file **_models/job-status.model.ts**

```typescript
export enum JobStatus {
  preparation = 'Preparation',
  application = 'Application',
  negotiation = 'Negotiation',
  closed = 'Closed'
}
```