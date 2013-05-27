> Maintaining good quality documentation is a priority for Serilog. If you find missing or inaccurate content, please let us know via the issue tracker. If you'd like to extend the wiki with a topic or tutorial please email the discussion forum linked in the Resources section below. Thanks!

## Why Serilog?

Like many other .NET libraries, Serilog provides basic diagnostic logging to files, the console, and so-on. It is easy to set up, has a clean API, and is portable between recent .NET platforms.

Unlike other logging libraries for .NET, parameters passed along with log messages are not destructively rendered into a text format. Instead, they're preserved as structured data, that can be written in document form to a NoSQL data store.

```
var sensorInput = new { Latitude = 25, Longitude = 134 };
var processingTimeMs = 34;

log.Information("Processed {@SensorInput} in {TimeMS:000} ms.", sensorInput, processingTimeMs);
```

Serilog message templates use a simple DSL that extends the regular .NET format strings. Properties are named within the message template, and matched positionally with the arguments provided to the log method.

This example records two properties, `SensorInput` and `TimeMS` along with the log event.

The properties captured in the example, in JSON format, would appear like:

```
{ "SensorInput": { "Latitude": 25, "Longitude": 134 },
  "TimeMS": 34 }
```

The `@` operator in front of `SensorInput` instructs Serilog to preserve the structure of the object passed in. If this is omitted, Serilog recognises simple types like strings, numbers, dates and times, dictionaries and enumerables; all other objects are converted into strings using `ToString()`. 'Stringification' can be forced using the `$` operator in place of `@`.

The `:000` segment following `TimeMS` is a standard .NET format string that affects how the property is rendered (not how it is captured). The standard console sink included with Serilog will render the above message as:

```
... [Information] Processed { Latitude: 25, Longitude: 134 } in 034 ms. 
```

## Documentation

* [[Getting Started|Getting-Started]]
* [[Configuration Basics|Configuration-Basics]]
* [[Writing Log Events|Writing-Log-Events]]
* [[Provided Sinks|Provided-Sinks]]
* [[Provided Enrichers|Provided-Enrichers]]
* [[Provided Filters|Provided-Filters]]
* [[Debugging and Diagnostics|Debugging-and-Diagnostics]]

## Resources

* [[Discussion forum|https://groups.google.com/group/serilog]]
* [[Blog posts at nblumhardt.com|http://nblumhardt.com]]
* [[Project homepage|http://serilog.net]]