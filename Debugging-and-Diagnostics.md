When Serilog is not behaving as you expect, this may be caused by an internal exception or configuration issue. Here are a couple of ways to sort things out.

## SelfLog

First, Serilog will write simple diagnostic messages to a text writer if provided. Set the `SelfLog.Out` property at program startup:

```
Serilog.Debugging.SelfLog.Out = Console.Out;
```

The system console, a file or an in-memory `StringWriter` can all be used to collect Serilog's output. Serilog will never write its own events to user-defined sinks.

## Debug Symbols

Most Serilog packages include debug symbols (_.PDB) on http://symbolsource.org - adding it as a symbol server in Visual Studio can help when determining the cause of an exception from a sink.