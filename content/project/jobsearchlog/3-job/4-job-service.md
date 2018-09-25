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

Open **job.service.ts** and add a few methods

- **getJobs** get paginated results of **Job[]**
- **getJob** get a single job by id
- **createJob** create a new job a return the document id
- **updateJob** update an existing job


```typescript
getJobs(limit: number, startAfterJob?: Job): Observable<Entity<Job>[]> {
  console.log('getJobs called');

  return this.db
    .collection(`/users/${this.uid}/jobs`, collRef => {
      let query = collRef.orderBy('').limit(limit);
      if (startAfterJob) {
        query = query.startAt(startAfterJob);
      }
      return query;
    })
    .snapshotChanges()
    .pipe(
      map(actions => {
        return actions.map(action => {
          const data = action.payload.doc.data() as Job;
          const id = action.payload.doc.id;
          return new Entity<Job>(id, data);
        });
      }),
      tap(next => console.log(`getJobs data length ${next.length}`))
    );
}

getJob(jobId: string): Observable<Entity<Job>> {
  console.log(`getJob called for id ${jobId}`);

  return this.db
    .doc<Job>(`users/${this.uid}/jobs/${jobId}`)
    .snapshotChanges()
    .pipe(
      map(action => {
        const data = action.payload.data() as Job;
        const id = action.payload.id;
        return new Entity<Job>(id, data);
      }),
      tap(data => console.log(data))
    );
}

createJob(job: Job): Observable<string | void> {
  job.createdAt = new Date();
  job.updatedAt = job.createdAt;
  console.log(`Creating new job ${JSON.stringify(job)}`);

  const documentIdPromise = this.db
    .collection(`users/${this.uid}/jobs/`)
    .add(job)
    .then(function(docRef) {
      console.log('Job document written with ID: ', docRef.id);
      return docRef.id;
    })
    .catch(function(error) {
      console.error('Error adding job document: ', error);
    });
  return from(documentIdPromise);
}

updateJob(jobEntity: Entity<Job>) {
  jobEntity.value.updatedAt = new Date();
  console.log(`Updating job ${jobEntity.id}`);

  const jobRef = this.db
    .collection(`users/${this.uid}/jobs/`)
    .doc(jobEntity.id);

  return jobRef
    .update(jobEntity.value)
    .then(function() {
      console.log(`Document ${jobEntity.id} successfully updated`);
    })
    .catch(function(error) {
      // The document probably doesn't exist.
      console.error('Error updating document: ', error);
    });
}
```
