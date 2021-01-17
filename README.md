# The-ASP.NET-Core-Request-Pipeline
20487C_MOD04_DEMO_L1


# The ASP.NET Core Request Pipeline

### Demonstration: Creating a Middleware for Custom Error Handling

1. Open a **Command Prompt** window.

2. To change the directory to the starter project, run the following command:

   ```bash
   cd [Repository Root]\Allfiles\Mod04\DemoFiles\ErrorHandlingMiddleware\Starter
   ```

3. To restore all dependencies and tools of a project, run the following command:

   ```base
   dotnet restore
   ```

4. Open the project in Microsoft Visual Studio Code, and then run the following command:

   ```bash
   code .
   ```

5. Expand the **BlueYonder.Flights.DAL** project, expand the **Repository** folder, and then select the **PassengerRepository** file.

6. To throw the **KeyNotFoundException** exception, locate the **GetPassenger** method, and then add the following code before **return**:

   ```cs
    if (passenger == null)
       throw new KeyNotFoundException();
   ```

7. Right-click **BlueYonder.Flights.Service**, select **New Folder**, and then name it **Middleware**.

8. Right-click the **Middleware** folder, select **New File**, name it **ExceptionHandlingMiddleware.cs**, and then select the new class.

9. Add the following **using** statements to the class:

   ```cs
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Net;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Http;
   using Newtonsoft.Json;
   ```

10. To add a namespace, enter the following code:

    ```cs
    namespace BlueYonder.Flights.Service.Middleware
    {
    
    }
    ```

11. To add a class declaration, enter the following code inside namespace brackets:

    ```cs
    public class ExceptionHandlingMiddleware
    {
    
    }
    ```

12. To add a constructor, enter the following code inside class brackets:

    ```cs
    private readonly RequestDelegate _next;
    
    public ExceptionHandlingMiddleware(RequestDelegate next)
    {
        _next = next;
    }
    ```

13. To add an **Invoke** method to catch all the exceptions, enter the following code:

    ```cs
    public async Task Invoke(HttpContext httpContext)
    {
        try
        {
             await _next(httpContext);
        }catch(Exception ex)
        {
            await HandleExceptionAsync(httpContext, ex);
        }
    }
    ```

14. To handle the exception and add **Status Code**, enter the following code:

    ```cs
    private Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        var code = HttpStatusCode.InternalServerError;
        if (exception is KeyNotFoundException) code = HttpStatusCode.NotFound;
        var result = JsonConvert.SerializeObject(new { error = exception.Message });
        context.Response.ContentType = "application/json";
        context.Response.StatusCode = (int)code;
        return context.Response.WriteAsync(result);
    }
    ```

15. To add **extension method** for **IApplicationBuilder**, enter the following code outside class brackets but inside namespace brackets:

    ```cs
    public static class ExceptionHandlingMiddlewareExtensions
    {
        public static IApplicationBuilder UseExceptionHandlingMiddleware(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<ExceptionHandlingMiddleware>();
        }
    }
    ```

16. In the **BlueYonder.Flights.Service** project, click the **Startup** file:

17. Add the following **using** statement:

    ```cs
    using BlueYonder.Flights.Service.Middleware;
    ```

18. To use **Exception Handling Middleware**, locate the **Configure** method and enter the following code before **app.UseHttpsRedirection();**:

    ```cs
    app.UseExceptionHandlingMiddleware();
    ```

19. Switch to the **Command Prompt** window.

20. To change the directory to **BlueYonder.Flights.Service**, run the following command:

    ```bash
    cd BlueYonder.Flights.Service  
    ```

21. To run the server, run the following command:

    ```bash
    dotnet run
    ```

22. Open Microsoft Edge, and then go to the following URL:

    ```url
    https://localhost:5001/api/passenger/5
    ```

23. In the title bar, click **Settings and more**, and then select **Developer Tools**.

24. In **Developer Tools**, click **Network**.

25. In the **Network** tab, locate the following URL:

    ```url
    https://localhost:5001/api/passenger/5
    ```

![20487D_Images](https://github.com/ialcaidef/The-ASP.NET-Core-Request-Pipeline/blob/master/Images/02.png)

26. Click the URL, and then verify that that the result column shows **404**.

![20487D_Images](https://github.com/ialcaidef/The-ASP.NET-Core-Request-Pipeline/blob/master/Images/01.png)

