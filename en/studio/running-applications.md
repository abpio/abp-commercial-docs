# ABP Studio Running Applications

Running applications in ABP Studio is a straightforward process to start one or more application(s) easier.

## Solution Runner

Navigate to the **Solution Runner** pane located in the left menu.

> The project structure might vary based on your selection. In an MVC microservice project, looks like the following;

![solution-runner](images/solution-runner/solution-runner.png)

### Start

To start an application, either click the arrow icon or navigate to the desired application, then select *Run* -> *Start* from the context menu. You can start multiple applications using a folder or the root solution. Starting an application doesn't build it automatically. Ensure that you've built your application before starting. Alternatively, you can choose *Run* -> *Build & Start* from the context menu.

> You can also run CLI commands with similiar steps. Such as `Docker-Dependencies`.

### Stop

While the application is running, you can either click the stop icon or choose *Run* -> *Stop* from the context menu. You can stop multiple applications using a folder or the root solution. If the running application is a C# application, you should see a chain icon next to the application name, that means the started application connected to ABP Studio.

> C# applications can connect to ABP Studio even when running from outside the Studio environment, for example using the `dotnet run` command. If the application is run from outside the Studio environment, it will display **(external)** information next to the chain icon.