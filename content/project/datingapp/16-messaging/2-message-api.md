---
title: "Message api"
date: 2018-08-02T13:57:37.190+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## GetMessage and CreateMessage

Add a new signature in **IDatingRepository**

```csharp
Task<Message> GetMessage(int id);
```

Implement in **DatingRepository**

```csharp
public async Task<Message> GetMessage(int id)
{
    return await _context.Messages.FirstOrDefaultAsync(x => x.Id == id);
}
```

## Controller

Create **MessagesController**

```csharp
namespace DatingApp.API.Controllers
{
    [ServiceFilter(typeof(Filters.LogUserActivity))]
    [Authorize]
    [Route("api/users/{userId}/[controller]")]
    [ApiController]
    public class MessagesController : ControllerBase
    {

        private readonly IDatingRepository _repo;
        private readonly IMapper _mapper;

        public MessagesController(IDatingRepository repo, IMapper mapper)
        {
            _repo = repo;
            _mapper = mapper;
        }

        [HttpGet("{id}", Name="GetMessage")]
        public async Task<IActionResult> GetMessage(int userId, int id) {
            if (userId != int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value))
                return Unauthorized();

            var messageFromRepo = await _repo.GetMessage(id);

            if(messageFromRepo == null)
                return NotFound();

            if(messageFromRepo.SenderId != userId && messageFromRepo.RecipientId != userId)
                return Unauthorized();

            return Ok(messageFromRepo);
        }
    }
}
```

Add a new DTO **MessageForCreation**

```csharp
public class MessageForCreation
{
    public int RecipientId { get; set; }
    public string Content { get; set; }
}
```

And a similar DTO **MessageForCreated**

```csharp
public class MessageForCreated
{
    public int Id { get; set; }
    public int SenderId { get; set; }
    public int RecipientId { get; set; }
    public string Content { get; set; }
    public DateTime SentDate { get; set; }
}
```

Create mappings in **AutoMapperProfiles**

```csharp
CreateMap<Message, MessageForCreated>();
CreateMap<MessageForCreation, Message>();
```

Use it in **MessagesController** for the **CreateMessage** method

```csharp
[HttpPost]
public async Task<IActionResult> CreateMessage(int userId, MessageForCreation messageForCreation)
{
    if (userId != int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value))
    {
        return Unauthorized();
    }

    var recipient = await _repo.GetUser(messageForCreation.RecipientId);

    if (recipient == null)
    {
        return NotFound();
    }

    var message = _mapper.Map<Message>(messageForCreation);
    message.SentDate = DateTime.UtcNow;
    message.SenderId = userId;

    _repo.Add(message);

    if (await _repo.SaveAll())
    {
        var messageToReturn = _mapper.Map<MessageForCreated>(message);
        return CreatedAtRoute("GetMessage", new { id = message.Id }, messageToReturn);
    }

    throw new Exception("Creation failed on save");
}
```

## Test with Postman

Create a new *POST* with body of type *application/JSON*

```
{{url}}/api/users/1/messages/

{
	"recipientId": 13,
	"content": "Message from Alicia to Apophis"
}
```

## GetMessagesForUser

Create a new DTO *MessageParams*

```csharp
public class MessageParams
{
    private const int MaxPageSize = 50;
    public int PageNumber { get; set; } = 1;

    private int pageSize = 10;
    public int PageSize
    {
        get { return pageSize; }
        set { pageSize = (value > MaxPageSize) ? MaxPageSize : value; }
    }
    public int UserId { get; set; }

    public string MessageContainer { get; set; } = "Unread";
}
```

Add a new signature in **IDatingRepository**

```csharp
Task<PagedList<Message>> GetMessagesForUser(MessageParams messageParams);
```

Implement in **DatingRepository**

```csharp
public async Task<PagedList<Message>> GetMessagesForUser(MessageParams messageParams)
{
    var messages = _context.Messages
        .Include(m => m.Sender).ThenInclude(u => u.Photos)
        .Include(m => m.Recipient).ThenInclude(u => u.Photos)
        .AsQueryable();

    switch(messageParams.MessageContainer)
    {
        case "Inbox":
            messages = messages.Where(m => m.RecipientId == messageParams.UserId);
            break;
        case "Outbox":
            messages = messages.Where(m => m.SenderId == messageParams.UserId);
            break;
        default: //"Unread"
            messages = messages.Where(m => m.RecipientId == messageParams.UserId && !m.IsRead);
            break;
    }

    messages = messages.OrderByDescending(m=>m.SentDate);
    return await PagedList<Message>.CreateAsync(messages, messageParams.PageNumber, messageParams.PageSize);
}
```

Create a new DTO **MessageForList** very similar to the original **Message** class. Note that Automapper recognizes properties like **SenderKnownAs** as  **Sender.KnownAs** in the source object (good boy automapper, good boy!)

```csharp
public class MessageForList
{
    public int Id { get; set; }
    public int SenderId { get; set; }
    public string SenderKnownAs { get; set; }
    public string SenderPhotoUrl { get; set; }
    public int RecipientId { get; set; }
    public String RecipientKnownAs { get; set; }
    public String RecipientPhotoUrl  { get; set; }
    public string Content { get; set; }
    public DateTime SentDate { get; set; }
    public bool IsRead { get; set; }
    public DateTime? ReadDate { get; set; }
}
```

Create mappings in **AutoMapperProfiles**

```csharp
CreateMap<Message, MessageForList>();
```

Add a new method in **MessagesController**

```csharp
[HttpGet]
public async Task<IActionResult> GetMessagesForUser(int userId, [FromQuery]MessageParams messageParams)
{
    if (userId != int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value))
    {
        return Unauthorized();
    }

    messageParams.UserId = userId;

    var messagesFromRepo = await _repo.GetMessagesForUser(messageParams);
    var messages = _mapper.Map<IEnumerable<MessageForList>>(messagesFromRepo);

    Response.AddPagination(messagesFromRepo.CurrentPage, messagesFromRepo.PageSize,
        messagesFromRepo.TotalCount, messagesFromRepo.TotalPages);

    return Ok(messages);
}
```

## Test with Postman

Create a new *GET*

```
{{url}}/api/users/1/messages?messageContainer=Outbox
```

## Message thread

Add a new signature in **IDatingRepository**

```csharp
Task<IEnumerable<Message>> GetMessageThread(int userId, int otherUserId);
```

Implement in **DatingRepository**

```csharp
public async Task<IEnumerable<Message>> GetMessageThread(int userId, int otherUserId)
{
    var messages = _context.Messages
        .Include(m => m.Sender).ThenInclude(u => u.Photos)
        .Include(m => m.Recipient).ThenInclude(u => u.Photos)
        .Where(m =>
            (m.SenderId == userId && m.RecipientId == otherUserId)
            || (m.SenderId == otherUserId && m.RecipientId == userId))
        .OrderByDescending(m => m.SentDate);

    return await messages.ToListAsync();
}
```

Add a new method in **MessagesController**

```csharp
[HttpGet("thread/{otherUserId}")]
public async Task<IActionResult> GetMessageThread(int userId, int otherUserId)
{
    if (userId != int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value))
    {
        return Unauthorized();
    }

    var messagesFromRepo = await _repo.GetMessageThread(userId, otherUserId);
    var messages = _mapper.Map<IEnumerable<MessageForList>>(messagesFromRepo);

    return Ok(messages);
}
```

## Test with Postman

Create a new *GET*

```
{{url}}/api/users/1/messages/thread/13
```
