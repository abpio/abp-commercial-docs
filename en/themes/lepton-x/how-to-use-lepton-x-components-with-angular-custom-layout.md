# Custom layout usage with Lepton X components


First, The  custom layout component should be created and implemented to angular application.
Related content can be found at [Component Replacement doc](https://docs.abp.io/en/abp/latest/UI/Angular/Component-Replacement#how-to-replace-a-layout)


 
After creating a custom layout, this imports should be imported on `app.module.ts` because the modules contains definitions of Lepton X components.


```typescript
// app.module.ts
import { LpxSideMenuLayoutModule } from '@volosoft/ngx-lepton-x/layouts';
import { LpxResponsiveModule } from '@volo/ngx-lepton-x.core';// optional. Only, if you are using lpxResponsive directive

 @NgModule({
 //... removed for clearity
  imports: [
  	//... removed for clearity
  	LpxSideMenuLayoutModule,
  	LpxResponsiveModule // <-- Optional
  	]
})
export class AppModule {}

```

Here is the simplified version of `side-menu-layout.ts`.  Only ABP Component Replacement codes have been removed.


```typescript
<ng-container *lpxResponsive="'all md-none'">
    <ng-container *ngTemplateOutlet="content"></ng-container>
  </ng-container>
  <ng-container *lpxResponsive="'md'">
    <div class="lpx-scroll-container ps" [perfectScrollbar]>
      <ng-container *ngTemplateOutlet="content"></ng-container>
    </div>
  </ng-container>
  <ng-template #content>
    <div
      id="lpx-wrapper">
      <div class="lpx-sidebar-container" *lpxResponsive="'md'">
        <div class="lpx-sidebar ps" [perfectScrollbar]>
            <lpx-navbar></lpx-navbar>
          <div class="lpx-logo-container">
            <lpx-brand-logo></lpx-brand-logo>
          </div>
        </div>
      </div>
  
      <div class="lpx-content-container">
        <div class="lpx-topbar-container">
          <div class="lpx-topbar">
            <div class="lpx-breadcrumb-container">
                <lpx-breadcrumb></lpx-breadcrumb>
            </div>
            <div class="lpx-topbar-content">
              <lpx-topbar-content></lpx-topbar-content>
            </div>
          </div>
        </div>
        <div class="lpx-content-wrapper">
          <div class="lpx-content">
            <router-outlet></router-outlet>
                  </div>
        </div>
        <div class="lpx-footbar-container">
            <lpx-footer></lpx-footer>
        </div>
      </div>
  
      <lpx-mobile-navbar *lpxResponsive="'all md-none'"></lpx-mobile-navbar>

      <div class="lpx-toolbar-container" *lpxResponsive="'md'">
        <lpx-toolbar-container></lpx-toolbar-container>
        <lpx-avatar></lpx-avatar>
        <lpx-settings></lpx-settings>
      </div>
    </div>
  </ng-template>

```

Add this code to your application template and customize it as desired.
