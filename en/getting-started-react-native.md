# Getting Started with the React Native

ABP Commercial platform provides a basic [React Native](https://reactnative.dev/) template to develop mobile applications **integrated to your ABP based backends**.

When you **create a new application** as described in the [getting started document](getting-started.md), the solution includes the React Native application in the `react-native` folder as default.

## Configure Your Local IP Address

A React Native application running on an Android emulator or a physical phone **cannot connect to the backend** on `localhost`. To fix this problem, it is necessary to run backend on your **local IP address**.

{{ if Tiered == "No"}}
![React Native host project local IP entry](D:/GitHub/abp-commercial-docs/en/images/rn-host-local-ip.png)

* Open the `appsettings.json` in the `.HttpApi.Host` folder. Replace the `localhost` address on the `SelfUrl` and `Authority` properties with your local IP address.
* Open the `launchSettings.json` in the `.HttpApi.Host/Properties` folder. Replace the `localhost` address on the `applicationUrl` properties with your local IP address.

{{ else if Tiered == "Yes" }}

![React Native tiered project local IP entry](D:/GitHub/abp-commercial-docs/en/images/rn-tiered-local-ip.png)

* Open the `appsettings.json` in the `.IdentityServer` folder. Replace the `localhost` address on the `SelfUrl` property with your local IP address.
* Open the `launchSettings.json` in the `.IdentityServer/Properties` folder. Replace the `localhost` address on the `applicationUrl` properties with your local IP address.
* Open the `appsettings.json` in the `.HttpApi.Host` folder. Replace the `localhost` address on the `Authority` property with your local IP address.
* Open the `launchSettings.json` in the `.HttpApi.Host/Properties` folder. Replace the `localhost` address on the `applicationUrl` properties with your local IP address.

{{ end }}

## Run the Server Application

Run the backend application as described in the [getting started document](getting-started.md).

> React Native application does not trust the auto-generated .NET HTTPS certificate, you should use the HTTP during development.

Go to the `react-native` folder, open a command line terminal, type the `yarn` command (we suggest to the [yarn](https://yarnpkg.com/) package manager while `npm install` will also work in most cases):

```bash
yarn
```

* Open the `Environment.js` in the `react-native` folder and replace the `localhost` address on the `apiUrl` and `issuer` properties with your local IP address as shown below:

![react native environment local IP](D:/GitHub/abp-commercial-docs/en/images/rn-environment-local-ip.png)

{{ if Tiered == "Yes" }}

> Make sure that `issuer` matches the running address of the `.IdentityServer` project, `apiUrl` matches the running address of the `.HttpApi.Host` project.

{{else}}

> Make sure that `issuer` and `apiUrl` matches the running address of the `.HttpApi.Host` or `.Web` project.

{{ end }}

Once all node modules are loaded, execute `yarn start` (or `npm start`) command:

```bash
yarn start
```

Wait Expo CLI to start. Expo CLI opens the management interface on the `http://localhost:19002/` address.

![expo-interface](D:/GitHub/abp-commercial-docs/en/images/expo-interface.png)

In the above management interface, you can start the application with an Android emulator, an iOS simulator or a physical phone by the scan the QR code with the [Expo Client](https://expo.io/tools#client).

> See the [Android Studio Emulator](https://docs.expo.io/workflow/android-studio-emulator), [iOS Simulator](https://docs.expo.io/workflow/ios-simulator) documents on expo.io.

![React Native login screen on iPhone 11](D:/GitHub/abp-commercial-docs/en/images/rn-login-iphone.png)

Enter **admin** as the username and **1q2w3E*** as the password to login to the application:

![React Native dashboard screen on iPhone 11](D:/GitHub/abp-commercial-docs/en/images/rn-dashboard-iphone.png)

The application is up and running. You can continue to develop your application based on this startup template.
