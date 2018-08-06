---
title: "Delete messages API"
date: 2018-08-06T14:38:45.555+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Controller

Open **MessagesController** and add a new *HTTP POST* method **DeleteMessage**. We are not using a *HTTP DELETE* because the messages are not really deleted but only marked as deleted for each of the two users.

```csharp
[HttpPost("{id}")]
public async Task<IActionResult> DeleteMessage(int userId, int id)
{
    if (userId != int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value))
    {
        return Unauthorized();
    }

    var messageToDelete = await _repo.GetMessage(id);

    if (messageToDelete.SenderId != userId && messageToDelete.RecipientId != userId)
    {
        return Unauthorized();
    }

    if (messageToDelete.SenderId == userId)
    {
        messageToDelete.SenderDeleted = true;
    }

    if (messageToDelete.RecipientId == userId)
    {
        messageToDelete.RecipientDeleted = true;
    }

    if(messageToDelete.SenderDeleted && messageToDelete.RecipientDeleted) {
        _repo.Delete(messageToDelete);
    }

    if(await _repo.SaveAll()) {
        return NoContent();
    }

    throw new Exception("Deletion failed on save");
}
```

## Repository

Update **DatingRepository** so that only the not deleted messages are loaded

```csharp
public async Task<PagedList<Message>> GetMessagesForUser(MessageParams messageParams)
{
    var messages = _context.Messages
        .Include(m => m.Sender).ThenInclude(u => u.Photos)
        .Include(m => m.Recipient).ThenInclude(u => u.Photos)
        .AsQueryable();

    switch (messageParams.MessageContainer)
    {
        case "Inbox":
            messages = messages.Where(m => m.RecipientId == messageParams.UserId && !m.RecipientDeleted);
            break;
        case "Outbox":
            messages = messages.Where(m => m.SenderId == messageParams.UserId && !m.SenderDeleted);
            break;
        default: //"Unread"
            messages = messages.Where(m => m.RecipientId == messageParams.UserId && !m.IsRead && !m.RecipientDeleted);
            break;
    }

    messages = messages.OrderByDescending(m => m.SentDate);
    return await PagedList<Message>.CreateAsync(messages, messageParams.PageNumber, messageParams.PageSize);
}

public async Task<IEnumerable<Message>> GetMessageThread(int userId, int otherUserId)
{
    var messages = _context.Messages
        .Include(m => m.Sender).ThenInclude(u => u.Photos)
        .Include(m => m.Recipient).ThenInclude(u => u.Photos)
        .Where(m =>
            (m.SenderId == userId && !m.SenderDeleted && m.RecipientId == otherUserId)
            || (m.SenderId == otherUserId && m.RecipientId == userId && !m.RecipientDeleted))
        .OrderByDescending(m => m.SentDate);

    return await messages.ToListAsync();
}
```
