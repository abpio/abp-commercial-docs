To add `LeptonX` into your existing projects, 

* Firstly, install `@volosoft/abp.ng.theme.lepton-x`

`npm install @volosoft/abp.ng.theme.lepton-x@preview` or 

`yarn add @volosoft/abp.ng.theme.lepton-x@preview`

* Then, edit `angular.json` as follows:

You can remove the `flag-icon` css since `LeptonX` no longer uses flags to demonstrate the languages. 
```JSON
{
  "input": "node_modules/flag-icon-css/css/flag-icon.min.css",
  "inject": true,
  "bundleName": "flag-icon.min"
},
```

Add the `bootstrap-icons` into the `styles` array

```JSON
"node_modules/bootstrap-icons/font/bootstrap-icons.css",
```

* At last, remove `ThemeLeptonModule` from `app.module.ts` and `shared.module.ts`, and import the following modules in `app.module.ts`

```js
import { ThemeLeptonXModule } from '@volosoft/abp.ng.theme.lepton-x';
import { SideMenuLayoutModule } from '@volosoft/abp.ng.theme.lepton-x/layouts';

@NgModule({
  // ...
  imports: [
    // ...
    // ThemeLeptonModule.forRoot(),
    ThemeLeptonXModule.forRoot(),
    SideMenuLayoutModule.forRoot(), // depends on which layout you choose
    // ...
  ],
  // ...
})
export class AppModule {}
```

Note: If you employ [Resource Owner Password Flow](https://docs.abp.io/en/abp/latest/UI/Angular/Authorization#resource-owner-password-flow) for authorization, you should import the following module as well:

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