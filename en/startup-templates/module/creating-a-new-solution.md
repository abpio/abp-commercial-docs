# Creating a new Module Solution

You can use the [ABP CLI](https://docs.abp.io/en/abp/latest/CLI) to create a new solution using the module startup template.

First, install the ABP CLI if you haven't installed it before:

```bash
dotnet tool install -g Volo.Abp.Cli
```
Then use the `abp new` command in an empty folder to create your new ABP module solution:

```bash
abp new Acme.IssueManagement -t module-pro
```

- `Acme.IssueManagement` is the solution name, like *YourCompany.YourProduct*. You can use single level, two-levels or three-levels naming.
- `-t module-pro` option specifies the template type. In this case we use `module-pro` for our ABP Commmercial projects.

## Without User Interface

Use `--no-ui` option to not include the UI layer. This is useful if you only want to create a service/microservice project without UI.


### Creating Module Solution via ABP Suite

ABP Suite allows you to create a new ABP Module Solution. 

If you have not installed ABP Suite, see [how to install ABP Suite](../../abp-suite/how-to-install.md).

To start ABP Suite, see [how to start ABP Suite](../../abp-suite/how-to-start.md). 

To create a new module solution, see [how to create a new module solution](../../abp-suite/create-module-solution.md). 


## What's Next?

* [Module Startup Template Solution Structure](solution-structure.md)
