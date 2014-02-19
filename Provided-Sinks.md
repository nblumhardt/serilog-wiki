Serilog provides _sinks_ for writing log events to storage in various formats.

**Portable** - Observers (Rx), TextWriter

**Full .NET Framework** - Azure Table Storage, Colored Console, Console, CouchDB, Dump File, File, Glimpse, log4net, MongoDB, RavenDB, Rolling File, Trace, Windows Event Log

## Alphabetical listing

### Azure Table Storage

Writes to a table in [[Windows Azure Table Storage|http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/]].

**Package** - [[Serilog.AzureTableStorage|http://nuget.org/packages/serilog.sinks.azuretablestorage]]
| **Platforms** - .NET 4.5

```
var storage = CloudStorageAccount.FromConfigurationSetting("MyStorage");

var log = new LoggerConfiguration()
    .WriteTo.AzureTableStorage(storage)
    .CreateLogger();
```

### Colored Console

Writes to the system console, using colour to emphasise levels and to highlight structured data within log messages. Makes the ordinary console sink look ordinary!

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .CreateLogger();
```

### Console

Writes to the system console. The colored console sink's boring cousin.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();
```

### CouchDB

Writes events as documents to [[CouchDB|http://couchdb.org]].

**Package** - [[Serilog.Sinks.CouchDB|http://nuget.org/packages/serilog.sinks.couchdb]]
| **Platforms** - .NET 4.5

You'll need to create a database on your CouchDB server. In the example shown, it is called `log`.

```
var log = new LoggerConfiguration()
    .WriteTo.CouchDB("http://mycouchdb/log/")
    .CreateLogger();
```

### Dump File

Writes log events to a file, printing all properties with their values. A development aid if you're working on Serilog.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.DumpFile("dump.txt")
    .CreateLogger();
```

### File

Writes log events to a text file.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.File("log.txt")
    .CreateLogger();
```

To avoid sinking apps with runaway disk usage the file sink **limits file size to 1GB by default**. The limit can be increased or removed using the `fileSizeLimitBytes` parameter.

```
    .WriteTo.File("log.txt", fileSizeLimitBytes: null)
```

### Glimpse

Writes log events to [[Glimpse|http://getglimpse.com]].

**Package** - [[Serilog.Sinks.Glimpse|http://nuget.org/packages/serilog.sinks.glimpse]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.Glimpse()
    .CreateLogger();
```

Log events will appear in a Serilog tab under the Glimpse UI.

### log4net

Duplicates Serilog events through the log4net pipeline to allow integration with existing code and libraries.

**Package** - [[Serilog.Sinks.Log4Net|http://nuget.org/packages/serilog.sinks.log4net]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.Log4Net()
    .CreateLogger();
```

[[(More information.)|http://nblumhardt.com/2013/06/serilog-sinks-log4net/]]

### MongoDB

Writes events as documents to [[MongoDB|http://mongodb.org]].

**Package** - [[Serilog.Sinks.MongoDB|http://nuget.org/packages/serilog.sinks.mongodb]]
| **Platforms** - .NET 4.5

You'll need to create a collection on your MongoDB server. In the example shown, it is called `log`.

```
var log = new LoggerConfiguration()
    .WriteTo.MongoDB("mongo://mymongodb/log")
    .CreateLogger();
```

### Observers (Rx)

Provides a hot `IObservable<LogEvent>` that can be subscribed to using the 
[[Reactive Extensions for .NET|http://msdn.microsoft.com/en-us/data/gg577609]].

**Package** - [[Serilog|http://nuget.org/packages/serilog]] |
 **Platforms** - .NET 4.5, Windows 8, Windows Phone 8

```
var log = new LoggerConfiguration()
    .WriteTo.Observers(logEvents => logEvents
        .Do(le => { Console.WriteLine(le); })
        .Subscribe())
    .CreateLogger();
```

### RavenDB

Writes events as documents to [[RavenDB|http://ravendb.net]].

**Package** - [[Serilog.Sinks.RavenDB|http://nuget.org/packages/serilog.sinks.ravendb]]
| **Platforms** - .NET 4.5

```
var logs = new DocumentStore { ConnectionStringName = "Logs" }.Initialize();

var log = new LoggerConfiguration()
    .WriteTo.RavenDB(logs)
    .CreateLogger();
```

You'll need to create a database on the server for logs, and specify this as your default database in the connection string or `DocumentStore.DefaultDatabase`.

[[(More information.)|http://nblumhardt.com/2013/06/serilog-and-ravendb/]]

**Important Note** - RavenDB is commercial software, you may need a license in order to run a RavenDB server.

### Rolling File

Writes log events to a set of text files, one per day.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

The filename must include the `{Date}` placeholder, which will be replaced with the date of the events contained in the file.

```
var log = new LoggerConfiguration()
    .WriteTo.RollingFile("log-{Date}.txt")
    .CreateLogger();
```

To avoid sinking apps with runaway disk usage the rolling file sink **limits file size to 1GB by default**. The limit can be changed or removed using the `fileSizeLimitBytes` parameter.

```
    .WriteTo.RollingFile("log-{Date}.txt", fileSizeLimitBytes: null)
```

For the same reason, only **the most recent 31 files** are retained by default (i.e. one long month). To change or remove this limit, pass the `retainedFileCountLimit` parameter.

```
    .WriteTo.RollingFile("log-{Date}.txt", retainedFileCountLimit: null)
```

### TextWriter

Writes to a specified `System.IO.TextWriter` and can thus be attached to practically any text-based .NET output and the in-memory `System.IO.StringWriter` class.

**Package** - [[Serilog|http://nuget.org/packages/serilog]] |
 **Platforms** - .NET 4.5, Windows 8, Windows Phone 8

```
var messages = new StringWriter();

var log = new LoggerConfiguration()
    .WriteTo.TextWriter(messages)
    .CreateLogger();
```

### Trace

Writes log events to the `System.Diagnostics.Trace`.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.DiagnosticTrace()
    .CreateLogger();
```

### Windows Event Log

Writes log events to the Windows Event Log

**Package** - [[Serilog.Sinks.EventLog|http://nuget.org/packages/serilog.sinks.eventlog]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.EventLog("My App")
    .CreateLogger();
```