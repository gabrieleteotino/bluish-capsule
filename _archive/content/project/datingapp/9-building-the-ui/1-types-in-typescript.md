---
title: "Types in Typescript"
date: 2018-06-19T19:52:17+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "jwt"]
categories: ["dev"]
draft: false
---

<!--more-->

## User interface

Create a folder for our models **/app/_models** then right click and select *Generate Interface* and call it **User**.

```typescript
export interface User {
  id: number;
  username: string;
  gender: string;
  age: string;
  knownAs: string;
  created: Date;
  lastActive: Date;
  introduction?: string;
  lookingFor?: string;
  interests?: string;
  city?: string;
  country?: string;
  profilePhotoUrl: string;
  photos?: Photo[];
}
```

Generate a new interface for **Photo**.

```typescript
export interface Photo {
    id: number;
    url: string;
    description: string;
    dateAdded: Date;
    isMain: boolean;
}
```

## JWT headers

To automatically send *JWT* headers for each request configure *angular-jwt*.

Open **app.module.ts** and add the configuration just below the *HttpClientModule*.

```typescript
...
HttpClientModule,
JwtModule.forRoot({
  config: {
    tokenGetter: () => {
      return localStorage.getItem('token');
    },
    whitelistedDomains: ['localhost:5001'],
    blacklistedRoutes: ['localhost:5001/api/auth/']
  }
}),
...
```

This will add the token to every request except for those sent to */api/auth*.

## Base API url

Open **src/environments.ts** and add the base address for the API.

```typescript
export const environment = {
  production: false,
  apiUrl: 'https://localhost:5001/api/'
};
```

## User service

Right click on **_service** and select *Generate Service* and name it **user**. The *JWT* token will automatically be added to the headers.

```typescript
export class UserService {
  baseUrl = environment.apiUrl;

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.baseUrl + 'users');
  }
}
```

Open **app.module.ts** and add the **UserService** to *providers*.

```typescript
providers: [
  AuthService,
  AlertifyService,
  UserService,
  AuthGuard
],
```

## Member list

Open **member-list.component.ts** and add DI for **UserService** and **AlertifyService**, then add a property to store the users and a function **loadUseres**;

```typescript
export class MemberListComponent implements OnInit {
  users: User[];

  constructor(
    private userService: UserService,
    private alertify: AlertifyService
  ) {}

  ngOnInit() {
    this.loadUsers();
  }

  loadUsers() {
    this.userService.getUsers().subscribe(
      users => {
        this.users = users;
      },
      error => {
        this.alertify.error(error);
      }
    );
  }
}
```

Let's add some html to our component.

```html
<div class="container">
  <div class="row">
    <div class="col-lg-2 col-md-3 col-sm-6">
      <p *ngFor="let user of users">{{ user.knownAs }}</p>
    </div>
  </div>
</div>
```

## Test

Open the application and click *Members* (login is necessary): we should see a list of our users.
