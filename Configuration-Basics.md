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

### Minimum Level

## Enrichers

## Filters

## Sub-loggers
