# ABP Studio Monitoring Applications

ABP Studio offers a comprehensive centralized monitoring solution, enabling you to oversee all applications from a single interface. Upon selecting the [solution runner](./solution-explorer.md) from the menu, monitoring tabs are automatically opened in the central interface. This seamless integration enables instant access to monitoring features while utilizing the [solution runner](./solution-explorer.md). You can start the applications for monitoring.

## Overall

You can see the overall information for C# applications in this tab. Such as current application state, how many instance running, uptime etc. You can start/stop the appliation in the *Action* column instead using [solution runner](./solution-explorer.md).

## Browse

ABP Studio includes a browser tool that allows to access websites and running applications. You can open new tabs to browse different websites or view active applications. It's a convenient utility access to web sites and applications without leaving ABP Studio. You can inspect the web page content using *Dev Tools*.

## HTTP Requests

Within this tab, you can view all *HTTP Requests* received by your applications. Clicking on a row enables you to view the details of each HTTP request. You have the option to filter requests using the search textbox or by selecting a specific application from the combobox. Additionally, clicking the gear icon allows you to ignore specific URLs by applying a regex pattern. The *Clear Requests* button removes all received requests.

## Events

In this tab, you can view all *Events* sent or received by your applications. Clicking on a row enables you to view the details of each event. You can filter them using the search textbox or by selecting a specific application. Additionally, you can choose the direction and source (inbox/outbox) of events. The *Clear Events* button removes all events.

## Exceptions

This tab displays all exceptions. Click on a row to inspect the details of each exception. You can filter them using the search textbox or by selecting a specific application. The *Clear Exceptions* button removes all exceptions.

## Logs

You can view the all logs in *Logs* tab. To see the logs you have to choice an application also you can add filter using the search textbox or selecting a specific log level. The *Clear* button removes all logs. When new logs coming it's automacilly scrolls if the *Auto Scroll* checked.
