---
title: "Message entity"
date: 2018-08-02T11:24:33.881+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "ef", "entity-framework"]
categories: ["dev"]
draft: false
---

<!--more-->

## Message

Create a new model class **Message.cs**

```csharp
public class Message
{
    public int Id { get; set; }
    public int SenderId { get; set; }
    public User Sender { get; set; }
    public int RecipientId { get; set; }
    public User Recipient { get; set; }
    public string Content { get; set; }
    [Required]
    public DateTime? SentDate { get; set; }
    public bool IsRead { get; set; }
    public DateTime? ReadDate { get; set; }
    public bool SenderDeleted { get; set; }
    public bool RecipientDeleted { get; set; }
}
```

Add the properties for sent and received to **User**

```csharp
public ICollection<Message> MessagesSent { get; set; }
public ICollection<Message> MessagesReceived { get; set; }
```

## Context, defaults and many to many

In **DatingContext** add a new *DbSet* and configure the relationships. Note that the foreign key can be ignored and *ef* will add it by convention (**Sender** has a **SenderId** and **Recipient** has a **RecipientId**)

```csharp
// ...
public DbSet<Message> Messages { get; set; }
// ...
protected override void OnModelCreating(ModelBuilder modelBuilder)
// ...
modelBuilder.Entity<Message>()
    .HasOne(m => m.Sender)
    .WithMany(u => u.MessagesSent)
    .OnDelete(DeleteBehavior.Restrict);

modelBuilder.Entity<Message>()
    .HasOne(m => m.Recipient)
    .WithMany(u => u.MessagesReceived)
    //.HasForeignKey(m => m.RecipientId)
    .OnDelete(DeleteBehavior.Restrict);
// ...
```

Stop *dotnet run*

Create a new migration

```shell
dotnet ef migrations add AddedMessages
```

Review the code of the migration and note how the *FK* and *IX* has been automatically discovered by *ef*.

Apply the canghes

```shell
dotnet ef database update
```

restart *dotnet run*
