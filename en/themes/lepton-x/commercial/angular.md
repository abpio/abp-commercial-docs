# Client Side

To add `LeptonX` into your existing projects, follow the steps below. 

* Firstly, install `@volosoft/abp.ng.theme.lepton-x` using the command below.

`yarn add @volosoft/abp.ng.theme.lepton-x@preview`

* Then, edit `angular.json` as follows:

Add the following ones into the `styles` array

```JSON
"node_modules/bootstrap-icons/font/bootstrap-icons.css",
```

* At last, remove `ThemeLeptonModule` from `app.module.ts` and `shared.module.ts`, and import the following modules in `app.module.ts`

```js
import { HttpErrorComponent, ThemeLeptonXModule } from '@volosoft/abp.ng.theme.lepton-x';
import { SideMenuLayoutModule } from '@volosoft/abp.ng.theme.lepton-x/layouts';

@NgModule({
  // ...
  imports: [
    // ...
    // ThemeLeptonModule.forRoot(), -> remove this line.
    ThemeLeptonXModule.forRoot(),
    SideMenuLayoutModule.forRoot(), // depends on which layout you choose
    // ...
  ],
  // ...
})
export class AppModule {}
```

Note: If you are using [Resource Owner Password Flow](https://docs.abp.io/en/abp/latest/UI/Angular/Authorization#resource-owner-password-flow) for authorization, you should import the following module as well to `app.module.ts`:

```js
import { AccountLayoutModule } from '@volosoft/abp.ng.theme.lepton-x/account';

@NgModule({
  // ...
  imports: [
    // ...
    AccountLayoutModule.forRoot({
      layout: {
        authLayoutImg: '/assets/images/login-bg.jpg',
      },
    }),
    // ...
  ],
  // ...
})
export class AppModule {}
```

`authLayoutImg`: (Optional) If not given, a default image will be placed on the authentication pages.


* At this point, `LeptonX` theme should be up and running within your application. However, you may need to overwrite some css variables based your needs for every theme available as follows:
  
```scss
:root {
  .lpx-theme-dark {
    --lpx-logo: url('/assets/images/logo/logo-light.svg');
    --lpx-logo-icon: url('/assets/images/logo/logo-light-icon.svg');
    --lpx-brand: #edae53;
  }

  .lpx-theme-dim {
    --lpx-logo: url('/assets/images/logo/logo-light.svg');
    --lpx-logo-icon: url('/assets/images/logo/logo-light-icon.svg');
    --lpx-brand: #f15835;
  }

  .lpx-theme-light {
    --lpx-logo: url('/assets/images/logo/logo-dark.svg');
    --lpx-logo-icon: url('/assets/images/logo/logo-dark-icon.svg');
    --lpx-brand: #69aada;
  }
}
```
If everything is ok, you can remove the `@volo/abp.ng.theme.lepton` in package.json

# Server Side

In order to migrate to LeptonX on your server side projects (Host and/or IdentityServer projects), please follow [Server Side Migration](mvc.md) document.


## Customization


### Layouts

The Angular version of LeptonX Lite provides **layout components** for your **user interface** on [ABP Framework Theming](https://docs.abp.io/en/abp/latest/UI/Angular/Theming). You can use layouts to **organize your user interface**. You can replace the **layout components** and some parts of the **layout components** with the [ABP replaceable component  system](https://docs.abp.io/en/abp/latest/UI/Angular/Component-Replacement).


The main responsibility of a theme is to **provide** the layouts. There are **three pre-defined layouts that must be implemented by all the themes:**

* **ApplicationLayoutComponent:** The **default** layout which is used by the **main** application pages.

* **AccountLayoutComponent:** Mostly used by the **account module** for **login**, **register**, **forgot password**... pages.

* **EmptyLayoutComponent:** The **Minimal** layout that **has no layout components** at all.

The **Layout components** and all the replacable components are predefined in  `eThemeLeptonXComponents` as enum.

### How to replace a component

```js
import { ReplaceableComponentsService } from '@abp/ng.core'; // imported ReplaceableComponentsService
import { eIdentityComponents } from '@abp/ng.identity'; // imported eIdentityComponents enum
import { eThemeLeptonXComponents } from '@abp/ng.theme.lepton-x';   // imported eThemeLeptonXComponents enum
//...
@Component(/* component metadata */)
export class AppComponent {
  constructor(
    private replaceableComponents: ReplaceableComponentsService, // injected the service
  ) {
    this.replaceableComponents.add({
      component: YourNewApplicationLayoutComponent,
      key: eThemeLeptonXComponents.ApplicationLayout,
    });
  }
}
```
See the [Component Replacement](https://docs.abp.io/en/abp/latest/UI/Angular/Component-Replacement) documentation for more information on how to replace components.


### Brand Component

The **brand component** is a simple component that can be used to display your brand. It contains a **logo** and a **company name**. You can change the logo via css but if you want to change logo component, the key is `eThemeLeptonXComponents.Logo`

```js
///...
    this.replaceableComponents.add({
        component: YourNewLogoComponent,
        key: eThemeLeptonXComponents.Logo,
    });
///...
```

![Brand component](../../images/leptonxlite-brand-component.png)



## Breadcrumb Component

On websites that have a lot of pages, **breadcrumb navigation** can greatly **enhance the way users find their way** around. In terms of **usability**, breadcrumbs reduce the number of actions a website **visitor** needs to take in order to get to a **higher-level page**, and they **improve** the **findability** of **website sections** and **pages**.

```js
///...
    this.replaceableComponents.add({
        component: YourNewSidebarComponent,
        key: eThemeLeptonXComponents.Breadcrumb,
    });
///...
```

![Breadcrumb component](../../images/leptonxlite-breadcrumb-component.png)

## Sidebar Menu Component

Sidebar menus have been used as a **directory for Related Pages** to a **Service** offering, **Navigation** items to a **specific service** or topic and even just as **Links** the user may be interested in.

```js
///...
    this.replaceableComponents.add({
        component: YourNewSidebarComponent,
        key: eThemeLeptonXComponents.Sidebar,
    });
///...
```
![Sidebar menu component](../../images/leptonxlite-sidebar-menu-component.png)

## Page Alerts Component

Provides contextual **feedback messages** for typical user actions with a handful of **available** and **flexible** **alert messages**. Alerts are available for any length of text, as well as an **optional dismiss button**.

![Page alerts component](../../images/leptonxlite-page-alerts-component.png)
```js
///...
    this.replaceableComponents.add({
        component: YourNewPageAlertContainerComponent,
        key: eThemeLeptonXComponents.PageAlertContainer,
    });
///...
```
## Toolbar Component
![Breadcrumb component](../../images/leptonxlite-toolbar-component.png)

Toolbar items are used to add **extra functionality to the toolbar**. The toolbar is a **horizontal bar** that **contains** a group of **toolbar items**.
```js
///...
    this.replaceableComponents.add({
        component: YourNewNavItemsComponent,
        key: eThemeLeptonXComponents.NavItems,
    });
///...
```

## Toolbar Items
There are two parts to the toolbar. The first is Language-Switch. The second is the User-Profile element. You can swap out each of these parts individually.

## Language Switch Component

Think about a **multi-lingual** website and the first thing that could **hit your mind** is **the language switch component**. A **navigation bar** is a **great place** to **embed a language switch**. By embedding the language switch in the navigation bar of your website, you would **make it simpler** for users to **find it** and **easily** switch the **language**  <u>**without trying to locate it across the website.**</u>

![Language switch component](../../images/leptonxlite-language-switch-component.png)
```js
///...
    this.replaceableComponents.add({
        component: YourNewLanguagesComponent,
        key: eThemeLeptonXComponents.Languages,
    });
///...
```

## User Menu Component

The **User Menu** is the **menu** that **drops down** when you **click your name** or **profile picture** in the **upper right corner** of your page (**in the toolbar**). It drops down options such as **Settings**, **Logout**, etc.

![User menu component](../../images/leptonxlite-user-menu-component.png)
```js
///...
    this.replaceableComponents.add({
        component: YourNewCurrentUserComponent,
        key: eThemeLeptonXComponents.CurrentUser,
    });
///...
```
Note: The language selection component in the Volo app is not replaceable. It is part of the settings menu.

## Mobile Navbar Component
The **mobile navbar component** is used to display the **navbar menu on mobile devices**. The mobile navbar component is a **dropdown menu** that contains language selection and user menu.

![Mobile user menu component](../../images/leptonxlite-mobile-user-menu-component.png)
```js
///...
    this.replaceableComponents.add({
        component: YourNewMobileNavbarComponent,
        key: eThemeLeptonXComponents.MobileNavbar,
    });
///...
```
## Mobile Navbar Items.
There are two parts of the mobile navbar. The mobile navbar has Language-Switch and User-Profile. You can swap out each of these parts individually.

The Mobile language-Selection component key is `eThemeLeptonXComponents.MobileLanguageSelection`.

The Mobile User-Profile component key is `eThemeLeptonXComponents.MobileUserProfile`.

