# Database Tables

This documentation describes all database tables and their purposes. You can read this documentation to get an overall knowledge of the database tables that comes from different modules.

## [Chat Module](chat.md)

### ChatUsers

ChatUsers Table is used to store chat users.

#### Description

This table stores the users of the Chat feature in the application. Each record in the table represents a user and allows to manage and track the users effectively. This table can be used to store information about Chat users, including the user id, name, email, and other relevant information.

---

### ChatConversations

ChatConversations Table is used to store chat conversations.

#### Description

This table stores information about the chat conversations. Each record in the table represents a chat conversation and allows to manage and track the chat conversations effectively. This table is important for managing and tracking the chat conversations of the application.

---

### ChatMessages

ChatMessages Table is used to store chat messages.

#### Description

This table stores the Chat messages in the application. Each record in the table represents a Chat message and allows to manage and track the messages effectively. This table can be used to store information about Chat messages, including the message id, text, sender, creation date and other relevant information. It can also be used to filter and search for messages, as well as to track the metrics associated with the messages, such as views and response time.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [ChatUserMessages](#chatusermessages) | ChatMessageId | To match the message. |

---

### ChatUserMessages

ChatUserMessages Table is used to store chat user messages.

#### Description

This table stores the Chat user messages in the application. Each record in the table represents a Chat user message and allows to manage and track the messages effectively. This table can be used to store information about Chat user messages, including the message id, sender, receiver and other relevant information. It can also be used to filter and search for messages, as well as to track the metrics associated with the messages, such as views and response time.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [ChatMessages](#chatmessages) | Id | To match the message. |

---

## [Cms Kit Module](Cms-Kit/index.md)

### CmsNewsletterPreferences

CmsNewsletterPreferences Table is used to store newsletter preferences.

#### Description

This table stores information about the newsletter preferences. Each record in the table represents a newsletter preference and allows to manage and track the newsletter preferences effectively. This table is important for managing and tracking the newsletter preferences of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [CmsNewsletterRecords](#cmsnewsletterrecords) | Id | To match the newsletter preference with the newsletter. |

---

### CmsNewsletterRecords

CmsNewsletterRecords Table is used to store newsletter records.

#### Description

This table stores information about the newsletter records. Each record in the table represents a newsletter record and allows to manage and track the newsletter records effectively. This table is important for managing and tracking the newsletter records of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [CmsNewsletterPreferences](#cmsnewsletterpreferences) | NewsletterRecordId | To match the newsletter preference with the newsletter. |

---

### CmsPolls

CmsPolls Table is used to store polls.

#### Description

This table stores information about the polls. Each record in the table represents a poll and allows to manage and track the polls effectively. This table is important for managing and tracking the polls of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [CmsPollOptions](#cmspolloptions) | PollId | To match the poll option with the poll. |

---

### CmsPollOptions

CmsPollOptions Table is used to store poll options.

#### Description

This table stores information about the poll options. Each record in the table represents a poll option and allows to manage and track the poll options effectively. This table is important for managing and tracking the poll options of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [CmsPolls](#cmspolls) | Id | To match the poll option with the poll. |

---

### CmsPollUserVotes

CmsPollUserVotes Table is used to store poll user votes.

#### Description

This table stores information about the poll user votes. Each record in the table represents a poll user vote and allows to manage and track the poll user votes effectively. This table is important for managing and tracking the poll user votes of the application.

---

### CmsShortenedUrls

CmsShortenedUrls Table is used to store shortened urls.

#### Description

This table stores information about the shortened urls. Each record in the table represents a shortened url and allows to manage and track the shortened urls effectively. This table is important for managing and tracking the shortened urls of the application.

---

## [File Management Module](file-management.md)

### FmDirectoryDescriptors

FmDirectoryDescriptors Table is used to store directory descriptors.

#### Description

This table stores information about the directory descriptors. Each record in the table represents a directory descriptor and allows to manage and track the directory descriptors effectively. This table is important for managing and tracking the directory descriptors of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [FmDirectories](#fmdirectorydescriptors) | Id | To match the directory descriptor with the parent directory. |

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [FmFileDescriptors](#fmfiledescriptors) | DirectoryDescriptorId | To match the file descriptor with the directory descriptor. |

---

### FmFileDescriptors

FmFileDescriptors Table is used to store file descriptors.

#### Description

This table stores information about the file descriptors. Each record in the table represents a file descriptor and allows to manage and track the file descriptors effectively. This table is important for managing and tracking the file descriptors of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [FmDirectoryDescriptors](#fmdirectorydescriptors) | Id | To match the file descriptor with the parent directory descriptor. |

---

## [Form Module](forms.md)

### FrmForms

FrmForms Table is used to store forms.

#### Description

This table stores information about the forms. Each record in the table represents a form and allows to manage and track the forms effectively. This table is important for managing and tracking the forms of the application.

---

### FrmQuestions

FrmQuestions Table is used to store form questions.

#### Description

This table stores information about the form questions. Each record in the table represents a form question and allows to manage and track the form questions effectively. This table is important for managing and tracking the form questions of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [FrmChoices](#frmchoices) | ChoosableQuestionId | To match the form choice with the form question. |

---

### FrmChoices

FrmChoices Table is used to store form choices.

#### Description

This table stores information about the form choices. Each record in the table represents a form choice and allows to manage and track the form choices effectively. This table is important for managing and tracking the form choices of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [FrmQuestions](#frmquestions) | Id | To match the form choice with the form question. |

---

### FrmFormResponses

FrmFormResponsesm Table is used to store form responses.

#### Description

This table stores information about the form responses. Each record in the table represents a form response and allows to manage and track the form responses effectively. This table is important for managing and tracking the form responses of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [FrmAnswers](#frmanswers) | FormResponseId | To match the form answer with the form response. |

---

### FrmAnswers

FrmAnswers Table is used to store form answers.

#### Description

This table stores information about the form answers. Each record in the table represents an form answer and allows to manage and track the form answers effectively. This table is important for managing and tracking the form answers of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [FrmFormResponses](#frmformresponses) | Id | To match the form answer with the form response. |

---

## [Gdpr Module](gdpr.md)

### GdprRequests

GdprRequests Table is used to store GDPR requests.

#### Description

This table stores information about the GDPR requests. Each record in the table represents a GDPR request and allows to manage and track the GDPR requests effectively. This table is important for managing and tracking the GDPR requests of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [GdprInfo](#gdprinfo) | RequestId | To match the GDPR information with the GDPR request. |

---

### GdprInfo

GdprInfo Table is used to store GDPR information.

#### Description

This table stores information about the GDPR information. Each record in the table represents a GDPR information and allows to manage and track the GDPR information effectively. This table is important for managing and tracking the GDPR information of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [GdprRequests](#gdprrequests) | Id | To match the GDPR information with the GDPR request. |

---

## [Language Management Module](language-management.md)

### AbpLanguages

AbpLanguages is a table to store languages.

#### Description

This table stores information about the languages supported in the application. Each record in the table represents a language and allows to manage and track the languages effectively. This table is important for supporting multiple languages in the application and for providing a better user experience by allowing users to switch between different languages.

---

### AbpLanguageTexts

AbpLanguageTexts is a table that stores the localization texts.

#### Description

This table stores the localized text for different languages in the application. Each record in the table represents a localized text and allows to manage and track the localized texts effectively. This table is important for providing a better user experience by allowing the application to display text in the user's preferred language.

---

## [Payment Module](payment.md)

### PayPaymentRequests

PayPaymentRequests Table is used to store payment requests.

#### Description

This table stores information about the payment requests. Each record in the table represents a payment request and allows to manage and track the payment requests effectively. This table is important for managing and tracking the payment requests of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [PayPaymentRequestProducts](#paypaymentrequestproducts) | PaymentRequestId | To match the payment request product with the payment request. |

---

### PayPaymentRequestProducts

PayPaymentRequestProducts Table is used to store payment request products.

#### Description

This table stores information about the payment request products. Each record in the table represents a payment request product and allows to manage and track the payment request products effectively. This table is important for managing and tracking the payment request products of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [PayPaymentRequests](#paypaymentrequests) | Id | To match the payment request product with the payment request. |

---

### PayPlans

PayPlans Table is used to store payment plans.

#### Description

This table stores information about the payment plans. Each record in the table represents a payment plan and allows to manage and track the payment plans effectively. This table is important for managing and tracking the payment plans of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [PayGatewayPlans](#paygatewayplans) | PlanId | To match the payment gateway plan with the plan. |

---

### PayGatewayPlans

PayGatewayPlans Table is used to store payment gateway plans.

#### Description

This table stores information about the payment gateway plans. Each record in the table represents a payment gateway plan and allows to manage and track the payment gateway plans effectively. This table is important for managing and tracking the payment gateway plans of the application.

#### Uses

| Table | Column | Description |
| --- | --- | --- |
| [PayPlans](#payplans) | Id | To match the payment gateway plan with the plan. |

---

## [SaaS Module](saas.md)

### SaasEditions

SaasEditions Table is used to store editions.

#### Description

This table stores information about the editions. Each record in the table represents an edition and allows to manage and track the editions effectively. This table is important for managing and tracking the editions of the application.

---

### SaasTenants

SaasTenants Table is used to store tenants.

#### Description

This table stores information about the tenants. Each record in the table represents a tenant and allows to manage and track the tenants effectively. This table is important for managing and tracking the tenants of the application.

#### Used By

| Table | Column | Description |
| --- | --- | --- |
| [SaasTenantConnectionStrings](#saastenantconnectionstrings) | TenantId | To match the tenant connection string with the tenant. |

---

### SaasTenantConnectionStrings

SaasTenantConnectionStrings Table is used to store tenant connection strings.

#### Description

This table stores information about the tenant connection strings. Each record in the table represents a tenant connection string and allows to manage and track the tenant connection strings effectively. This table is important for managing and tracking the tenant connection strings of the application.

## Uses

| Table | Column | Description |
| --- | --- | --- |
| [SaasTenants](#saastenants) | Id | To match the tenant connection string with the tenant. |

---

## [Text Template Management Module](text-template-management.md)

### AbpTextTemplateContents

The AbpTextTemplateContents table is used to store the content of the text templates.

#### Description

This table stores the text template contents for the application. Each record in the table represents a text template content and allows to manage and track the text template contents effectively. This table can be used to store reusable text templates that can be easily referenced and rendered by the application at runtime.