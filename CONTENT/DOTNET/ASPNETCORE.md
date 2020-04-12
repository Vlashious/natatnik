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

## Startup Class

Startup is the entering point of the app. This class configures the app, services, which will be used, sets the components for request handling or middleware.

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

namespace App
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            // If the app is under development.
            if (env.IsDevelopment())
            {
                // Show the information about the error.
                app.UseDeveloperExceptionPage();
            }

            // Add possibilities of routing.
            app.UseRouting();

            // Sets the addresses to proccess.
            app.UseEndpoints(endpoints =>
            {
                // Proccessing of the request. context if the object of the request.
                endpoints.MapGet("/", async context =>
                {
                    // Send the response.
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

### ConfigureServices Method

Registers services used by the app. Receives `IServiceCollection`, which represents all of the services in the app. To add a service, `service.Add[ServiceName]` method should be called.

### Configure Method

Determines how the app is going to handle a request. To set components for handling, methods of `IApplicationBuilder` are used. `IWebHostEnvironment` lets you receive the information about the environment, where the app is starting, and work with it.

## Request Handling Pipeline

Proccessing of reuqests are like pipeline: the first component of the pipeline receives the request. After proccessing, it sends the request further to the next component and so on. These components of the pipeline are called **middleware**. In ASP.NET Core for middleware manipulation `Configure` method in `Startup.cs` class is used.<br>

### Configuring the middleware

To configure the middleware, methods of the object `IApplicationBuilder` that start with `Run`, `Map` and `Use` are used. Every component can be *inline* or have a separate class.<br>

To create a middleware, `public delegate Task RequestDelegate(HttpContext context)` is used, which does some stuff and receives the context of the request.<br>

**!IMPORTANT!** The order of the middleware matters!

### In-built middleware components in ASP.NET Core

- Authentication : support of authentication.
- Cookie Policy : traces the agreement of the user on collecting his information in cookies.
- CORS : support of crossdomen requests.
- Diagnostics : everything for program debugging.
- Forwarded Headers : redirects request headers.
- Health Check : checks the status of asp.net app.
- HTTP Method Override : lets incoming POST-requests to override method.
- HTTPS Redirection : redirects all HTTP requests to HTTPS.
- HTTP Strict Transport Security (HSTS) : for better privacy adds response header.
- MVC : support of MVC design pattern.
- Request Localization : support of localization.
- Response Caching : lets response caching.
- Response Compression : lets response compressing.
- URL Rewrite : lets URL Rewriting.
- Endpoint Routing : brings routing mechanism.
- Session - support of sessions.
- Static Files - support of static files proccessing.
- WebSockets - support of WebSockets protocol.
