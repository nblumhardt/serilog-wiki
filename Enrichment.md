Log events can be _enriched_ with properties in various ways. Configuration for enrichment is done via the `Enrich` configuration property:

```
var log = new LoggerConfiguration()
    .Enrich.WithThreadId()
    .WriteTo.Console()
    .CreateLogger();
```

All events written through `log` will carry a property `ThreadId` with the id of the managed thread that wrote them. (By convention, any `.WithXyz()` methods on `Enrich` create properties named `Xyz`.)

## The `LogContext`

`Serilog.Context.LogContext` can be used to dynamically add and remove properties from the ambient "execution context"; for example, all messages written during a transaction might carry the id of that transaction, and so-on.

This feature must be added to the logger at configuration-time using `.FromLogContext()`:

```
var log = new LoggerConfiguration()
    .Enrich.FromLogContext()
```

Then, properties can be added and removed from the context using `LogContext.PushProperty()`:

```
log.Information("No contextual properties");

using (LogContext.PushProperty("A", 1))
{
    log.Information("Carries property A = 1");

    using (LogContext.PushProperty("A", 2))
    using (LogContext.PushProperty("B", 1))
    {
        log.Information("Carries A = 2 and B = 1");
    }

    log.Information("Carries property A = 1, again");
}
```

Pushing property onto the context will override any existing properties with the same name, until the object returned from `PushProperty()` is disposed, as the property `A` in the example demonstrates.

**Important:** properties _must_ be popped from the context in the precise order in which they were added. Behavior otherwise is undefined.