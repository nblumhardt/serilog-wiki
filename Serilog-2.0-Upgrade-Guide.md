_Serilog 2.0 is currently a release candidate (RC). The final release-to-web version will ship in conjunction with the .NET Core RTW in July 2016. These instructions are work-in-progress until that date._

Serilog 2.0 adds to the performance and reliability of the earlier Serilog releases, while aligning Serilog's packaging with the needs of new .NET targets including .NET Core and Linux. Very minimal breaking changes have been made where necessary - practically all code written against Serilog 1.5 will recompile against Serilog 2.0 without errors.

### Updating to Serilog 2.0

Until RTW, the `-Pre` (pre-release) flag is required when installing Serilog 2.0 packages.

The core Serilog package is `Serilog`. At the Visual Studio _Package Manager Console_:

```powershell
Update-Package Serilog -Pre
```

All projects used in a Serilog 2.0 application should be upgraded and rebuilt. Although source compatibility has been maintained, the old **Serilog.FullNetFx** assembly has been removed, and types in it such as `LogContext` moved into the core `Serilog` package. Failing to upgrade projects can lead to `TypeLoadException` being thrown at runtime.

### Installing 2.0 sink packages

In Serilog 1.5, the core _sinks_, including `Console`, `ColoredConsole`, `File`, `RollingFile` and so-on were part of the `Serilog` packages. These are now separate.

To install the new `Console` sink, use:

```powershell
Install-Package Serilog.Sinks.Console -Pre
```

The names of sink packages are consistent so that it's easy to guess the correct name, with very few exceptions:

| Sink name | `WriteTo.*` | Package |
| --------- | ----------- | ------- |
| Colored Console | `ColoredConsole` | [Serilog.Sinks.ColoredConsole](https://nuget.org/packages/serilog.sinks.coloredconsole) |
| Console | `Console` | [Serilog.Sinks.Console](https://nuget.org/packages/serilog.sinks.console) |
| File | `File` | [Serilog.Sinks.File](https://nuget.org/packages/serilog.sinks.file) |
| Rolling File | `RollingFile` | [Serilog.Sinks.RollingFile](https://nuget.org/packages/serilog.sinks.rollingfile) |

(TBC)

### Installing 2.0 enricher packages

If your application uses any of the built-in enrichers such as machine name or thread, you'll need to install the corresponding package:

```powershell
Install-Package Serilog.Enrichers.Environment -Pre
```

The three packages that were previously included in the core `Serilog` package are:

| Enricher package | `Enrich.*` |
| ---------------- | ---------- |
| [Serilog.Enrichers.Environment](https://nuget.org/packages/serilog.enrichers.environment) | `MachineName`, `UserName` |
| [Serilog.Enrichers.Process](https://nuget.org/packages/serilog.enrichers.process) | `ProcessId` |
| [Serilog.Enrichers.Thread](https://nuget.org/packages/serilog.enrichers.thread) | `ThreadId` |

### XML `<appSettings>` support

This has moved to [Serilog.Settings.AppSettings](https://nuget.org/packages/serilog.settings.appsettings):

```powershell
Install-Package Serilog.Settings.AppSettings -Pre
```

### Shutting down or disposing the logger

If your application uses any network-based or asynchronous sinks, Serilog 2.0 requires you to explicitly shut down the logging pipeline so that queued events are properly flushed before the application exits.

It's not strictly necessary to add this code if you only use local file/console logging, but considered best practice to do so anyway.

(This change makes Serilog's behavior consistent across platforms, including .NET Core, where `AppDomain` unloading events are no longer available).

If you use the static `Log` class:

```csharp
Log.CloseAndFlush();
```

Or otherwise, if you use the return value of `CreateLogger()` directly, you will need to call `Dispose()` on it.

This can be done:

 * At the end of `Main()`
 * In the `Application_End()` method of ASP.NET's `Global.asax`

### Getting help

If you need help with an upgrade to Serilog 2.0, please post your question at [Stack Overflow](https://stackoverflow.com). This will allow the widest group of people to assist you, as well as make the answer visible for others in a similar situation. Thanks!