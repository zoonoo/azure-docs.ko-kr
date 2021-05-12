---
title: ILogger를 사용하여 .NET 추적 로그 살펴보기 - Azure Application Insights
description: ASP.NET Core 및 콘솔 애플리케이션에서 Application Insights ILogger 공급자를 사용하는 예제
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568593"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>Microsoft.Extension.Logging을 위한 ApplicationInsightsLoggerProvider

이 문서에서는 콘솔 및 ASP.NET Core 애플리케이션에서 `ApplicationInsightsLoggerProvider`을 사용해 `ILogger` 로그를 사용하는 방법을 보여 줍니다.
로깅의 자세한 내용은 [ASP.NET Core의 로깅](/aspnet/core/fundamentals/logging)을 참조하세요.

## <a name="aspnet-core-applications"></a>ASP.NET Core 애플리케이션

`ApplicationInsightsLoggerProvider`는 [코드](./asp-net-core.md) 또는 [코드리스](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) 방법을 사용하여 ApplicationInsights를 구성할 때 ASP.NET Core 애플리케이션에 대해 기본적으로 사용하도록 설정됩니다.

기본적으로 모든 [범주](/aspnet/core/fundamentals/logging/#log-category)의 *경고* 이상의 `ILogger` 로그만 Application Insights로 전송됩니다. 그러나 [이 동작을 사용자 지정](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection)할 수 있습니다. **Program.cs** 또는 **Startup.cs** 에서 ILogger 로그를 캡처하려면 추가 단계가 필요합니다. ([ASP.NET Core 애플리케이션의 Startup.cs 및 Program.cs에서 ILogger 로그 캡처](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)를 참조하세요.)

다른 Application Insights 모니터링 없이 `ApplicationInsightsLoggerProvider`만 사용하려는 경우 다음 단계를 사용하세요.

1. NuGet 패키지 설치

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. `Program.cs`를 다음과 같이 수정합니다.

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

2 단계의 코드로 `ApplicationInsightsLoggerProvider`를 구성합니다. 다음 코드는 `ILogger`를 사용하여 로그를 전송하는 예제 컨트롤러 클래스를 보여 줍니다. Application Insights에서 로그를 캡처합니다.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core 애플리케이션의 Startup.cs 및 Program.cs에서 ILogger 로그 캡처

> [!NOTE]
> ASP.NET Core 3.0 이상에서는 더 이상 Startup.cs 및 Program.cs에 `ILogger`를 삽입할 수 없습니다. 자세한 내용은 https://github.com/aspnet/Announcements/issues/353를 참조하세요.

`ApplicationInsightsLoggerProvider`는 애플리케이션 시작 초기부터 로그를 캡처할 수 있습니다. ApplicationInsightsLoggerProvider는 Application Insights에서 자동으로 사용하도록 설정되지만(버전 2.7.1부터), 파이프라인에서 이후 버전까지 계측 키는 설정되지 않습니다. 따라서 **컨트롤러**/기타 클래스의 로그만 캡처됩니다. **Program.cs** 와 **Startup.cs** 자체를 사용하여 모든 로그를 캡처하려면 ApplicationInsightsLoggerProvider에 대하여 계측 키를 명시적으로 사용하도록 설정해야 합니다. 또한 **Program.cs** 또는 **Startup.cs** 자체에서 로그하는 경우에는 *TelemetryConfiguration* 이 완전하게 설정되지 않습니다. 따라서 이러한 로그에는 [InMemoryChannel](./telemetry-channels.md)을 사용하고 [샘플링](./sampling.md)과 표준 [원격 분석 이니셜라이저 또는 프로세서](./api-filtering-sampling.md)를 사용하지 않는 최소 구성이 포함됩니다.

다음 예에서는 **Program.cs** 및 **Startup.cs** 를 사용하는 본 기능을 보여 줍니다.

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

Microsoft.ApplicationInsights.AspNet SDK의 2.7.1 이전 버전은 현재는 사용되지 않는 로깅 공급자를 지원했습니다. 이 공급자는 ILoggerFactory의 **AddApplicationInsights()** 확장 메서드를 통해 사용하도록 설정되었습니다. 이제 다음 두 단계를 통해 새 공급자로 마이그레이션할 것을 권장합니다.

1. 이중 로깅을 방지하기 위해 **Startup.Configure()** 메서드에서 *ILoggerFactory.AddApplicationInsights()* 호출을 제거합니다.
2. 코드의 모든 필터링 규칙은 새 공급자에서 준수하지 않으므로 다시 적용합니다. 최소한의 LogLevel 또는 필터 함수로 *ILoggerFactory. AddApplicationInsights()* 가 오버로드됐습니다. 하지만 새 공급자를 사용하면 필터링은 로깅 프레임워크 자체의 일부입니다. Application Insights 공급자가 이 작업을 수행하지 않습니다. 따라서 *ILoggerFactory. AddApplicationInsights()* 오버로드를 통해 제공되는 모든 필터를 제거해야 합니다. 그리고 필터링 규칙은 [로깅 수준 컨트롤](#control-logging-level) 지침에 따라 제공되어야 합니다. *appsettings.json* 을 사용하여 로깅을 필터링하는 경우 둘 다 동일한 공급자 별칭인 *ApplicationInsights* 를 사용하기 때문에 새 공급자에서 계속 작동합니다.

기존 공급자도 계속 사용할 수 있습니다. (3.*xx* 로 주 버전 변경이 있을 때만 제거될 예정입니다.) 그러나 다음과 같은 이유로 새 공급자로 마이그레이션할 것을 권장합니다.

- 이전 공급자는 [로그 범위](/aspnet/core/fundamentals/logging#log-scopes)에 대한 지원이 부족합니다. 새 공급자에서는 범위의 속성이 수집된 원격 분석에 자동으로 사용자 지정 속성으로 추가됩니다.
- 이제 애플리케이션 시작 파이프라인에서 로그를 훨씬 일찍 캡처할 수 있습니다. **Program** 및 **Startup** 클래스의 로그를 캡처할 수 있습니다.
- 새 공급자를 사용하여 필터링이 프레임워크 수준 자체에서 수행됩니다. 콘솔, 디버그 등 기본 제공 공급자를 비롯한 다른 공급자와 동일한 방식으로 로그를 Application Insights 공급자로 필터링할 수 있습니다. 여러 공급자에 동일한 필터를 적용할 수도 있습니다.
- ASP.NET Core(2.0 이상)에서는 **Program.cs** 자체의 ILoggingBuilder에서 확장 메서드를 사용하여 [로깅 공급자를 사용하도록 설정](https://github.com/aspnet/Announcements/issues/255)할 것을 권장합니다.

> [!Note]
> 새 공급자는 NETSTANDARD 2.0 이상을 대상으로 하는 애플리케이션에서 사용할 수 있습니다. [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.14.0부터는 .NET Framework NET461 이상을 대상으로 하는 애플리케이션에서도 새 공급자를 사용할 수 있습니다. 애플리케이션이 .NET Core 1.1 같은 구 버전의 .NET Core를 대상으로 하거나 NET46보다 낮은 .NET Framework를 대상으로 하는 경우 이전 공급자를 계속 사용하십시오.

## <a name="console-application"></a>콘솔 애플리케이션

> [!NOTE]
> [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)라는 새 Application Insights SDK가 있습니다. 이를 사용하여 모든 콘솔 애플리케이션에 대해 Application Insights(ILogger 및 기타 Application Insights 원격 분석)를 사용하도록 설정할 수 있습니다. [여기](./worker-service.md)에서 이 패키지 및 관련 지침을 사용하는 것이 좋습니다.

다른 Application Insights 모니터링 없이 ApplicationInsightsLoggerProvider만 사용하려는 경우 다음 단계를 사용하십시오.

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

이 예에서는 독립 실행형 패키지 `Microsoft.Extensions.Logging.ApplicationInsights`를 사용하고 있습니다. 기본적으로 이 구성은 Application Insights에 데이터를 전송하기 위해 “최소한”의 TelemetryConfiguration을 사용합니다. 최소한이란 InMemoryChannel이 채널로 사용되고 있음을 의미합니다. 샘플링과 표준 TelemetryInitializers가 없습니다. 아래 예제와 같이 콘솔 애플리케이션의 경우 이 동작을 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션에서는 **services.Configure\<TelemetryConfiguration>()** 메서드를 사용하여 기본 TelemetryConfiguration을 재정의하는 방법을 보여 줍니다. 이 예제에서는 `ServerTelemetryChannel`과 샘플링을 설정합니다. 그리고, 사용자 지정 ITelemetryInitializer를 TelemetryConfiguration에 추가합니다.

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

## <a name="control-logging-level"></a>로깅 수준 컨트롤

`ILogger`에는 [로그 필터링](/aspnet/core/fundamentals/logging#log-filtering)을 적용하는 기본 제공 메커니즘이 있습니다. 이를 통해 Application Insights 공급자를 포함하여 등록된 각 공급자로 전송되는 로그를 제어할 수 있습니다. 필터링은 구성에서(일반적으로 *appsettings.json* 파일을 사용하여) 또는 코드에서 수행할 수 있습니다.

다음 예에서는 `ApplicationInsightsLoggerProvider`에 필터 규칙을 적용하는 방법을 보여 줍니다.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json을 사용하여 구성에서 필터 규칙 만들기

ApplicationInsightsLoggerProvider의 경우 공급자 별칭은 `ApplicationInsights`입니다. *appsettings.json* 의 다음 섹션에서는 로깅 공급자가 일반적으로 *경고* 이상의 수준에서 로그하도록 지시합니다. 그런 다음 `ApplicationInsightsLoggerProvider`를 *오류* 수준 이상에서 "Microsoft"로 시작하는 범주를 로그할 수 있도록 재정의합니다.

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

다음 코드 조각은 모든 범주에서 *경고* 이상에 대한 로그를 구성하고, `ApplicationInsightsLoggerProvider`로 전송될 대상으로서 "Microsoft"로 시작하는 범주에서 *오류* 이상에 대한 로그를 구성합니다. 이 구성은 *appsettings.json* 의 이전 섹션과 동일합니다.

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

`ApplicationInsightsLoggingProvider`는 [로그 범위](/aspnet/core/fundamentals/logging#log-scopes)를 지원하고, 범위는 기본적으로 사용하도록 설정돼 있습니다.

범위가 `IReadOnlyCollection<KeyValuePair<string,object>>` 형식이면 컬렉션의 각 키-값 쌍이 애플리케이션 인사이트 원격 분석에 사용자 지정 속성으로 추가됩니다. 아래 예제에서는 로그가 `TraceTelemetry`로 캡처되고 로그의 속성에 ("MyKey", "MyValue")가 포함됩니다.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

다른 형식이 범위로 사용되는 경우에는 로그가 애플리케이션 인사이트 원격 분석의 "범위" 속성 아래에 저장됩니다. 아래 예제에서는 `TraceTelemetry`가 "범위" 라는 속성을 갖게 되고, 이 속성에 해당 범위가 포함됩니다.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider의 구 버전과 새 버전은 무엇인가요?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)에는 **ILoggerFactory** 확장 메서드를 통해 사용하도록 설정된 기본 제공 ApplicationInsightsLoggerProvider(Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)가 포함되었습니다. 이 공급자는 버전 2.7.1부터 사용되지 않는 것으로 표시됩니다. 다음 주 버전 변경에서는 완전히 제거될 예정입니다. [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 패키지 자체가 더 이상 사용되지 않는 것은 아닙니다. 요청, 종속성 등의 모니터링을 사용하도록 설정하는 데 필요합니다.

이에 기능이 향상된 ApplicationInsightsLoggerProvider(Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider)와 이를 사용하기 위한 ILoggerBuilder의 확장 메서드를 포함하는 새로운 독립 실행형 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 패키지를 사용할 것을 제안합니다.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1은 새 패키지에 대한 종속성을 사용하고 ILogger 캡처를 자동으로 사용하도록 설정합니다.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Application Insights에서 일부 ILogger 로그가 두 번 표시되는 이유는 무엇인가요?

`ILoggerFactory`에서 `AddApplicationInsights`를 호출하여 이전(사용되지 않는) 버전의 ApplicationInsightsLoggerProvider를 사용하도록 설정한 경우 중복이 발생할 수 있습니다. **Configure** 메서드에 다음이 있는지 확인하고 제거합니다.

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio에서 디버그할 때 이중 로깅을 경험하는 경우 다음과 같이 Application Insights를 사용하도록 설정하는 코드에서 `EnableDebugLogger`를 *false* 로 설정합니다. 이 중복 및 픽스는 애플리케이션을 디버깅하는 경우에만 관련이 있습니다.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1로 업데이트했는데 ILogger의 로그가 자동으로 캡처됩니다. 이 기능을 완전히 해제하려면 어떻게 해야 하나요?

[로깅 수준 컨트롤](#control-logging-level) 섹션에서 일반적인 로그 필터링 방법을 참조하세요. ApplicationInsightsLoggerProvider를 해제하려면 `LogLevel.None`을 사용하면 됩니다.

**코드**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**구성**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>일부 ILogger 로그가 다른 속성과 같은 속성을 포함하지 않는 이유는 무엇인가요?

Application Insights는 다른 모든 원격 분석에 사용되는 것과 동일한 TelemetryConfiguration를 사용하여 ILogger 로그를 캡처하고 전송합니다. 그러나 예외가 있습니다. 기본적으로, **Program.cs** 또는 **Startup.cs** 에서 로그하는 경우에는 TelemetryConfiguration이 완전하게 설정되지 않습니다. 이러한 위치의 로그에는 기본 구성이 없으므로 모든 TelemetryInitializer와 TelemetryProcessor를 실행하지는 않습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>독립 실행형 패키지 Microsoft.Extensions.Logging.ApplicationInsights를 사용하고 있고, 수동으로 일부 사용자 지정 원격 분석을 추가적으로 로그하고 싶습니다. 방법을 알려주세요.

독립 실행형 패키지를 사용하는 경우 `TelemetryClient`는 DI 컨테이너에 삽입되지 않으므로 다음 코드와 같이의 `TelemetryClient`의 새 인스턴스를 만들고 로거 공급자에서 사용하는 것과 동일한 구성을 사용해야 합니다. 이렇게 하면 모든 사용자 지정 원격 분석 및 ILogger의 원격 분석에 동일한 구성이 사용됩니다.

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
> Microsoft.ApplicationInsights.AspNetCore 패키지를 사용하여 Application Insights를 사용하도록 설정하는 경우 이 코드를 수정하여 `TelemetryClient`를 생성자에서 직접 가져옵니다. 예제는 [이 FAQ](./asp-net-core.md#frequently-asked-questions)를 참조하세요.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 로그에서는 어떤 Application Insights 원격 분석 유형을 생성하나요? 또는 Application Insights의 어디에서 ILogger 로그를 볼 수 있나요?

ApplicationInsightsLoggerProvider는 ILogger 로그를 캡처하여 해당 로그에서 TraceTelemetry를 만듭니다. 예외 개체가 `ILogger`의 `Log` 메서드에 전달되는 경우 TraceTelemetry 대신 *ExceptionTelemetry* 가 생성됩니다. 이러한 원격 분석 항목은 포털, 분석 또는 Visual Studio 로컬 디버거를 비롯하여 Application Insights의 다른 TraceTelemetry 또는 ExceptionTelemetry와 동일한 위치에서 찾을 수 있습니다.

항상 TraceTelemetry을 전송하려면 이 코드 조각을 사용하십시오: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK가 설치되어 있지 않고, Azure Web Apps 확장을 사용하여 ASP.NET Core 애플리케이션에 Application Insights를 사용하도록 설정하고 있습니다. 새 공급자는 어떻게 사용하나요? 

Azure Web Apps의 Application Insights 확장은 새 공급자를 사용합니다. 사용 중인 애플리케이션에 대한 *appsettings.json* 파일의 필터링 규칙을 수정할 수 있습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>독립 실행형 패키지 Microsoft.Extensions.Logging.ApplicationInsights를 사용하고, **builder.AddApplicationInsights("ikey")** 를 호출하여 Application Insights 공급자를 사용하도록 설정하고 있습니다. 구성에서 계측 키를 가져올 수 있는 옵션이 있나요?


Program.cs와 appsettings.json을 다음과 같이 수정하십시오.

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

   `appsettings.json`의 관련 섹션:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

이 코드는 독립 실행형 로깅 공급자를 사용하는 경우에만 필요합니다. 일반 Application Insights 모니터링의 경우 계측 키는 구성 경로 *Applicationinsights: InstrumentationKey* 에서 자동으로 로드됩니다. Appsettings.json은 다음과 같습니다.

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> 새 Azure 지역에서는 계측 키 대신 연결 문자열을 **사용해야 합니다**. [연결 문자열](./sdk-connection-string.md?tabs=net)은 원격 분석 데이터를 연결하려는 리소스를 식별합니다. 또한 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [ASP.NET Core에 로그인](/aspnet/core/fundamentals/logging)
* [Application Insights의 .NET 추적 로그](./asp-net-trace-logs.md)

