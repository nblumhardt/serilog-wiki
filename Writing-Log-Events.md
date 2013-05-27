Log events are written to sinks using the `Log` static class, or the methods on an `ILogger`. These examples will use `Log` for syntactic brevity, but the same methods shown below are available also on the interface.

```
Log.Warning("Disk quota {Quota} exceeded by {User}", quota, user);
```

### Message Template Recommendations

**Fluent Style Guideline** - good Serilog events use the names of properties as content within the message as in the `User` example above. This improves readability and makes events more compact.

**Sentences vs. Fragments** - log event messages are fragments, not sentences; for consistency with other libraries that use Serilog, avoid a trailing period/full stop when possible.

**Templates vs. Messages** - Serilog events have a message template associated, _not_ a message. Internally, Serilog parses and caches every template (up to a fixed size limit). Treating the string parameter to log methods as a message, as in the case below, will degrade performance and consume cache memory.

```
// Don't:
Log.Information("The time is " + DateTime.Now);
```

Instead, _always_ use template properties to include variables in messages:

```
// Do:
Log.Information("The time is {Now}", DateTime.Now);
```

## Log Event Levels

Serilog uses levels as the primary means for assigning importance to log events.

## Level Detection

## Source Contexts

## Correlation
