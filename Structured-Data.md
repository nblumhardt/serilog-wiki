Serilog is a kind of serialiser. In many cases it has good default behaviour that suits its purpose, but on occasion it is necessary to instruct Serilog on how to store properties that are attached to a log event.

There are a few unusual terms that Serilog uses to refer to how .NET objects map to its internal (sink-agnostic) property representation. These are explained in more detail below, so you can skip this section if you're planning to read the whole page.

* _Stringification_ is the process of taking a supplied .NET property and invoking its `ToString()` method so that the representation reaching the sinks is a simple string
* _Destructuring_ is the process of taking a complex .NET object and converting it into a structure, which may later be represented as say, a JSON object or XML blob
* _Scalars_ are .NET types that can be represented as atomic values; most value types like `int` fit this description, but so do some reference types like `Uri` and `string`

## Why Control Representation?

There are potentially many ways to record an object to the log. Most types can be nicely represented as strings or simple values, but some make more sense to record as collections, and others as structures with named properties.

The storage representation for a log event property makes a big difference to its size in the log, and the memory and processing overhead involved in getting it there.

With this in mind, let’s take a look at how Serilog is configured to work in the simple cases.

## Default Behaviour

When properties are specified in log events, Serilog does its best to determine the correct representation.

### Simple, Scalar Values

```csharp
var count = 456;
Log.Information("Retrieved {Count} records", count);
```

There's little ambiguity as to how the `Count` property should be stored in this case. Being a simple integer value, Serilog will choose that as its representation.

```csharp
{ "Count": 456 }
```

These examples use JSON, but the same principles apply to other formats as well.

Out of the box, Serilog recognises the following list as basic _scalar_ types, regardless of any other policies applied:

* _Booleans_ - `bool`
* _Numerics_ - `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, `decimal`
* _Strings_ - `string`, `byte[]`
* _Temporals_ - `DateTime`, `DateTimeOffset`, `TimeSpan`
* _Others_ - `Guid`, `Uri`
* _Nullables_ - nullable versions of any of the types above

### Collections

If the object passed as a property is `IEnumerable`, Serilog will treat that property as a collection.

```csharp
var fruit = new[] { "Apple", "Pear", "Orange" };
Log.Information("In my bowl I have {Fruit}", fruit);
```

The equivalent JSON includes an array.

```csharp
{ "Fruit": ["Apple", "Pear", "Orange"] }
```

Serilog makes this choice because most enumerable types are of interest for their elements, and represent poorly as structures or strings.

Serilog also recognises `Dictionary<TKey,TValue>`, as long as the key type is one of the scalar types listed above.

```csharp
var fruit = new Dictionary<string,int> {{ "Apple", 1}, { "Pear", 5 }};
Log.Information("In my bowl I have {Fruit}", fruit);
```

Formatters that support dictionaries can record the property as such.

```csharp
{ "Fruit": { "Apple": 1, "Pear": 5 }}
```

**`IDictionary<TKey,TValue>`** - objects implementing dictionary interfaces are not serialised as dictionaries. Firstly because it is less efficient in .NET to check for generic interface compatibility, and second because a single object may implement more than one generic dictionary interface, creating an ambiguity.

### Objects

Apart from the types above, which are specially handled by Serilog, it is difficult to make intelligent choices about how data should be rendered and persisted. Objects not explicitly intended for serialisation tend to serialise very poorly.

```csharp
SqlConnection conn = ...;
Log.Information("Connected to {Connection}", conn);
```

(Yikes! How does one serialise an `SqlConnection`?)

When Serilog doesn't recognise the type, and no operator is specified (see below) then the object will be rendered using `ToString()`.

## Preserving Object Structure

There are many places where, given the capability, it makes sense to serialise a log event property as a structured object. DTOs (data transfer objects), messages, events and models are often best logged by breaking them down into properties with values.

For this task, Serilog provides the `@` _destructuring_ operator.

```csharp
var sensorInput = new { Latitude = 25, Longitude = 134 };
Log.Information("Processing {@SensorInput}", sensorInput);
```

('Destructuring' is a term borrowed from various programming languages; it is a style of pattern matching used to pull values out from structured data. The usage is Serilog is only notionally related at the moment, but possible future extensions to this operator could match the broader definition more accurately.) 

### Customizing the stored data

Often only a selection of properties on a complex object are of interest. To customise how Serilog persists a destructured complex type, use the `Destructure` configuration object on `LoggerConfiguration`:

```csharp
Log.Logger = new LoggerConfiguration()
    .Destructure.ByTransforming<HttpRequest>(
        r => new { RawUrl = r.RawUrl, Method = r.Method })
    .WriteTo...
```

This example transforms objects of type `HttpRequest` to preserve only the `RawUrl` and `Method` properties. A number of different strategies for destructuring are available, and custom ones can be created by implementing `IDestructuringPolicy`.

**Note:** the function provided to `Destructure.ByTransforming()` must return a different type from the one passed in, or it will be called recursively. Use a custom `IDestructuringPolicy` instead to implement conditional transformations.

### Operators vs. Formats

While both operators and formats affect the representation of a property, it is important to realise their distinct roles. Operators are applied at the point the property is captured, to preserve or structure the data in some way. Formats are used only when displaying properties as text, and don't impact the serialised representation at all.

### Formatting Collections and Structures

When format strings are specified for complex properties, they are generally ignored. Only enumerables take format string strings into account, passing them to their elements when rendering for display.

## Forcing Stringification

Sometimes, the type of an object being logged may not be exactly known, or may vary in a way that is undesirable to preserve in the log events. In these cases the `$` stringification operator will convert the property value to a string before any other processing takes place, regardless of its type or implemented interfaces.

```csharp
var unknown = new[] { 1, 2, 3 }
Log.Information("Received {$Data}", unknown);
```

Despite being an enumerable type, the `unknown` variable is captured and rendered as a string.

```
Received "System.Int32[]"
```
