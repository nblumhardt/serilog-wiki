Serilog mostly "just works" and doesn't require a lot of thought to be put into creating and disposing loggers. However since there are:

- background processes involved in some sinks, especially those that use the network, and
- resources held by some sinks, particularly `File` and `RollingFile`

some usage patterns work better and more reliably than others.

## In all apps

The easiest way to use Serilog is via the global `Log` class:

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.File(@"myapp\log.txt")
    .CreateLogger();

Log.Information("Hello!");

// Your application runs, then:

Log.CloseAndFlush();
```

If you do this, only configure the logger *once* and then use it for the lifetime of the application.

To create more specialized loggers:

* Call `Log.ForContext(...)` to receive an `ILogger` with additional properties attached; this doesn't need any special close/flush logic, as this will be handled by the parent logger
* In rare cases, create a separate additional `ILogger` using a separate `LoggerConfiguration` and use `WriteTo.Logger(Log.Logger)` to pipe events to the root logger; in this case the disposal logic below must be followed

## Without using `Log`

If you do not wish to use the static `Log` class, you will use `LoggerConfiguration` to create an `ILogger`.

```csharp
using (var log = new LoggerConfiguration()
        .WriteTo.File(@"myapp\log.txt")
        .CreateLogger())
{
    log.Information("Hello again!");

    // Your app runs, then disposal of `log` flushes any buffers
}
```

In this case, `Log.CloseAndFlush()` is not used. Instead, when the application no longer needs the logger it is disposed.

Only the root logger created from a `LoggerConfiguration` needs to be treated this way. `ILogger`s returned from `ForContext()` and similar methods don't need any special treatment.

## Using an IoC container

See the [Autofac integration example](https://github.com/nblumhardt/autofac-serilog-integration) showing how injectable `ILogger`s can be used with the [Autofac](http://autofac.org) IoC container. Please raise an issue to update this page with instructions for alternative containers.