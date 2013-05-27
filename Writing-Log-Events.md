Log events are written to sinks using the `Log` static class, or the methods on an `ILogger`. These examples will use `Log` for syntactic brevity, but the same methods shown below are available also on the interface.

```
Log.Warning("Disk quota {Quota} exceeded by {User}", quota, user);
```

**Fluent Style Guideline** - good Serilog events use the names of properties as content within the message as in the `User` example above. This improves readability and makes events more compact.

**Sentences vs. Fragments** - log event messages are fragments, not sentences; for consistency with other libraries that use Serilog, avoid a trailing period/full stop when possible.

## Message Templates

## Log Event Levels

## Level Detection

## Source Contexts

## Correlation
