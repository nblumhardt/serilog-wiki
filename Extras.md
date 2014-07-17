There are a couple of extra packages for Serilog which you can get from NuGet. They extend Serilog for a certain use case or application type. Add a package by installing it using the NuGet package manager, like this:

```
PM> Install-Package Serilog.Extras.AppSettings
```

### Serilog.Extras.AppSettings

Provides a simple XML configuration syntax.

For more information about the AppSettings package, see the [[dedicated page|AppSettings]].

### Serilog.Extras.Attributed

Makes it possible to manipulate how objects are logged using attributes.

**Package** - [[Serilog.Extras.Attributed|http://nuget.org/packages/serilog.extras.attributed]]
| **Platforms** - .NET 4.5, Windows 8, Windows Phone 8

Enabling the module:

```csharp
var log = new LoggerConfiguration()
  .Destructure.UsingAttributes()
  ...
```

#### Ignoring a property

Apply the `NotLogged` attribute:

```csharp
public class LoginCommand
{
  public string Username { get; set; }

  [NotLogged]
  public string Password { get; set; }
}
```

When the object is passed using `{@...}` syntax the attributes will be consulted.

```
var command = new LoginCommand { Username = "logged", Password = "not logged" };
log.Information("Logging in {@Command}", command);
```

#### Treating types and properties as scalars

To prevent destructuring of a type or property at all, apply the `[LoggedAsScalar]` attribute.

### Serilog.Extras.Web

When you work with an ASP.NET web application, this package adds additional enrichers and an `HttpModule` to enhance the logging output. The following enrichers are available:

*  **HttpRequestId** A GUID used to identify requests.
*  **HttpRequestNumber** an incrementing number per request.
*  **HttpRequestTraceId** GUID matching the RequestTraceIdentifier assigned by IIS and used throughout ASP.NET/ETW. IIS ETW tracing must be enabled for this to work.
*  **HttpSessionId** The current ASP.NET session id.
*  **UserName** The current username or, when anonymous, a defined value. By default this is set to _(anonymous)_.

**Package** - [[Serilog.Extras.Web|http://nuget.org/packages/serilog.extras.web]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .Enrich.With<HttpRequestIdEnricher>()
    .Enrich.With<UserNameEnricher>()
    .CreateLogger();
```

To override the username enricher behaviour:

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .Enrich.With(new UserNameEnricher("not known yet", System.Environment.UserName))
    .CreateLogger();
```

The **ApplicationLifecycleModule** will automatically be enabled and will write information events about the current method and url that is being accessed. Optionally you also store any form data that is posted to the server.
When an unhandled exception occurs, the module will capture it and log it as an error event.

To enable the posting of form data:

```
ApplicationLifecycleModule.DebugLogPostedFormData = true;
```

If you want to disable the logging completely, use the following statement:

```
ApplicationLifecycleModule.IsEnabled = false;
```

### Serilog.Extras.Timing

To simplify the ability to time operations, you can use the timing package. Code that is been executed while wrapped inside a **TimedOperation** block will write to the log when the operation starts and ends and include the TimeSpan of the elapsed time.

**Package** - [[Serilog.Extras.Timing|http://nuget.org/packages/serilog.extras.timing]]
| **Platforms** - .NET 4.5

**Timer**

```csharp
var logger = new LoggerConfiguration()
                .MinimumLevel.Debug()
                .WriteTo.Trace()
                .CreateLogger();

using (logger.BeginTimedOperation("Time a thread sleep for 2 seconds."))
{
     Thread.Sleep(2000);
}
```

By default the log level is Information and a GUID will be generated to uniquely identify this operation. You can also override those defaults.

```csharp
using (logger.BeginTimedOperation("Using a passed in identifier", "test-loop"))
{
   var a = "";
   for (int i = 0; i < 1000; i++)
   {
      a += "b";
   }
}
```

The output will be something like this:

```
2014-03-28 21:37:35.558 +01:00 [Information] Beginning operation "test-loop": "Using a passed in identifier"
2014-03-28 21:37:35.572 +01:00 [Information] Completed operation "test-loop": "Using a passed in identifier" in 00:00:00.0015726
```

As an option it is possible to specify a maximum time. When the operation takes more time than the maximum time, a warning will be generated.

```csharp
using (logger.BeginTimedOperation("This should execute within 1 second.",null, LogEventLevel.Debug, TimeSpan.FromSeconds(1)))
{
   Thread.Sleep(1100);
}
```

Example output:

```
2014-03-28 21:37:35.588 +01:00 [Debug] Beginning operation "f14ad055-ecbb-4b43-884b-ccada6af72de": "This should execute within 1 second."
2014-03-28 21:37:36.702 +01:00 [Warning] Operation "f14ad055-ecbb-4b43-884b-ccada6af72de": "This should execute within 1 second." exceeded the limit of 00:00:01 by completing in 00:00:01.0992064
```

As a property, the **TimedOperationId**, **TimedOperationDescription** and **TimedOperationElapsed** are available for the log events created by the timed operation.

Keep in mind that the TimedOperation will internally use a Stopwatch. So be aware of the frequency and what a Stopwatch will actually time. There are other alternatives, but not implemented here.

**Gauge**

The gauge allows you to perform a measurement of a value. For example to get the current items in a queue. Although you can read the value and write it to the log, this gauge operation will do that in a consistent way. You need to invoke the Write() method to perform the gauge and write to log.

```csharp
  var queue = new Queue<int>();
  var gauge = logger.GaugeOperation("queue", "item(s)", () => queue.Count());

  gauge.Write();

  queue.Enqueue(20);

  gauge.Write();

  queue.Dequeue();

  gauge.Write();
```

**Counter**

The counter is a special type of gauge to write the value of an atomic long variable. By calling the Increment, Decrement or Reset methods, you can change the value in a thread safe way and optionally write the value directly to the event log. If you pass false as argument in the constructor, you need to explicitly call the Write() method to output the current value to the log. 

```csharp
var counter = logger.CountOperation("counter", "operation(s)", true, LogEventLevel.Debug);
counter.Increment();
counter.Increment();
counter.Increment();
counter.Decrement();
```

### Serilog.Extras.Topshelf

Implements a logging interface for [[Topshelf|http://docs.topshelf-project.com/en/latest/index.html]], a framework for hosting NT Services. 

**Package** - [[Serilog.Extras.TopShelf|http://nuget.org/packages/serilog.extras.topshelf]]
| **Platforms** - .NET 4.5

You can pass in an existing ILogger instance or keep it null so it will use the default static version.

```csharp
HostFactory.New(x =>
{
    x.UseSerilog();
});
```