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

### Example of middleware

```c#
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app)
    {
        int x = 2;
        app.Run(async (context) =>
        {
            x = x * 2;  //  2 * 2 = 4 and so on with every request.
            await context.Response.WriteAsync($"Result: {x}");
        });
    }
}
```

Everytime there is a request, x value is doubled and sent as a response.

#### Run Method

Proccesses a request, but doesn't send it further. Should be used in the end of the pipeline.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello World!");
        });
    }
}
```

#### Use Method

Proccesses a request and sends it further.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        int x = 5;
        int y = 8;
        int z = 0;
        app.Use(async (context, next) =>
        {
            z = x * y;
            await next.Invoke(); // Proccesses request and then invokes the next method. In this case, the next method is Run().
        });

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync($"x * y = {z}");
        });
    }
}
```

**!IMPORTANT!** Don't invoke the next method after `context.Response.WriteAsync()`. One component should be responsible only for one task, not both. And it can cause HTTP protocol violation.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        int x = 2;
        app.Use(async (context, next) =>
        {
            x = x * 2;      // 2 * 2 = 4
            await next.Invoke();    // The program goes to the next method and waits for it to finish.
                                    // Then the program goes back here and does the unfinished stuff.
            x = x * 2;      // 8 * 2 = 16
            await context.Response.WriteAsync($"Result: {x}");
        });

        app.Run(async (context) =>
        {
            x = x * 2;  //  4 * 2 = 8
            await Task.FromResult(0);
        });
    }
}
```

#### Map Method

Used to correspond each request path with its own delegate to proccess.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Map("/index", Index);
        app.Map("/about", About);

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Page Not Found");
        });
    }

    private static void Index(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Index");
        });
    }
    private static void About(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("About");
        });
    }
}
```

Nested Map is supported.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Map("/home", home =>
        {
            home.Map("/index", Index);
            home.Map("/about", About);
        });

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Page Not Found");
        });
    }

    private static void Index(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Index");
        });
    }
    private static void About(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("About");
        });
    }
}
```

##### MapWhen Method

Acts like Map, but accepts `Func<HttpContext, bool>` delegate and proccesses a request only if this delegate is `true`.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        // If request has id key and its value is 5, invoke HandleId method.
        app.MapWhen(context => {
            return context.Request.Query.ContainsKey("id") &&
                    context.Request.Query["id"] == "5";
        }, HandleId);

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Good bye, World...");
        });
    }

    private static void HandleId(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("id is equal to 5");
        });
    }
}
```

#### Custom Middleware

There is a possibility to write custom middleware.

```c#
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    // This constructer is called by the ASP.NET itself.
    // RequestDelegate is the next middleware in the pipeline.
    public TokenMiddleware(RequestDelegate next)
    {
        this._next = next;
    }

    // Can be InvokeAsync or Invoke.
    public async Task InvokeAsync(HttpContext context)
    {
        var token = context.Request.Query["token"];

        // If token key is 12345678? go to the next component of the pipeline.
        // Else, send to request that token is invalid.
        if (token!="12345678")
        {
            context.Response.StatusCode = 403;
            await context.Response.WriteAsync("Token is invalid");
        }
        else
        {
            await _next.Invoke(context);
        }
    }
}
```

To use a custom middleware, simply call a `UseMiddleware<T>();` of an IApplicationBuilder object.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.UseMiddleware<CustomMiddleware>();

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello World");
        });
    }
}
```

There is a practice of helper classes usage. Instead of calling `UseMiddleware<T>()`, you can create a new class with the following content:

```c#
using Microsoft.AspNetCore.Builder;
public static class MiddlewareExtensions
{
    public static IApplicationBuilder UseCustomMiddleWare(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<CustomMiddleware>();
    }
}
```

So now we can simply call `UseCustomMiddleware()` of object IApplicationBuilder, because we injected the standart IApplicationBuilder with our custom middleware.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        //app.UseMiddleware<CustomMiddleware>();
        app.UseCustomMiddleware();

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello World");
        });
    }
}
```

Also we can pass parameters to custom middleware.

```c#
//  CustomMiddleware.cs
public class CustomMiddleware
{
    private readonly RequestDelegate _next;
    private string _pattern;

    public TokenMiddleware(RequestDelegate next, string pattern)
    {
        this._next = next;
        this._pattern = pattern;
    }

    // Can be InvokeAsync or Invoke.
    public async Task InvokeAsync(HttpContext context)
    {
        var token = context.Request.Query["token"];

        // If token key is 12345678? go to the next component of the pipeline.
        // Else, send to request that token is invalid.
        if (token != pattern)
        {
            context.Response.StatusCode = 403;
            await context.Response.WriteAsync("Token is invalid");
        }
        else
        {
            await _next.Invoke(context);
        }
    }
}

// MiddlewareExtensions.cs
using Microsoft.AspNetCore.Builder;
public static class MiddlewareExtensions
{
    public static IApplicationBuilder UseCustomMiddleWare(this IApplicationBuilder builder, string pattern)
    {
        return builder.UseMiddleware<CustomMiddleware>(pattern);
    }
}

// Configure Method
public void Configure(IApplicationBuilder app)
{
    app.UseCustomMiddleware("555555");

    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World");
    });
}
```

