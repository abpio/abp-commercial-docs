# Creating a new Module Solution

You can use the [ABP CLI](https://docs.abp.io/en/abp/latest/CLI) to create a new project using the module startup template.

First, install the ABP CLI if you haven't installed before:

```bash
dotnet tool install -g Volo.Abp.Cli
```

Then use the `abp new` command in an empty folder to create a new solution:

```bash
abp new Acme.IssueManagement -t module-pro
```

- `Acme.IssueManagement` is the solution name, like *YourCompany.YourProduct*. You can use single level, two-levels or three-levels naming.
- `-t module-pro` option specifies the template name.

## Without User Interface

Use `--no-ui` option to not include the UI layer. This is useful if you only want to create a service/microservice project.

## What's Next?

* [Module Startup Template Solution Structure](solution-structure.md)