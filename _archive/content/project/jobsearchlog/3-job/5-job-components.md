---
title: "Job components"
date: 2018-09-25T11:13:24.403+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: false
---

<!--more-->

Jobs will be organized using three different routes: list, create and update.

The list will use a *Material CDK DataSource* to load data for a *mat-table*

The editing is split between three components: one *Presentational* component with a form and two *Container* components one for create and one for update. This pattern will simplify code for the components by keeping them simple and with limited responsability.

## Routes

Before we implement the code for job list let's setup the routes. Create a file **/src/app/routes.ts**

```typescript
export const ROUTES: Routes = [
  { path: 'home', component: HomeComponent },
  {
    path: '',
    runGuardsAndResolvers: 'always',
    canActivate: [AuthGuard],
    children: [
      {
        path: 'jobs',
        component: JobListComponent
      },
      {
        path: 'job/create',
        component: JobCreateComponent
      },
      {
        path: 'job/edit/:id',
        component: JobEditComponent,
        resolve: {
          job: JobResolver
        } /*,
        canDeactivate: [PreventUnsavedChangesGuard]*/
      }
    ]
  },
  { path: '**', redirectTo: 'home', pathMatch: 'full' }
];
```

Open **app.modulte.ts** and import and configure the router

```typescript
import { RouterModule } from '@angular/router';
import { ROUTES } from './routes';
...
   imports: [
      RouterModule.forRoot(ROUTES),
```

## Job list

Create a new component **JobList**. Inside the folder **app/job-list** add a new file **job-datasource.ts**

The datasource will provide data for the **mat-table** and (not yet implemented) give support for pagination and a loading message.

```typescript
export class JobsDataSource extends DataSource<Entity<Job>> {
  constructor(private jobService: JobService, private pageSize = 3) {
    super();
  }

  connect(collectionViewer: CollectionViewer): Observable<Entity<Job>[]> {
    console.log('Datasource connecting to data');
    return this.jobService
      .getJobs(this.pageSize)
      .pipe(
        tap(data =>
          console.log(`Datasource retrieved ${data.length} rows for data`)
        )
      );
  }

  disconnect(collectionViewer: CollectionViewer): void {}
}
```

Edit **job-list.component.html**

```html
<button mat-fab [routerLink]="['/job/create']">
  <mat-icon aria-label="Add a new job application">add</mat-icon>
</button>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">
  <ng-container matColumnDef="description">
    <th mat-header-cell *matHeaderCellDef> description </th>
    <td mat-cell *matCellDef="let element"> {{element.value.description}} </td>
  </ng-container>

  <ng-container matColumnDef="company">
    <th mat-header-cell *matHeaderCellDef> company </th>
    <td mat-cell *matCellDef="let element"> {{element.value.company}} </td>
  </ng-container>

  <ng-container matColumnDef="companyUrl">
    <th mat-header-cell *matHeaderCellDef> companyUrl </th>
    <td mat-cell *matCellDef="let element"> {{element.value.companyUrl}} </td>
  </ng-container>

  <ng-container matColumnDef="createdAt">
    <th mat-header-cell *matHeaderCellDef> createdAt </th>
    <td mat-cell *matCellDef="let element"> {{element.value.createdAt.toDate() | date:'medium'}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;" (click)="editRecord(row.id)"></tr>
</table>
```

Then edit **job-list.component.ts**

```typescript
...
export class JobListComponent implements OnInit {
  displayedColumns: string[] = [
    'description',
    'company',
    'companyUrl',
    'createdAt'
  ];
  dataSource: JobsDataSource = new JobsDataSource(this.jobService);

  constructor(private jobService: JobService, private router: Router) {}

  ngOnInit() {}

  editRecord(jobId) {
    this.router.navigate(['/job', 'edit', jobId]);
  }
}
```

## Job create and edit

Create a new component **JobForm** edit the html template (this is still very crude)

```html
<div [formGroup]="form">
  <div>
    <mat-form-field>
      <input matInput placeholder="Position description" formControlName="description">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Company" formControlName="company">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Agency" formControlName="agency">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Lead" formControlName="lead">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Company Url" formControlName="companyUrl">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Offer Url" formControlName="offerUrl">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Status" formControlName="status">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Location" formControlName="location">
    </mat-form-field>
  </div>
  <div>
    <mat-form-field>
      <input matInput placeholder="Contract" formControlName="contract">
    </mat-form-field>
  </div>
</div>
<div>
  <button class="mat-raised-button mat-primary" (click)="saveForm()" [disabled]="!form.valid">Save</button>
</div>
```

Edit **job-form.component.ts**

```typescript
import { Component, OnInit, Input, EventEmitter, Output } from '@angular/core';
import { FormGroup, FormBuilder, Validators } from '@angular/forms';
import { Job } from '../_models/job.model';

@Component({
  selector: 'app-job-form',
  templateUrl: './job-form.component.html',
  styleUrls: ['./job-form.component.css']
})
export class JobFormComponent implements OnInit {
  @Input() job: Job;
  @Output() save = new EventEmitter<Job>();
  form: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.buildForm(this.job);
  }

  private buildForm(job: Job) {
    this.form = this.fb.group({
      description: [job.description, Validators.required],
      company: [job.company, Validators.required],
      agency: [job.agency],
      lead: [job.lead],
      companyUrl: [job.companyUrl],
      offerUrl: [job.offerUrl],
      status: [job.status],
      location: [job.location],
      contract: [job.contract]
    });
  }

  saveForm() {
    if (this.form.invalid) {
      return;
    }
    this.save.emit(this.form.value);
  }
}
```

Note that the component doesn't know what is the document id, just the job data is passed in and out.

Create a new component **JobCreate**

```html
<h2>New job offer</h2>

<app-job-form [job]="job" (save)="saveJob($event)"></app-job-form>
```

```typescript
...
export class JobCreateComponent implements OnInit {
  job = <Job>{};

  constructor(private jobService: JobService) {}

  ngOnInit() {}

  saveJob(job: Job) {
    console.log(`Saving new entity ${JSON.stringify(job)}`);
    this.jobService.createJob(job);
  }
}
```

Here the job is a new entity, so this component also doesn't use the document id.

Create a new component **JobEdit**

```html
<h2>Edit job offer</h2>

<app-job-form [job]="jobEntity.value" (save)="saveJob($event)"></app-job-form>
```

```typescript
...
export class JobEditComponent implements OnInit {
  id: string;
  jobEntity: Entity<Job>;

  constructor(private jobService: JobService, private route: ActivatedRoute) {}

  ngOnInit() {
    this.route.data.subscribe(
      (data: { job: Entity<Job> }) => (this.jobEntity = data.job)
    );
  }

  saveJob(job: Job) {
    console.log(`Saving ${JSON.stringify(job)}`);
    this.jobEntity.value = job;
    this.jobService.updateJob(this.jobEntity);
  }
}
```
