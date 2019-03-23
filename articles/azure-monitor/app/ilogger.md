---
title: ILogger를 사용하여 Azure Application Insights에서 .NET 추적 로그 살펴보기
description: ASP.NET Core 및 콘솔 애플리케이션에서 Application Insights ILogger 구현을 사용하는 예제
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: deaddfbd27c4ffe6738988c6368ce4f9c3a7fa78
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359146"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다. 이 문서에서는 콘솔 및 ASP.NET Core 애플리케이션에서 Application Insights ILogger 구현을 통해 로그를 처리하는 방법을 보여 줍니다. ILogger 기반 로깅에 대한 자세한 내용은 [이 문서](https://docs.microsoft.com/aspnet/core/fundamentals/logging)를 참조하세요.

## <a name="console-application"></a>콘솔 애플리케이션

다음은 Application Insights로 ILogger 추적을 보내도록 구성된 샘플 콘솔 애플리케이션입니다.

설치된 패키지:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>ASP.NET Core 애플리케이션

다음은 Application Insights로 ILogger 추적을 보내도록 구성된 샘플 ASP.NET Core 애플리케이션입니다. 이 예제에 따라 Program.cs, Startup.cs 또는 다른 컨트롤러/애플리케이션 논리에서 ILogger 추적을 보낼 수 있습니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up by Application Insights.  
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

위의 두 예제에서는 독립 실행형 패키지 Microsoft.Extensions.Logging.ApplicationInsights가 사용됩니다. 기본적으로 이 구성은 Application Insights에 데이터를 전송하기 위해 최저 `TelemetryConfiguration`을 사용합니다. 최저는 사용되는 채널이 샘플링 및 표준 TelemetryInitializers 없는 `InMemoryChannel`임을 의미합니다. 아래 예제와 같이 콘솔 애플리케이션의 경우 이 동작을 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션에서는 기본 `TelemetryConfiguration`을 재정의하는 방법을 보여 줍니다. 이 예제에서는 `ServerTelemetryChannel`, 샘플링 및 사용자 지정 `ITelemetryInitializer`를 구성합니다.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

위 접근 방법을 ASP.NET Core 애플리케이션에서 사용할 수 있지만, 좀 더 일반적인 방법은 아래와 같이 ILogger 캡처에 일반 애플리케이션 모니터링(요청, 종속성 등)을 결합하는 것입니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

다음을 `ConfigureServices` 메서드에 추가합니다. 이 코드는 기본 구성(ServerTelemetryChannel LiveMetrics, 요청/종속성, 상관 관계 등)의 일반 애플리케이션 모니터링을 사용하도록 설정합니다.

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

이 예제에서 `ApplicationInsightsLoggerProvider`에 사용되는 구성은 일반 애플리케이션 모니터링에서 사용되는 구성과 같습니다. 따라서 `ILogger` 추적 및 기타 원격 분석(요청, 종속성 등) 둘 다 동일한 `TelemetryInitializers` `TelemetryProcessors` 및 `TelemetryChannel` 세트에서 실행됩니다. 이러한 두 항목은 같은 방식으로 상관 관계가 형성되고 샘플링/샘플링 해제됩니다.

그러나 이 동작에는 예외가 있습니다. 기본 `TelemetryConfiguration`은 `Program.cs` 또는 `Startup.cs` 자체에서 로깅할 때는 완전히 설정되지 않으므로 이러한 로그에는 기본 구성이 사용되지 않습니다. 그러나 다른 모든 로그(예: 컨트롤러, 모델 등의 로그)는 구성을 공유합니다.

## <a name="control-logging-level"></a>컨트롤 로깅 수준

위의 예제와 같이 코드에서 로그를 필터링, 외에도 것도 가능 컨트롤을 수정 하 여 Application Insights 로깅 수준을 캡처하는 `appsettings.json`합니다. 합니다 [기초 설명서 로깅 ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) 이 작업을 수행 하는 방법을 보여 줍니다. 구체적으로 Application Insights 공급자 별칭의 이름은 `ApplicationInsights`에서처럼는 아래 구성 예제 `ApplicationInsights` 의 로그만 캡처하려면 `Warning` 이상 모든 범주에서.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [ILogger 기반 로깅](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [.NET 추적 로그](../../azure-monitor/app/asp-net-trace-logs.md)
