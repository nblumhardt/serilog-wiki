Serilog provides sinks for writing log events in various formats.

**Portable** - TextWriter

**Full .NET Framework** - Azure Table Storage, Colored Console, Console, CouchDB, Dump File, File, log4net, MongoDB, RavenDB, Rolling File, Trace

## Portable

### TextWriter

Writes to a specified `System.IO.TextWriter` and can thus be attached to practically any text-based .NET output and the in-memory `System.IO.StringWriter` class.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
 **Platforms** - .NET 4.5, Windows 8, Windows Phone 8

```
var messages = new StringWriter();

var log = new LoggerConfiguration()
    .WriteTo.TextWriter(messages)
    .CreateLogger();
```

## Full .NET Framework

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
    .WriteTo.CouchDB("http://mycouchdb/log")
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

### log4net

Duplicates Serilog events through the log4net pipeline to allow integration with existing code and libraries.

**Package** - [[Serilog.Sinks.Log4Net|http://nuget.org/packages/serilog.sinks.log4net]]
| **Platforms** - .NET 4.5

```
var log = new LoggerConfiguration()
    .WriteTo.Log4Net()
    .CreateLogger();
```

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

**Important Note** - RavenDB is commercial software, you may need a license in order to run a RavenDB server. 

### Rolling File

Writes log events to a set of text files, one per day.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

The filename must include the `{0}` placeholder, which will be replaced with the date of the events contained in the file.

```
var log = new LoggerConfiguration()
    .WriteTo.RollingFile("log-{0}.txt")
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
