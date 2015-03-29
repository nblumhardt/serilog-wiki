There are a couple of extra packages for Serilog which you can get from NuGet. They extend Serilog for a certain use case or application type. Add a package by installing it using the NuGet package manager, like this:

```
PM> Install-Package Serilog.Extras.AppSettings
```

### Serilog.Extras.AppSettings

Provides a simple XML configuration syntax.

For more information about the AppSettings package, see the [[dedicated page|AppSettings]].

### Serilog.Extras.Web

When you work with an ASP.NET web application, this package adds additional enrichers and an `HttpModule` to enhance the logging output. The following enrichers are available:

*  **HttpRequestId** A GUID used to identify requests.
*  **HttpRequestNumber** an incrementing number per request.
*  **HttpRequestTraceId** GUID matching the RequestTraceIdentifier assigned by IIS and used throughout ASP.NET/ETW. IIS ETW tracing must be enabled for this to work.
*  **HttpSessionId** The current ASP.NET session id.
*  **UserName** The current username or, when anonymous, a defined value. By default this is set to _(anonymous)_.

**Package** - [[Serilog.Extras.Web|http://nuget.org/packages/serilog.extras.web]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .Enrich.With<HttpRequestIdEnricher>()
    .Enrich.With<UserNameEnricher>()
    .CreateLogger();
```

To override the username enricher behaviour:

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .Enrich.With(new UserNameEnricher("not known yet", System.Environment.UserName))
    .CreateLogger();
```

The **ApplicationLifecycleModule** will automatically be enabled and will write information events about the current method and url that is being accessed. Optionally you also store any form data that is posted to the server.
When an unhandled exception occurs, the module will capture it and log it as an error event.

To enable the posting of form data:

```
ApplicationLifecycleModule.DebugLogPostedFormData = true;
```

If you want to disable the logging completely, use the following statement:

```
ApplicationLifecycleModule.IsEnabled = false;
```
