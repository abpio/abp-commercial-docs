# LeptonX MVC UI
LeptonX theme is implemented and ready to use with ABP Commercial. No custom implementation is needed.

## Installation

### With CLI

- Install package to your **Web** project with CLI.
```bash
abp add-package Volo.Abp.AspNetCore.Mvc.UI.Theme.LeptonX
```

- Remove old theme from **DependsOn** attribute in your module class.

```diff
[DependsOn(
        // ...
-        typeof(LeptonThemeManagementWebModule),
-        typeof(AbpAspNetCoreMvcUiLeptonThemeModule)
)]
```

### Manual

- Install **Volo.Abp.AspNetCore.Mvc.UI.Theme.LeptonX** nuget package manually to your **Web** project.

- Remove old theme from **DependsOn** attribute in your module class and add **AbpAspNetCoreMvcUiLeptonXThemeModule** type to **DependsOn** attribute.

```diff
[DependsOn(
        // ...
-        typeof(LeptonThemeManagementWebModule),
-        typeof(AbpAspNetCoreMvcUiLeptonThemeModule),
+        typeof(AbpAspNetCoreMvcUiLeptonXThemeModule)
)]
```

---

## Customization

### Themes
TODO

*adding new themes and removing existing ones*

### Toolbars
TODO

*adding new items or removing existing ones from general settings & mobile menu*

### LeptonXThemeMvcOptions
TODO

*changing layout*
*configuring mobile menu item selector*

