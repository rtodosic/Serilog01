## .Net Core Serilog – Basic

Serilog is quite easy to setup, the following outlines the simple steps to add Serilog to a .Net Core Web API project:
1.	Create a new a ASP.NET Core Web Application and select API. (Weather API Sample)
2.	Change the Get() method in WeatherForcastController.cs to the following:
  ```C#
  [HttpGet]
  public IEnumerable<WeatherForecast> Get()
  {
      var cnt = 10;
      var minTemp = -20;
      var maxTemp = 55;

      _logger.LogInformation("===> Get was called <===: Count: {Count}, MinTemp: {MinTemp}, MaxTemp: {MaxTemp}", cnt, minTemp, maxTemp);

      var rng = new Random();
      return Enumerable.Range(1, cnt).Select(index => new WeatherForecast
      {
          Date = DateTime.Now.AddDays(index),
          TemperatureC = rng.Next(minTemp, maxTemp),
          Summary = Summaries[rng.Next(Summaries.Length)]
      })
      .ToArray();
  }
  ```
3.	Run the app (without Serilog), you should see the default logging implementation at the command line.
  <<insert image>>
4.	Now add the “Serilog.AspNetCore” NuGet package to your project.
  <<insert image>>
5.	In Program.cs, add “using Serilog;” to the top of the file and change the  CreateHostBuilder() method to the following:
  ```C#
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
      .ConfigureWebHostDefaults(webBuilder =>
      {
        webBuilder.UseStartup<Startup>();
      })
      .UseSerilog((hostingContect, loggerConfiguration) => loggerConfiguration
        .WriteTo.Console()
  );
  ```
6.	In the Startup.cs file, add the following, before app.UseEndpoints() near the bottom of the file.
  ```C#
  public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
  {
      if (env.IsDevelopment())
      {
          app.UseDeveloperExceptionPage();
      }

      app.UseHttpsRedirection();

      app.UseRouting();

      app.UseAuthorization();

      app.UseSerilogRequestLogging();

      app.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
      });
  }
  ```
7.	Run the app again and notice the output.
8.  You have a basic implementation of Serilog.