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

This would provide the following output on the console

```
[07:12:57 INF] Created User {Id=1, Name="Adam", Created=07/18/2016 07:12:57} on 07/18/2016 07:12:57
```


```csharp
    public class Program
    {
        class User
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public DateTime Created { get; set; }
        }

        class ACustomDateFormatter : IFormatProvider
        {
            readonly IFormatProvider basedOn;
            readonly string shortDatePattern;
            public ACustomDateFormatter(string shortDatePattern, IFormatProvider basedOn)
            {
                this.shortDatePattern = shortDatePattern;
                this.basedOn = basedOn;
            }
            public object GetFormat(Type formatType)
            {
                if (formatType == typeof(DateTimeFormatInfo))
                {
                    var basedOnDateFormatInfo = (DateTimeFormatInfo)basedOn.GetFormat(formatType);
                    var dateFormatInfo = (DateTimeFormatInfo)basedOnDateFormatInfo.Clone();
                    dateFormatInfo.ShortDatePattern = this.shortDatePattern;
                    return dateFormatInfo;
                }
                return this.basedOn;
            }
        }

        public static void Main(string[] args)
        {

            var formatter = new ACustomDateFormatter("dd-MMM-yyyy", new CultureInfo("en-AU"));
            var log = new LoggerConfiguration() 
                .WriteTo.LiterateConsole(formatProvider: new CultureInfo("en-AU"))  //Console1
                .WriteTo.LiterateConsole(formatProvider: formatter)                 //Console2
                .CreateLogger();

            var exampleUser = new User { Id = 1, Name = "Adam", Created = DateTime.Now };
            log.Information("Created {@User} on {Created}", exampleUser, DateTime.Now);

            Console.ReadLine();
        }
    }
```