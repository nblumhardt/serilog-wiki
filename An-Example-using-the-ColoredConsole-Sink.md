##An Example (ColoredConsole Sink)

- Create a new project
- Install the Serilog Package (`Install-Package Serilog`)
- Add the following code

```
using System;
using Serilog;

namespace A.Sample.Serilog.App.Using.The.ColoredConsole.Sink
{
	class MainClass
	{
		public static void Main (string[] args)
		{
			Log.Logger = new LoggerConfiguration()
				.WriteTo.ColoredConsole()
				.MinimumLevel.Debug()
				.CreateLogger();

			Log.Debug ("Debug");
			Log.Information ("Info");
			Log.Warning ("Warning");
			Log.Error ("Error");

			Console.ReadLine ();
		}
	}
}
```
- Run the app