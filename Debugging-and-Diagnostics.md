When Serilog is not behaving as you expect, this may be caused by an internal exception or configuration issue. Here are a couple of ways to sort things out.

## SelfLog

First, Serilog will write simple diagnostic messages to a text writer if provided. Set the `SelfLog.Out` property at program startup:

```csharp
Serilog.Debugging.SelfLog.Out = Console.Out;
```

The system console, a file or an in-memory `StringWriter` can all be used to collect Serilog's output. Serilog will never write its own events to user-defined sinks.

**Warning**: `SelfLog` does not perform any synchronization over the provided `TextWriter`. For most implementations you should use the `TextWriter.Synchronized()` method to ensure the object being passed in can be written from multiple threads:

```csharp
var file = File.CreateText(...);
Serlog.Debugging.SelfLog.Out = TextWriter.Synchronized(file);
```

## Debug Symbols

Most Serilog packages include debug symbols (_.PDB) on http://symbolsource.org - adding it as a symbol server in Visual Studio can help when determining the cause of an exception from a sink.