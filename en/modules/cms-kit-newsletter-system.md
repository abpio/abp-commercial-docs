# Newsletter System

CMS kit provides a provides a **newsletter** system to allow users to subscribe to newsletters. This section describes the details of the newsletter system. 

## Feature

CMS kit uses the [global feature](https://docs.abp.io/en/abp/latest/Global-Features) system for all implemented features. Startup templates come with all the CMS kit related features are enabled by default. If you want to enable newsletter system feature individually, open the `GlobalFeatureConfigurator` class in the `Domain.Shared` project and place the following code to the `Configure ` method.

```csharp
GlobalFeatureManager.Instance.Modules.CmsKitPro(cmsKitPro =>
{
    cmsKitPro.Newsletter.Enable();
});
```

# Options

## NewsletterOptions

Beforing using the newsletter system, you need to define the preferences. In order to define preferences, you can use the `NewsletterOptions` type. 

`NewsletterOptions` can be configured in the domain layer, in the `ConfigureServices` method of your [module](https://docs.abp.io/en/abp/latest/Module-Development-Basics). Example:

```csharp
options.AddPreference("TechNewsletter",
    new NewsletterPreferenceDefinition(
        "Daily Technology Newsletter",
        privacyPolicyConfirmation: "I accept the <a href='/privacy-policy'>Privacy Policy</a>.")
    )
);
```

`NewsletterOptions` properties:

- `Preferences`: List of defined newsletter preferences(`NewsletterPreferenceDefinition`) in the newsletter system.
- `WidgetViewPath`: Default view path for all newsletter preferences.

`NewsletterPreferenceDefinition` properties:

- ``