Serilog provides several output formatting mechanisms.

## Formatting plain text

Sinks that write plain text output, such as the console and file-based sinks, generally accept _output templates_ to control how log event data is formatted.

The format of events written by these sinks can be modified using the `outputTemplate` configuration parameter. For example, to control the [console sink](https://github.com/serilog/serilog-sinks-console):

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.Console(outputTemplate:
        "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}")
    .CreateLogger();
```

A number of built-in properties can appear in output templates:

 * `Exception` - The full exception message and stack trace, formatted across multiple lines. Empty if no exception is associated with the event.
 * `Level` - The log event level, formatted as the full level name. For more compact level names, use a format such as `{Level:u3}` or `{Level:w3}` for three-character upper- or lowercase level names, respectively.
 * `Message` - The log event's message, rendered as plain text. The `:l` format specifier switches of quoting of strings, and `:j` uses JSON-style rendering for any embedded structured data.
 * `NewLine` - A property with the value of `System.Environment.NewLine`.
 * `Properties` - All event property values that don't appear elsewhere in the output. Use the `:j` format to use JSON rendering.
 * `Timestamp` - The event's timestamp, as a `DateTimeOffset`.

Properties from events, including those attached using enrichers, can also appear in the output template.

## Formatting JSON

Many sinks record log events as JSON, or can be configured to do so. To emit JSON, rather than plain text, a `formatter` can be specified. This example configures the [file sink](https://github.com/serilog/serilog-sinks-file) using the formatter from [_Serilog.Formatting.Compact_](https://github.com/serilog/serilog-formatting-compact).

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.File(new CompactJsonFormatter(), "log.txt")
    .CreateLogger();
```

There are three JSON formatters provided by the Serilog project:

 * `Serilog.Formatting.Json.JsonFormatter` - This is the historical default shipped in the _Serilog_ package. It produces a complete rendering of the log event and supports a few configuration options.
 * `Serilog.Formatting.Compact.CompactJsonFormatter` - A newer, more space-efficient JSON formatter shipped in [Serilog.Formatting.Compact](https://github.com/serilog/serilog-formatting-compact).
 * `Serilog.Formatting.Compact.RenderedCompactJsonFormatter` - Also shipped in [Serilog.Formatting.Compact](https://github.com/serilog/serilog-formatting-compact), this formatter pre-renders [message templates](https://messagetemplates.org) into text.

Custom JSON formatters can be built around the `JsonValueFormatter` class included in Serilog. For some details see [this blog post](https://nblumhardt.com/2016/07/serilog-2-0-json-improvements/).

## Custom text formatters

Both plain text and JSON formatting are implemented using the `ITextFormatter` interface. Implementations of this interface can format log events into any text-based format.

## Format providers

There are a number of options available to formatting the output of individual types like dates. One example is the use of the format provider that is accepted by most sinks. 

Below is a simple console sample using the [_Serilog.Sinks.Console_](https://github.com/serilog/serilog-sinks-literate) sink. This is using the default behavior for rendering a date.
 
```csharp
class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime Created { get; set; }
}

public class Program
{
    public static void Main(string[] args)
    {
        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .CreateLogger();

        var exampleUser = new User { Id = 1, Name = "Adam", Created = DateTime.Now };
        Log.Information("Created {@User} on {Created}", exampleUser, DateTime.Now);

        Log.CloseAndFlush();
    }
}
```

This writes the following output to the console.

```
[07:12:57 INF] Created User {Id=1, Name="Adam", Created=07/18/2016 07:12:57}
  on 07/18/2016 07:12:57
```

There may be scenarios where it is desirable to override or specify the way a `DateTime` is formatted.  This can be done via the implementation of `IFormatProvider`. This strategy applies to any type that you pass to Serilog.

```csharp
class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime Created { get; set; }
}

class CustomDateFormatter : IFormatProvider
{
    readonly IFormatProvider basedOn;
    readonly string shortDatePattern;
    public CustomDateFormatter(string shortDatePattern, IFormatProvider basedOn)
    {
        this.shortDatePattern = shortDatePattern;
        this.basedOn = basedOn;
    }
    public object GetFormat(Type formatType)
    {
        if (formatType == typeof(DateTimeFormatInfo))
        {
            var basedOnFormatInfo = (DateTimeFormatInfo)basedOn.GetFormat(formatType);
            var dateFormatInfo = (DateTimeFormatInfo)basedOnFormatInfo.Clone();
            dateFormatInfo.ShortDatePattern = this.shortDatePattern;
            return dateFormatInfo;
        }
        return this.basedOn;
    }
}

public class Program
{
    public static void Main(string[] args)
    {

        var formatter = new CustomDateFormatter("dd-MMM-yyyy", new CultureInfo("en-AU"));
        Log.Logger = new LoggerConfiguration() 
            .WriteTo.Console(formatProvider: new CultureInfo("en-AU")) //Console1
            .WriteTo.Console(formatProvider: formatter)                //Console2
            .CreateLogger();

        var exampleUser = new User { Id = 1, Name = "Adam", Created = DateTime.Now };
        Log.Information("Created {@User} on {Created}", exampleUser, DateTime.Now);

        Log.CloseAndFlush();
    }
}
```

The following is the output of the above example, with two consoles sinks configured.

```
[08:04:48 INF] Created User {Id=1, Name="Adam", Created=18/07/2016 8:04:48 AM} 
  on 18/07/2016 8:04:48 AM   //Console1
[08:04:48 INF] Created User {Id=1, Name="Adam", Created=18-Jul-2016 8:04:48 AM}
  on 18-Jul-2016 8:04:48 AM //Console2
```