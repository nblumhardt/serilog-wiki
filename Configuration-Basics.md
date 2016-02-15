Serilog uses a simple **C# API** to configure logging. When XML configuration is desirable it can be mixed in sparingly using the [[AppSettings]] package.

## Creating a Logger

Loggers are created using a `LoggerConfiguration` object:

```csharp
Log.Logger = new LoggerConfiguration().CreateLogger();
Log.Information("No one listens to me!");
```

The example above will create a logger that does not record events anywhere. To see log events, a _sink_ must be configured.

## Sinks

Log event sinks generally record log events to some external representation, typically the console, a file or data store.

> As of Serilog 2.0 it will be necessary to `Install-Package Serilog.Sinks.ColoredConsole` for this example to work.

Sinks are configured using the `WriteTo` configuration object.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .CreateLogger();

Log.Information("Ah, there you are!");
```

Multiple sinks can be active at the same time. Adding additional sinks is a simple as chaining `WriteTo` blocks:

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .WriteTo.RollingFile(@"C:\Log-{Date}.txt")
    .CreateLogger();
```

### Output Templates

Each sink is provided with an output template that controls how the sink renders events. This template uses the same format as the message templates that go with log events themselves, and can be specified during configuration.

## Minimum Level

Serilog implements the common concept of a 'minimum level' for log event processing.

```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Debug()
    .WriteTo.ColoredConsole()
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

### Overriding per Sink

Sometimes it is desirable to write detailed logs to one medium, but less detailed logs to another.

```csharp
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Debug()
    .WriteTo.RollingFile(@"C:\Logs\myapp-{Date}.txt")
    .WriteTo.ColoredConsole(restrictedToMinimumLevel: LogEventLevel.Information)
    .CreateLogger();
```

In this example debug logs will be written to the rolling file, while only `Information` level logs and higher will be written to the console.

All provided sinks support the `restrictedToMinimumLevel` configuration parameter.

**Logger vs. Sink Minimums** - it is important to realise that the logging level can only be raised for sinks, not lowered. So, if the logger's `MinimumLevel` is set to `Information` then a sink with `Debug` as its specified level will still only see `Information` level events. This is because the logger-level configuration controls which logging statements will result in the creation of events, while the sink-level configuration only filters these. To create a single logger with a more verbose level, use a separate `LoggerConfiguration`.

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
    .WriteTo.ColoredConsole(outputTemplate:
        "{Timestamp:HH:mm} [{Level}] ({ThreadId}) {Message}{NewLine}{Exception}")
    .CreateLogger();
```

The configuration above shows how a property added by an enricher can be used in output formatting.

If the enriched property value is constant throughout the application run, the shortcut `WithProperty` method can be used to simplify configuration.

```csharp
Log.Logger = new LoggerConfiguration()
    .Enrich.WithProperty("Version", "1.0.0")
    .WriteTo.ColoredConsole()
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
    .WriteTo.ColoredConsole()
    .WriteTo.Logger(lc => lc
        .Filter.ByIncludingOnly(...)
        .WriteTo.RollingFile(@"C:\Logs\myapp-{Date}.txt"))
    .CreateLogger();
```

For scenarios not handled well by sub-loggers, it's fine to create multiple independent top-level pipelines. Only one pipeline can be assigned to `Log.Logger`, but your app can use as many additional `ILogger` instances as it requires.