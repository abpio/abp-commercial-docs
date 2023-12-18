# ABP Studio Running Applications

Running application in ABP Studio is a straightforward process to start one or more applications easier.

## Solution Runner

Navigate to the **Solution Runner** pane located in the left menu.

> The project structure might vary based on your selection. In an MVC microservice project, looks like the following;

![solution-runner](images/solution-runner/solution-runner.png)

The solution runner contains 4 different types to define tree structure.
- **Profile root** - `Acme.BookStore (Default)`.
- **Folder** - `apps`, `gateways`, `infrastructure`, `services`. 
- **C# Application** `Acme.BookStore.AuthServer`, `Acme.BookStore.Web`, `Acme.BookStore.WebGateway`, etc...
- **CLI Application** `Docker-Dependencies`

### Start

You can start CLI and C# applications, either click the arrow icon or navigate to the desired application, then select *Run* -> *Start* from the context menu. You can start multiple applications using a **folder** or the **profile root**. Starting a C# application doesn't build it automatically. Ensure that you've built your application before starting. Alternatively, you can choose *Run* -> *Build & Start* from the context menu.

### Stop

While the application is running, you can either click the stop icon or choose *Run* -> *Stop* from the context menu. You can stop multiple applications using a **folder** or the **profile root**. If the running application is a C# application, you should see a chain icon next to the application name, that means the started application connected to ABP Studio.

> C# application can connect to ABP Studio even when running from outside the Studio environment, for example using the `dotnet run` command. If the application is run from outside the Studio environment, it will display **(external)** information next to the chain icon.

### Restart

While the application is running, you can select *Run* -> *Restart* from the context menu. For C# application, you can also use *Build & Restart* to build the application before restarting.

### Watch

You can enable/disable watch for C# application with *Run* -> *Enable Watch* or *Run* -> *Disable Watch* from the context menu. If watch is enable C# application gonna start with [dotnet watch](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-watch) command.

## Profile

*TODO*