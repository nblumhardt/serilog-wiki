Serilog uses a simple C# API to configure logging.

**App.config Support** - in the future, an external configuration provider may be built, but if you require XML configuration today the best option is to retrieve logger settings by hand from `ConfigurationManager`.

## Creating a Logger

Loggers are created using a `LoggerConfiguration` object:

```csharp
Log.Logger = new LoggerConfiguration().CreateLogger();
Log.Information("No one listens to me!");
```

The example above will create a logger that does not record events anywhere. To see log events, a _sink_ must be configured.

## Sinks

Log event sinks generally record log events to some external representation, typically the console, a file or data store.

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

**Default Level** - if no `MinimumLevel` is specified, then `Information` level events and higher will be processed.

### Overriding per Sink

Sometimes it is desirable to write detailed logs to one medium, but less detailed logs to another.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .WriteTo.MongoDB("mongo://myserver/logs", minimumLevel: LogEventLevel.Warning)
    .CreateLogger();
```

In this example informational logs will be written to the console (the default) while only `Warning` level logs and higher will be written to MongoDB.

All provided sinks support the `minimumLevel` configuration parameter.

**Logger vs. Sink Minimums** - it is important to realise that the logging level can only be raised for sinks, not lowered. So, if the logger's `MinimumLevel` is set to `Information` then a sink with `Debug` as its specified level will still only see `Information` level events. This is because the logger-level configuration controls which logging statements will result in the creation of events, while the sink-level configuration only filters these. To create a single logger with a more verbose level, use a separate `LoggerConfiguration`.

## Enrichers

Enrichers are simple components that add, remove or modify the properties attached to a log event. This can be used for the purpose of attaching a thread id to each event, for example.

```csharp
class ThreadIdEnricher : ILogEventEnricher
{
    public void Enrich(LogEvent logEvent, ILogEventPropertyFactory propertyFactory)
    {
        logEvent.AddPropertyIfAbsent(
            propertyFactory.CreateProperty("ThreadId", Thread.CurrentThread.ManagedThreadId));
    }
}
```

Enrichers are added using the `Enrich` configuration object.

```csharp
Log.Logger = new LoggerConfiguration()
    .Enrich.With(new ThreadIdEnricher())
    .WriteTo.ColoredConsole(
        outputTemplate: "{Timestamp:HH:mm} [{Level}] ({ThreadId}) {Message:l}{NewLine:l}{Exception:l}")
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
        .WriteTo.CouchDB("http://mycouch/logs"))
    .CreateLogger();
```

Sub-loggers incur some performance cost; if this is problematic, creating multiple top-level loggers is the recommended alternative.