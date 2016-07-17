# Format Providers

There are a number of options available to formatting the output of types.  One example is the use of the format provider that is exposed in most sinks. 

For example, it may desirable to override or specify the way a `DateTime` is formatted.  This can be done via the implementation of `IFormatProvider`.

 
```csharp
public class Program
    {
        class User
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public DateTime Created { get; set; }
        }
        public static void Main(string[] args)
        {
            var log = new LoggerConfiguration()
                .WriteTo.LiterateConsole()
                .CreateLogger();

            var exampleUser = new User { Id = 1, Name = "Adam", Created = DateTime.Now };
            log.Information("Created {@User} on {Created}", exampleUser, DateTime.Now);

            Console.ReadLine();
        }
    }
```

```csharp
void Main()
{
    var myCustomDateTimeFormatter =
        new MyCustomDateFormatter("dd-MMM-yyyy", new CultureInfo("en-AU"));

    DateTime myDt = DateTime.Now;
    Console.WriteLine(myDt.ToString(null, myCustomDateTimeFormatter));
        
    var logger = new LoggerConfiguration()
        .WriteTo.Console(formatProvider: new CultureInfo("en-AU")) //Console1
        .WriteTo.Console(formatProvider: myCustomDateTimeFormatter) //Console2
        .CreateLogger();

    logger.Information("Created {@User} on {Created}", User.ExampleUser, DateTime.Now);
    // Console1 = 2016-07-17 11:27:32 [Information] Created User { Id: 1, Name: "Adam", Created: 17/07/2016 11:27:32 AM } on 17/07/2016
    // Console2 = 2016-07-17 11:27:32 [Information] Created User { Id: 1, Name: "Adam", Created: 17-Jul-2016  } on 17-Jul-2016
}

class MyCustomDateFormatter : IFormatProvider
{
    readonly IFormatProvider basedOn;
    readonly string shortDatePattern;
    public MyCustomDateFormatter(string shortDatePattern, IFormatProvider basedOn) {
        this.shortDatePattern = shortDatePattern;
        this.basedOn = basedOn;
    }
    public object GetFormat(Type formatType) {
        if (formatType == typeof(DateTimeFormatInfo)) {
            var basedOnDtfi = (DateTimeFormatInfo)basedOn.GetFormat(formatType);
            var dtfi = (DateTimeFormatInfo)basedOnDtfi.Clone();
            dtfi.ShortDatePattern = this.shortDatePattern;
            return dtfi;
        }
        return this.basedOn;
    }
}

class User {
    public static readonly User ExampleUser = new User { Id=1, Name="Adam", Created=DateTime.Now };
    public int Id { get; private set; }
    public string Name { get; private set; }
    public DateTime Created { get; private set; }
}
```