# Web Application Development Tutorial - Part 1: Creating the Server Side
````json
//[doc-params]
{
    "UI": ["MVC", "Blazor", "BlazorServer", "NG", "MAUIBlazor"],
    "DB": ["EF","Mongo"]
}
````
## About this tutorial

In this tutorial series, you will build an ABP Commercial application named `Acme.BookStore`. This application is used to manage a list of books and their authors. It is developed using the following technologies:

* **{{DB_Value}}** as the database provider. 
* **{{UI_Value}}** as the UI Framework.

> Instead of creating this application manually, you can automatically generate the same code using the [ABP Suite](../../abp-suite/index.md). However, it is recommended to follow this tutorial to learn the basics of an application development using the ABP Commercial.

This tutorial is organized as the following parts;

- **Part 1: Creating the server side (this part)**
- [Part 2: The book list page](part-2.md)
- [Part 3: Creating, updating and deleting books](part-3.md)
- [Part 4: Integration tests](part-4.md)
- [Part 5: Authorization](part-5.md)
- [Part 6: Authors: Domain layer](part-6.md)
- [Part 7: Authors: Database Integration](part-7.md)
- [Part 8: Authors: Application Layer](part-8.md)
- [Part 9: Authors: User Interface](part-9.md)
- [Part 10: Book to Author Relation](part-10.md)

### Download the Source Code

This tutorial has multiple versions based on your **UI** and **Database** preferences. We've prepared a few combinations of the source code to be downloaded:

