Serilog provides _sinks_ for writing log events to storage in various formats.

## Built-in

These sinks come with the [[Serilog|http://nuget.org/packages/serilog]] package.

#### Colored Console

Writes to the system console, using colour to emphasise levels and to highlight structured data within log messages. Makes the ordinary console sink look ordinary! (_See also: [Literate Console](https://github.com/serilog/serilog-sinks-literate)_)

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .CreateLogger();
```
Or in XML [app-settings format](https://github.com/serilog/serilog/wiki/AppSettings):

```xml
<add key="serilog:write-to:ColoredConsole" />
```

#### Console

Writes to the system console. The colored console sink's boring cousin.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();
```

#### File

Writes log events to a text file.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.File("log.txt")
    .CreateLogger();
```

To avoid sinking apps with runaway disk usage the file sink **limits file size to 1GB by default**. The limit can be increased or removed using the `fileSizeLimitBytes` parameter.

```csharp
    .WriteTo.File("log.txt", fileSizeLimitBytes: null)
```

Or in XML [app-settings format](https://github.com/serilog/serilog/wiki/AppSettings):

```xml
<add key="serilog:write-to:File.path" value="log.txt" />
```

#### Logger

Allows events from one Serilog logger to be directed to another.

```csharp
var log1 = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();

var log2 = new LoggerConfiguration()
    .WriteTo.Logger(log1)
    .WriteTo.RollingFile(...)
    .CreateLogger();

log1.Information("This will be written to the console");
log2.Information("This will go to both console and file");
```

Only events that pass through the first logger will reach the second, i.e. the pipelines are chained together. It's important to realise that each logger has its own, independent `MinimumLevel` setting, so this must be configured on both loggers if different from the default.

#### Observers (Rx)

Provides a hot `IObservable<LogEvent>` that can be subscribed to using the 
[[Reactive Extensions for .NET|http://msdn.microsoft.com/en-us/data/gg577609]].

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Observers(logEvents => logEvents
        .Do(le => { Console.WriteLine(le); })
        .Subscribe())
    .CreateLogger();
```

#### Rolling File

Writes log events to a set of text files, one per day.

The filename can include the `{Date}` placeholder, which will be replaced with the date of the events contained in the file.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.RollingFile("log-{Date}.txt")
    .CreateLogger();
```

To avoid sinking apps with runaway disk usage the rolling file sink **limits file size to 1GB by default**. The limit can be changed or removed using the `fileSizeLimitBytes` parameter.

```csharp
    .WriteTo.RollingFile("log-{Date}.txt", fileSizeLimitBytes: null)
```

For the same reason, only **the most recent 31 files** are retained by default (i.e. one long month). To change or remove this limit, pass the `retainedFileCountLimit` parameter.

```csharp
    .WriteTo.RollingFile("log-{Date}.txt", retainedFileCountLimit: null)
```

#### TextWriter

Writes to a specified `System.IO.TextWriter` and can thus be attached to practically any text-based .NET output and the in-memory `System.IO.StringWriter` class.

```csharp
var messages = new StringWriter();

var log = new LoggerConfiguration()
    .WriteTo.TextWriter(messages)
    .CreateLogger();
```

#### Trace

Writes log events to the `System.Diagnostics.Trace`.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Trace()
    .CreateLogger();
```

## From the Serilog Project

The sinks below are maintained under the Serilog project.

 * [Amazon Kinesis](https://github.com/serilog/serilog-sinks-amazonkinesis)
 * [Application Insights](https://github.com/serilog/serilog-sinks-applicationinsights) 
 * [Azure DocumentDB](https://github.com/serilog/serilog-sinks-azuredocumentdb) 
 * [Azure Event Hubs](https://github.com/serilog/serilog-sinks-azureeventhub)
 * [Azure Table Storage](https://github.com/serilog/serilog-sinks-azuretablestorage) 
 * [CouchDB](https://github.com/serilog/serilog-sinks-couchdb) 
 * [CouchBase](https://github.com/serilog/serilog-sinks-couchbase)
 * [DataDog](https://github.com/serilog/serilog-sinks-datadog)
 * [Elasticsearch](https://github.com/serilog/serilog-sinks-elasticsearch) 
 * [elmah.io](https://github.com/serilog/serilog-sinks-elmahio) 
 * [Email](https://github.com/serilog/serilog-sinks-email)
 * [Exceptionless](https://github.com/serilog/serilog-sinks-exceptionless) 
 * [Glimpse](https://github.com/serilog/serilog-sinks-glimpse)
 * [Literate Console](https://github.com/serilog/serilog-sinks-literate)
 * [log4net](https://github.com/serilog/serilog-sinks-log4net) 
 * [Logentries](https://github.com/serilog/serilog-sinks-logentries) 
 * [Loggly](https://github.com/serilog/serilog-sinks-loggly)
 * [Loggr](https://github.com/serilog/serilog-sinks-loggr) 
 * [MongoDB](https://github.com/serilog/serilog-sinks-mongodb)
 * [NLog](https://github.com/serilog/serilog-sinks-nlog)
 * [Seq](https://github.com/serilog/serilog-sinks-seq) 
 * [SignalR](https://github.com/serilog/serilog-sinks-signalr) 
 * [Splunk](https://github.com/serilog/serilog-sinks-splunk)
 * [SQL Server](https://github.com/serilog/serilog-sinks-mssqlserver)
 * [RavenDB](https://github.com/serilog/serilog-sinks-ravendb) 
 * [Raygun](https://github.com/serilog/serilog-sinks-raygun) 
 * [RethinkDB](https://github.com/serilog/serilog-sinks-rethinkdb)
 * [Windows Event Log](https://github.com/serilog/serilog-sinks-eventlog) 
 * [Xamarin](https://github.com/serilog/serilog-sinks-xamarin)
 * [XSockets.NET](https://github.com/serilog/serilog-sinks-xsockets)

## Third-Party

 * [Alternate Rolling File](https://github.com/bedegaming/sinks-rollingfile)
 * [NewRelic](https://github.com/Applicita/serilog-sinks-newrelic)
 * [OrientDB](https://github.com/dev-informatics/Serilog.Sinks.OrientDB)
 * [RabbitMQ](https://github.com/sonicjolt/serilog-sinks-rabbitmq)
