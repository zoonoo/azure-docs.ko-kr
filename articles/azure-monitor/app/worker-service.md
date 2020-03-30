---
title: 작업자 서비스 앱(HTTP가 아닌 앱)에 대한 응용 프로그램 인사이트
description: Azure 모니터 응용 프로그램 인사이트를 통해 .NET Core/.NET 프레임워크 비HTTP 앱을 모니터링합니다.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 34a64ffa67b1a43a77391e0d50ddf1bfad0f73ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501168"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>작업자 서비스 응용 프로그램에 대한 응용 프로그램 인사이트(HTTP가 아닌 응용 프로그램)

응용 프로그램 인사이트는 메시징, 백그라운드 작업, 콘솔 응용 프로그램 등과 같은 비 HTTP 워크로드에 가장 적합한 새로운 SDK를 `Microsoft.ApplicationInsights.WorkerService`출시합니다. 이러한 유형의 응용 프로그램은 기존 ASP.NET/ASP.NET 코어 웹 응용 프로그램과 같은 들어오는 HTTP 요청의 개념을 가지고 있지 않으므로 [ASP.NET](asp-net.md) 또는 [ASP.NET 코어](asp-net-core.md) 응용 프로그램에 대한 응용 프로그램 인사이트 패키지를 사용하는 것은 지원되지 않습니다.

