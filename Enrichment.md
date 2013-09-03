Log events can be _enriched_ with properties in various ways. Configuration for enrichment is done via the `Enrich` configuration property:

```
var log = new LoggerConfiguration()
    .Enrich.WithThreadId()
    .WriteTo.Console()
    .CreateLogger();
```

All events written through `log` will carry a property `ThreadId` with the id of the managed thread that wrote them. (By convention, any `.WithXyz()` methods on `Enrich` create properties named `Xyz`.)

## The `LogContext`