* [MVC (Razor Pages) UI with EF Core](https://abp.io/Account/Login?returnUrl=/api/download/samples/bookstore-mvc-ef)
* [Blazor UI with EF Core](https://abp.io/Account/Login?returnUrl=/api/download/samples/bookstore-blazor-efcore)
* [Angular UI with MongoDB](https://abp.io/Account/Login?returnUrl=/api/download/samples/bookstore-angular-mongodb)

> If you encounter the "filename too long" or "unzip" error on Windows, please see [this guide](https://docs.abp.io/en/abp/7.0/KB/Windows-Path-Too-Long-Fix).

> After downloading the source code, you might need to run some commands before running the application. See the _After Creating the Solution_ section below for more information.

## Creating the Solution

Before starting to the development, create a new solution named `Acme.BookStore` and run it by following the [getting started tutorial](../../getting-started.md).

> **Note**: If you are considering following the [MAUI - Mobile Application Development tutorial](./mobile/maui.md)/[React Native - Mobile Application development tutorial](./mobile/react-native.md) or creating a mobile application, don't forget to specify the mobile option as described in the [getting started tutorial](../../getting-started.md).

## After Creating the Solution

### Installing the Client-Side Packages

[ABP CLI](https://docs.abp.io/en/abp/latest/CLI) runs the `abp install-libs` command behind the scenes to install the required NPM packages for your solution while creating the application. So, if you have created the application via ABP CLI or [ABP Suite](../../abp-suite/index.md), you don't need to run this command manually.

However, sometimes this command might need to be manually run. For example, you need to run this command, if you have cloned the application, or the resources from *node_modules* folder didn't copy to *wwwroot/libs* folder, or if you have added a new client-side package dependency to your solution.

For such cases, run the `abp install-libs` command on the root directory of your solution to install all required NPM packages:

```bash
abp install-libs
```

> We suggest you install [Yarn](https://classic.yarnpkg.com/) to prevent possible package inconsistencies, if you haven't installed it yet.

{{if UI=="Blazor" || UI=="BlazorServer"}}

### Bundling and Minification

`abp bundle` command offers bundling and minification support for client-side resources (JavaScript and CSS files) for Blazor projects. This command automatically run when you create a new solution with the [ABP CLI](https://docs.abp.io/en/abp/latest/CLI).

However, sometimes you might need to run this command manually. To update script & style references without worrying about dependencies, ordering, etc. in a project, you can run this command in the directory of your blazor application:

```bash
abp bundle
```

> For more details about managing style and script references in Blazor or MAUI Blazor apps, see [Managing Global Scripts & Styles](https://docs.abp.io/en/abp/latest/UI/Blazor/Global-Scripts-Styles).

{{end}}


## Create the Book Entity

**Domain layer** in the startup template is separated into two projects:

- `Acme.BookStore.Domain` contains your [entities](https://docs.abp.io/en/abp/latest/Entities), [domain services](https://docs.abp.io/en/abp/latest/Domain-Services) and other core domain objects.
- `Acme.BookStore.Domain.Shared` contains `constants`, `enums` or other domain related objects those can be shared with clients.

So, define your entities in the **domain layer** (`Acme.BookStore.Domain` project) of the solution. 

The main entity of the application is the `Book`. Create a `Books` folder (namespace) in the `Acme.BookStore.Domain` project and add a `Book` class inside it:

````csharp
using System;
using Volo.Abp.Domain.Entities.Auditing;

namespace Acme.BookStore.Books;

public class Book : AuditedAggregateRoot<Guid>
{
    public string Name { get; set; }

    public BookType Type { get; set; }

    public DateTime PublishDate { get; set; }

    public float Price { get; set; }
}
````

* ABP Framework has two fundamental base classes for entities: `AggregateRoot` and `Entity`. **Aggregate Root** is a [Domain Driven Design](https://docs.abp.io/en/abp/latest/Domain-Driven-Design) concept which can be thought as a root entity that is directly queried and worked on (see the [entities document](https://docs.abp.io/en/abp/latest/Entities) for more).
* `Book` entity inherits from the `AuditedAggregateRoot` which adds some base [auditing](https://docs.abp.io/en/abp/latest/Audit-Logging) properties (like `CreationTime`, `CreatorId`, `LastModificationTime`...) on top of the `AggregateRoot` class. ABP automatically manages these properties for you.
* `Guid` is the **primary key type** of the `Book` entity.

> This tutorial leaves the entity properties with **public get/set** for the sake of simplicity. See the [entities document](https://docs.abp.io/en/abp/latest/Entities) if you learn more about DDD best practices.

### BookType Enum

The `Book` entity uses the `BookType` enum. Create a `Books` folder and then create the `BookType` in the `Acme.BookStore.Domain.Shared` project:

````csharp
namespace Acme.BookStore.Books;

public enum BookType
{
    Undefined,
    Adventure,
    Biography,
    Dystopia,
    Fantastic,
    Horror,
    Science,
    ScienceFiction,
    Poetry
}
````

The final folder/file structure should be as shown below:

![bookstore-book-and-booktype](images/bookstore-book-and-booktype.png)

### Add book entity to the DbContext

{{if DB == "EF"}}

EF Core requires to relate entities with your `DbContext`. The easiest way to do this is to add a `DbSet` property to the `BookStoreDbContext` class in the `Acme.BookStore.EntityFrameworkCore` project, as shown below:

````csharp
//....
public class BookStoreDbContext : AbpDbContext<BookStoreDbContext> //....
{
    public DbSet<Book> Books { get; set; }

    //....
    //....
}
````

{{end}}

{{if DB == "Mongo"}}

Add a `IMongoCollection<Book> Books` property to the `BookStoreMongoDbContext` inside the `Acme.BookStore.MongoDB` project:

```csharp
using Acme.BookStore.Books;
//...

public class BookStoreMongoDbContext : AbpMongoDbContext
{
    public IMongoCollection<Book> Books => Collection<Book>();
    //...
}
```

{{end}}


{{if DB == "EF"}}

### Map the Book Entity to a Database Table

Locate to `OnModelCreating` method in the `BookStoreDbContext` class and add the mapping code for the `Book` entity:

````csharp
using Acme.BookStore.Books;
using Volo.Abp.EntityFrameworkCore.Modeling;
//...

namespace Acme.BookStore.EntityFrameworkCore;

[ReplaceDbContext(typeof(IIdentityProDbContext))]
[ReplaceDbContext(typeof(ISaasDbContext))]
[ConnectionStringName("Default")]
public class BookStoreDbContext :
    AbpDbContext<BookStoreDbContext>,
    IIdentityProDbContext,
    ISaasDbContext
{
    /* Add DbSet properties for your Aggregate Roots / Entities here. */
    public DbSet<Book> Books { get; set; }

    //...

    public BookStoreDbContext(DbContextOptions<BookStoreDbContext> options)
        : base(options)
    {

    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        /* Include modules to your migration db context */

        builder.ConfigurePermissionManagement();
        //...

        /* Configure your own tables/entities inside here */

        builder.Entity<Book>(b =>
        {
            b.ToTable(BookStoreConsts.DbTablePrefix + "Books",
                BookStoreConsts.DbSchema);
            b.ConfigureByConvention(); //auto configure for the base class props
            b.Property(x => x.Name).IsRequired().HasMaxLength(128);
        });
    }
}
````

* `BookStoreConsts` has constant values for schema and table prefixes for your tables. You don't have to use it, but suggested to control the table prefixes in a single point.
* `ConfigureByConvention()` method gracefully configures/maps the inherited properties. Always use it for all your entities.

### Add Database Migration

The startup solution is configured to use [Entity Framework Core Code First Migrations](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/). Since we've changed the database mapping configuration, we should create a new migration and apply changes to the database.

Open a command-line terminal in the directory of the `Acme.BookStore.EntityFrameworkCore` project and type the following command:

```bash
dotnet ef migrations add Created_Book_Entity
```

This will add a new migration class to the project:

![bookstore-efcore-migration](./images/bookstore-efcore-migration.png)

> If you are using Visual Studio, you may want to use the `Add-Migration Created_Book_Entity` and `Update-Database` commands in the *Package Manager Console (PMC)*. In this case, ensure that `Acme.BookStore.EntityFrameworkCore` is the startup project in Visual Studio and `Acme.BookStore.EntityFrameworkCore` is the *Default Project* in PMC.

{{end}}

### Add Sample Seed Data

> It's good to have some initial data in the database before running the application. This section introduces the [Data Seeding](https://docs.abp.io/en/abp/latest/Data-Seeding) system of the ABP framework. You can skip this section if you don't want to create seed data, but it is suggested to follow it to learn this useful ABP Framework feature.

Create a class deriving from the `IDataSeedContributor` in the `*.Domain` project by copying the following code:

```csharp
using System;
using System.Threading.Tasks;
using Acme.BookStore.Books;
using Volo.Abp.Data;
using Volo.Abp.DependencyInjection;
using Volo.Abp.Domain.Repositories;

namespace Acme.BookStore;

public class BookStoreDataSeederContributor
    : IDataSeedContributor, ITransientDependency
{
    private readonly IRepository<Book, Guid> _bookRepository;

    public BookStoreDataSeederContributor(IRepository<Book, Guid> bookRepository)
    {
        _bookRepository = bookRepository;
    }

    public async Task SeedAsync(DataSeedContext context)
    {
        if (await _bookRepository.GetCountAsync() > 0)
        {
            return;
        }

        await _bookRepository.InsertAsync(
            new Book
            {
                Name = "1984",
                Type = BookType.Dystopia,
                PublishDate = new DateTime(1949, 6, 8),
                Price = 19.84f
            },
            autoSave: true
        );

        await _bookRepository.InsertAsync(
            new Book
            {
                Name = "The Hitchhiker's Guide to the Galaxy",
                Type = BookType.ScienceFiction,
                PublishDate = new DateTime(1995, 9, 27),
                Price = 42.0f
            },
            autoSave: true
        );
    }
}
```

* This code simply uses the `IRepository<Book, Guid>` (the default [repository](https://docs.abp.io/en/abp/latest/Repositories)) to insert two books to the database, if there is no book currently in the database.

### Update the Database

Run the `Acme.BookStore.DbMigrator` application to update the database:

![bookstore-dbmigrator-on-solution](images/bookstore-dbmigrator-on-solution.png)

{{if DB == "EF"}}

`.DbMigrator` is a console application that can be run to **migrate the database schema** and **seed the data** on **development** and **production** environments.

{{end}}

{{if DB == "Mongo"}}

While MongoDB **doesn't require** a database schema migration, it is still good to run this application since it **seeds the initial data** on the database. This application can be used on **development** and **production** environments.

{{end}}

## Create the Application Service

* The application layer is separated into two projects:

  * `Acme.BookStore.Application.Contracts` contains your [DTO](https://docs.abp.io/en/abp/latest/Data-Transfer-Objects)s and [application service](https://docs.abp.io/en/abp/latest/Application-Services) interfaces.
  * `Acme.BookStore.Application` contains the implementations of your application services.

  In this section, you will create an application service to get, create, update and delete books using the `CrudAppService` base class of the ABP Framework.

### BookDto

`CrudAppService` base class requires to define the fundamental DTOs for the entity. Create a `Books` folder (namespace) in the `Acme.BookStore.Application.Contracts` project and add a `BookDto` class inside it:

````csharp
using System;
using Volo.Abp.Application.Dtos;

namespace Acme.BookStore.Books;

public class BookDto : AuditedEntityDto<Guid>
{
    public string Name { get; set; }

    public BookType Type { get; set; }

    public DateTime PublishDate { get; set; }

    public float Price { get; set; }
}
````

* **DTO** classes are used to **transfer data** between the *presentation layer* and the *application layer*. See the [Data Transfer Objects document](https://docs.abp.io/en/abp/latest/Data-Transfer-Objects) for more details.
* `BookDto` is used to transfer book data to the presentation layer in order to show the book information on the UI.
* `BookDto` is derived from the `AuditedEntityDto<Guid>` which has audit properties just like the `Book` class defined above.

It will be needed to map `Book` entities to `BookDto` objects while returning books to the presentation layer. [AutoMapper](https://automapper.org) library can automate this conversion when you define the proper mapping. The startup template comes with AutoMapper configured, so you can just define the mapping in the `BookStoreApplicationAutoMapperProfile` class in the `Acme.BookStore.Application` project:

````csharp
using Acme.BookStore.Books;
using AutoMapper;

namespace Acme.BookStore;

public class BookStoreApplicationAutoMapperProfile : Profile
{
    public BookStoreApplicationAutoMapperProfile()
    {
        CreateMap<Book, BookDto>();
    }
}
````

See the [object to object mapping](https://docs.abp.io/en/abp/latest/Object-To-Object-Mapping) document for details.

### CreateUpdateBookDto

Create a `CreateUpdateBookDto` class in the Books folder (namespace) of the `Acme.BookStore.Application.Contracts` project:

````csharp
using System;
using System.ComponentModel.DataAnnotations;

namespace Acme.BookStore.Books;

public class CreateUpdateBookDto
{
    [Required]
    [StringLength(128)]
    public string Name { get; set; } = string.Empty;

    [Required]
    public BookType Type { get; set; } = BookType.Undefined;

    [Required]
    [DataType(DataType.Date)]
    public DateTime PublishDate { get; set; } = DateTime.Now;

    [Required]
    public float Price { get; set; }
}
````

* This `DTO` class is used to get book information from the user interface while creating or updating a book.
* It defines data annotation attributes (like `[Required]`) to define validations for the properties. `DTO`s are [automatically validated](https://docs.abp.io/en/abp/latest/Validation) by the ABP framework.

Just like done for the `BookDto` above, we should define the mapping from the `CreateUpdateBookDto` object to the `Book` entity. The final class will be like shown below:

````csharp
using Acme.BookStore.Books;
using AutoMapper;

namespace Acme.BookStore;

public class BookStoreApplicationAutoMapperProfile : Profile
{
    public BookStoreApplicationAutoMapperProfile()
    {
        CreateMap<Book, BookDto>();
        CreateMap<CreateUpdateBookDto, Book>();
    }
}
````

### IBookAppService

Next step is to define an interface for the application service. Create an interface named `IBookAppService` in the `Acme.BookStore.Application.Contracts` project:

````csharp
using System;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;

namespace Acme.BookStore.Books;

public interface IBookAppService :
    ICrudAppService< //Defines CRUD methods
        BookDto, //Used to show books
        Guid, //Primary key of the book entity
        PagedAndSortedResultRequestDto, //Used for paging/sorting
        CreateUpdateBookDto> //Used to create/update a book
{

}
````

* Defining interfaces for the application services **are not required** by the framework. However, it's suggested as a best practice.
* `ICrudAppService` defines common **CRUD** methods: `GetAsync`, `GetListAsync`, `CreateAsync`, `UpdateAsync` and `DeleteAsync`. It's not required to extend it. Instead, you could inherit from the empty `IApplicationService` interface and define your own methods manually (which will be done for the authors in the next parts).
* There are some variations of the `ICrudAppService` where you can use separated DTOs for each method (like using different DTOs for create and update).

### BookAppService

Implement the `IBookAppService` as `BookAppService` in the `Books` folder in the `Acme.BookStore.Application` project:


````csharp
using System;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;
using Volo.Abp.Domain.Repositories;

namespace Acme.BookStore.Books;

public class BookAppService :
    CrudAppService<
        Book, //The Book entity
        BookDto, //Used to show books
        Guid, //Primary key of the book entity
        PagedAndSortedResultRequestDto, //Used for paging/sorting
        CreateUpdateBookDto>, //Used to create/update a book
    IBookAppService //implement the IBookAppService
{
    public BookAppService(IRepository<Book, Guid> repository)
        : base(repository)
    {

    }
}
````

* `BookAppService` is derived from `CrudAppService<...>` which implements all the CRUD (create, read, update, delete) methods defined above.
* `BookAppService` injects `IRepository<Book, Guid>` which is the default repository for the `Book` entity. ABP automatically creates default repositories for each aggregate root (or entity). See the [repository document](https://docs.abp.io/en/abp/latest/Repositories).
* `BookAppService` uses `IObjectMapper` to map `Book` objects to `BookDto` objects and `CreateUpdateBookDto` objects to `Book` objects. The Startup template uses the [AutoMapper](http://automapper.org/) library as the object mapping provider. We have defined the mappings before, so it will work as expected.

## Auto API Controllers

In a typical ASP.NET Core application, you create **API Controllers** to expose application services as **HTTP API** endpoints. This allows browsers or 3rd-party clients to call them over HTTP.

ABP can [**automagically**](https://docs.abp.io/en/abp/latest/API/Auto-API-Controllers) configures your application services as MVC API Controllers by convention.

### Swagger UI

The startup template is configured to run the [Swagger UI](https://swagger.io/tools/swagger-ui/) using the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) library. Run the application ({{if UI=="MVC"}}`Acme.BookStore.Web`{{else}}`Acme.BookStore.HttpApi.Host`{{end}}) by pressing `CTRL+F5` and navigate to `https://localhost:<port>/swagger/` on your browser. Replace `<port>` with your own port number.

You will see some built-in service endpoints as well as the `Book` service and its REST-style endpoints:

![bookstore-swagger](../../images/bookstore-swagger.png)

Swagger has a nice interface to test the APIs.

If you try to execute the `[GET] /api/app/book` API to get a list of books, the server returns such a JSON result:

````json
{
    "totalCount": 2,
    "items": [
        {
            "name": "The Hitchhiker's Guide to the Galaxy",
            "type": 7,
            "publishDate": "1995-09-27T00:00:00",
            "price": 42,
            "lastModificationTime": null,
            "lastModifierId": null,
            "creationTime": "2023-01-02T11:36:07.4735924",
            "creatorId": null,
            "id": "79fdadb4-a0c8-1e37-3b60-3a0883cc82b1"
        },
        {
            "name": "1984",
            "type": 3,
            "publishDate": "1949-06-08T00:00:00",
            "price": 19.84,
            "lastModificationTime": null,
            "lastModifierId": null,
            "creationTime": "2023-01-02T11:36:07.4639037",
            "creatorId": null,
            "id": "8186d48f-5b70-849b-2918-3a0883cc829b"
        }
    ]
}
````

That's pretty cool since we haven't written a single line of code to create the API controller, but now we have a fully working REST API!

## The Next Part

See the [next part](part-2.md) of this tutorial.