Notice how we don't need to pass IApplicationBuilder as a parameter when calling `UseCustomMiddleware()` method. It is done by ASP.NET Core.

##### Example

```c#
public class RoutingMiddleware
{
    private readonly RequestDelegate _next;
    public RoutingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        string path = context.Request.Path.Value.ToLower();
        
        // If request path is determined in the code, write a message.
        // Else, set status code to 404 (not found).
        if (path == "/index")
        {
            await context.Response.WriteAsync("Home Page");
        }
        else if (path == "/about")
        {
            await context.Response.WriteAsync("About");
        }
        else
        {
            context.Response.StatusCode = 404;
        }
    }
}

// Some form of the authenticating.
public class AuthenticationMiddleware
{
    private RequestDelegate _next;
    public AuthenticationMiddleware(RequestDelegate next)
    {
        _next = next;
    }
    public async Task InvokeAsync(HttpContext context)
    {
        var token = context.Request.Query["token"];

        // If token is not null or space,
        // authenticating is successful and
        // else is called to continue the pipeline.
        // If token is null, set status code to 403 (access denied)
        if (string.IsNullOrWhiteSpace(token))
        {
            context.Response.StatusCode = 403;
        }
        else
        {
            await _next.Invoke(context);
        }
    }
}

// Handle Errors in the pipeline proccess.
public class ErrorHandlingMiddleware
{
    private RequestDelegate _next;
    public ErrorHandlingMiddleware(RequestDelegate next)
    {
        _next = next;
    }
    public async Task InvokeAsync(HttpContext context)
    {
        // Invoke the next method.
        await _next.Invoke(context);

        // If after all of thhe methods ahead
        // status code of the http response is 403,
        // write Access Denied.
        if (context.Response.StatusCode == 403)
        {
            await context.Response.WriteAsync("Access Denied");
        }

        // Else if status code is 404, write Not Found.
        else if (context.Response.StatusCode == 404)
        {
            await context.Response.WriteAsync("Not Found");
        }
    }
}

public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.UseMiddleware<ErrorHandlingMiddleware>();
        app.UseMiddleware<AuthenticationMiddleware>();
        app.UseMiddleware<RoutingMiddleware>();
    }
}
```

## IHostingEnvironment

Objects of this type are used for communication with the environment of the application. This interface offers several properties, which can be of help:

- ApplicationName - name of the application.
- EnvironmentName - returns description of the environment of the application.
- ContentRootPath - returns root path of the application.
- WebRootPath - returns path to a folder, where static content of the application is. Commonly, it is a `wwwroot` folder.
- ContentRootFileProvider - returns an implementation of `Microsoft.AspNetCore.FileProviders.IFileProvider` interface, which can be used to read files in ContentRootPath.
- WebRootFileProvider - returns an implementation of `Microsoft.AspNetCore.FileProviders.IFileProvider` interface, which can be used to read files in WebRootPath.

These settings can be set in `launchSettings.json` file of the project.

### IWebHostEnvironment

Has some special methods like:

- IsEnvironment(string envName) - returns true if the name of the environment equals `envName`. It means that you can have custom environment names, not only "Development", "Staging" or "Production".
- IsDevelopment() - returns true if the name of the environment is "Development".
- IsStaging() - returns true if the name of the environment is "Staging".
- IsProduction() - returns true if the name of the environment is "Production".

For example, this lines of code execute only if the application is in development proccess. It means that it won't be executed when the application is in production.

```c#
if (env.IsDevelopment())
{
    // Tells the application to show error pages
    // with full error traceback. Can be EXTREMELY
    // dangerous for confidentiality
    // when this happens in the production.
    app.UseDeveloperExceptionPage();
}
```

## Static Files

Static files should be placed in ContentRoot/WebRoot. Standart folder for WebRoot is `wwwroot`. To access static files, `app.UseStaticFiles()` middleware should be used.

```c#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.UseStaticFiles();   // Add support for static files.

        // Runs only if there is no files in wwwroot folder to show.
        // For example, if we have wwwroot/index.html file,
        // when request comes and its url is /index.html,
        // this file will be shown as a response and that's it.
        // If there is no such file, output "Hello Babe" instead.
        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello Babe");
        });
    }
}
```

It is possible to override the default path for WebRoot. For example, if you wanted to use "static" folder instead of "wwwroot", use `webBuilder.UseWebRoot("static");`.
Notice that this is a Program.cs file.

```c#
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

                //Override the default WebRoot to some custom.
                webBuilder.UseWebRoot("static");
            });
}
```
