---
title: "Job service"
date: 2018-09-18T14:56:10.583+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

Generate a new service

```shell
ng g service _services/job
```

Open **job.service.ts** and add a method to get paginated results of **Job** given a user id.

```typescript
import { Injectable } from '@angular/core';
import {
  AngularFirestore,
  DocumentChangeAction
} from '@angular/fire/firestore';
import { AuthService } from './auth.service';
import { Observable, from } from 'rxjs';
import { map } from 'rxjs/operators';
import { Job } from '../_models/job.model';

@Injectable({
  providedIn: 'root'
})
export class JobService {
  private uid: string;

  constructor(private db: AngularFirestore, private authService: AuthService) {
    authService.user.subscribe(u => this.uid = u.uid);
  }

  getJobs(limit: number, startAfterJob?: Job): Observable<Job[]> {
    const userRef = this.db.collection('users').doc(this.uid);

    let query = this.db
      .collection('users')
      .doc(uid)
      .collection('jobs')
      .ref.orderBy('createdAt', 'desc');

    if (startAfterJob) {
      query = query.startAfter(startAfterJob);
    }
    query = query.limit(limit);

    return from(
      query.get().then(querySnapshot => {
        return querySnapshot.docs.map(documentSnapshot => {
          return documentSnapshot.data() as Job;
        });
      })
    );
  }
}
```
