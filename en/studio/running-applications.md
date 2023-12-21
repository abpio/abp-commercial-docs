# Running Applications Using ABP Studio Solution Runner

Running application in ABP Studio is a straightforward process to start one or more applications easier. Navigate to the **Solution Runner** panel located in the left menu.

![solution-runner](images/solution-runner/solution-runner.png)

> The project structure might be different based on your selection. In an MVC microservice project, looks like the following;

The solution runner contains 4 different types to define tree structure.
- **Profile root** - `Acme.BookStore (Default)`.
- **Folder** - `apps`, `gateways`, `infrastructure`, `services`. 
- **C# Application** `Acme.BookStore.AuthServer`, `Acme.BookStore.Web`, `Acme.BookStore.WebGateway`, etc...
- **CLI Application** `Docker-Dependencies`

## Profile

We can add or remove profiles to define different profile roots, which is provide us to organize our tree structure as needed. We can collapse or expand the entire tree using the up and down arrow icons. The *Default* profile comes with the project creation, includes all projects in the tree to manage at once. You can view all profiles in the combobox and change the current profile. To edit, click the gear icon located on the right side.

![solution-runner-edit](images/solution-runner/solution-runner-edit.png)

It opens the *Manage Run Profiles* window. You can edit/delete existing profiles or add a new one.

![manage-run-profiles](images/solution-runner/manage-run-profiles.png)

When you click *Add New Profile*, it opens the *Create New Profile* window. You can provide an arbitrary profile name, which should only contain letters, numbers, underscores, dashes, and dots in the text. When you create a new profile, it stores the JSON file at the specified path. You can specify the path `abp-solution-path/etc/abp-studio/run-profiles` for microservice projects or `abp-solution-path/etc/run-profiles` for other project types to adhere to the standard format. Click *OK* button to save profile.

![create-new-profile](images/solution-runner/create-new-profile.png)

> You can change the current profile while applications are running in the previous profile. The applications continue to run under the previous profile. For example if we start the `Acme.BookStore.AdministrationService`, `Acme.BookStore.IdentityService` applications when current profile is *team-1* and after change the current profile to *team-2* the applications continue to run under *team-1*.

> When a profile is deleted while running some applications, those applications will be stopped. If we edit a profile while running some applications, only the applications associated with the edited profile will be stopped. However, applications running under a different profile will continue to run unaffected. Lastly if we add a new profile then all profiles runned applications gonna be stopped.

## Profile Root

After selecting the current profile, which is *Default* comes out of the box we can utilize the *Profile Root*. This allows us to execute collective commands and create various tree structures based on our specific needs. You can navigate to the profile root and right-click to view the context menu, which includes 3 options `Run`, `Build` and `Add`.

![profile-root-context-menu](images/solution-runner/profile-root-context-menu.png)

### Run

*Profile Root* -> *Run* context menu, there are 3 options available:

- `Start All`: Start all(CLI, C#) applications.
- `Stop All`: Stop all(CLI, C#) applications.
- `Build & Start All`: It builds each C# applications using the [dotnet build](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build) command in the [Background Tasks](./overview/index.md#background-tasks) and starts all(CLI, C#) applications after the build tasks are completed.

![profile-root-context-menu-run](images/solution-runner/profile-root-context-menu-run.png)

### Build

*Profile Root* -> *Build* context menu, there are 4 options available:

- `Build All`: It builds each C# applications using the [dotnet build](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build) command in the [Background Tasks](./overview/index.md#background-tasks).
- `Graph Build`: It builds each C# applications using the [dotnet build](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build) command with [graphBuild](https://learn.microsoft.com/en-us/visualstudio/msbuild/build-process-overview?view=vs-2022#graph-option) option in the [Background Tasks](./overview/index.md#background-tasks).
- `Restore`: It restores each C# applications using the [dotnet restore](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-restore) command in the [Background Tasks](./overview/index.md#background-tasks).
- `Clean`: It cleans each C# applications using the [dotnet clean](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-clean) command in the [Background Tasks](./overview/index.md#background-tasks).

![profile-root-context-menu-build](images/solution-runner/profile-root-context-menu-build.png)