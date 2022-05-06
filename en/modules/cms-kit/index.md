# CMS Kit Pro Module

This module extends the [open-source CMS Kit module](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Index) and adds additional CMS (Content Management System) capabilities for your application.

> **This module currently available only for the MVC / Razor Pages UI**. While there is no official Blazor package, it can also work in a Blazor Server UI since a Blazor Server UI is actually a hybrid application that runs in an ASP.NET Core MVC / Razor Pages application.

The following features are provided by the open source CMS Kit module:

- [**Page**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Pages) management system to manage dynamic pages with dynamic URLs.
- [**Blogging**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Blogging) system to create publish blog posts with multiple blog support.
- [**Tagging**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Tags) system to tag any kind of resource, like a blog post.
- [**Comment**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Comments) system to add comments feature to any kind of resource, like blog post or a product review page.
- [**Reaction**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Reactions) system to add reactions (smileys) feature to any kind of resource, like a blog post or a comment.
- [**Rating**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Ratings) system to add rating feature to any kind of resource.
- [**Menu**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Menus) system to manage public menus dynamically.
- [**Global resources**](https://docs.abp.io/en/abp/latest/Modules/Cms-Kit/Global-Resources) system to add global styles and scripts dynamically.

And the following features are provided by the CMS Kit pro version:

* [**Newsletter**](newsletter.md) system to allow users to subscribe to newsletters.
* [**Contact form**](contact-form.md) system to allow users to write message to you.
* [**URL forwarding**](url-forwarding.md) system to create URLs that redirect to other pages or external websites.

Click to a feature to understand and learn how to use it. See [the module description page](https://commercial.abp.io/modules/Volo.CmsKit.Pro) for an overview of the module features.

## How to Install

### New Solutions

CMS Kit Pro is pre-installed in [the startup templates](../../startup-templates/application/index.md) if you create the solution with **public website** option. If you are using ABP CLI, you should specify the the `--with-public-website` option as shown below:

```bash
abp new Acme.BookStore --with-public-website
```

### Existing Solutions

If you want to add the CMS kit to your existing solution, you can use the ABP CLI `add-module` command:

```bash
abp add-module Volo.CmsKit.Pro
```
Open the `GlobalFeatureConfigurator` class in the `Domain.Shared` project and place the following code to the `Configure` method to enable all open-source and commercial features in the CMS Kit module.

```csharp
GlobalFeatureManager.Instance.Modules.CmsKit(cmsKit =>
{
    cmsKit.EnableAll();
});

GlobalFeatureManager.Instance.Modules.CmsKitPro(cmsKitPro =>
{
    cmsKitPro.EnableAll();
});
```

Alternatively, you can enable features individually, like `cmsKit.Comments.Enable();`.

> If you are using Entity Framework Core, do not forget to add a new migration and update your database.



