The _Serilog.Extras.AppSettings_ integrates Serilog configuration with `App.config` and `Web.config` files to set the minimum level and control log output.

Serilog is primarily configured using code, but while the AppSettings package doesn't provide a comprehensive XML-based configuration system, most common logger configuration tasks can be achieved with it.

## Installing the package

To include AppSettings support in a project, install the _Serilog.Extras.AppSettings_ package from NuGet.

```
PM> Install-Package Serilog.Extras.AppSettings
```

## Enabling AppSettings configuration

To read configuration from AppSettings use the `ReadAppSettings()` extension method on your `LoggerConfiguration`:

```
Log.Logger = new LoggerConfiguration()
  .ReadAppSettings()
  ... // Other configuration here, then
  .CreateLogger()
```

You can mix and match XML and code-based configuration, but each sink must be configured either using XML or in code - sinks added in code can't be modified via AppSettings.

## Configuring the logger

To configure the logger, an `<appSettings>` element should be included in the program's _App.config_ or _Web.config_ file.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <appSettings>
    <add key="serilog:minimum-level" value="Verbose" />
    <!-- More settings here -->
```

### Setting the minimum level

To set the logging level for the app use the `serilog:minimum-level` setting key. 

```xml
    <add key="serilog:minimum-level" value="Verbose" />
```

Valid values are those defined in the `LogEventLevel` enumeration: `Verbose`, `Debug`, `Information`, `Warning`, `Error`, `Fatal`.

### Adding a sink

Sinks are added with the `serilog:write-to` key. The setting name matches the configuration method name that you'd use in code, so the following are equivalent:

```csharp
    .WriteTo.ColoredConsole()
```

In XML:

```xml
    <add key="serilog:write-to:ColoredConsole" />
```

If the sink accepts parameters, these are specified by appending the parameter name to the setting.

```csharp
    .WriteTo.RollingFile(@"C:\Logs\myapp-{Date}.txt", retainedFileCountLimit: 10)
```

In XML:

```xml
    <add key="serilog:write-to:RollingFile.pathFormat" value="C:\Logs\myapp-{Date}.txt" />
    <add key="serilog:write-to:RollingFile.retainedFileCountLimit" value="10" />
```

### Using extensions from additional assemblies

To use sinks from additional assemblies, specify them with the `serilog:using` key.

For example, to use configuration from the `Serilog.Sinks.EventLog` assembly:

```xml 
    <add key="serilog:using" value="Serilog.Sinks.EventLog" />
    <add key="serilog:write-to:EventLog.source" value="Serilog Demo" />
```
