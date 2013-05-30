Log events are written to sinks using the `Log` static class, or the methods on an `ILogger`. These examples will use `Log` for syntactic brevity, but the same methods shown below are available also on the interface.

```
Log.Warning("Disk quota {Quota} MB exceeded by {User}", quota, user);
```

The warning event created from this log method will have two associated properties, `Quota` and `User`. Assuming `quota` is an integer, and `user` a string, the rendered message may look like the one below.

```
Disk quota 1024 MB exceeded by "nblumhardt"
```

(Serilog renders `string` values in double quotes to more transparently indicate the underlying data type, and to make the property value stand out from the surrounding message text. To omit the quotes, use the `:l` literal format specifier on the property, e.g. `{User:l}`.)

### Message Template Syntax

The string above `"Disk quota {Quota} exceeded by {User}"` is a Serilog _message template_. Message templates are a superset of standard .NET format strings, so any format string acceptable to `string.Format()` will also be correctly processed by Serilog.

* Property names are written between `{` and `}` brackets
* Brackets can be escaped by doubling them, e.g. `{{` will be rendered as `{`
* Formats that use numeric property names, like `{0}` and `{1}` exclusively, will be matched with the log method's parameters by treating the property names as indexes; this is identical to `string.Format()`'s behaviour
* If any of the property names are non-numeric, then all property names will be matched from left-to-right with the log method's parameters
* Property names may be prefixed with an optional operator, `@` or `$`, to control how the property is serialised
* Property names may be suffixed with an optional format, e.g. `:000`, to control how the property is rendered; these format strings behave exactly as their counterparts within the `string.Format()` syntax

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

Serilog uses levels as the primary means for assigning importance to log events. The levels in increasing order of importance are:

1. **Verbose** - tracing information and debugging minutiae; generally only switched on in unusual situations
2. **Debug** - internal control flow and diagnostic state dumps to facilitate pinpointing of recognised problems
3. **Information** - events of interest or that have relevance to outside observers; the default enabled minimum logging level
4. **Warning** - indicators of possible issues or service/functionality degradation
5. **Error** - indicating a failure within the application or connected system
6. **Fatal** - critical errors causing complete failure of the application

### The role of the Information level

The _Information_ level is unlike the other specified levels - it has no specified semantics and in many ways expresses the absence of other levels.

Because Serilog allows the event stream from the application to be processed or analysed, the _Information_ level can be thought of as a synonym for _event_. That is, most interesting application event data should be logged at this level.

### Level Detection

In most cases, applications should write events without checking the active logging level. Level checking is extremely cheap and the overhead of calling disabled logger methods very low.

In _rare, performance-sensitive_ cases, the recommended pattern for level detection is to store the results of level detection in a field, for example:

```
readonly bool _isDebug = Log.IsLevelEnabled(LogEventLevel.Debug);
```

The `_isDebug` field can be checked efficiently before writing log events:

```
if (_isDebug) Log.Write("Someone is stuck debugging...");
```

## Source Contexts

Serilog, like most .NET logging frameworks, allows events to be tagged with their source, generally speaking the name of the class writing them:

```
var myLog = Log.ForContext<MyClass>();
myLog.Information("Hello!");
```

The event written will include a property `SourceContext` with value `"MyNamespace.MyClass"` that can later be used to filter out noisy events, or selectively write them to particular sinks. For more on filters and logger topology see [[Configuration Basics|Configuration-Basics]].

## Correlation

Just as `ForContext<T>()` tags log events with the class that wrote them, other overloads of `ForContext()` enable log events to be tagged with identifiers that later support correlation of the events written with that identifier.

```
var job = GetNextJob();
var jobLog = Log.ForContext("JobId", job.Id);
jobLog.Information("Running a new job");
job.Run();
jobLog.Information("Finished");
```

Here both of the log events will carry the `JobId` property including the job identifier.