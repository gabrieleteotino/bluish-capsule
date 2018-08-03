---
title: "Message component"
date: 2018-08-03T12:45:53.517+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Model

Create a new interface **_models/Message.ts**

```typescript

```

## Service

We will use the **UserService** and not create a new service for the messages. Add new method **getMessages**

```typescript
getMessages(
  userId: number, pageNumber?: number, itemsPerPage?: number, messageContainer?: string
): Observable<PaginatedResult<Message[]>> {
  let params = new HttpParams();
  if (pageNumber !== null && itemsPerPage !== null) {
    params = params.set('pageNumber', pageNumber.toString()).set('pageSize', itemsPerPage.toString());
  }
  if (messageContainer !== null) {
    params = params.set('messageContainer', messageContainer);
  }

  return this.http
    .get<Message[]>(this.baseUrl + userId + '/messages', {
      params: params,
      observe: 'response'
    })
    .pipe(
      map(response => {
        const paginatedResults = new PaginatedResult<Message[]>();
        paginatedResults.result = response.body;
        if (response.headers.get('Pagination') !== null) {
          paginatedResults.pagination = JSON.parse(response.headers.get('Pagination'));
        }
        return paginatedResults;
      })
    );
}
```

## Resolver

Create a new resolver **messages.resolver.ts**

```typescript
@Injectable()
export class MessagesResolver implements Resolve<PaginatedResult<Message[]>> {
  pageNumber = 1;
  pageSize = 5;
  messageContainer = 'Unread';

  constructor(
    private userService: UserService,
    private authService: AuthService,
    private router: Router,
    private alertify: AlertifyService
  ) {}

  resolve(route: ActivatedRouteSnapshot): Observable<PaginatedResult<Message[]>> {
    return this.userService.getMessages(this.authService.getUserId(), this.pageNumber, this.pageSize, this.messageContainer).pipe(
      catchError(error => {
        this.alertify.error('Problem retrieving messages');
        this.router.navigate(['/home']);
        return empty();
      })
    );
  }
}
```

Register the resolver in **app.module.ts** in the *providers*.

Add the resolver in **routes.ts**

```typescript
{
  path: 'messages',
  component: MessagesComponent,
  resolve: { messages: MessagesResolver }
}
```

## Component

Open **messages.component.ts**

```typescript
@Component({
  selector: 'app-messages',
  templateUrl: './messages.component.html',
  styleUrls: ['./messages.component.css']
})
export class MessagesComponent implements OnInit {
  messages: Message[];
  pagination: Pagination;
  messageContainer: 'Unread';

  constructor(
    private userService: UserService,
    private authService: AuthService,
    private route: ActivatedRoute,
    private alertify: AlertifyService
  ) {}

  ngOnInit() {
    this.route.data.subscribe(data => {
      const paginatedResult = <PaginatedResult<Message[]>>data['messages'];
      this.messages = paginatedResult.result;
      this.pagination = paginatedResult.pagination;
    });
  }

  loadMessages() {
    this.userService
      .getMessages(
        this.authService.getUserId(),
        this.pagination.currentPage,
        this.pagination.itemsPerPage,
        this.messageContainer
      )
      .subscribe(
        paginatedResult => {
          this.messages = paginatedResult.result;
          this.pagination = paginatedResult.pagination;
        },
        error => this.alertify.error(error)
      );
  }

  pageChanged(event: any): void {
    this.pagination.currentPage = event.page;
    this.loadMessages();
  }
}
```

## Template

Open **message.component.html**

```typescript
<div class="container mt-5">
  <div class="row">
    <div class="btn-group" btnRadioGroup [(ngModel)]="this.messageContainer">
      <label class="btn btn-primary" btnRadio="Unread" (click)="loadMessages()">
        <i class="fa fa-envelope"></i> Unread
      </label>
      <label class="btn btn-primary" btnRadio="Inbox" (click)="loadMessages()">
        <i class="fa fa-envelope-open"></i> Inbox
      </label>
      <label class="btn btn-primary" btnRadio="Outbox" (click)="loadMessages()">
        <i class="fa fa-paper-plane"></i> Outbox
      </label>
    </div>
  </div>

  <div class="row" *ngIf="messages.length == 0">
    <h3>No messages</h3>
  </div>

  <div class="row" *ngIf="messages.length > 0">
    <table class="table table-hover" style="cursor: pointer">
      <tr>
        <th style="width: 40%">Message</th>
        <th style="width: 20%">From / To</th>
        <th style="width: 20%">Sent / Received</th>
        <th style="width: 20%"></th>
      </tr>
      <tr *ngFor="let message of messages" [routerLink]="['/members',
        messageContainer == 'Outbox' ? message.recipientId : message.senderId]">
        <td>{{message.content}}</td>
        <td>
          <div *ngIf="messageContainer != 'Outbox'">
            <img src={{message?.senderPhotoUrl}} class="img-circle rounded-circle mr-1">
            <strong>{{message.senderKnownAs}}</strong>
          </div>
          <div *ngIf="messageContainer == 'Outbox'">
            <img src={{message?.recipientPhotoUrl}} class="img-circle rounded-circle mr-1">
            <strong>{{message.recipientKnownAs}}</strong>
          </div>
        </td>
        <td>{{message.sentDate | timeAgo}}</td>
        <td>
          <button class="btn btn-danger">Delete</button>
        </td>
      </tr>
    </table>
  </div>
</div>

<div class="d-flex justify-content-center" *ngIf="messages.length > 0">
  <pagination [boundaryLinks]="true" [totalItems]="pagination.totalItems" [itemsPerPage]="pagination.itemsPerPage" [(ngModel)]="pagination.currentPage"
    (pageChanged)="pageChanged($event)" previousText="&lsaquo;" nextText="&rsaquo;" firstText="&laquo;" lastText="&raquo;">
  </pagination>
</div>
```

Add some style **message.component.css**

```css
table {
    margin-top: 15px;
}

.img-circle {
    max-height: 50px;
}

td {
    vertical-align: middle;
}
```
