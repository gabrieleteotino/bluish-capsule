---
title: "Mark message as read"
date: 2018-08-06T15:39:30.672+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Controller

Open **MessagesController** and add a new method

```csharp
HttpPost("{id}/read")]
public async Task<IActionResult> MarkMessageAsRead(int userId, int id)
{
    if (userId != int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value))
    {
        return Unauthorized();
    }

    var messageToRead = await _repo.GetMessage(id);
    if (messageToRead.RecipientId != userId)
    {
        return Unauthorized();
    }
    if (messageToRead.IsRead)
    {
        return BadRequest("Message is already marked as read");
    }

    messageToRead.IsRead = true;
    messageToRead.ReadDate = DateTime.UtcNow;

    if (await _repo.SaveAll())
    {
        return NoContent();
    }
    throw new Exception("Mark as read failed on save");
}
```

## Service

Open **user.service.ts**

```typescript
markMessageAsRead(userId: number, messageId: number) {
  return this.http.post(this.baseUrl + userId + '/messages/' + messageId + '/read', {}).subscribe();
}
```

## Component

In **member-message.component.ts** when we load the messages we mark all the received messages as read

```typescript
loadMessages() {
  const userID = +this.authService.getUserId();
  this.userService
    .getMessageThread(userID, this.recipientId)
    .pipe(
      tap(messages => {
        for (let i = 0; i < messages.length; i++) {
          const message = messages[i];
          if (message.recipientId === userID && !message.isRead) {
            this.userService.markMessageAsRead(userID, message.id);
          }
        }
      })
    )
    .subscribe(messages => (this.messages = messages), error => this.alertify.error(error));
}
```

Test the application.