새 SDK는 자체적으로 원격 분석 컬렉션을 수행하지 않습니다. 대신, 그것은 다른 잘 알려진 응용 프로그램 인사이트 자동 수집기 같은 [의존수집기,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) [PerfCounterCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) [응용 프로그램 인사이트 로깅 공급자](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 등. 이 SDK는 원격 `IServiceCollection` 분석 컬렉션을 활성화하고 구성하기 위해 확장 메서드를 노출합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

[작업자 서비스를 위한 응용 프로그램 인사이트 SDK는](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 실행 장소 나 방법에 관계없이 HTTP가 아닌 응용 프로그램에 가장 적합합니다. 응용 프로그램이 실행 중이고 Azure에 대한 네트워크 연결이 있는 경우 원격 분석을 수집할 수 있습니다. 응용 프로그램 인사이트 모니터링은 모든 곳에서 지원됩니다.NET 코어가 지원됩니다. 이 패키지는 새로 도입된 [.NET Core 3.0 작업자 서비스,](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) [Asp.Net 코어 2.1/2.2의 백그라운드 작업,](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)콘솔 앱(.NET Core/ .NET Framework) 등에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

유효한 애플리케이션 인사이트 계측 키입니다. 이 키는 모든 원격 분석을 응용 프로그램 인사이트로 전송하는 데 필요합니다. 계측 키를 얻으려면 새 응용 프로그램 인사이트 리소스를 만들어야 하는 경우 [응용 프로그램 인사이트 만들기 리소스를](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)참조하십시오.

## <a name="using-application-insights-sdk-for-worker-services"></a>작업자 서비스를 위한 애플리케이션 인사이트 SDK 사용

1. 응용 프로그램에 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치합니다.
   다음 코드 조각은 프로젝트 `.csproj` 파일에 추가해야 하는 변경 내용을 보여 줍니다.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. 에 `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` 호출 `IServiceCollection`확장 메서드를 제공 합니다. 이 메서드는 응용 프로그램의 시작 부분에 호출 해야 합니다. 정확한 위치는 응용 프로그램의 유형에 따라 다릅니다.

1. 생성자 `ILogger` 주입을 호출하거나 `TelemetryClient` `serviceProvider.GetRequiredService<TelemetryClient>();` 사용하여 DI(종속성 주입) 컨테이너에서 인스턴스 또는 인스턴스를 검색합니다. 이 단계는 설정 `TelemetryConfiguration` 및 자동 수집 모듈을 트리거합니다.

각 응용 프로그램 유형에 대한 특정 지침은 다음 섹션에 설명되어 있습니다.

## <a name="net-core-30-worker-service-application"></a>.NET 코어 3.0 작업자 서비스 응용 프로그램

전체 예제는 [여기에](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) 공유됩니다.

1. [.NET 코어 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) 다운로드 및 설치
2. Visual Studio 새 프로젝트 템플릿 또는 명령줄을 사용하여 새 작업자 서비스 프로젝트 만들기`dotnet new worker`
3. 응용 프로그램에 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치합니다.

4. 이 `services.AddApplicationInsightsTelemetryWorkerService();` 예제와 같이 클래스의 `CreateHostBuilder()` 메서드에 추가합니다. `Program.cs`

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. 아래 `Worker.cs` 예제에 따라 수정합니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. 계측 키를 설정합니다.

    계측 키를 인수로 `AddApplicationInsightsTelemetryWorkerService`제공할 수 있지만 구성에서 계측 키를 지정하는 것이 좋습니다. 다음 코드 샘플에서는 에서 계측 키를 지정하는 방법을 보여 주며 있습니다. `appsettings.json` 게시하는 `appsettings.json` 동안 응용 프로그램 루트 폴더에 복사되어 있는지 확인합니다.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

또는 다음 환경 변수 중 하나에 계측 키를 지정합니다.
`APPINSIGHTS_INSTRUMENTATIONKEY` 또는 `ApplicationInsights:InstrumentationKey`

예: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
또는`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

일반적으로 `APPINSIGHTS_INSTRUMENTATIONKEY` 웹 앱에 배포된 응용 프로그램에 대한 계측 키를 웹 작업으로 지정합니다.

> [!NOTE]
> 코드에 지정된 계측 키는 환경 `APPINSIGHTS_INSTRUMENTATIONKEY`변수를 이긴다.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>호스팅된 서비스를 통해 ASP.NET 핵심 백그라운드 작업

[이](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) 문서에서는 ASP.NET Core 2.1/2.2 응용 프로그램에서 배경 작업을 만드는 방법을 설명합니다.

전체 예제는 [여기에](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) 공유됩니다.

1. 설치 Microsoft.ApplicationInsights.WorkerService(응용https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 프로그램에 패키지)
2. 이 `services.AddApplicationInsightsTelemetryWorkerService();` 예제에서와 같이 메서드에 `ConfigureServices()` 추가합니다.

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

다음은 백그라운드 `TimedHostedService` 작업 논리가 있는 코드입니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 계측 키를 설정합니다.
   위의 .NET Core 3.0 작업자 서비스 예제에서 동일을 `appsettings.json` 사용합니다.

## <a name="net-corenet-framework-console-application"></a>.NET 코어/.NET 프레임워크 콘솔 애플리케이션

이 문서의 시작 부분에서 설명한 것처럼 새 패키지를 사용하여 일반 콘솔 응용 프로그램에서도 응용 프로그램 인사이트 원격 분석을 활성화할 수 있습니다. 이 패키지는 대상이므로 [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard).NET Core 2.0 이상 및 .NET Framework 4.7.2 이상의 콘솔 앱에 사용할 수 있습니다.

전체 예제는 [여기에](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) 공유됩니다.

1. 설치 Microsoft.ApplicationInsights.WorkerService(응용https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 프로그램에 패키지)

2. 아래 예제와 같이 Program.cs 수정합니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

이 콘솔 응용 프로그램은 `TelemetryConfiguration`또한 동일한 기본값을 사용하며 이전 섹션의 예제와 동일한 방식으로 사용자 지정할 수 있습니다.

## <a name="run-your-application"></a>애플리케이션 실행

애플리케이션을 실행합니다. 위의 모든 예제 작업자는 bing.com 위해 매 초마다 http 호출을 하고 ILogger를 사용하여 로그를 거의 내보릅니다. 이러한 줄은 `StartOperation` 작업을 `TelemetryClient`만드는 데 사용되는 의 호출 내부에 `RequestTelemetry` 래핑됩니다(이 예제에서는 "작업"이라고 함). 응용 프로그램 인사이트는 이러한 ILogger 로그(기본적으로 경고 또는 위)와 종속성을 수집하며 `RequestTelemetry` 부모-자식 관계와 상관관계가 있습니다. 상관 관계는 교차 프로세스/네트워크 경계에서도 작동합니다. 예를 들어 모니터링되는 다른 구성 요소에 대한 호출이 이루어진 경우 이 구성 요소도 이 부모와 상관관계가 됩니다.

이 사용자 `RequestTelemetry` 지정 작업은 일반적인 웹 응용 프로그램에서 들어오는 웹 요청과 동일하다고 생각할 수 있습니다. 작업을 사용할 필요는 없지만 부모 작업역할을 하는 [응용 프로그램 Insights 상관 관계 데이터 모델과](https://docs.microsoft.com/azure/azure-monitor/app/correlation) `RequestTelemetry` 동일한 작업에 논리적으로 속하는 것으로 처리되는 작업자 반복 내에서 생성된 모든 원격 분석과 가장 적합합니다. 또한 이 방법을 사용하면 생성된 모든 원격 분석(자동 및 `operation_id`수동)이 동일합니다. 샘플링은 에서 `operation_id`를 기반으로 하므로 샘플링 알고리즘은 단일 반복에서 모든 원격 분석을 유지하거나 삭제합니다.

다음은 응용 프로그램 인사이트에서 자동으로 수집한 전체 원격 분석을 나열합니다.

### <a name="live-metrics"></a>라이브 메트릭

[라이브 메트릭을](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) 사용하여 애플리케이션 인사이트 모니터링이 올바르게 구성되었는지 신속하게 확인할 수 있습니다. 원격 분석이 포털 및 분석에 나타나기 까지 몇 분 정도 걸릴 수 있지만 라이브 메트릭은 실행 중인 프로세스의 CPU 사용량을 거의 실시간으로 표시합니다. 또한 요청, 종속성, 추적 등과 같은 다른 원격 분석을 표시할 수도 있습니다.

### <a name="ilogger-logs"></a>ILogger 로그

심각도 `Warning` 또는 그 `ILogger` 이상을 통해 내보낸 로그는 자동으로 캡처됩니다. [ILogger 문서를](ilogger.md#control-logging-level) 따라 응용 프로그램 인사이트로 캡처되는 로그 수준을 사용자 지정합니다.

### <a name="dependencies"></a>종속성

종속성 컬렉션은 기본적으로 활성화되어 있습니다. [이](asp-net-dependencies.md#automatically-tracked-dependencies) 문서에서는 자동으로 수집되는 종속성을 설명하고 수동 추적을 수행하는 단계도 포함되어 있습니다.

### <a name="eventcounter"></a>이벤트 카운터

`EventCounterCollectionModule`은 기본적으로 활성화되어 있으며 .NET Core 3.0 앱에서 기본 카운터 집합을 수집합니다. [EventCounter](eventcounters.md) 자습서에는 수집된 기본 카운터 집합이 나열됩니다. 또한 목록을 사용자 지정하는 방법에 대한 지침도 있습니다.

### <a name="manually-tracking-additional-telemetry"></a>추가 원격 분석을 수동으로 추적

SDK는 위에서 설명한 대로 원격 분석을 자동으로 수집하지만 대부분의 경우 사용자는 응용 프로그램 인사이트 서비스에 추가 원격 분석을 보내야 합니다. 추가 원격 분석을 추적하는 권장 방법은 종속성 `TelemetryClient` 주입에서 인스턴스를 얻은 다음 지원되는 `TrackXXX()` [API](api-custom-events-metrics.md) 메서드 중 하나를 호출하는 것입니다. 또 다른 일반적인 사용 사례는 [작업을 사용자 지정 추적하는](custom-operations-tracking.md)것입니다. 이 방법은 위의 Worker 예제에서 보여 주어 도 있습니다.

## <a name="configure-the-application-insights-sdk"></a>애플리케이션 인사이트 SDK 구성

작업자 `TelemetryConfiguration` 서비스 SDK에서 사용하는 기본값은 ASP.NET 또는 ASP.NET 코어 응용 프로그램에서 원격 분석을 보강하는 데 사용되는 원격 `HttpContext`분석 초기화자를 제외한 ASP.NET 또는 ASP.NET 코어 응용 프로그램에서 사용되는 자동 구성과 유사합니다.

작업자 서비스에 대한 응용 프로그램 인사이트 SDK를 사용자 지정하여 기본 구성을 변경할 수 있습니다. 응용 프로그램 인사이트 ASP.NET 코어 SDK 사용자는 ASP.NET 기본 제공 [종속성 주입을](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)사용하여 구성을 변경하는 데 익숙할 수 있습니다. WorkerService SDK도 유사한 원칙을 기반으로 합니다. 아래에 자세히 설명된 `ConfigureServices()` 대로 에 `IServiceCollection`대해 적절한 메서드를 호출하여 섹션의 거의 모든 구성을 변경합니다.

> [!NOTE]
> 이 SDK를 사용하는 동안 수정을 통해 구성을 변경하는 `TelemetryConfiguration.Active` 것은 지원되지 않으며 변경 내용은 반영되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>응용 프로그램 인사이트서비스 옵션 사용

이 예제에서와 같이 에 `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`전달하여 몇 가지 일반적인 설정을 수정할 수 있습니다.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

이 `ApplicationInsightsServiceOptions` SDK에서 ASP.NET 코어 SDK와 `Microsoft.ApplicationInsights.AspNetCore.Extensions` 는 달리 네임스페이스에 `Microsoft.ApplicationInsights.WorkerService` 있습니다.

에서 일반적으로 사용되는 설정`ApplicationInsightsServiceOptions`

|설정 | 설명 | 기본값
|---------------|-------|-------
|인에이블퀵펄스메트릭스트림 | 라이브메트릭 사용/비활성화 기능 | true
|인에이블적응샘플링 | 어댑티브 샘플링 사용/비활성화 | true
|하트비트 사용 | 주기적으로 (15분 기본값) 하트비트 활성화/비활성화 기능으로 .NET 버전, Azure 환경 정보(해당하는 경우) 등과 같은 런타임에 대한 정보가 포함된 'HeartBeatState'라는 사용자 지정 메트릭을 보냅니다. | true
|추가 자동 수집메트릭 추출기 | 자동 수집메트릭 제거 추출기는 샘플링이 수행되기 전에 요청/종속성에 대한 사전 집계된 메트릭을 보내는 원격 측정 프로세서입니다. | true

최신 목록에 대한 [구성 `ApplicationInsightsServiceOptions` 가능한 설정을](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) 참조하십시오.

### <a name="sampling"></a>샘플링

작업자 서비스를 위한 애플리케이션 인사이트 SDK는 고정 속도 및 적응형 샘플링을 모두 지원합니다. 적응 형 샘플링은 기본적으로 활성화됩니다. 작업자 서비스에 대한 샘플링 구성은 [ASP.NET 핵심 응용 프로그램과](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)동일한 방식으로 수행됩니다.

### <a name="adding-telemetryinitializers"></a>원격 측정 초기화추가

모든 원격 분석과 함께 전송되는 속성을 정의하려는 경우 원격 [분석 초기화자를](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) 사용합니다.

컨테이너에 `TelemetryInitializer` 새 `DependencyInjection` 새 를 추가하면 SDK가 `TelemetryConfiguration`자동으로 에 추가됩니다.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>원격 분석 초기라이저 제거

원격 분석 초기화자는 기본적으로 존재합니다. 모든 또는 특정 원격 분석 초기화자를 제거하려면 *after* 다음 샘플 `AddApplicationInsightsTelemetryWorkerService()`코드를 호출한 후 사용합니다.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>원격 분석 프로세서 추가

에서 확장 메서드를 `TelemetryConfiguration` `AddApplicationInsightsTelemetryProcessor` 사용하여 사용자 지정 원격 분석 프로세서를 추가할 수 있습니다. `IServiceCollection` [고급 필터링 시나리오에서](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) 원격 분석 프로세서를 사용하면 Application Insights 서비스로 보내는 원격 분석에서 포함하거나 제외된 내용을 보다 직접적으로 제어할 수 있습니다. 다음 예제를 사용합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 원격 분석 모듈 구성 또는 제거

Application Insights는 원격 분석 모듈을 사용하여 수동 추적 없이 특정 워크로드에 대한 원격 분석을 자동으로 수집합니다.

다음 자동 수집 모듈은 기본적으로 활성화되어 있습니다. 이러한 모듈은 원격 분석을 자동으로 수집합니다. 기본 동작을 변경하도록 사용하지 않도록 설정하거나 구성할 수 있습니다.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

기본값을 `TelemetryModule`구성하려면 다음 예제와 `IServiceCollection`같이 에서 확장 메서드를 `ConfigureTelemetryModule<T>` 사용합니다.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>원격 분석 채널 구성

기본 채널은 `ServerTelemetryChannel`. 다음 예제에서 볼 수 있듯이 재정의할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>원격 분석을 동적으로 사용하지 않도록 설정

원격 분석을 조건부 및 동적으로 사용하지 않도록 설정하려면 코드의 아무 곳이나 ASP.NET Core 종속성 주입 컨테이너를 사용하여 인스턴스를 해결하고 `TelemetryConfiguration` 플래그를 설정할 `DisableTelemetry` 수 있습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집되지 않은 원격 분석을 추적하면 어떻게 해야 합니까?

생성자 주입을 사용하여 인스턴스를 `TelemetryClient` 얻고 필요한 `TrackXXX()` 메서드를 호출합니다. 새 `TelemetryClient` 인스턴스를 만드는 것은 권장되지 않습니다. 단일 `TelemetryClient` 인스턴스는 이미 `DependencyInjection` 컨테이너에 등록되어 있으며 `TelemetryConfiguration` 나머지 원격 분석과 공유합니다. 새 `TelemetryClient` 인스턴스를 만드는 것은 나머지 원격 분석과 는 별개의 구성이 필요한 경우에만 권장됩니다.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Visual Studio IDE를 사용하여 작업자 서비스 프로젝트에 응용 프로그램 인사이트를 온보감할 수 있습니까?

비주얼 스튜디오 IDE 온보딩은 현재 ASP.NET/ASP.NET 핵심 응용 프로그램에대해서만 지원됩니다. 이 문서는 Visual Studio에서 온보딩 작업자 서비스 응용 프로그램에 대한 지원을 제공하면 업데이트됩니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>상태 모니터와 같은 도구를 사용하여 애플리케이션 인사이트 모니터링을 활성화할 수 있습니까?

아니요. [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) 및 [상태 모니터 v2는](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 현재 4.x ASP.NET 지원합니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux에서 응용 프로그램을 실행하는 경우 모든 기능이 지원되나요?

예. 이 SDK에 대한 기능 지원은 모든 플랫폼에서 동일하지만 다음과 같은 예외는 다음과 같습니다.

* 성능 카운터는 라이브 메트릭에 표시된 프로세스 CPU/메모리를 제외한 Windows에서만 지원됩니다.
* 기본적으로 `ServerTelemetryChannel` 활성화되어 있더라도 응용 프로그램이 Linux 또는 MacOS에서 실행 중인 경우 채널은 네트워크 문제가 있는 경우 원격 분석을 일시적으로 유지하기 위해 로컬 저장소 폴더를 자동으로 만들지 않습니다. 이러한 제한으로 인해 일시적인 네트워크 또는 서버 문제가 있을 때 원격 분석이 손실됩니다. 이 문제를 해결하려면 채널의 로컬 폴더를 구성합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>샘플 애플리케이션

[.NET 코어 콘솔 애플리케이션](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) .NET 코어(2.0 이상) 또는 .NET 프레임워크(4.7.2 이상)로 작성된 콘솔 응용 프로그램을 사용하는 경우 이 샘플을 사용합니다.

[호스팅 서비스가 있는 ASP.NET 핵심 백그라운드 작업](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) 코어 2.1/2.2에 Asp.Net 경우 이 샘플을 사용하고 [공식](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2) 지침에 따라 백그라운드 작업을 만듭니다.

[.NET 코어 3.0 작업자 서비스](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) [공식](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template) 지침에 따라 .NET Core 3.0 작업자 서비스 응용 프로그램이 있는 경우 이 샘플을 사용합니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK

[코드를 읽고 기여합니다.](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="next-steps"></a>다음 단계

* [API를 사용하여](../../azure-monitor/app/api-custom-events-metrics.md) 앱의 성능 및 사용량에 대한 자세한 보기를 위해 고유한 이벤트 및 메트릭을 보냅니다.
* [자동으로 추적되지 않는 추가 종속성을 추적합니다.](../../azure-monitor/app/auto-collect-dependencies.md)
* [보강 또는 필터 자동 수집 원격 분석](../../azure-monitor/app/api-filtering-sampling.md).
* [ASP.NET 코어에 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
