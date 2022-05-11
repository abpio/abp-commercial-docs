# Poll System

CMS kit provides a **poll** system to allow users to create, edit and delete polls. Here is a screenshot of the poll widget:

![cmskit-module-poll-widget](../../images/cmskit-module-poll-widget.png)

## User Interface

### Menu Items

**Polls**: Opens the poll management page.

### Pages

#### Polls

You can list, create, update and delete on the admin side of your solution.

![poll-page](../../images/cmskit-module-poll-page.png)
![poll-edit-question-page](../../images/cmskit-module-poll-edit-question-page.png)
![poll-edit-options-page](../../images/cmskit-module-poll-edit-options-page.png)

## The Poll Widget

The poll system provides a poll [widget](https://docs.abp.io/en/abp/latest/UI/AspNetCore/Widgets) users to vote and show the result.
You can simply place the widget on a page like below: 

```csharp
@await Component.InvokeAsync(
    typeof(PollViewComponent),
    new
    {
        widgetName = "poll"
    })
```

## Options

Before using the poll system, you need to define the widgets. You can use the `CmsKitPollingOptions`. `CmsKitPollingOptions` can be configured in the domain layer, in the `ConfigureServices` method of your [module](https://docs.abp.io/en/abp/latest/Module-Development-Basics).

**Example:**

```csharp
Configure<CmsKitPollingOptions>(options =>
    {
        options.AddWidget("poll");
    });
```

`CmsKitPollingOptions` properties:

- `WidgetNames`: List of defined widgets in the poll system.

# Internals

## Domain Layer

#### Aggregates

This module follows the [Entity Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Entities) guide.

##### Poll

A poll represents a created poll with its options. 

- `Poll` (aggregate root): Represents a poll by including the options in the system.
- `PollOption` (entity): Represents the defined poll options related to the poll in the system.

##### PollUserVote

A poll user vote represents voted poll from a user.
- `PollUserVote` (aggregate root): Represents poll user votes in the system.

#### Repositories

This module follows the [Repository Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Repositories) guide.

Following custom repositories are defined for these features:

- `IPollRepository`
- `IPollUserVoteRepository`

#### Domain services

This module follows the [Domain Services Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Domain-Services) guide.

##### Poll Manager

`PollManager` is used to perform some operations for the `Poll` aggregate root.

### Application layer

#### Application services

- `PollAdminAppService` (implements `IPollAdminAppService`): Implements the use cases of poll management for admin side.
- `PollPublicAppService` (implements `IPollPublicAppService`): Implements the use cases of polls for public websites.

### Database providers

#### Common

##### Table / collection prefix & schema

All tables/collections use the `Cms` prefix by default. Set static properties on the `CmsKitDbProperties` class if you need to change the table prefix or set a schema name (if supported by your database provider).

##### Connection string

This module uses `CmsKit` for the connection string name. If you don't define a connection string with this name, it fallbacks to the `Default` connection string.

See the [connection strings](https://docs.abp.io/en/abp/latest/Connection-Strings) documentation for details.

#### Entity Framework Core

##### Tables

- CmsPolls
  - CmsPollOptions
- CmsPollUserVotes

#### MongoDB

##### Collections

- **CmsPolls**
- **CmsPollUserVotes**
