The following example uses the `dotnet` command to create a project.

## Create the project

```
mkdir SimpleSink
cd SimpleSink
dotnet new console
```

### Add Dependencies

* Add the Serilog Package from NuGet

```
dotnet add package serilog
```
## Building a Simple Sink

Include the following `using` statements.  These are used by the sink class and also the configuring Serilog.

```csharp
using Serilog;
using Serilog.Core;
using Serilog.Events;
using Serilog.Configuration;

```

### Create the Sink

A sink is simply a class that implements the `ILogEventSink` interface.  The following example renders every message regardless of log level to the console.

```csharp
public class MySink : ILogEventSink
{
    private readonly IFormatProvider _formatProvider;

    public MySink(IFormatProvider formatProvider)
    {
        _formatProvider = formatProvider;
    }

    public void Emit(LogEvent logEvent)
    {
        var message = logEvent.RenderMessage(_formatProvider);
        Console.WriteLine(DateTimeOffset.Now.ToString() + " "  + message);
    }
}
```

### Extensions for configuration

A pattern often used when configuring a sink, is to provide an extension method class for the `LoggerSinkConfiguration`.  The following code illustrates this approach by exposing a `MySink` option when configuring Serilog.

```csharp
public static class MySinkExtensions
{
    public static LoggerConfiguration MySink(
              this LoggerSinkConfiguration loggerConfiguration,
              IFormatProvider formatProvider = null)
    {
        return loggerConfiguration.Sink(new MySink(formatProvider));
    }
}
```

### Using the sink

As seen in [Configuration Basics](https://github.com/serilog/serilog/wiki/Configuration-Basics) the new sink can be configured as follows.

```csharp
var log = new LoggerConfiguration()
    .MinimumLevel.Information()
    .WriteTo.MySink()
    .CreateLogger();
```

## Full Sample

Below is the full example code as a console app.

```csharp
using System;
using Serilog;
using Serilog.Core;
using Serilog.Events;
using Serilog.Configuration;

namespace SimpleSink
{
    class Program
    {
        static void Main(string[] args)
        {
            var log = new LoggerConfiguration()
                .MinimumLevel.Information()
                .WriteTo.MySink()
                .CreateLogger();
            
            var position = new { Latitude = 25, Longitude = 134 };
            var elapsedMs = 34;

            log.Information("Processed {@Position} in {Elapsed:000} ms.", position, elapsedMs);

        }
    }

    public class MySink : ILogEventSink
    {
        private readonly IFormatProvider _formatProvider;

        public MySink(IFormatProvider formatProvider)
        {
            _formatProvider = formatProvider;
        }

        public void Emit(LogEvent logEvent)
        {
            var message = logEvent.RenderMessage(_formatProvider);
            Console.WriteLine(DateTimeOffset.Now.ToString() + " "  + message);
        }
    }

    public static class MySinkExtensions
    {
        public static LoggerConfiguration MySink(
                  this LoggerSinkConfiguration loggerConfiguration,
                  IFormatProvider formatProvider = null)
        {
            return loggerConfiguration.Sink(new MySink(formatProvider));
        }
    }
}
```

*Example Output*

```
17/01/2017 3:10:26 PM +10:00 Processed { Latitude: 25, Longitude: 134 } in 034 ms.
```