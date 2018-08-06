---
title: "Message thread"
date: 2018-08-06T9:52:24.495+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Service

Create a new method **UserService.getMessageThread**

```typescript
getMessageThread(fromUserId: number, toUserId: number) {
  return this.http.get<Message[]>(this.baseUrl + fromUserId + '/messages/thread/' + toUserId);
}
```

## Component

Create a new component **member/member-messages** and add it to the register section off **app.module.ts**

```typescript
export class MemberMessagesComponent implements OnInit {
  @Input() recipientId: number;
  messages: Message[];

  constructor(private userService: UserService, private authService: AuthService, private alertify: AlertifyService) {}

  ngOnInit() {
    this.loadMessages();
  }

  loadMessages() {
    this.userService
      .getMessageThread(this.authService.getUserId(), this.recipientId)
      .subscribe(next => (this.messages = next), error => this.alertify.error(error));
  }
}
```

Add some simple rendering so that we can test the component

```html
<p *ngFor="let message of messages">
  {{message.content}}
</p>
```

Use the new component in **member-detail.component.html**

```html
<tab heading="Messages">
  <app-member-messages [recipientId]="user.id"></app-member-messages>
</tab>
```

Test the application.

## Template

Remove the test code in **member-messages.component.html** and add the new template

```html
<div class="card">
  <div class="card-body">
    <div *ngIf="messages?.length === 0">
      <p>No messages yet... say hi using the message box below</p>
    </div>

    <ul class="chat">
      <li *ngFor="let message of messages">
        <!-- to them -->
        <div *ngIf="message.senderId == recipientId">
          <span class="chat-img float-left">
            <img src="{{message.senderPhotoUrl}}" alt="{{message.senderKnownAs}}" class="rounded-circle">
          </span>
          <div class="chat-body">
            <div class="header">
              <strong class="primary-font">{{message.senderKnownAs}}</strong>
              <small class="text-muted float-right">
                <span class="fa fa-clock">{{message.sentDate | timeAgo}}</span>
              </small>
            </div>
          </div>
          <p>{{message.content}}</p>
        </div>

        <!-- to me -->
        <div *ngIf="message.senderId != recipientId">
            <span class="chat-img float-right">
              <img src="{{message.senderPhotoUrl}}" alt="{{message.senderKnownAs}}" class="rounded-circle">
            </span>
            <div class="chat-body">
              <div class="header">
                <small class="text-muted">
                  <span class="fa fa-clock">{{message.sentDate | timeAgo}}</span>
                  <span *ngIf="!message.isRead" class="text-danger">(unread)</span>
                  <span *ngIf="message.isRead" class="text-success">(read {{message.readDate | timeAgo}})</span>
                </small>
                <strong class="primary-font float-right">{{message.senderKnownAs}}</strong>
              </div>
            </div>
            <p>{{message.content}}</p>
          </div>
      </li>
    </ul>
  </div>
  <div class="card-footer">
    <form action="">
      <div class="input-group">
        <input type="text" class="form-control input-sm" placeholder="send a private message">
        <div class="input-group-append">
          <button class="btn btn-primary">Send</button>
        </div>
      </div>
    </form>
  </div>
</div>
```

```css
.card {
  border: none;
}

.chat {
  list-style: none;
  margin: 0;
  padding: 0;
}

.chat li {
  margin-bottom: 10px;
  padding-bottom: 10px;
  border-bottom: 1px dotted #b3a9a9;
}

.rounded-circle {
  height: 50px;
  width: 50px;
}

.card-body {
  overflow-y: scroll;
  height: 400px;
}
```

## Tab selection

Feature: when the user clicks the message button in the profile component the tab messagesd is activated.

Valor-soft [example](https://valor-software.com/ngx-bootstrap/#/tabs#tabs-manual-select).

Open **members-detail.component.html** and add a template reference to the *tabset*

```html
<tabset class="member-tabset" #memberTabs>
```

Add a *ViewChild* variable in **members-detail.component.ts** and a method to activate a tab by id

```typescript
@ViewChild('memberTabs') memberTabs: TabsetComponent;
// ...
selectTab(tabId: number) {
  if (tabId >= 0 && tabId < this.memberTabs.tabs.length) {
    this.memberTabs.tabs[tabId].active = true;
  }
}
```

Back in the html change the *Message* button code to open the *Messages* tab

```html
<button class="btn btn-success w-100" (click)="selectTab(3)">Message</button>
```

## Query parameters

Feature: when the user clicks a message in his inbox the profile component is opened and the tab messagesd is activated.

Open **messages.component.html** and add a query string parameter to the *routerLink* of each message

```html
<tr *ngFor="let message of messages" [routerLink]="['/members',
        messageContainer == 'Outbox' ? message.recipientId : message.senderId]" [queryParams]="{tab: 3}">
```

Back in **members-detail.component.ts** subscribe to the query parameter from the route in **ngOnInit**

```typescript
this.route.queryParams.subscribe(params => {
  const tabId = Number(params['tab'] || 0);

  if (!isNaN(tabId)) {
    this.selectTab(tabId);
  }
});
```

Add the *queryParams* and *routerLink* in **member-card.component.html**

```html
<li class="list-inline-item">
  <button class="btn btn-primary btn-responsive" [routerLink]="['/members/', user.id]" [queryParams]="{tab: 3}">
    <i class="fa fa-envelope"></i>
  </button>
</li>
```
