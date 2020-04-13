# Content Toolbar Extensions for Angular UI

## Introduction

Content toolbar extension system allows you to add a new action to the content toolbar of a page. A "Click Me" action was added to the user management page below:

![Content Toolbar Extension Example: "Click Me!" Action](../../images/user-content-toolbar-extension-click-me-ng.png)

You can take any action (open a modal, make an HTTP API call, redirect to another page... etc) by writing your custom code. You can also access to the current entity list in your code.

## How to Set Up

In this example, we will add a "Click Me!" action and execute a JavaScript code for the user management page of the [Identity Module](../../modules/identity.md).

### Step 1. Create Entity Action Contributors

The following code prepares a constant named `identityToolbarContributors`, ready to be imported and used in your root module:

```js
// content-toolbar-contributors.ts

import { ActionList, EntityAction } from '@volo/abp.commercial.ng.ui';
import { Identity } from '@volo/abp.ng.identity';
import { IdentityToolbarContributors } from '@volo/abp.ng.identity.config';

const logUserNames = new EntityAction<Identity.User[]>({
  text: 'Click Me!',
  action: data => {
    // Replace log with your custom code
    console.log(data.record.map(user => user.userName));
  },
});

export function logUserNamesContributor(actionList: ActionList<Identity.User[]>) {
  actionList.addHead(logUserNames);
}

export const identityToolbarContributors: IdentityToolbarContributors = {
  // 'Identity.UsersComponent' indicates where this action will be placed
  'Identity.UsersComponent': [
    logUserNamesContributor,
    // You can add more contributors here
  ],
};

```

The list of actions, conveniently named as `actionList`, is a **doubly linked list**. That is why we have used the `addHead` method, which adds the given value to the beginning of the list. You may find [all available methods here](../../Common/Utils/Linked-List).

> **Important Note:** AoT compilation does not support function calls in decorator metadata. This is why we have defined `logUserNamesContributor` as an exported function declaration here. Please do not forget exporting your contributor callbacks and forget about lambda functions (a.k.a. arrow functions). Please refer to [AoT metadata errors](https://angular.io/guide/aot-metadata-errors#function-calls-not-supported) for details.

### Step 2. Import and Use Content Toolbar Contributors

Import `identityToolbarContributors` in your root module and pass it to the static `forRoot` method of `IdentityConfigModule` as seen below:

```js
import { IdentityConfigModule } from '@volo/abp.ng.identity.config';
import { identityToolbarContributors } from './content-toolbar-contributors';

@NgModule({
  imports: [
    // Other imports
    
    IdentityConfigModule.forRoot({
      toolbarActionContributors: identityToolbarContributors,
    }),
    
    // Other imports
  ],
  providers: [],
  declarations: [AppComponent],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

That is it, `logUserNames` toolbar action will be added as the first action on the content toolbar in the users page (`UsersComponent`) of the `IdentityModule`.

## API

Content toolbar extension exposes the same API with [entity action extension](./entity-action-extensions.md).
