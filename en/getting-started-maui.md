# Getting Started with the MAUI

ABP Commercial platform provides a basic [MAUI](https://docs.microsoft.com/en-us/dotnet/maui/what-is-maui) template to develop mobile applications **integrated to your ABP based backends**.

## Run the Server Application

Run the backend application as described in the [getting started document](getting-started.md).

Open the `appsettings.json` in the `MAUI` project:

{{ if Tiered == "Yes" }}

* Make sure that `Authority` matches the running address of the `.AuthServer` project, `BaseUrl` matches the running address of the `.HttpApi.Host` project.

{{else}}

* Make sure that `Authority` and `BaseUrl` matches the running address of the `.HttpApi.Host` or `.Web` project.

{{ end }}

### Configure Port Forward

The emulator or a physical phone **cannot connect to the backend** on `localhost`. To fix this problem, we need to configure port forward.

#### Android

Open a command line terminal and run the [`adb forward`](https://developer.android.com/studio/command-line/adb#forwardports) command to forwards backend application port on a emulator. For exampe:

`adb forward tcp:44305: tcp:44305`
