# ASP.NET Core

ASP.NET Core is the new web framework from Microsoft. It has been redesigned from the ground up to be fast, flexible, modern, and work across different platforms. Moving forward, ASP.NET Core is the framework that can be used for web development with .NET.

## Program.cs

There the application starts.

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

namespace App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
}
```

To start the app, **IHost** object is required, on which the whole app is basing. To create **IHost** IHostBuilder is being used. In the static method **CreateHostBuilder** IHostBuilder is created and configured. The creation of IHostBuilder is done by `Host.CreateDefaultBuilder(args)`. `ConfigureWebHostDefaults()` configures the IHostBuilder.<br>

`webBuilder.UseStartup<Startup>()` tells the application `Startup` is the starting class.<br>

Afther the IHostBuilder is created, `Build()` is being invoked to build an actual IHost, and `Run()` is called in the end. After that, application is running and web server starts to listen to all of the HTTP requests.
