## Installing from NuGet

The core logging package is [[Serilog|http://nuget.org/packages/serilog]]. The supported platforms are .NET 4.5, Windows 8 (WinRT) and Windows Phone 8.

```
PM> Install-Package Serilog
```

Browse the [[serilog|http://nuget.org/packages?q=Tags%3A%22serilog%22%22]] tag on NuGet to see the available sinks, extensions and related third-party packages.

## Setup

Types are in the Serilog namespace.

```csharp
using Serilog;
```

An `ILogger` is created using `LoggerConfiguration`.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .CreateLogger();
```

This is typically done once at application start-up, and the logger saved for later use by application classes. Multiple loggers can be created and used independently if required.

```csharp
log.Information("Hello, Serilog!");
```

Serilog's global, statically accessible logger, is set via `Log.Logger` and can be invoked using the static methods on the `Log` class.

```csharp
Log.Logger = log;
Log.Information("The global logger has been configured");
```

_Configuring and using the `Log` class is an optional convenience that makes it easier for libraries to adopt Serilog. Serilog does not and will never adopt any static/process-wide state within the logging pipeline itself._

## Example application

The complete example below shows logging in a simple console application.

##### 1. Create a new Console Application project
##### 2. Install the core Serilog package and the [literate console sink](https://github.com/serilog/serilog-sinks-literate)

```powershell
using System;
using Serilog;

namespace SerilogExample
{
    class Program
    {
        static void Main()
        {
            Log.Logger = new LoggerConfiguration()
                .MinimumLevel.Debug()
                .WriteTo.LiterateConsole()
                .CreateLogger();

            Log.Information("Hello, world!");

            int a = 10, b = 0;
            try
            {
                Log.Debug("Dividing {A} by {B}", a, b);
                Console.WriteLine(a / b);
            }
            catch (Exception ex)
            {
                Log.Error(ex, "Something went wrong");
            }

            Log.CloseAndFlush();
            Console.ReadKey();
        }
    }
}
```

##### 4. Run the program


