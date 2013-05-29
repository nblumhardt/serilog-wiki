Serilog uses a simple C# API to configure logging.

**App.config Support** - in the future, an external configuration provider may be built, but if you require XML configuration today the best option is to retrieve logger settings by hand from `ConfigurationManager`.

## Creating a Logger

Loggers are created using a `LoggerConfiguration` object:

```
Log.Logger = new LoggerConfiguration().CreateLogger();
Log.Information("No one listens to me!");
```

The example above will create a logger that does not record events anywhere. To see log events, a _sink_ must be configured.

## Sinks

Log event sinks generally record log events to some external representation, typically the console, a file or data store.

Sinks are configured using the `WriteTo` configuration object.

```
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .CreateLogger();

Log.Information("Ah, there you are!");
```

Multiple sinks can be active at the same time. Adding additional sinks is a simple as chaining `WriteTo` blocks:

```
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .WriteTo.RollingFile(@"C:\Log.txt")
    .CreateLogger();
```

### Output Templates

Each sink is provided with an output template that controls how the sink renders events. This template uses the same format as the message templates that go with log events themselves, and can be specified during configuration.

## Minimum Level

Serilog implements the common concept of a 'minimum level' for log event processing.

```
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Debug()
    .WriteTo.ColoredConsole()
    .CreateLogger();
```

The `MinimumLevel` configuration object provides for one of the log event levels to be specified as the minimum. In the example above, log events with level `Debug` and higher will be processed and ultimately written to the console.

**Default Level** - if no `MinimumLevel` is specified, then `Information` level events and higher will be processed.

### Overriding per Sink

Sometimes it is desirable to write detailed logs to one medium, but less detailed logs to another.

```
Log.Logger = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .WriteTo.MongoDB("mongo://myserver/logs", minimumLevel: LogEventLevel.Warning)
    .CreateLogger();
```

In this example informational logs will be written to the console (the default) while only `Warning` level logs and higher will be written to MongoDB.

All provided sinks support the `minimumLevel` configuration parameter.

**Logger vs. Sink Minimums** - it is important to realise that the logging level can only be raised for sinks, not lowered. So, if the logger's `MinimumLevel` is set to `Information` then a sink with `Debug` as its specified level will still only see `Information` level events. This is because the logger-level configuration controls which logging statements will result in the creation of events, while the sink-level configuration only filters these. To create a single logger with a more verbose level, use a separate `LoggerConfiguration`.

## Enrichers

## Filters

## Sub-loggers