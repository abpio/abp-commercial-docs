## ChatUsers

ChatUsers Table is used to store chat users.

### Description

This table stores the users of the Chat feature in the application. Each record in the table represents a user and allows to manage and track the users effectively. This table can be used to store information about Chat users, including the user id, name, email, and other relevant information.

### Module

[`Volo.Abp.Chat`](../chat.md)

---

## ChatConversations

ChatConversations Table is used to store chat conversations.

### Description

This table stores information about the chat conversations. Each record in the table represents a chat conversation and allows to manage and track the chat conversations effectively. This table is important for managing and tracking the chat conversations of the application.

### Module

[`Volo.Abp.Chat`](../chat.md)

---

## ChatMessages

ChatMessages Table is used to store chat messages.

### Description

This table stores the Chat messages in the application. Each record in the table represents a Chat message and allows to manage and track the messages effectively. This table can be used to store information about Chat messages, including the message id, text, sender, creation date and other relevant information. It can also be used to filter and search for messages, as well as to track the metrics associated with the messages, such as views and response time.

### Module

[`Volo.Abp.Chat`](../chat.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [ChatUserMessages](#chatusermessages) | ChatMessageId | To match the message. |

---

## ChatUserMessages

ChatUserMessages Table is used to store chat user messages.

### Description

This table stores the Chat user messages in the application. Each record in the table represents a Chat user message and allows to manage and track the messages effectively. This table can be used to store information about Chat user messages, including the message id, sender, receiver and other relevant information. It can also be used to filter and search for messages, as well as to track the metrics associated with the messages, such as views and response time.

### Module

[`Volo.Abp.Chat`](../chat.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [ChatMessages](#chatmessages) | Id | To match the message. |