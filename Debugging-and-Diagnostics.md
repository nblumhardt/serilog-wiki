When Serilog is not behaving as you expect, this may be caused by an internal exception or configuration issue. Here are a couple of ways to sort things out.

## SelfLog

First, Serilog will write simple diagnostic messages to a text writer if provided. Set the `SelfLog.Out` property at program startup:

```
Serilog.Debugging.SelfLog.Out = Console.Out;
```

The system console, a file or an in-memory `StringWriter` can all be used to collect Serilog's output. Serilog will never write its own events to user-defined sinks.

## Printing the Logger Configuration

A string dump of the logger configuration/sinks/hierarchy is planned but not implemented.