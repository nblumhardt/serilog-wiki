Serilog uses a simple **C# API** to configure logging. When XML configuration is desirable it can be mixed in sparingly using the [[AppSettings]] package.

## Creating a logger

Loggers are created using a `LoggerConfiguration` object:

```csharp
Log.Logger = new LoggerConfiguration().CreateLogger();
Log.Information("No one listens to me!");
```

The example above will create a logger that does not record events anywhere. To see log events, a _sink_ must be configured.

## Sinks

Log event sinks generally record log events to some external representation, typically the console, a file or data store. Serilog sinks are distributed via NuGet. A [curated list of available sinks](https://github.com/serilog/serilog/wiki/Provided-Sinks) is listed here on the wiki.

This example will use the colored [literate console](https://github.com/serilog/serilog-sinks-literate) package, which pretty-prints log data, and the [rolling file](https://github.com/serilog/serilog-sinks-rollingfile) package, which writes log events to a set of date-stamped text files.

```powershell
Install-Package Serilog.Sinks.Literate
Install-Package Serilog.Sinks.RollingFile
```

Sinks are configured using the `WriteTo` configuration object.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.LiterateConsole()
    .CreateLogger();

Log.Information("Ah, there you are!");
```

Multiple sinks can be active at the same time. Adding additional sinks is a simple as chaining `WriteTo` blocks:

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.LiterateConsole()
    .WriteTo.RollingFile("log-{Date}.txt")
    .CreateLogger();
```

### Output templates

Text-based sinks use _output templates_ to control formatting. this can be modified through the `outputTemplate` parameter:

```csharp
    .WriteTo.RollingFile("log-{Date}.txt",
        outputTemplate: "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level}] {Message}{NewLine}{Exception}")
```

The default template, shown in the example above, uses built-in properties like `Timestamp` and `Level`. Properties from events, including those attached using [enrichers](https://github.com/serilog/serilog/wiki/Enrichment), can also appear in the output template.

For more compact level names, use a format such as `{Level:u3}` or `{Level:w3}` for three-character upper- or lowercase level names, respectively.

## Minimum level

Serilog implements the common concept of a 'minimum level' for log event processing.

```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Debug()
    .WriteTo.LiterateConsole()
    .CreateLogger();
```

The `MinimumLevel` configuration object provides for one of the log event levels to be specified as the minimum. In the example above, log events with level `Debug` and higher will be processed and ultimately written to the console.

| Level | Usage |
| ------- | ------- |
| `Verbose` |  Verbose is the noisiest level, rarely (if ever) enabled for a production app. |
| `Debug` | Debug is used for internal system events that are not necessarily observable from the outside, but useful when determining how something happened. |
| `Information` | Information events describe things happening in the system that correspond to its responsibilities and functions. Generally these are the observable actions the system can perform. |
| `Warning` | When service is degraded, endangered, or may be behaving outside of its expected parameters, Warning level events are used. |
| `Error` | When functionality is unavailable or expectations broken, an Error event is used. |
| `Fatal` | The most critical level, Fatal events demand immediate attention. |

**Default Level** - if no `MinimumLevel` is specified, then `Information` level events and higher will be processed.

### Overriding per sink

Sometimes it is desirable to write detailed logs to one medium, but less detailed logs to another.

```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Debug()
    .WriteTo.RollingFile("log-{Date}.txt")
    .WriteTo.LiterateConsole(restrictedToMinimumLevel: LogEventLevel.Information)
    .CreateLogger();
```

In this example debug logs will be written to the rolling file, while only `Information` level logs and higher will be written to the console.

All provided sinks support the `restrictedToMinimumLevel` configuration parameter.

**Logger vs. sink minimums** - it is important to realize that the logging level can only be raised for sinks, not lowered. So, if the logger's `MinimumLevel` is set to `Information` then a sink with `Debug` as its specified level will still only see `Information` level events. This is because the logger-level configuration controls which logging statements will result in the creation of events, while the sink-level configuration only filters these. To create a single logger with a more verbose level, use a separate `LoggerConfiguration`.

## Enrichers

Enrichers are simple components that add, remove or modify the properties attached to a log event. This can be used for the purpose of attaching a thread id to each event, for example.

```csharp
class ThreadIdEnricher : ILogEventEnricher
{
    public void Enrich(LogEvent logEvent, ILogEventPropertyFactory propertyFactory)
    {
        logEvent.AddPropertyIfAbsent(propertyFactory.CreateProperty(
                "ThreadId", Thread.CurrentThread.ManagedThreadId));
    }
}
```

Enrichers are added using the `Enrich` configuration object.

```csharp
Log.Logger = new LoggerConfiguration()
    .Enrich.With(new ThreadIdEnricher())
    .WriteTo.LiterateConsole(outputTemplate:
        "{Timestamp:HH:mm} [{Level}] ({ThreadId}) {Message}{NewLine}{Exception}")
    .CreateLogger();
```

The configuration above shows how a property added by an enricher can be used in output formatting.

If the enriched property value is constant throughout the application run, the shortcut `WithProperty` method can be used to simplify configuration.

```csharp
Log.Logger = new LoggerConfiguration()
    .Enrich.WithProperty("Version", "1.0.0")
    .WriteTo.LiterateConsole()
    .CreateLogger();
```

Enrichers and the properties they attach are generally more useful with sinks that use structured storage, where the property values can be viewed and filtered.

## Filters

Events can be selectively logged by filtering. Filters are just predicates over `LogEvent`, with some common scenarios handled by the `Matching` class.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .Filter.ByExcluding(Matching.WithProperty<int>("Count", p => p < 10))
    .CreateLogger();
```

## Sub-loggers

Sometimes a finer level of control over what is seen by a sink is necessary. For this, Serilog allows a full logging pipeline to act as a sink.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.LiterateConsole()
    .WriteTo.Logger(lc => lc
        .Filter.ByIncludingOnly(...)
        .WriteTo.RollingFile("log-{Date}.txt"))
    .CreateLogger();
```

For scenarios not handled well by sub-loggers, it's fine to create multiple independent top-level pipelines. Only one pipeline can be assigned to `Log.Logger`, but your app can use as many additional `ILogger` instances as it requires.