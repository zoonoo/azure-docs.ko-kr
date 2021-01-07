---
title: ILogger-Azure 애플리케이션 Insights를 사용 하 여 .NET 추적 로그 탐색
description: ASP.NET Core 및 콘솔 응용 프로그램과 함께 Azure 애플리케이션 Insights ILogger 공급자를 사용 하는 샘플입니다.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 987d5b78c5fe680f43ff6a001e7a31a8ae9f6124
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931464"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider에 대 한 자세한 로깅

이 문서에서는를 사용 하 여 `ApplicationInsightsLoggerProvider` `ILogger` 콘솔 및 ASP.NET Core 응용 프로그램에서 로그를 캡처하는 방법을 보여 줍니다.
자세한 로깅에 대 한 자세한 내용은 [ASP.NET Core](/aspnet/core/fundamentals/logging)로그인을 참조 하세요.

## <a name="aspnet-core-applications"></a>응용 프로그램 ASP.NET Core

`ApplicationInsightsLoggerProvider` 는 [코드](./asp-net-core.md) 또는 [코드](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) 를 사용 하지 않는 방법을 사용 하 여 applicationinsights를 구성할 때 ASP.NET Core 응용 프로그램에 대해 기본적으로 사용 하도록 설정 됩니다.

*Warning* `ILogger` 기본적으로 모든 [범주의](/aspnet/core/fundamentals/logging/#log-category)경고 및 위의 로그만 Application Insights으로 전송 됩니다. 그러나 [이 동작을 사용자 지정할](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection)수 있습니다. **Program.cs** 또는 **Startup.cs**에서 ILogger 로그를 캡처하려면 추가 단계가 필요 합니다. [ASP.NET Core 응용 프로그램에서 Startup.cs 및 Program.cs의 ILogger 로그 캡처](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)를 참조 하세요.

`ApplicationInsightsLoggerProvider`다른 Application Insights 모니터링 없이만 사용 하려는 경우 다음 단계를 사용 합니다.

1. NuGet 패키지를 설치 합니다.

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. 다음과 같이 수정 합니다 `Program.cs` .

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
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

2 단계에서 코드를 구성 `ApplicationInsightsLoggerProvider` 합니다. 다음 코드에서는를 사용 하 여 로그를 보내는 예제 컨트롤러 클래스를 보여 줍니다 `ILogger` . 로그는 Application Insights에 의해 캡처됩니다.

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
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core apps의 Startup.cs 및 Program.cs에서 ILogger 로그 캡처

> [!NOTE]
> ASP.NET Core 3.0 이상에서는 더 이상 `ILogger` Startup.cs 및 Program.cs에 삽입할 수 없습니다. 자세한 내용은 https://github.com/aspnet/Announcements/issues/353을 참조하십시오.

`ApplicationInsightsLoggerProvider` 응용 프로그램 시작 초기부터 로그를 캡처할 수 있습니다. ApplicationInsightsLoggerProvider는 Application Insights (버전 2.7.1부터)에서 자동으로 사용 하도록 설정 되지만 파이프라인에서 나중에까지 계측 키를 설정 하지 않습니다. 따라서 **컨트롤러**/기타 클래스의 로그만 캡처됩니다. **Program.cs** 및 **Startup.cs** 자체에서 시작 하는 모든 로그를 캡처하려면 ApplicationInsightsLoggerProvider에 대 한 계측 키를 명시적으로 사용 하도록 설정 해야 합니다. 또한 **Program.cs** 또는 **Startup.cs** 자체에서 로그 하는 경우 *TelemetryConfiguration* 는 완전히 설정 되지 않습니다. 따라서 이러한 로그에는 [Inmemorychannel](./telemetry-channels.md), [샘플링](./sampling.md), 표준 [원격 분석 이니셜라이저 또는 프로세서](./api-filtering-sampling.md)를 사용 하지 않는 최소 구성이 포함 됩니다.

다음 예에서는 **Program.cs** 및 **Startup.cs**를 사용 하 여이 기능을 보여 줍니다.

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

#### <a name="example-startupcs"></a>예 Startup.cs

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

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>이전 ApplicationInsightsLoggerProvider에서 마이그레이션

2.7.1 이전 버전의 Microsoft SDK 버전은 이제 사용 되지 않는 로깅 공급자를 지원 합니다. 이 공급자는 ILoggerFactory의 **Addapplicationinsights ()** 확장 메서드를 통해 사용 하도록 설정 되었습니다. 다음 두 단계를 포함 하는 새 공급자로 마이그레이션하는 것이 좋습니다.

1. 이중 로깅을 방지 하려면 **Startup.Config시 ()** 메서드에서 *ILoggerFactory ()* 호출을 제거 합니다.
2. 모든 필터링 규칙은 새 공급자가 적용 하지 않으므로 코드에서 다시 적용 합니다. *ILoggerFactory. AddApplicationInsights ()* 의 오버 로드에는 최소 LogLevel 또는 필터 함수가 걸렸습니다. 새 공급자를 사용 하는 경우 필터링은 로깅 프레임 워크 자체의 일부입니다. Application Insights 공급자는이 작업을 수행 하지 않습니다. 따라서 *ILoggerFactory. AddApplicationInsights ()* 오버 로드를 통해 제공 되는 모든 필터를 제거 해야 합니다. 및 필터링 규칙은 [컨트롤 로깅 수준](#control-logging-level) 지침에 따라 제공 되어야 합니다. *에서appsettings.js* 를 사용 하 여 로깅을 필터링 하는 경우 둘 다 동일한 공급자 별칭인 *applicationinsights*를 사용 하기 때문에 새 공급자와 계속 작동 합니다.

이전 공급자를 계속 사용할 수 있습니다. 주 버전은 3으로 변경 될 때만 제거 됩니다. *xx*.) 그러나 다음과 같은 이유로 새 공급자로 마이그레이션하는 것이 좋습니다.

- 이전 공급자는 [로그 범위](/aspnet/core/fundamentals/logging#log-scopes)를 지원 하지 않습니다. 새 공급자에서 범위의 속성은 수집 된 원격 분석에 자동으로 사용자 지정 속성으로 추가 됩니다.
- 이제 응용 프로그램 시작 파이프라인에서 로그를 훨씬 일찍 캡처할 수 있습니다. 이제 **프로그램** 의 로그 및 **시작** 클래스를 캡처할 수 있습니다.
- 새 공급자를 사용 하 여 필터링은 프레임 워크 수준 자체에서 수행 됩니다. 콘솔, 디버그 등의 기본 제공 공급자를 비롯 한 다른 공급자와 동일한 방식으로 로그를 Application Insights 공급자로 필터링 할 수 있습니다. 여러 공급자에 동일한 필터를 적용할 수도 있습니다.
- ASP.NET Core (2.0 이상)에서  [로깅 공급자를 사용 하도록 설정](https://github.com/aspnet/Announcements/issues/255) 하는 권장 방법은 **Program.cs** 의 ILoggingBuilder에 대 한 확장 메서드를 사용 하는 것입니다.

> [!Note]
> 새 공급자는 NETSTANDARD 2.0 이상을 대상으로 하는 응용 프로그램에 사용할 수 있습니다. 2.14.0 [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전부터: net461 이상을 .NET Framework 대상으로 하는 응용 프로그램 에서도 새 공급자를 사용할 수 있습니다. 응용 프로그램이 .NET Core 1.1 같은 이전 버전의 .NET Core를 대상으로 하거나 고 NET46 보다 작은 .NET Framework 대상으로 지정 하는 경우 이전 공급자를 계속 사용 합니다.

## <a name="console-application"></a>콘솔 애플리케이션

> [!NOTE]
> 모든 콘솔 응용 프로그램에 대해 Application Insights (ILogger 및 기타 Application Insights 원격 분석)를 사용 하도록 설정 하는 데 사용할 수 있는 [라는 새로운](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) Application Insights SDK가 있습니다. [여기](./worker-service.md)에서 이 패키지 및 관련 지침을 사용하는 것이 좋습니다.

다른 Application Insights 모니터링 없이 ApplicationInsightsLoggerProvider를 사용 하려면 다음 단계를 사용 합니다.

설치된 패키지:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
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

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

이 예에서는 독립 실행형 패키지를 사용 `Microsoft.Extensions.Logging.ApplicationInsights` 합니다. 기본적으로이 구성은 Application Insights 데이터를 전송 하는 데 "완전 한" TelemetryConfiguration를 사용 합니다. 최소는 InMemoryChannel이 사용 되는 채널 임을 의미 합니다. 샘플링은 없고 표준 TelemetryInitializers 없습니다. 이 동작은 다음 예제와 같이 콘솔 응용 프로그램에 대해 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션에서는 **services.Config\<TelemetryConfiguration> ()** 메서드를 사용 하 여 기본 TelemetryConfiguration를 재정의 하는 방법을 보여 줍니다. 이 예제에서는를 설정 `ServerTelemetryChannel` 하 고 샘플링 합니다. 사용자 지정 ITelemetryInitializer를 TelemetryConfiguration에 추가 합니다.

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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>제어 로깅 수준

`ILogger` 에는 [로그 필터링](/aspnet/core/fundamentals/logging#log-filtering)을 적용 하는 기본 제공 메커니즘이 있습니다. 이렇게 하면 Application Insights 공급자를 포함 하 여 등록 된 각 공급자로 전송 되는 로그를 제어할 수 있습니다. 필터링은 구성에서 (일반적으로 파일 * 에appsettings.js* 를 사용 하 여) 또는 코드에서 수행할 수 있습니다.

다음 예에서는에 필터 규칙을 적용 하는 방법을 보여 줍니다 `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.js사용 하 여 구성에서 필터 규칙 만들기

ApplicationInsightsLoggerProvider의 경우 공급자 별칭은 `ApplicationInsights` 입니다. *appsettings.js* 의 다음 섹션에서는 로깅 공급자가 일반적으로 수준 *경고* 이상에서 로그를 기록 하도록 지시 합니다. 그런 다음을 `ApplicationInsightsLoggerProvider` (를) 수준 *오류* 이상으로 "Microsoft"로 시작 하는 로그 범주에 재정의 합니다.

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

다음 코드 조각에서는 모든 범주에서 *경고* 이상에 대 한 로그를 구성 하 고, "Microsoft"로 시작 하는 범주에서 *오류* 이상을 구성 `ApplicationInsightsLoggerProvider` 합니다. 이 구성은 *appsettings.js*의 이전 섹션과 동일 합니다.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>로깅 범위

`ApplicationInsightsLoggingProvider` 에서는 기본적으로 [로그 범위](/aspnet/core/fundamentals/logging#log-scopes) 및 범위를 사용할 수 있습니다.

범위가 형식이 면 `IReadOnlyCollection<KeyValuePair<string,object>>` 컬렉션의 각 키-값 쌍이 application insights 원격 분석에 사용자 지정 속성으로 추가 됩니다. 아래 예제에서는 로그가로 캡처되고 `TraceTelemetry` 속성에 ("MyKey", "MyValue")가 포함 됩니다.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

다른 형식이 범위로 사용 되는 경우에는 application insights 원격 분석의 "범위" 속성 아래에 저장 됩니다. 아래 예제에서에는 `TraceTelemetry` 범위를 포함 하는 "범위" 라는 속성이 포함 됩니다.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider의 이전 버전 및 새 버전은 무엇 인가요?

ApplicationInsightsLoggerProvider [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 에는 **ILoggerFactory** 확장 메서드를 통해 사용 하도록 설정 된 기본 제공 (AspNetCore ApplicationInsightsLoggerProvider)이 포함 되어 있습니다. 이 공급자는 버전 2.7.1에서 사용 되지 않는 것으로 표시 되었습니다. 다음 주 버전 변경에서 완전히 제거 됩니다. [AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 패키지 자체는 더 이상 사용 되지 않습니다. 요청, 종속성 등의 모니터링을 사용 하도록 설정 하는 데 필요 합니다.

대신 향상 된 ApplicationInsightsLoggerProvider (ApplicationInsightsLoggerProvider) 및 확장 메서드를 사용 하도록 설정 하는 ILoggerBuilder의 확장 메서드를 포함 하는 새로운 독립 [실행형 패키지를](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)사용 하는 것이 좋습니다.

[Microsoft 2.7.1 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전은 새 패키지에 대 한 종속성을 사용 하 고 ILogger 캡처를 자동으로 사용 하도록 설정 합니다.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Application Insights에서 일부 ILogger 로그가 두 번 표시 되는 이유는 무엇 인가요?

에서를 호출 하 여 이전 (사용 되지 않는) 버전의 ApplicationInsightsLoggerProvider를 사용 하도록 설정한 경우 중복이 발생할 수 있습니다 `AddApplicationInsights` `ILoggerFactory` . **구성** 메서드에 다음이 있는지 확인 하 고 제거 합니다.

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio에서 디버그할 때 이중 로깅이 발생 하 `EnableDebugLogger` 는 경우 다음과 같이 Application Insights를 사용 하도록 설정 하는 코드에서를  *false* 로 설정 합니다. 이 중복 및 픽스는 응용 프로그램을 디버깅 하는 경우에만 관련이 있습니다.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft 2.7.1 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전을 업데이트 하 고 ILogger의 로그가 자동으로 캡처됩니다. 이 기능을 완전히 해제 어떻게 할까요?

일반적으로 로그를 필터링 하는 방법을 보려면 [컨트롤 로깅 수준](#control-logging-level) 섹션을 참조 하세요. ApplicationInsightsLoggerProvider를 해제 하려면 다음을 사용 합니다 `LogLevel.None` .

**코드**

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>일부 ILogger 로그가 다른 속성과 같은 속성을 포함 하지 않는 이유는 무엇 인가요?

Application Insights는 다른 모든 원격 분석에 사용 되는 것과 동일한 TelemetryConfiguration를 사용 하 여 ILogger 로그를 캡처 및 보냅니다. 그러나 예외가 있습니다. 기본적으로 TelemetryConfiguration는 **Program.cs** 또는 **Startup.cs**에서 로그할 때 완전 하 게 설정 되지 않습니다. 이러한 위치의 로그에는 기본 구성이 없으므로 모든 TelemetryInitializers 및 TelemetryProcessors를 실행 하지 않습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>독립형 패키지를 사용 하 고 있습니다. Microsoft는 추가 사용자 지정 원격 분석을 수동으로 기록 하려고 합니다. 어떻게 해야 하나요?

독립 실행형 패키지를 사용 하는 경우 `TelemetryClient` 은 DI 컨테이너에 삽입 되지 않으므로 다음 코드와 같이의 새 인스턴스를 만들고 `TelemetryClient` 로 거 공급자에서 사용 하는 것과 동일한 구성을 사용 해야 합니다. 이렇게 하면 모든 사용자 지정 원격 분석 및 ILogger의 원격 분석에 동일한 구성이 사용 됩니다.

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
> AspNetCore 패키지를 사용 하 여 Application Insights를 사용 하도록 설정 하는 경우이 코드를 수정 하 여 `TelemetryClient` 생성자에서 직접 가져옵니다. 예제는 [이 FAQ](./asp-net-core.md#frequently-asked-questions)를 참조 하세요.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 로그에서 어떤 Application Insights 원격 분석 유형을 생성 하나요? 또는 Application Insights에서 ILogger 로그를 볼 수 있나요?

ApplicationInsightsLoggerProvider는 ILogger 로그를 캡처하여 해당 로그에서 TraceTelemetry를 만듭니다. 예외 개체가의 메서드에 전달 되는 경우 `Log` `ILogger` TraceTelemetry 대신 *exceptiontelemetry 분석이* 생성 됩니다. 이러한 원격 분석 항목은 포털, 분석 또는 Visual Studio 로컬 디버거를 비롯 한 Application Insights에 대 한 다른 TraceTelemetry 또는 ExceptionTelemetry 분석과 동일한 위치에서 찾을 수 있습니다.

항상 TraceTelemetry을 보내려면 다음 코드 조각을 사용 합니다. ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK가 설치 되어 있지 않으며, Azure Web Apps 확장을 사용 하 여 내 ASP.NET Core 응용 프로그램에 대 한 Application Insights를 사용 하도록 설정 합니다. 새 공급자를 사용 어떻게 할까요?? 

Azure Web Apps의 Application Insights 확장은 새 공급자를 사용 합니다. 응용 프로그램에 대 한 *appsettings.js* 파일의 필터링 규칙을 수정할 수 있습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>독립 실행형 패키지를 사용 하 여 작성기를 호출 하 여 Application Insights 공급자를 사용 하도록 설정 합니다. ** AddApplicationInsights ("ikey")**. 구성에서 계측 키를 가져올 수 있는 옵션이 있나요?


Program.cs를 수정 하 고 다음과 같이 appsettings.js합니다.

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

   관련 섹션 `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

이 코드는 독립 실행형 로깅 공급자를 사용 하는 경우에만 필요 합니다. 일반 Application Insights 모니터링의 경우 계측 키는 구성 경로 *Applicationinsights: Instrumentationkey*에서 자동으로 로드 됩니다. Appsettings.js은 다음과 같습니다.

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

* [ASP.NET Core에 로그인](/aspnet/core/fundamentals/logging)
* [Application Insights의 .NET 추적 로그](./asp-net-trace-logs.md)

