---
title: "Resume service"
date: 2018-10-02T17:27:55.641+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: false
---

<!--more-->

## Read

Readings docs is pretty straightforward. Get the object from firestore, convert it to our model and encapsulate it in an **Entity** with the firestore id.

```typescript
getResume(resumeId: string): Observable<Entity<Resume>> {
  console.log('getResume called');

  return this.afs
    .doc(`/users/${this.uid}/resumes/${resumeId}`)
    .snapshotChanges()
    .pipe(
      map(action => {
        const resume = this.convertFromFirestore(action.payload.data());
        const id = action.payload.id;
        return new Entity<Resume>(id, resume);
      }),
      tap(next => console.log(`getResumes returned ${JSON.stringify(next)}`))
    );
}

getResumes(limit: number, startAfter?: Resume) {
  console.log('getResumes called');

  return this.afs
    .collection(`/users/${this.uid}/resumes`, queryFn => {
      let paginatedQuery = queryFn.orderBy('name').limit(limit);
      if (startAfter) {
        paginatedQuery = paginatedQuery.startAt(startAfter);
      }
      return paginatedQuery;
    })
    .snapshotChanges()
    .pipe(
      map(actions => {
        return actions.map(action => {
          const resume = this.convertFromFirestore(action.payload.doc.data());
          const id = action.payload.doc.id;
          return new Entity<Resume>(id, resume);
        });
      }),
      tap(next => console.log(`getResumes data length ${next.length}`))
    );
}
```

The conversion is necessary because Firestore changes the Dates to a Timestamp object.

```typescript
convertFromFirestore(rawResume: any): Resume {
  const versions = rawResume.versions.map(
    rawversion =>
      <ResumeVersion>{
        version: rawversion.version,
        // Convert Timestamp to Date
        createdAt: rawversion.createdAt.toDate(),
        archived: rawversion.archived,
        filename: rawversion.filename
      }
  );
  const resume: Resume = {
    name: rawResume.name,
    versions: versions
  };
  return resume;
}
```

## Creation

Creating a **Resume** is a two step process, first we create the firestore document, then we upload a File to Firebase Storage

```typescript
createResume(name: string, file: File): Observable<UploadTaskSnapshot> {
  const newVersionNumber = 1;
  const resume = <Resume>{
    name: name,
    versions: [
      <ResumeVersion>{
        version: newVersionNumber,
        createdAt: new Date(),
        filename: file.name,
        archived: false
      }
    ]
  };
  console.log({ resume });

  const uploadTask = this.afs
    .collection(`users/${this.uid}/resumes/`)
    .add(resume)
    .then(function(docRef) {
      console.log('Resource document written with ID: ', docRef.id);
      return new Entity<Resume>(docRef.id, resume);
    })
    .then(resumeEntity => {
      const path = this.storagePath(resumeEntity, newVersionNumber);
      const sorageRef = this.storage.ref(path);
      const task = sorageRef.put(file);

      return task;
    });
  return from(uploadTask);
}
```

## Update

Updating is divided in two part

- changing the **name** of the resume
- adding a new version by uploading a new file

Uploading a new file requires to update the document and then do the upload.

```typescript
updateResume(resumeId: string, name: string) {
  return this.afs
    .doc(`/users/${this.uid}/resumes/${resumeId}`)
    .update({ name: name });
}

addResumeVersion(resumeEntity: Entity<Resume>, file?: File) {
  const resume = resumeEntity.value;
  // Not sure about this, maybe the array can be reordered some way
  const newVersionNumber =
    resume.versions[resume.versions.length - 1].version + 1;
  resume.versions.push(<ResumeVersion>{
    version: newVersionNumber,
    createdAt: new Date(),
    filename: file.name,
    archived: false
  });

  const uploadTask = this.afs
    .doc(`/users/${this.uid}/resumes/${resumeEntity.id}`)
    .update(resume)
    .then(_ => {
      const path = this.storagePath(resumeEntity, newVersionNumber);
      const sorageRef = this.storage.ref(path);
      const task = sorageRef.put(file);
      return task;
    });
  return from(uploadTask);
}
```
