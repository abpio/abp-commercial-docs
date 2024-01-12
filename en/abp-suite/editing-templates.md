# Editing templates

ABP Suite uses templates to generate the code files. You can see the the template files by clicking "Edit Templates" menu item.

![Suite template list](../images/suite-templates-8.1.png)

The are 2 template filters:

1. **UI**:  You can switch between `Angular` and `MVC` templates.
2. **Database provider:** You can switch between `EF Core` and `MongoDb` templates.

These filters are automatically selected based on your ABP solution. The selected solution is shown on the top right of the page. In the current screenshot it is "SAM". 

ABP Suite templates are embedded resources which are stored in the `Volo.Abp.Commercial.SuiteTemplates` package. When you update your project, Suite templates are also being updated. To be able to generate the correct code for your project, the version of `Volo.Abp.Commercial.SuiteTemplates` must be the same as your Suite version.

## How do I find which template to edit ?

There's a search box in the templates page. To find the related template, pick a unique text from your generated code file and search it. The search, filters by template content.

There's a naming convention for the template files. 

* If the template name has `Server` prefix, it's used for backend code like repositories, application services, localizations, controllers, permissions, mappings, unit tests.
* If the template name has `Frontend.Angular` prefix, it's used for Angular code generation. The Angular code is being generated via [Angular Schematics](https://angular.io/guide/schematics).
* If the template name has `Frontend.Mvc`  prefix, it's used for razor pages, menus, JavaScript, CSS files.

## How do I edit the templates?

When you click the Edit button of a template card, you will see the template content and variables of the template. These variables are written in `%%my-variable%%` format. 

When you edit a template, you will see the "Customized" badge on the template card. You can revert back to the original content by clicking "Revert customization".

![Customized template](../images/suite-customized-template.png)



You may need the variable list while editing a template content. 

**Variables:**

These variables are populated based on the following project information;

* Solution name: **Acme.BookStore**
* Entity name: **OrderLine**
* Plural name: **OrderLines**
* Database table: **OrderLines**
* Namespace: **OrderLines**
* Base class: **FullAuditedAggregateRoot**
* Primary key type: **Guid**

| Variable name                    | Value                                                   |
| -------------------------------- | ------------------------------------------------------- |
| %%solution-namespace%%           | Acme.BookStore                                          |
| %%solution-namespace-camelcase%% | acme.bookStore                                          |
| %%project-name%%                 | Acme.BookStore                                          |
| %%only-project-name%%            | BookStore                                               |
| %%only-project-name-camelcase%%  | bookStore                                               |
| %%entity-namespace%%             | OrderLines                                              |
| %%entity-namespace-camelcase%%   | orderLines                                              |
| %%entity-name%%                  | OrderLine                                               |
| %%entity-name-camelcase%%        | orderLine                                               |
| %%entity-name-plural%%           | OrderLines                                              |
| %%entity-name-plural-kebabcase%% | order-lines                                             |
| %%entity-name-plural-camelcase%% | orderLines                                              |
| %%primary-key%%                  | Guid                                                    |
| %%database-table-name%%          | OrderLines                                              |
| %%base-class%%                   | FullAuditedAggregateRoot                                |
| %%other-interfaces%%             | , IMultiTenant (*if multitenant*)                       |
| %%default-sorting%%              | {0}Price asc                                            |
| %%with-navigation-properties%%   | WithNavigationProperties (*if has navigation property*) |
| %%entity-name-prefix%%           | OrderLine. (*if has navigation property*)               |
| %%entity-name-prefix-camelcase%% | orderLine. (*if has navigation property*)               |
| %%entity-name-prefix-nullable%%  | orderLine?. (*if has navigation property*)              |
| %%module-name-slash-postfix%%    | BookStore/  (*for module template*)                     |

**Conditions:**

Conditions are if statements to render the related code-block. If the condition is true, then the inner text of the condition gets rendered. You can also use variables inside the condition blocks.

| Variable name                                                | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| %%&lt;if:IMultiTenantEntity&gt;%%    %%&lt;/if:IMultiTenantEntity&gt;%% | If the entity is multi tenant.                               |
| %%&lt;if:ApplicationContractsNotExists&gt;%%    %%&lt;/if:ApplicationContractsNotExists&gt;%% | if `*.Application.Contracts` project exists                  |
| %%&lt;if:IsNonTieredArchitecture&gt;%%    %%&lt;/if:IsNonTieredArchitecture&gt;%% | if it's not a tiered application template. Means `*.HttpApi.Host` project doesn't exist |
| %%&lt;if:IsTieredArchitecture&gt;%%    %%&lt;/if:IsTieredArchitecture&gt;%% | if it's a tiered application template. Means `*.HttpApi.Host` project exists |
| %%&lt;if:IsModule&gt;%%    %%&lt;/if:IsModule&gt;%%          | if it's a module template.                                   |


