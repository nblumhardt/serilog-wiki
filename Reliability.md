Serilog takes the view that, all things considered, logging is a lower priority than other application code and should _never avoidably impact_ the operation of a running application.

In practise, this translates mostly into a strategy for handling exceptions throughout Serilog. Some trade-offs with usability are made in the process. This document explains what you can expect as a user of the Serilog library, and if you're extending Seriog, how to write code that works nicely with the rest of the codebase.

## Configuration

At configuration time, i.e. when calling methods on `LoggerConfiguration`, errors fall into two categories.

### Runtime configuration errors

If the sink cannot be configured because of runtime state on the host machine, Serilog will catch any resulting exceptions and write them to `SelfLog` (see [Debugging and Diagnostics]).

```csharp
// X: does not exist, but this is a runtime condition
// so Serilog will not fail here.
Log.Logger = new LoggerConfiguration()
    .WriteTo.File("X:\\log.txt")
    .CreateLogger();
```

This policy prevents transient issues in the deployment environment from causing an otherwise valid application to fail.

### Development-time invariant violations

An allowance is made at configuration time for code that _could never validly execute_, e.g. where the invariants of the API are voilated:

```csharp
// Null is never acceptable as a filename, so
// Seriog will throw ArgumentNullException here.
Log.Logger = new LoggerConfiguration()
    .WriteTo.File(null)
    .CreateLogger();
```

This decision is made for two reasons:

 * These errors are unlikely to make it past a developer's workstation or test environment, since logger configuration occurs on startup and should always fail in the same way
 * Allowing invalid values, or silently ignoring them, leads to unexpected behavior that can be hard to debug, makes the library harder to configure correctly

If you wish, you may wrap logger configuration code in a try/catch construct to avoid exception propagation here, but this *isn't recommended*.

**Sink authors:** the responsibility for implementing this is on the sink implementation itself, so needs to be explicitly considered/addressed.

## Writing log events

Events are written to the logging pipeline in phases. First the logger is called, then the event itself is constructed, next it is enriched, filters are applied, finally it is passed ('emitted') to configured sinks.

### Calling the logger

Methods on `ILogger` and the static `Log` class silently ignore invalid arguments:

```csharp
// Safely does nothing
Log.Warning(null);
```

This is done because logging statements in rarely-executed code paths may not be tested, and should not fail at runtime.

### Constructing log events

When a log event is constructed, Serilog may reflect over properties of any destructured objects.

If these properties throw, Serilog will catch the error, write to `SelfLog`, and include the error message instead of the property value in the destructured object.

#### A note on type loading

It's possible, if an application is deployed without required dependencies, that the loader will fail to find/construct a valid type. This is a major application configuration error, that may manifest during destructuring or, at a later point e.g. under the JIT. These are very rare. Serilog will do nothing in this case, allowing the error to propagate.

### Enrichers

Enrichers add properties to a log event. Enrichers may throw exceptions: Serilog will catch these and write to `SelfLog`.

**Enricher authors:** Serilog itself implements this policy, enrichers should throw exceptions if they fail in an unexpected way (though avoiding this is wise for performance reasons).

### Filters

Filters determine which events are passed down the logging pipeline. Filters will not throw exceptions, instead writing to `SelfLog`:

```csharp
// No events will be carried through this pipeline, but no
// errors will be thrown.
Log.Logger = new LoggerConfiguration()
    .Filter.ByExcluding(e => { throw new Exception(); })
    .WriteTo.ColoredConsole()
    .CreateLogger();
```

**Filter authors:** it is considered a bug if a filter throws exceptions unexpectedly - the performance and functionality impact of this is significant.

### Emitting to sinks

Serilog catches and writes to `SelfLog` any exceptions raised by by sinks. These are by far the majority of exceptions that occur during normal Serilog use.

**Sink authors:** sinks should throw exceptions when they fail. Serilog will catch and handle these consistently.

#### A note on uncatchable exceptions

It's important to note, there are still a class of uncatchable exceptions that Serilog is forced to propagate: `StackOverflowException` and so-on.

## Asynchronous/batched network operations

Many Serilog sinks use the same underlying `PeriodicBatchingSink` infrastructure. These sinks (including for example the batching Azure Table Storage sink, CouchDB sink, RavenDB sink and Seq sink (in non-durable mode) buffer log events to reduce the number of network round-trips that must be made to carry log data to a remote host.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.CouchDB("http://example.com/missing")
    .CreateLogger()
```

These sinks never fail when events are written, but may fail to asynchronously send a batch in the background. When a batch fails, details are written to `SelfLog`.

The batch being sent will be held in memory, and will be re-tried at an increasing interval that steps up from 5 seconds to 10 minutes. The increasing interval protects the receiver from a flood of connections when it comes back online after a period of downtime.

If the batch cannot be sent after 4 such attempts, it will be dropped and a new batch attempted. This protects against a "bad" event rejected by the receiver from clogging the logger. A subsequent success will allow other batches to continue transmission normally.

If two more attempts fail (totalling 6 failed attempts, generally around the 10 minute mark) the entire buffer of waiting log events will be dropped. This protects against out-of-memory errors when log events cannot be delivered for a long time.

If the connection remains broken, the buffer will be flushed at 10 minute intervals until the connection is re-established.

**Sink authors:** by deriving from `PeriodicBatchingSink` this behavior is provided by default. Implementing a custom `ILogEventSink` is necessary if different behavior is required.