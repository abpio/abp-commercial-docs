## GdprRequests

GdprRequests Table is used to store GDPR requests.

### Description

This table stores information about the GDPR requests. Each record in the table represents a GDPR request and allows to manage and track the GDPR requests effectively. This table is important for managing and tracking the GDPR requests of the application.

### Module

[`Volo.Gdpr`](../gdpr.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`GdprInfo`](#gdprinfo) | RequestId | To match the GDPR information with the GDPR request. |

---

## GdprInfo

GdprInfo Table is used to store GDPR information.

### Description

This table stores information about the GDPR information. Each record in the table represents a GDPR information and allows to manage and track the GDPR information effectively. This table is important for managing and tracking the GDPR information of the application.

### Module

[`Volo.Gdpr`](../gdpr.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [`GdprRequests`](#gdprrequests) | Id | To match the GDPR information with the GDPR request. |