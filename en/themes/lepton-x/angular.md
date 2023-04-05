# LeptonX Angular UI

To add `LeptonX` into your existing projects, follow the steps below. 

* Firstly, install `@volosoft/abp.ng.theme.lepton-x` using the command below.

`yarn add @volosoft/abp.ng.theme.lepton-x`

* Then, edit `angular.json` as follows:

Add theme-specific styles into the `styles` array of the file.  Check the [Theme Configurations](https://docs.abp.io/en/abp/latest/UI/Angular/Theme-Configurations) documentation for more information.

Importing a CSS file as an ECMA module is not supported in Angular 14. Therefore, we need to add the styles in the angular.json file. 

> You can check [the release notes of Angular 14](https://github.com/angular/angular-cli/releases/tag/14.0.0) for more information. 

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

If you want to create your own layout, [see the docs](https://github.com/abpio/abp-commercial-docs/blob/dev/en/themes/lepton-x/how-to-use-lepton-x-components-with-angular-custom-layout.md)
