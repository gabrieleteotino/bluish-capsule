---
title: "Member edit"
date: 2018-06-29T23:19:43+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Component creation

Create a new component **member-edit** in the **members** folder. Add the new component in the *declarations* section of **app.module.ts**.

Open **routes.ts** and add a new route. A *resolver* will be added soon.

```typescript
{ path: 'member/edit', component: MemberEditComponent },
```

Open **nav.component.html** and add a *a-routerLink* in the *Edit profile* anchor.

```html
<li><a [routerLink]="['/member/edit']"><i class="fa fa-user"></i> Edit profile</a></li>
```

Now we can test if the component works.

## Resolver

Create **member-edit.resolver.ts**.

Before we implement the resolver we need to add a new method to **auth.service.ts**

```typescript
getUserId() {
  if (!this.decodedToken) {
    this.loadTokenFromLocalStorage();
  }

  // the token can still be null because we are not logged in
  return this.decodedToken ? this.decodedToken.nameid : '';
}
```

The implementation of the *resolver* is very similar to **member-detail.resolver.ts**

```typescript
@Injectable()
export class MemberEditResolver implements Resolve<User> {
  constructor(
    private userService: UserService,
    private router: Router,
    private alertify: AlertifyService,
    private authService: AuthService
  ) {}

  resolve(route: ActivatedRouteSnapshot): Observable<User> {
    return this.userService.getUser(this.authService.getUserId()).pipe(
      catchError(error => {
        this.alertify.error('Problem retrieving data');
        this.router.navigate(['/home']);
        return empty();
      })
    );
  }
}
```

Add the resolver to the **providers** section of **app.module.ts** and configure the edit route in **routes.ts**.

```typescript
{
  path: 'member/edit',
  component: MemberEditComponent,
  resolve: { user: MemberEditResolver }
},
```

## Implementing the component

Open **member-edit.component.ts** add a **user** property, inject an *ActivatedRoute* an load the user from the resolver.

```typescript
export class MemberEditComponent implements OnInit {
  user: User;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.route.data.subscribe(data => {
      this.user = data['user'];
    });
  }
}
```

Open **member-edit.component.html** and add a simple binding.

```html
<p>
  {{user.knownAs}}
</p>
```

Test the application.

## Template structure

The template for edit has the same basic structure of the **member-detail**. The info and the save button are not yet binded to the form events and the photo upload is just a placeholder.

**member-edit.component.html**

```html
<div class="container">
  <div class="row">
    <div class="col-sm-4">
      <h1>Your profile</h1>
    </div>
    <div class="col-sm-8">
      <div class="alert alert-info">
        <p>
          <strong>Information: </strong>You have made changes. Any unsaved changes will be lost!</p>
      </div>
    </div>
  </div>
  <div class="row">
    <div class="col-sm-4">
      <div class="panel panel-default">
        <img class="thumbnail profile-image" src="{{user.profilePhotoUrl}}" alt="{{user.knownAs}}">
        <div class="panel-body">
          <div>
            <strong>Location:</strong>
            <p>{{user.city}}, {{user.country}}</p>
          </div>
          <div>
            <strong>Age:</strong>
            <p>{{user.age}}</p>
          </div>
          <div>
            <strong>Last active:</strong>
            <p>{{user.lastActive}}</p>
          </div>
          <div>
            <strong>Member since:</strong>
            <p>{{user.created}}</p>
          </div>
          <div class="panel-footer">
            <button class="btn btn-success btn-block">Save changes</button>
          </div>
        </div>
      </div>
    </div>
    <div class="col-sm-8">
      <div class="tab-panel">
        <tabset class="member-tabset">
          <tab heading="Edit profile">
            <h4>Description</h4>
            <textarea name="introduction" class="form-control" rows="6" [(ngModel)]="user.introduction"></textarea>
            <h4>Looking for</h4>
            <textarea name="lookingFor" class="form-control" rows="6" [(ngModel)]="user.lookingFor"></textarea>
            <h4>Interests</h4>
            <textarea name="interests" class="form-control" rows="6" [(ngModel)]="user.interests"></textarea>
            <h4>Location details</h4>
            <div class="form-inline">
              <label for="city">City</label>
              <input type="text" class="form-control" name="city" [(ngModel)]="user.city">
              <label for="country">Country</label>
              <input type="text" class="form-control" name="country" [(ngModel)]="user.country">
            </div>
          </tab>
          <tab heading="Photos">
            Coming soon.
          </tab>
        </tabset>
      </div>
    </div>
  </div>
</div>
```

**member-edit.component.css**

```css
.profile-image {
  margin: 25px;
  width: 85%;
  height: 85%;
}

.panel-body {
  padding: 0 25px;
}

.panel-footer {
  padding: 10 15px;
  background-color: #fff;
  border-top: none;
}

.form-inline > * {
  margin-right: 5px;
}
```

## Form

Add a *form* with a template variable **editForm** and use the *dirty* property to disable the save button and hide the info. The button is outside of the form tag so we need to add an id to the form and add a reference to this id in the button.

```html
...
<div class="alert alert-info" [hidden]="!editForm.dirty">
...
<button [disabled]="!editForm.dirty" form="editForm" class="btn btn-success btn-block">Save changes</button>
...
<form #editForm="ngForm" id="editForm" (ngSubmit)="updateUser()">
...          
```

In **member-edit.component.ts** use *@ViewChild* to map **editForm** to a variable. Create the method **updateUser** and use the **editForm** to reset the state.

```typescript
...
@ViewChild('editForm') editForm: NgForm;
...
updateUser() {
  console.log(this.user);
  this.editForm.reset(this.user);
  this.alertify.success('Profile updated succesfully');
}
...
```
