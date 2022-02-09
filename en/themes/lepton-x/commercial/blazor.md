# LeptonX Blazor UI

````json
//[doc-params]
{
    "UI": ["Blazor", "BlazorServer"]
}
````

LeptonX theme is implemented and ready to use with ABP Commercial. No custom implementation is needed for Blazor Server & WebAssembly.

## Installation

{{if UI == "Blazor"}}

- Add **Volo.Abp.AspNetCore.Components.WebAssembly.LeptonXTheme** package to your **Blazor wasm** application.

- Remove old theme from **DependsOn** attribute in your module class and add **AbpAspNetCoreComponentsWebAssemblyLeptonXThemeModule** type to **DependsOn** attribute.

```diff
[DependsOn(
-    typeof(LeptonThemeManagementBlazorModule),
-    typeof(AbpAspNetCoreComponentsWebAssemblyLeptonThemeModule),
+    typeof(AbpAspNetCoreComponentsWebAssemblyLeptonXThemeModule)
)]
```

{{end}}


{{if UI == "BlazorServer"}}

- Complete [MVC Installation steps](mvc.md#installation) first.

- Add **Volo.Abp.AspNetCore.Components.Server.LeptonXTheme** package to your **Blazor wasm** application.

- Remove old theme from **DependsOn** attribute in your module class and add **AbpAspNetCoreComponentsServerLeptonXThemeModule** type to **DependsOn** attribute.

```diff
[DependsOn(
-    typeof(LeptonThemeManagementBlazorModule),
-    typeof(AbpAspNetCoreComponentsServerLeptonThemeModule),
+    typeof(AbpAspNetCoreComponentsServerLeptonXThemeModule)
)]
```
{{end}}

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

