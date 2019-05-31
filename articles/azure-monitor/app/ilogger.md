---
title: ILogger를 사용하여 Azure Application Insights에서 .NET 추적 로그 살펴보기
description: Azure Application Insights ILogger 공급자를 사용 하 여 ASP.NET Core 및 콘솔 응용 프로그램의 샘플입니다.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fd5a16334fff0319d7993fb2403a48d1777f6bce
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955334"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>.NET Core ILogger에 대 한 ApplicationInsightsLoggerProvider 로그

ASP.NET Core는 여러 가지 기본 제공 및 타사 로깅 공급자를 사용 하 여 작동 하는 로깅 API를 지원 합니다. 호출 하 여 로깅을 수행할 **log ()** 또는에서 변형 *ILogger* 인스턴스. 이 문서에서는 사용 하는 방법을 보여 줍니다 *ApplicationInsightsLoggerProvider* 콘솔 및 ASP.NET Core 응용 프로그램에서 ILogger 로그를 캡처할 수 있습니다. 또한 ApplicationInsightsLoggerProvider 다른 Application Insights 원격 분석을 통합 하는 방법에 대해서도 설명 합니다.
자세한 내용은 [ASP.NET Core의 로깅](https://docs.microsoft.com/aspnet/core/fundamentals/logging)을 참조하세요.

## <a name="aspnet-core-applications"></a>ASP.NET Core 응용 프로그램

ApplicationInsightsLoggerProvider에서 기본적으로 사용 됩니다 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.0-beta3 (이상)의 표준 중 하나를 통해 일반 Application Insights 모니터링 설정 방법:
- 호출 하 여 합니다 **UseApplicationInsights** IWebHostBuilder 확장 메서드 
- 호출 하 여 합니다 **AddApplicationInsightsTelemetry** IServiceCollection 확장 메서드

ApplicationInsightsLoggerProvider 캡처하는 ILogger 로그 수집 되는 다른 원격 분석과 동일한 구성을 적용 됩니다. TelemetryInitializers 및 TelemetryProcessors의 동일한 집합, 동일한 TelemetryChannel, 및이 상관 관계 및 다른 원격 분석을 동일한 방식으로 샘플링 합니다. 버전 2.7.0-beta3 사용 하거나 나중에 조치가 필요 하지 않습니다 ILogger 로그를 캡처할 합니다.

만 *경고* 또는 더 높은 ILogger 로그 (모든 범주)에서 기본적으로 Application Insights로 전송 됩니다. 수 있습니다 [이 동작을 수정 하는 필터를 적용](#control-logging-level)합니다. ILogger 로그를 캡처할 하려면 추가 단계가 필요 **Program.cs** 하거나 **Startup.cs**합니다. (참조 [ASP.NET Core 응용 프로그램의 Startup.cs에서 Program.cs ILogger 캡처 로그](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Microsoft.ApplicationInsights.AspNet SDK의 이전 버전을 사용 또는 다른 Application Insights 모니터링 없이 ApplicationInsightsLoggerProvider 사용 하려는 경우 다음 절차를 따르십시오.

1. NuGet 패키지를 설치 합니다.

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 수정할 **Program.cs** 다음과 같이 합니다.

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

2 단계에서 코드 구성 `ApplicationInsightsLoggerProvider`합니다. 다음 코드 예를 사용 하는 컨트롤러 클래스를 보여 줍니다. `ILogger` 로그를 보내려고 합니다. 로그는 Application Insights에 의해 캡처됩니다.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core 앱에서 Startup.cs에서 Program.cs ILogger 로그 캡처

새 ApplicationInsightsLoggerProvider 응용 프로그램 시작 파이프라인 초기에 로그를 캡처할 수 있습니다. ApplicationInsightsLoggerProvider (버전 2.7.0-beta3부터 시작) 하는 Application Insights에서 자동으로 활성화 되어, 있지만 파이프라인의 뒷부분에 나오는까지 설정 하는 계측 키가 필요는 없습니다. 따라서에서 로그만 **컨트롤러**/ 다른 클래스 캡처됩니다. 부터 모든 로그를 캡처하려면 **Program.cs** 하 고 **Startup.cs** 자체에 명시적으로 설정 해야 계측 키 ApplicationInsightsLoggerProvider에 대 한 합니다. 또한 *TelemetryConfiguration* 에서 로그 하도록 완전히 설정 되지 않습니다 **Program.cs** 하거나 **Startup.cs** 자체입니다. 따라서 이러한 로그 InMemoryChannel, 샘플링 없음 고 표준 원격 분석 이니셜라이저 또는 프로세서를 사용 하는 최소 구성을 해야 합니다.

다음 예제에서는이 기능을 보여 줍니다 **Program.cs** 하 고 **Startup.cs**합니다.

#### <a name="example-programcs"></a>예제 Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>예제 Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>이전 ApplicationInsightsLoggerProvider에서 마이그레이션

Microsoft.ApplicationInsights.AspNet SDK 버전 2.7.0-beta2는 로깅 공급자를 지원 하기 전에 사용 되지 않습니다. 이 공급자를 통해 사용 하도록 설정 합니다 **AddApplicationInsights()** ILoggerFactory의 확장 메서드. 두 단계를 포함 하는 새 공급자를 마이그레이션하는 것이 좋습니다.

1. 제거 합니다 *ILoggerFactory.AddApplicationInsights()* 에서 호출 합니다 **Startup.Configure()** double 로깅을 방지 하는 방법입니다.
2. 새 공급자가 적용 되지 것입니다 때문에 코드에서 필터링 규칙을 다시 적용 합니다. 오버 로드 *ILoggerFactory.AddApplicationInsights()* 최소 LogLevel 또는 필터 함수를 수행 합니다. 새 공급자를 사용 하 여 필터링은 자체 로깅 프레임 워크의 일부입니다. Application Insights 공급자가 수행 되지 않습니다. 통해 제공 되는 모든 필터 *ILoggerFactory.AddApplicationInsights()* 오버 로드를 제거 해야 합니다. 필터링 규칙에 따라 제공 해야 합니다 [로깅 수준을 제어](#control-logging-level) 지침입니다. 사용 하는 경우 *appsettings.json* 로깅을 필터링 하려면 계속 새 공급자를 사용 하려면 둘 다 동일한 공급자 별칭을 사용 하므로 *ApplicationInsights*합니다.

여전히 이전 공급자를 사용할 수 있습니다. (제거 됩니다 3 주 버전 변경에만 합니다. *xx*.) 하지만 다음과 같은 이유로 새 공급자를 마이그레이션하는 것이 좋습니다.

- 이전 공급자에 대 한 지원 부족 [로그 범위](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)합니다. 새 공급자의 범위에서 속성은 자동으로 사용자 지정 속성으로 수집된 된 원격 분석에 추가 됩니다.
- 로그는 이제 응용 프로그램 시작 파이프라인에서 훨씬 더 빨리 캡처할 수 있습니다. 로그를 **프로그램** 하 고 **시작** 클래스는 이제 캡처할 수 있습니다.
- 새 공급자를 사용 하 여 자체 프레임 워크 수준의 필터링이 수행 합니다. 콘솔, Debug와 같은 기본 제공 공급자를 비롯 한 다른 공급자와 마찬가지로 로그는 Application Insights 공급자를 필터링 하 고 등 수 있습니다. 또한 여러 공급자에 게 동일한 필터를 적용할 수 있습니다.
- ASP.NET core (2.0 이상)에서 권장 되는 방법은 [로깅 공급자를 사용 하도록 설정](https://github.com/aspnet/Announcements/issues/255) ILoggingBuilder에서에서 확장 메서드를 사용 하 여 **Program.cs** 자체입니다.

> [!Note]
> 새 공급자란 이상 NETSTANDARD2.0를 대상으로 하는 응용 프로그램에서 사용할 수 있습니다. 응용 프로그램에.NET Core 1.1 등의 이전.NET Core 버전을 대상으로 하거나.NET Framework를 대상으로 하는 경우 계속 이전 공급자를 사용 합니다.

## <a name="console-application"></a>콘솔 애플리케이션

다음 코드에서는 ILogger 추적을 Application Insights로 전송 하도록 구성 되어 있는 샘플 콘솔 응용 프로그램을 보여 줍니다.

설치된 패키지:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

이 예제에서는 독립 실행형 패키지 `Microsoft.Extensions.Logging.ApplicationInsights`합니다. 기본적으로이 구성을 사용 하 여 "최소한" TelemetryConfiguration Application Insights에 데이터를 전송 합니다. 최소한 InMemoryChannel 사용 되는 채널을 의미 합니다. 샘플링 및 이상 표준 TelemetryInitializers 없는 경우 콘솔 응용 프로그램의 경우 다음 예제와 같이이 동작을 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션을 사용 하 여 기본 TelemetryConfiguration을 재정의 하는 방법을 보여 줍니다는 **서비스입니다. 구성할<TelemetryConfiguration>()** 메서드. 이 예제에서는 설정 `ServerTelemetryChannel` 및 샘플링 합니다. 사용자 지정 ITelemetryInitializer TelemetryConfiguration을 추가합니다.

```csharp
    // Create the DI container.
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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>컨트롤 로깅 수준

ASP.NET Core *ILogger* 인프라에 적용할 기본 제공 메커니즘이 [로그 필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)합니다. 이 기능을 사용 하면 Application Insights 공급자를 포함 하 여 등록 된 각 공급자에 전송 되는 로그를 제어할 수 있습니다. 필터링을 수행할 수 있으며 구성에서 (일반적으로 사용 하 여는 *appsettings.json* 파일) 또는 코드입니다. 이 기능 자체 프레임 워크에서 제공 됩니다. Application Insights 공급자에 게 특정 것입니다.

다음 예제에서는 ApplicationInsightsLoggerProvider에 필터 규칙을 적용 합니다.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Appsettings.json 사용 하 여 구성에서 필터 규칙 만들기

공급자 별칭은 ApplicationInsightsLoggerProvider에 대 한 `ApplicationInsights`합니다. 다음 섹션 *appsettings.json* 에 대 한 로그를 구성 합니다 *경고* 이상인 모든 범주의 및 *오류* 이상로 시작 하는 범주에서 " 전송할 수 있도록 Microsoft" `ApplicationInsightsLoggerProvider`합니다.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>코드에서 필터 규칙 만들기

다음 코드 조각에 대 한 로그를 구성 합니다. *경고* 이상에 대 한 모든 범주에서 *오류* 이상 보낼 "Microsoft"로 시작 하는 범주의 `ApplicationInsightsLoggerProvider`합니다. 이 구성은 이전 섹션에서와 동일한 *appsettings.json*합니다.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider의 이전 버전과 새 버전은 무엇입니까?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 를 통해 사용 하도록 설정 된 기본 제공 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) 포함  **ILoggerFactory** 확장 메서드입니다. 이 공급자에서 버전 2.7.0-beta2 obsolete로 표시 되어 있습니다. 다음 주 버전 변경에서 완전히 제거 될 예정. 합니다 [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 패키지 자체는 사용 되지 않는 되지 않습니다. 해당 하는 데 필요한 요청, 종속성 및 등의 모니터링을 사용 하도록 설정 합니다.

권장 방법은 새 독립 실행형 패키지 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)에 향상 된 ApplicationInsightsLoggerProvider (포함 하는 Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) 및 ILoggerBuilder에 확장 메서드를 사용 하도록 설정 하는 것에 대 한 합니다.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.0-beta3 새 패키지에 종속 및 ILogger 캡처를 사용 하면 자동으로 합니다.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Application Insights에 두 번 일부 ILogger 로그를 표시 하는 이유

중복 ApplicationInsightsLoggerProvider 호출 하 여 사용 하도록 설정 (이제 사용 되지 않음) 이전 버전을 사용 하는 경우 발생할 수 있습니다 `AddApplicationInsights` 에서 `ILoggerFactory`합니다. 확인 프로그램 **구성** 메서드는 다음과 같은 및 제거 합니다.

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio에서 디버그 하는 경우 이중 로깅 발생 하면 설정 `EnableDebugLogger` 하 *false* 다음과 같이 Application Insights를 사용 하는 코드에서. 이 중복 및 수정 프로그램은 응용 프로그램을 디버깅할 때만 관련.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>업데이트 했지만 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.0-beta3 및 ILogger의 로그를 자동으로 캡처됩니다. 해제 하려면 어떻게 합니까이 기능은 완전히?

참조를 [로깅 수준을 제어](../../azure-monitor/app/ilogger.md#control-logging-level) 필터링 하는 방법을 보려면 섹션 일반적 기록 합니다. 전원 끄기 ApplicationInsightsLoggerProvider 사용 `LogLevel.None`:

**코드:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**구성:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>이유는 일부 ILogger 로그가 없는 동일한 속성을 다른 사용자가?

Application Insights를 캡처하고 다른 모든 원격 분석에 사용 되는 동일한 TelemetryConfiguration을 사용 하 여 ILogger 로그를 전송 합니다. 하지만 예외가 발생 합니다. 기본적으로 TelemetryConfiguration 완벽 하 게 하도록 설정 되지 않습니다에서 로그인 할 때는 **Program.cs** 하거나 **Startup.cs**합니다. 이러한 위치에서 로그는 모든 TelemetryInitializers 및 TelemetryProcessors 실행 되지 않는 있도록 기본 구성으로를 없습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>독립 실행형 패키지 Microsoft.Extensions.Logging.ApplicationInsights 합니다 그런가요 및 일부 추가 사용자 지정 원격 분석을 수동으로 로그인 하려고 하는 경우. 어떻게 해야 합니까?

독립 실행형 패키지를 사용 하는 경우 `TelemetryClient` 의 새 인스턴스를 만들어야 하므로 DI 컨테이너에 삽입 되지 않습니다는 `TelemetryClient` 고로 거 공급자는 다음 코드와 같이 동일한 구성을 사용 합니다. 이렇게 하면 동일한 구성을 ILogger에서 원격 분석 뿐만 아니라 모든 사용자 지정 원격 분석에 대 한 사용 됩니다.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Microsoft.ApplicationInsights.AspNetCore 패키지를 사용 하 여 Application Insights를 사용 하도록 설정 하는 경우이 코드를 수정 `TelemetryClient` 생성자에서 직접. 예를 들어 참조 [이 FAQ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)합니다.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Application Insights 원격 분석 유형은 ILogger 로그에서 생성 됩니다. 또는 Application Insights에서 로그 ILogger 어디서 확인할 수 있나요?

ApplicationInsightsLoggerProvider는 ILogger 로그 캡처하고 TraceTelemetry에서 만듭니다. 예외 개체에 전달 되 면 합니다 **log ()** ILogger 메서드 *ExceptionTelemetry* TraceTelemetry 대신 만들어집니다. 포털, 분석 또는 로컬 Visual Studio 디버거를 포함 하 여 Application Insights에 대 한 다른 TraceTelemetry 또는 ExceptionTelemetry와 동일한 위치에 이러한 원격 분석 항목을 찾을 수 있습니다.

항상 TraceTelemetry를 송신 하려는 경우이 조각을 사용 합니다. ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK가 설치 하 고, 없는 경우 및 ASP.NET Core 응용 프로그램에 대 한 Application Insights를 사용 하도록 설정 하려면 Azure 웹 앱 확장을 사용 합니다. 새 공급자를 사용 하는 방법 

이전 공급자를 사용 하는 Azure Web Apps에서 Application Insights 확장 합니다. 필터링 규칙을 수정할 수 있습니다 합니다 *appsettings.json* 응용 프로그램에 대 한 파일입니다. 새 공급자를 활용 하려면 SDK에서 NuGet 종속성을 수행 하 여 빌드 시간 계측을 사용 합니다. 이 문서에서는 확장 새 공급자를 사용 하도록 전환 하는 경우 업데이트 됩니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Microsoft.Extensions.Logging.ApplicationInsights 독립 실행형 패키지를 사용 하 고 호출 하 여 Application Insights 공급자를 사용 하도록 설정 하면 **작성기입니다. AddApplicationInsights("ikey")** 합니다. 구성에서 계측 키를 가져오려면 옵션 있나요?


Program.cs 및 appsettings.json을 다음과 같이 수정 합니다.

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   관련 섹션에서 `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

이 코드는 독립 실행형 로깅 공급자를 사용 하는 경우에 필요 합니다. 일반 Application Insights 모니터링에 대 한 계측 키가 자동으로 로드 구성 경로에서 *ApplicationInsights: Instrumentationkey*합니다. Appsettings.json 다음과 같아야 합니다.

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [ASP.NET Core에 로그인](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights에서.NET 추적 로그](../../azure-monitor/app/asp-net-trace-logs.md)
