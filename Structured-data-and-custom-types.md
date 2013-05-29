Serilog is a kind of serialiser. In many cases it has good default behaviour that suits its purpose, but on occasion it is necessary to instruct Serilog on how to store properties that are attached to a log event.

There are a few unusual terms that Serilog uses to refer to how .NET objects map to its internal (sink-agnostic) property representation. These are explained in more detail below, so you can skip this section if you're planning to read the whole page.

* _Stringification_ is the process of taking a supplied .NET property and invoking its `ToString()` method so that the representation reaching the sinks is a simple string
* _Destructuring_ is the process of taking a complex .NET object and converting it into a structure, which may later be represented as say, a JSON object or XML blob
* _Scalars_ are .NET types that can be represented as atomic values; most value types like `int` fit this description, but so do some reference types like `Uri` and `string`

## Why Control Representation?

There are 