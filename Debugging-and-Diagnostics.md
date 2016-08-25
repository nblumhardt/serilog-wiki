When Serilog is not behaving as you expect, this may be caused by an internal exception or configuration issue. Here are a couple of ways to sort things out.

## SelfLog

First, Serilog will write simple diagnostic messages to user-specified output if provided. Call `SelfLog.Enable()` at program startup:

```csharp
Serilog.Debugging.SelfLog.Enable(msg => Debug.WriteLine(msg));
```

The system console, a file or an in-memory `StringWriter` can all be used to collect Serilog's output by providing a `TextWriter` instead of a delegate:

```csharp
Serilog.Debugging.SelfLog.Enable(Console.Error);
```

Serilog never write sits own events to user-defined sinks.

**Warning**: `SelfLog` does not perform any synchronization over the provided `TextWriter`. For most implementations you should use the `TextWriter.Synchronized()` method to ensure the object being passed in can be written from multiple threads:

```csharp
var file = File.CreateText(...);
Serilog.Debugging.SelfLog.Enable(TextWriter.Synchronized(file));
```

## Debug Symbols

Most Serilog packages include debug symbols (_.PDB) on http://symbolsource.org - adding it as a symbol server in Visual Studio can help when determining the cause of an exception from a sink.