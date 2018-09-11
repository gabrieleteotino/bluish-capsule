---
title: "Send message"
date: 2018-08-06T12:26:03.782+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: false
---

<!--more-->

## Service

Open **user.service.ts** and add a method **sendMessage**

```typescript
sendMessage(id: number, message: Message) {
  return this.http.post(this.baseUrl + id + '/messages', message);
}
```

## Component

Open **member-messages.component.ts** and add a new method **sendMessage**

```typescript
newMessage: any = {};

sendMessage() {
  this.newMessage.recipientId = this.recipientId;
  this.userService.sendMessage(this.authService.getUserId(), this.newMessage).subscribe(
    (message: Message) => {
      this.messages.unshift(message);
      // reset the form
      this.newMessage.content = '';
    },
    error => this.alertify.error(error)
  );
}
```

And in **member-messages.component.html**

```html
<form #messageForm="ngForm" (ngSubmit)="messageForm.valid && sendMessage()">
  <div class="input-group">
    <input type="text" name="content" [(ngModel)]="newMessage.content" required class="form-control input-sm" placeholder="send a private message">
    <div class="input-group-append">
      <button class="btn btn-primary" [disabled]="!messageForm.valid">Send</button>
    </div>
  </div>
</form>
```

## Fix the webapi

In **MessagesController.CreateMessage** we need to change the return value to the full *message*

```csharp
if (await _repo.SaveAll())
{
    // Load the sender detail so that automapper can use it
    var sender = await _repo.GetUser(userId);
    var messageToReturn = _mapper.Map<MessageForList>(message);
    return CreatedAtRoute("GetMessage", new { id = message.Id }, messageToReturn);
}
```
