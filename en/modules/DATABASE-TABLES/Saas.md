## SaasEditions

SaasEditions Table is used to store editions.

### Description

This table stores information about the editions. Each record in the table represents an edition and allows to manage and track the editions effectively. This table is important for managing and tracking the editions of the application.

### Module

[`Volo.Saas`](../saas.md)

---

## SaasTenants

SaasTenants Table is used to store tenants.

### Description

This table stores information about the tenants. Each record in the table represents a tenant and allows to manage and track the tenants effectively. This table is important for managing and tracking the tenants of the application.

### Module

[`Volo.Saas`](../saas.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`SaasTenantConnectionStrings`](#saastenantconnectionstrings) | TenantId | To match the tenant connection string with the tenant. |

---

## SaasTenantConnectionStrings

SaasTenantConnectionStrings Table is used to store tenant connection strings.

### Description

This table stores information about the tenant connection strings. Each record in the table represents a tenant connection string and allows to manage and track the tenant connection strings effectively. This table is important for managing and tracking the tenant connection strings of the application.

### Module

[`Volo.Saas`](../saas.md)

## Uses

| Table | Column | Description |
| --- | --- | --- |
| [`SaasTenants`](#saastenants) | Id | To match the tenant connection string with the tenant. |