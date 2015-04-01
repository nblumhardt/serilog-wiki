Serilog provides _sinks_ for writing log events to storage in various formats.

**Portable** - Observers (Rx), TextWriter

**Full .NET Framework** - Azure Table Storage, Colored Console, Console, CouchDB, Dump File, ElasticSearch, elmah.io, Email, File, Glimpse, log4net, Logentries, Loggly, Loggr, MongoDB, MS SQL Server, NLog, RavenDB, Rolling File, Seq, Splunk, Trace, Windows Event Log

> You can find samples demonstrating the use of most sinks in the [[samples repository|https://github.com/serilog/serilog-samples]]

## Overview

| Name | Description | NuGet | Owner |
|--------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|---------|
| [Elasticsearch](https://github.com/serilog/serilog-sinks-elasticsearch) | Stores your events inside the Elasticsearch NoSQL search engine. | [[Serilog.Sinks.ElasticSearch|http://nuget.org/packages/serilog.sinks.elasticsearch]] | @mivano |
| [ElmahIO](https://github.com/serilog/serilog-sinks-elmahio) | Hosted solution that stores events | [[Serilog.Sinks.ElmahIO|http://nuget.org/packages/serilog.sinks.ElmahIO]] | @mivano |
| [XSockets](https://github.com/serilog/serilog-sinks-xsockets) |  |  |  |
| [Splunk](https://github.com/serilog/serilog-sinks-splunk) | Sends events to the Splunk service. |  |  |
| [SignalR](https://github.com/serilog/serilog-sinks-signalr) |  |  |  |
| [Raygun](https://github.com/serilog/serilog-sinks-raygun) | Stores your events in the Raygun service. |  | @mivano |
| [RavenDB](https://github.com/serilog/serilog-sinks-ravendb) | Use the RavenDB NoSQL database as storage. |  |  |
| [NLog](https://github.com/serilog/serilog-sinks-nlog) | Adapts Serilog to write events through existing NLog infrastructure. |  |  |
| [MSSQLServer](https://github.com/serilog/serilog-sinks-mssqlserver) | Use a Microsoft SQL Server as the data store for the events. |  | @mivano |
| [Loggr](https://github.com/serilog/serilog-sinks-loggr) | [[Loggr|http://www.loggr.net]] is a cloud hosted solution to track users, events and other kinds of items. It provides analytics and notifications and more. |  | @mivano |
| [LogEntries](https://github.com/serilog/serilog-sinks-logentries) | [[Logentries|http://www.logentries.com]] allows you to collect machine statistics and log files. |  | @mivano |
| [Log4Net](https://github.com/serilog/serilog-sinks-log4net) | Duplicates Serilog events through the log4net pipeline to allow integration with existing code and libraries. |  |  |
| [Glimpse](https://github.com/serilog/serilog-sinks-glimpse) | Writes log events to [[Glimpse|http://getglimpse.com]]. |  |  |
| [EventLog](https://github.com/serilog/serilog-sinks-eventlog) |  |  |  |
| [Email](https://github.com/serilog/serilog-sinks-email) | Sends log events by email. |  |  |
| [CouchDB](https://github.com/serilog/serilog-sinks-couchdb) | Writes events as documents to [[CouchDB|http://couchdb.org]]. | [[Serilog.Sinks.CouchDB|http://nuget.org/packages/serilog.sinks.couchdb]] |  |
| [CouchBase](https://github.com/serilog/serilog-sinks-couchbase) |  |  |  |
| [AzureTableStorage](https://github.com/serilog/serilog-sinks-azuretablestorage) | Writes to a table in [[Windows Azure Table Storage|http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/]]. | [[Serilog.AzureTableStorage|http://nuget.org/packages/serilog.sinks.azuretablestorage]] |  |
| [AzureEventHub](https://github.com/serilog/serilog-sinks-azureeventhub) |  |  |  |
| [AzureDocumentDB](https://github.com/serilog/serilog-sinks-azuredocumentdb) |  |  |  |
| [Seq](https://github.com/serilog/serilog-sinks-seq) | [[Seq|http://getseq.net]] is an on-premises log server that's built specifically for storing structured log events. |  |  |
| [ExceptionLess](https://github.com/serilog/serilog-sinks-exceptionless) |  |  |  |
| [Application insights](https://github.com/serilog/serilog-sinks-applicationinsights) |  |  |  |
| [Xamarin](https://github.com/serilog/serilog-sinks-xamarin) | Writes events to Xamarin mobile targets |  |  |
| [MongoDB](https://github.com/serilog/serilog-sinks-mongodb) | Stores your events in the MongoDB NoSQL database. |  |  |
| [Loggly](https://github.com/serilog/serilog-sinks-loggly) | [[Loggly|http://www.loggly.com]] is a cloud based log management service. |  | @mivano |

## Alphabetical listing

### Azure Table Storage

Writes to a table in [[Windows Azure Table Storage|http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/]].

**Package** - [[Serilog.AzureTableStorage|http://nuget.org/packages/serilog.sinks.azuretablestorage]]
| **Platforms** - .NET 4.5

```csharp
var storage = CloudStorageAccount.FromConfigurationSetting("MyStorage");

var log = new LoggerConfiguration()
    .WriteTo.AzureTableStorage(storage)
    .CreateLogger();
```

### Colored Console

Writes to the system console, using colour to emphasise levels and to highlight structured data within log messages. Makes the ordinary console sink look ordinary!

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ColoredConsole()
    .CreateLogger();
```

### Console

Writes to the system console. The colored console sink's boring cousin.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();
```

### CouchDB

Writes events as documents to [[CouchDB|http://couchdb.org]].

**Package** - [[Serilog.Sinks.CouchDB|http://nuget.org/packages/serilog.sinks.couchdb]]
| **Platforms** - .NET 4.5

You'll need to create a database on your CouchDB server. In the example shown, it is called `log`.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.CouchDB("http://mycouchdb/log/")
    .CreateLogger();
```

### Dump File

Writes log events to a file, printing all properties with their values. A development aid if you're working on Serilog.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.DumpFile("dump.txt")
    .CreateLogger();
```

### ElasticSearch

Send your log events directly into ElasticSearch. By default it will connect to an ElasticSearch node running on your localhost and port 9200. Indexes are created automatically and are based on the indexFormat which is standard logstash-{yyyy.MM.dd} matching the logstash format also used by Kibana. ElasticSearch and Kibana can be downloaded from [[elasticsearch.net|http://www.elasticsearch.org/overview/elkdownloads/]]. You can override those defaults.

It is recommended to use a template so ElasticSearch knows how to efficiently handle the events. The sink will not force any and will create standard indexes. A default logstash format will already help the indexing process. You can find one [[here|https://gist.github.com/mivano/9688328]]. Add it to ElasticSearch by placing it under the config/templates folder or by sending it to the node using [[PUT|http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/indices-templates.html#indices-templates]].

**Package** - [[Serilog.Sinks.ElasticSearch|http://nuget.org/packages/serilog.sinks.elasticsearch]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ElasticSearch()
    .CreateLogger();
```

### Elmah

[[Elmah.io|http://www.elmah.io]] is a cloud hosted solution to capture errors. Register for an account at their website and use the provided GUID in the configuration for serilog.

**Package** - [[Serilog.Sinks.ElmahIO|http://nuget.org/packages/serilog.sinks.ElmahIO]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ElmahIO(new Guid("{your guid}"))
    .CreateLogger();
```

As elmah.io is primarily used for error tracking, the default level is set to `Error`. You can override this, but not all the data on the site is filled in as not all the Serilog properties can be matched. 

_Keep in mind that Elmah.io is a commercial service._

### Email

Sends log events by email.

**Package** - [[Serilog.Sinks.Email|http://nuget.org/packages/serilog.sinks.email]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Email(
        from: "app@example.com",
        to: "support@example.com",
        mailServer: "smtp.example.com")
    .CreateLogger();
```

An overload accepting `EmailConnectionInfo` can be used to specify advanced options.

### File

Writes log events to a text file.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.File("log.txt")
    .CreateLogger();
```

To avoid sinking apps with runaway disk usage the file sink **limits file size to 1GB by default**. The limit can be increased or removed using the `fileSizeLimitBytes` parameter.

```csharp
    .WriteTo.File("log.txt", fileSizeLimitBytes: null)
```

### Glimpse

Writes log events to [[Glimpse|http://getglimpse.com]].

**Package** - [[Serilog.Sinks.Glimpse|http://nuget.org/packages/serilog.sinks.glimpse]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Glimpse()
    .CreateLogger();
```

Log events will appear in a Serilog tab under the Glimpse UI.

### log4net

Duplicates Serilog events through the log4net pipeline to allow integration with existing code and libraries.

**Package** - [[Serilog.Sinks.Log4Net|http://nuget.org/packages/serilog.sinks.log4net]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Log4Net()
    .CreateLogger();
```

[[(More information.)|http://nblumhardt.com/2013/06/serilog-sinks-log4net/]]

### Logentries

[[Logentries|http://www.logentries.com]] allows you to collect machine statistics and log files. 
In your Logentries dashboard, select the option to create a new log and pick the .NET log type. You will see details about log4net and nlog which you can ignore. At the bottom, there is a button to configure your app. Clicking on this button will reveal a token in the form of a guid. This token you need to enter in the Logentries configuration. By default the sink will use a TCP token using SSL.

**Package** - [[Serilog.Sinks.Logentries|http://nuget.org/packages/serilog.sinks.logentries]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Logentries("token")
    .CreateLogger();
```

The properties are not send to Logentries, it only consists of the message which you can configure.

_Keep in mind that Logentries is a commercial service._

### Loggly

[[Loggly|http://www.loggly.com]] is a cloud based log management service. Create a new input and specify that you want to use a http input with JSON enabled. Use the [[loggly-csharp-configuration|https://github.com/neutmute/loggly-csharp]] XML configuration syntax to configure the sink.

**Package** - [[Serilog.Sinks.Loggly|http://nuget.org/packages/serilog.sinks.loggly]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Loggly()
    .CreateLogger();
```

Properties will be send along to Loggly. The level is send as category.

_Keep in mind that Loggly is a commercial service._

### Loggr

[[Loggr|http://www.loggr.net]] is a cloud hosted solution to track users, events and other kinds of items. It provides analytics and notifications and more. Register for an account at their website and [[find|http://docs.loggr.net/post#A Quick Example]] your logkey and apikey. Pass those to the loggr sink using the extension.

**Package** - [[Serilog.Sinks.Loggr|http://nuget.org/packages/serilog.sinks.loggr]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Loggr("<log key>", "<api key>")
    .CreateLogger();
```

As Loggr can track users, the sink will try to find a property called `UserName` and pass that to Loggr as a dedicated property. You can change the name of the property in one of the settings of the extension of the sink. The rest of the properties are being send as data elements to Loggr.

_Keep in mind that Loggr is a commercial service._

### MongoDB

Writes events as documents to [[MongoDB|http://mongodb.org]].

**Package** - [[Serilog.Sinks.MongoDB|http://nuget.org/packages/serilog.sinks.mongodb]]
| **Platforms** - .NET 4.5

You'll need to create a collection on your MongoDB server. In the example shown, it is called `log`.

```csharp
var log = new LoggerConfiguration()
    .WriteTo.MongoDB("mongo://mymongodb/log")
    .CreateLogger();
```

### MS SQL Server

Use a Microsoft SQL Server as the data store for the events. While a NoSql store allows for more flexibility to store the different kinds of properties, it sometimes is easier to use an already existing MS SQL server. This sink will write the logevent data to a table and can optionally also store the properties inside an Xml column so they can be queried.

**Package** - [[Serilog.Sinks.MSSqlServer|http://nuget.org/packages/serilog.sinks.mssqlserver]]
| **Platforms** - .NET 4.5

You'll need to create a database and add a table like the one you can find in this [[Gist|https://gist.github.com/mivano/10429656]]. 

```csharp
var log = new LoggerConfiguration()
    .WriteTo.MSSqlServer(@"Server=.\SQLEXPRESS;Database=LogEvents;Trusted_Connection=True;", "Logs")
    .CreateLogger();
```

Make sure to set up security in such a way that the sink can write to the log table. If you don't plan on using the properties, then you can disable the storage of them. 

### NLog

Adapts Serilog to write events through existing NLog infrastructure.

**Package** - [[Serilog.Sinks.NLog|http://nuget.org/packages/serilog.sinks.nlog]]
| **Platforms** - .NET 4.5

You'll need to configure NLog, too. 

```csharp
var log = new LoggerConfiguration()
    .WriteTo.NLog()
    .CreateLogger();
```

### Observers (Rx)

Provides a hot `IObservable<LogEvent>` that can be subscribed to using the 
[[Reactive Extensions for .NET|http://msdn.microsoft.com/en-us/data/gg577609]].

**Package** - [[Serilog|http://nuget.org/packages/serilog]] |
 **Platforms** - .NET 4.5, Windows 8, Windows Phone 8

```csharp
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

```csharp
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

### Seq*

[[Seq|http://getseq.net]] is an on-premises log server that's built specifically for storing structured log events. The Seq web UI provides [[queries|https://getseq.atlassian.net/wiki/display/SEQ10/Querying+log+events]] over text and structured data, and the server itself can be extended with [[C# handlers|https://getseq.atlassian.net/wiki/display/SEQ10/Writing+Seq+apps]].

**Package** - [[Serilog.Sinks.Seq|http://nuget.org/packages/serilog.sinks.seq]]
| **Platforms** - .NET 4.0, 4.5+

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Seq("http://my-seq-server")
    .CreateLogger();
```

The sink supports durable (disk-buffered) log shipping, and can take advantage of Seq's API keys to authenticate clients and dynamically attach properties to events at the server-side. Visit the [[full documentation|https://getseq.atlassian.net/wiki/display/SEQ10/Logging+to+Seq+with+Serilog]] for examples.

_Seq is a separate commercial product developed by the creators of Serilog._

### Splunk

Writes events to [[Splunk|http://splunk.com]].

**Package** - [[Serilog.Sink.Splunk|http://nuget.org/packages/serilog.sink.splunk]]
| **Platforms** - .NET 4.5+

```csharp
var log = new LoggerConfiguration()
    .WriteTo.SplunkViaUdp("127.0.0.1", 10000)
    .WriteTo.SplunkViaTcp("127.0.0.1", 10001)
    .CreateLogger();
```

_Splunk is a commercial product._

### TextWriter

Writes to a specified `System.IO.TextWriter` and can thus be attached to practically any text-based .NET output and the in-memory `System.IO.StringWriter` class.

**Package** - [[Serilog|http://nuget.org/packages/serilog]] |
 **Platforms** - .NET 4.5, Windows 8, Windows Phone 8

```csharp
var messages = new StringWriter();

var log = new LoggerConfiguration()
    .WriteTo.TextWriter(messages)
    .CreateLogger();
```

### Trace

Writes log events to the `System.Diagnostics.Trace`.

**Package** - [[Serilog|http://nuget.org/packages/serilog]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.Trace()
    .CreateLogger();
```

### Windows Event Log

Writes log events to the Windows Event Log

**Package** - [[Serilog.Sinks.EventLog|http://nuget.org/packages/serilog.sinks.eventlog]]
| **Platforms** - .NET 4.5

```csharp
var log = new LoggerConfiguration()
    .WriteTo.EventLog("My App")
    .CreateLogger();
```