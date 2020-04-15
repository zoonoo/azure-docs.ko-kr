---
title: ILogger - Azure 응용 프로그램 인사이트를 통해 .NET 추적 로그 탐색
description: ASP.NET 코어 및 콘솔 응용 프로그램을 사용하여 Azure 응용 프로그램 인사이트 ILogger 공급자를 사용하는 샘플입니다.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 33effe9cfec6d766d573617ff03b58564e5b34d1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313651"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>.NET 코어 ILogger 로그에 대한 응용 프로그램 인사이트로거 공급자

ASP.NET Core는 다양한 종류의 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다. 로깅은 *ILogger* 인스턴스에서 **Log()** 또는 변형을 호출하여 수행됩니다. 이 문서에서는 *ApplicationInsightsLoggerProvider를* 사용하여 콘솔에서 ILogger 로그를 캡처하고 핵심 응용 프로그램을 ASP.NET 방법을 보여 줍니다. 이 문서에서는 ApplicationInsightsLoggerProvider가 다른 응용 프로그램 인사이트 원격 분석과 통합하는 방법에 대해서도 설명합니다.
자세한 내용은 [ASP.NET Core의 로깅](https://docs.microsoft.com/aspnet/core/fundamentals/logging)을 참조하세요.

## <a name="aspnet-core-applications"></a>ASP.NET 핵심 애플리케이션

ApplicationInsightsLoggerProvider는 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1(및 이후)에서 기본적으로 활성화되어 있으며, 다음 방법 중 하나를 통해 일반 응용 프로그램 인사이트 모니터링을 켜십시오.

- IWebHostBuilder에서 **UseApplicationInsights** 확장 메서드를 호출 (이제 더 이상 사용 되지 않음)
- IServiceCollection에서 **AddApplicationInsightsTelemetry** 확장 메서드를 호출합니다.

ILogger는 ApplicationInsightsLoggerProvider 캡처가 수집된 다른 원격 분석과 동일한 구성의 적용을 받는다는 것을 로그합니다. 텔레메트리초기라이저 와 텔레메트리프로세서 세트는 동일하며 동일한 원격 분석 채널을 사용하며 다른 원격 분석과 동일한 방식으로 상호 관련되고 샘플링됩니다. 버전 2.7.1 이상을 사용하는 경우 ILogger 로그를 캡처하는 데 아무작업도 필요하지 않습니다.

모든 [범주에서](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category) *경고* 또는 더 높은 ILogger 로그만 기본적으로 응용 프로그램 인사이트로 전송됩니다. 그러나 [필터를 적용하여 이 동작을 수정할](#control-logging-level)수 있습니다. Program.cs **또는** **Startup.cs**ILogger 로그를 캡처하려면 추가 단계가 필요합니다. (Startup.cs [Program.cs Program.cs ASP.NET 핵심 응용 프로그램에서 ILogger 로그 캡처를](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)참조하십시오.)

이전 버전의 Microsoft.ApplicationInsights.AspNet SDK를 사용하거나 다른 응용 프로그램 인사이트 모니터링 없이 ApplicationInsightsLoggerProvider를 사용하려는 경우 다음 절차를 사용합니다.

1. NuGet 패키지 설치:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 여기에 표시된 대로 **Program.cs** 수정합니다.

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

2단계의 코드는 `ApplicationInsightsLoggerProvider`을 구성합니다. 다음 코드는 로그를 보내는 데 `ILogger` 사용하는 Controller 클래스 예제를 보여 주며, 이 클래스는 다음과 같은 것입니다. 로그는 응용 프로그램 인사이트에 의해 캡처됩니다.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Startup.cs ILogger 로그 캡처 및 ASP.NET 코어 앱에서 Program.cs

> [!NOTE]
> 코어 3.0 이후ASP.NET Startup.cs Program.cs 주입할 `ILogger` 수 없습니다. 자세한 내용은 https://github.com/aspnet/Announcements/issues/353를 참조하세요.

새로운 ApplicationInsightsLoggerProvider는 응용 프로그램 시작 파이프라인초기의 로그를 캡처할 수 있습니다. ApplicationInsightsLoggerProvider는 응용 프로그램 인사이트(버전 2.7.1부터 시작)에서 자동으로 활성화되지만 파이프라인의 후반부까지 계측 키를 설정하지 않습니다. 따라서 **컨트롤러**/다른 클래스의 로그만 캡처됩니다. **Program.cs** Startup.cs **자체로** 시작하여 모든 로그를 캡처하려면 ApplicationInsightsLoggerProvider에 대한 계측 키를 명시적으로 활성화해야 합니다. 또한 *원격 분석 구성은* **Program.cs** 로그온하거나 **자체적으로 Startup.cs** 때 완전히 설정되지 않습니다. 따라서 이러한 로그는 InMemoryChannel을 사용하는 최소 구성을 가지며 샘플링이 없으며 표준 원격 분석 초기화자 또는 프로세서가 없습니다.

다음 예제에서는 **Program.cs** 및 **Startup.cs**이 기능을 보여 줍니다.

#### <a name="example-programcs"></a>예 Program.cs

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

#### <a name="example-startupcs"></a>Startup.cs 예

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>이전 응용 프로그램인InsightsLogger공급자에서 마이그레이션

2.7.1 이전에 Microsoft.ApplicationInsights.AspNet SDK 버전은 이제 더 이상 사용되지 않는 로깅 공급자를 지원했습니다. 이 공급자는 ILoggerFactory의 **AddApplicationInsights()** 확장 메서드를 통해 활성화되었습니다. 다음 두 단계가 포함된 새 공급자로 마이그레이션하는 것이 좋습니다.

1. 이중 로깅을 방지하려면 **Startup.Configure()** 메서드에서 *ILoggerFactory.AddApplicationInsights()* 호출을 제거합니다.
2. 새 공급자가 적용되지 않으므로 코드에서 필터링 규칙을 다시 적용합니다. *ILoggerFactory.AddApplicationInsights()의* 오버로드는 최소 LogLevel 또는 필터 함수를 사용했습니다. 새 공급자를 사용하면 필터링이 로깅 프레임워크 자체의 일부입니다. 응용 프로그램 인사이트 공급자가 수행하지 않습니다. 따라서 *ILoggerFactory.AddApplicationInsights()* 오버로드를 통해 제공되는 모든 필터를 제거해야 합니다. 그리고 필터링 규칙은 [제어 로깅 수준](#control-logging-level) 지침에 따라 제공되어야 합니다. *appsettings.json을* 사용하여 로깅을 필터링하는 경우 둘 다 동일한 공급자 별칭인 ApplicationInsights를 사용하기 때문에 새 공급자와 계속 *작동합니다.*

이전 공급자를 계속 사용할 수 있습니다. (주 버전 변경 에서만 제거 됩니다 3. *xx.)* 그러나 다음과 같은 이유로 새 공급자로 마이그레이션하는 것이 좋습니다.

- 이전 공급자는 [로그 범위에](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)대한 지원이 없습니다. 새 공급자에서 범위의 속성은 수집된 원격 분석에 사용자 지정 속성으로 자동으로 추가됩니다.
- 이제 응용 프로그램 시작 파이프라인에서 훨씬 더 일찍 로그를 캡처할 수 있습니다. 이제 **프로그램** 및 **시작** 클래스의 로그를 캡처할 수 있습니다.
- 새 공급자를 사용하면 프레임워크 수준 자체에서 필터링이 수행됩니다. 콘솔, 디버그 등과 같은 기본 제공 공급자를 포함하여 다른 공급자와 동일한 방식으로 Application Insights 공급자로 로그를 필터링할 수 있습니다. 동일한 필터를 여러 공급자에 적용할 수도 있습니다.
- ASP.NET 코어(2.0 이상)에서는 [로깅 공급자를 사용하도록 설정하는](https://github.com/aspnet/Announcements/issues/255) 권장 방법은 **Program.cs** 자체에서 ILoggingBuilder에서 확장 메서드를 사용하는 것입니다.

> [!Note]
> NETSTANDARD2.0 이상을 대상으로 하는 응용 프로그램에 새 공급자를 사용할 수 있습니다. [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.14.0 이후부터 .NET Framework NET461 이상을 대상으로 하는 응용 프로그램에도 새 공급자를 사용할 수 있습니다. 응용 프로그램이 .NET Core 1.1과 같은 이전 .NET Core 버전을 대상으로 하거나 NET46 보다 작게 .NET Framework를 대상으로 하는 경우 이전 공급자를 계속 사용합니다.

## <a name="console-application"></a>콘솔 애플리케이션

> [!NOTE]
> 모든 콘솔 응용 프로그램에 대한 응용 프로그램 인사이트(ILogger 및 기타 응용 프로그램 인사이트 원격 분석)를 활성화하는 데 사용할 수 있는 [Microsoft.ApplicationInsights.WorkerService라는](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 새로운 응용 프로그램 인사이트 SDK가 있습니다. [여기에서](../../azure-monitor/app/worker-service.md)이 패키지 및 관련 지침을 사용하는 것이 좋습니다.

다음 코드는 ILogger 추적을 응용 프로그램 인사이트로 보내도록 구성된 샘플 콘솔 응용 프로그램을 보여 주며 있습니다.

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

이 예제에서는 독립 `Microsoft.Extensions.Logging.ApplicationInsights`실행형 패키지를 사용합니다. 기본적으로 이 구성은 응용 프로그램 인사이트로 데이터를 전송하기 위해 "최소" 원격 분석 구성을 사용합니다. 최소값은 InMemoryChannel이 사용되는 채널임을 의미합니다. 샘플링이 없고 표준 원격 측정 초기라이저가 없습니다. 다음 예제와 같이 콘솔 응용 프로그램에 대해 이 동작을 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션에서는 서비스를 사용하여 기본 TelemetryConfiguration을 재정의하는 방법을 보여 **주었습니다. 원격\<분석 구성>()** 메서드 구성. 이 예제는 `ServerTelemetryChannel` 설정 및 샘플링입니다. 원격 분석 구성에 사용자 지정 iTelemetry초기소프트웨어를 추가합니다.

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

## <a name="control-logging-level"></a>로깅 수준 제어

ASP.NET 코어 *ILogger* 인프라에는 [로그 필터링을](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)적용하는 기본 제공 메커니즘이 있습니다. 이렇게 하면 Application Insights 공급자를 포함하여 등록된 각 공급자에게 전송되는 로그를 제어할 수 있습니다. 필터링은 구성(일반적으로 *appsettings.json* 파일을 사용하여) 또는 코드에서 수행할 수 있습니다. 이 기능은 프레임워크 자체에서 제공합니다. 응용 프로그램 인사이트 공급자에만 국한되지 않습니다.

다음 예제는 응용 프로그램 인사이트Logger공급자에 필터 규칙을 적용합니다.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json을 사용하여 구성에서 필터 규칙 만들기

ApplicationInsightsLoggerProvider의 경우 공급자 별칭은 은 `ApplicationInsights` *appsettings.json의* 다음 섹션은 일반적으로 로깅 공급자에게 *경고* 수준 이상으로 기록하도록 지시합니다. 그런 다음 레벨 `ApplicationInsightsLoggerProvider` *오류* 이상에서 "Microsoft"로 시작하는 로그 범주를 재정의합니다.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>코드에서 필터 규칙 만들기

다음 코드 스니펫은 모든 범주에서 및 *오류* 이상에 대한 `ApplicationInsightsLoggerProvider` *경고* 에 대한 로그를 "Microsoft"로 시작하는 범주에서 로그를 구성합니다. 이 구성은 *appsettings.json의*이전 섹션과 동일합니다.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>응용 프로그램 인사이트로거 공급자의 이전 버전과 새 버전은 무엇입니까?

[Microsoft.ApplicationInsights.AspNet SDK에는](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) **ILoggerFactory** 확장 방법을 통해 활성화된 기본 제공자 인인사이트로거 공급자(Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)가 포함되어 있습니다. 이 공급자는 버전 2.7.1에서 더 이상 사용되지 않는 것으로 표시됩니다. 다음 주 버전 변경에서 완전히 제거됩니다. [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 패키지 자체는 더 이상 사용되지 않습니다. 요청, 종속성 등을 모니터링하는 데 필요합니다.

제안 된 대안은 새로운 독립 실행 형 패키지 [Microsoft.Extension.Loggings.ApplicationInsights,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)이는 향상된 응용 프로그램 인사이트로거 제공 자 (Microsoft.Extension.Loggings.ApplicationInsights.ApplicationInsightsLogger공급자) 및 ILoggerBuilder에 확장 메서드를 포함.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1은 새 패키지에 대한 종속성을 적용하고 ILogger 캡처를 자동으로 활성화합니다.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>일부 ILogger 로그가 응용 프로그램 인사이트에 두 번 표시되는 이유는 무엇입니까?

ApplicationInsightsLogger공급자의 `AddApplicationInsights` 이전 버전(현재 사용되지 않는) 버전이 있는 경우 중복이 `ILoggerFactory`발생할 수 있습니다. **Configure** 메서드에 다음이 있는지 확인하고 제거합니다.

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio에서 디버깅할 때 이중 `EnableDebugLogger` 로깅이 발생하는 경우 다음과 같이 응용 프로그램 인사이트를 활성화하는 코드에서 *false로* 설정합니다. 이 중복 및 수정은 응용 프로그램을 디버깅할 때만 관련이 있습니다.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>나는 [마이크로 소프트.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1로 업데이트하고, ILogger에서 로그가 자동으로 캡처됩니다. 이 기능을 완전히 끄면 어떻게 해야 합니까?

일반적으로 [로그를](../../azure-monitor/app/ilogger.md#control-logging-level) 필터링하는 방법을 보려면 로깅 제어 수준 섹션을 참조하십시오. 응용 프로그램 인사이트로거 공급자를 `LogLevel.None`끄려면 다음을 사용합니다.

**코드:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**구성에서:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>일부 ILogger 로그는 다른 로그와 동일한 속성을 갖지 않는 이유는 무엇입니까?

응용 프로그램 인사이트는 다른 모든 원격 분석에 사용되는 동일한 원격 분석 구성을 사용하여 ILogger 로그를 캡처하고 보냅니다. 그러나 예외가 있습니다. 기본적으로 원격 분석 구성은 **Program.cs** 또는 **Startup.cs**에서 로그온할 때 완전히 설정되지 않습니다. 이러한 위치에서 로그 기본 구성을 하지 않습니다., 그래서 그들은 모든 원격 측정 초기화 및 원격 측정 프로세서를 실행 되지 않습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>독립 실행형 패키지를 사용하고 있는 Microsoft.Extensions.Loggings.ApplicationInsights를 사용하고 있으며 몇 가지 추가 사용자 지정 원격 분석을 수동으로 기록하고 싶습니다. 어떻게 해야 하나요?

독립 실행형 패키지를 `TelemetryClient` 사용하는 경우 DI 컨테이너에 주입되지 않으므로 다음 코드와 `TelemetryClient` 같이 로거 공급자가 사용하는 것과 동일한 구성을 새 인스턴스를 만들고 사용해야 합니다. 이렇게 하면 ILogger의 원격 분석뿐만 아니라 모든 사용자 지정 원격 분석에도 동일한 구성이 사용됩니다.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Microsoft.ApplicationInsights.AspNetCore 패키지를 사용하여 응용 프로그램 인사이트를 사용하도록 설정하는 `TelemetryClient` 경우 이 코드를 수정하여 생성자에서 직접 가져옵니다. 예를 들어 이 [FAQ를](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)참조하십시오.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 로그에서 생성되는 응용 프로그램 인사이트 원격 분석 유형은 무엇입니까? 또는 응용 프로그램 인사이트에서 ILogger 로그를 어디에서 볼 수 있습니까?

ApplicationInsightsLogger공급자는 ILogger 로그를 캡처하고 그로부터 추적 텔레메타를 만듭니다. 예외 개체가 ILogger의 **Log()** 메서드에 전달되면 *예외 텔레메타가* TraceTelemetry 대신 만들어집니다. 이러한 원격 분석 항목은 포털, 분석 또는 Visual Studio 로컬 디버거를 포함하여 응용 프로그램 인사이트를 위한 다른 TraceTeletelemetry 또는 ExceptionTeletelemetry와 동일한 위치에서 찾을 수 있습니다.

항상 TraceTelemetry를 보내려면 다음 스니펫을 사용합니다.```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK가 설치되어 있지 않으며 Azure Web Apps 확장을 사용하여 ASP.NET 핵심 응용 프로그램에 대한 응용 프로그램 인사이트를 사용하도록 설정합니다. 새 공급자를 사용하려면 어떻게 해야 합니까? 

Azure 웹 앱의 응용 프로그램 인사이트 확장은 새 공급자를 사용합니다. 응용 프로그램에 대한 *appsettings.json* 파일에서 필터링 규칙을 수정할 수 있습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>독립 실행형 패키지 Microsoft.Extension.Loggings.ApplicationInsights를 사용하고 빌더를 호출하여 응용 프로그램 인사이트 공급자를 사용하도록 **설정합니다. 추가 응용 프로그램 인사이트 ("ikey")**. 구성에서 계측 키를 얻을 수있는 옵션이 있습니까?


다음과 같이 Program.cs 및 appsettings.json을 수정합니다.

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

   관련 섹션 `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

이 코드는 독립 실행형 로깅 공급자를 사용하는 경우에만 필요합니다. 정기적인 애플리케이션 인사이트 모니터링의 경우, 어드미티드 *인사이트: 계측키*구성 경로에서 계측 키가 자동으로 로드됩니다. Appsettings.json은 다음과 같이 표시되어야합니다.

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
* [응용 프로그램 인사이트에서 .NET 추적 로그](../../azure-monitor/app/asp-net-trace-logs.md)
