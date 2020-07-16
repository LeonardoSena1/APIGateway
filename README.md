# APIGateway

#Como usar essa Api de exemplo


biblioteca para APIGateway:
*Ocelot

Retirando toda as informações que a microsoft oferece.

-----------------------------Startup.cs

private IConfiguration _configuration;

public Startup(IConfiguration configuration)
{
    _configuration = configuration;
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{

  app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/", async context => {
			
			//teste para confirma que api está funcionando	
            await context.Response.WriteAsync("API Gateway funcionando");
        });
    });

	app.UseOcelot().Wait();
}




-----------------------------Program.cs

Subistituir:

ConfigureWebHostDefaults(webBuilder =>
{
    webBuilder.UseStartup<Startup>();
});


Por:
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config
        .SetBasePath(hostingContext.HostingEnvironment.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{hostingContext.HostingEnvironment.EnvironmentName}.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"configuration.{hostingContext.HostingEnvironment.EnvironmentName}.json")
        .AddEnvironmentVariables();
})
.ConfigureWebHostDefaults(webBuilder =>
{
    webBuilder.UseStartup<Startup>();
});



------------------------Criar um aruiqvo Configuration.Development.json:
{
  "ReRoutes": [
    {
      "DownstreamPathTemplate": "/{everything}",
      "DownstreamScheme": "https",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 44339
        }
      ],
      "UpstreamPathTemplate": "/api1/{everything}",
      "UpstreamHttpMethod": [ "Get", "Post", "Put", "Delete" ]
    },
    {
      "DownstreamPathTemplate": "/{everything}",
      "DownstreamScheme": "https",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 44377
        }
      ],
      "UpstreamPathTemplate": "/api2/{everything}",
      "UpstreamHttpMethod": [ "Get", "Post", "Put", "Delete" ]
    }
  ],

  "GlobalConfiguration": {
  }
}


Robar os 3 projetos

abrir o localhost do ApiGateway no postman e fazer as chamadas das apis


APIGateway : https://localhost:44335/

https://localhost:44335/api1/weatherforecast
https://localhost:44335/api2/weatherforecast
