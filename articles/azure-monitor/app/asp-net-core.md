---
title: ASP.NET Core용 Azure Application Insights | Microsoft Docs
description: ASP.NET Core 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226407"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램용 application Insights

이 문서에 대 한 Application Insights를 사용 하는 방법에 설명 합니다는 [ASP.NET Core](https://docs.microsoft.com/aspnet/core) 응용 프로그램입니다. 이 문서의 지침을 완료 하면 Application Insights는 ASP.NET Core 응용 프로그램에서 요청, 종속성, 예외, 성능 카운터, 하트 비트 및 로그 수집을 시작 합니다. 예제 응용 프로그램은는 [MVC 응용 프로그램](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) 대상 `netcoreapp2.2`, 이지만 이러한 지침은 모든 ASP.NET Core 응용 프로그램에 적용할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

합니다 [ASP.NET Core 용 Application Insights SDK (소프트웨어 개발 키트)](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 응용 프로그램 실행은 어떻게에 관계 없이 응용 프로그램을 모니터링할 수 있습니다. 응용 프로그램이 실행 되 고 Azure로 네트워크에 연결 되어, 경우에 원격 분석을 수집할 수 있습니다. 즉, Application Insights 모니터링을 지원 하는지 everywhere.NET Core는 지원 됩니다. 모든 운영 체제 (Windows, Linux, Mac), 호스팅 메서드 (in process vs out of process), 배포 방법 (프레임 워크 종속 및 자체 포함된), 웹 서버 (IIS, Kestrel), (Azure Web Apps, Azure VM, Docker를 호스팅 플랫폼이이 지원 포함 AKS (azure Kubernetes Service), 및 등입니다.) 또는 IDE (Visual Studio, VS Code 명령 줄)

## <a name="prerequisites"></a>필수 조건

- 작동 ASP.NET Core 응용 프로그램입니다. 수행 합니다 [시작 하는 ASP.NET Core 설명서](https://docs.microsoft.com/aspnet/core/getting-started/) 필요한 경우 ASP.NET Core 응용 프로그램을 만들려면.
- 유효한 Application Insights 계측 키를 Application Insights 서비스에 모든 원격 분석을 전송 하는 데 필요한 합니다. 에 따라 합니다 [지침 리소스 만들기](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) 목록이 필요한 경우 새 Application Insights 리소스를 만들고 계측 키를 가져오기만 하 합니다.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights 서버 쪽 원격 분석 (Visual Studio)를 사용 하도록 설정

1. Visual Studio에서 새 프로젝트를 엽니다.

    > [!TIP]
    > Not 필수 단계 동안 Application Insights에서 변경한 모든 내용을 추적할 수 있도록 프로젝트에 대 한 소스 제어를 설정 하는 데 도움이 수 있습니다. 소스 제어 선택 수 있도록 **파일** > **소스 제어에 추가**합니다.

2. **프로젝트** > **Application Insights 원격 분석 추가**를 선택합니다.

3. **시작**을 선택합니다. (Visual Studio의 버전에 따라 텍스트는 약간 달라질 수 있습니다. 일부 이전 버전에는 대신 **평가판 시작** 단추가 있습니다.)

4. 구독을 선택한 다음, **리소스** > **등록**을 선택합니다.

5. 프로젝트에 Application Insights를 추가한 후 확인 하는 SDK의 안정적인 최신 릴리스를 사용 하 고 있는지 확인 합니다. 로 이동 **프로젝트** > **NuGet 패키지 관리** > **Microsoft.ApplicationInsights.AspNetCore** > 선택 필요한경우**업데이트**합니다.

     ![선택한 업데이트에 대 한 Application Insights 패키지를 사용 하 여 NuGet 패키지 화면을 관리 하는 스크린샷](./media/asp-net-core/update-nuget-package.png)

6. 선택적 팁을 따르고 소스 제어에 프로젝트를 추가 하는 경우 이동할 수 있습니다 다음 **뷰** > **팀 탐색기** > **변경** 및 diff 보기를 Application Insights 원격 분석 추가에서 변경한 내용은 각 개별 파일을 선택 합니다.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>(Visual Studio)가 없는 서버 쪽 원격 분석을 Application Insights를 사용 하도록 설정

1. 설치 합니다 [ASP.NET Core 용 Application Insights SDK NuGet 패키지](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)합니다. 항상 안정적인 최신 버전을 사용 하도록 권장 합니다. SDK에 대 한 전체 릴리스 정보에서 확인할 수 있습니다 합니다 [소스 GitHub 리포지토리를 열고](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)합니다.

    다음 코드 예제 프로젝트에 추가할 변경 내용을 `.csproj` 파일입니다.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. 추가 `services.AddApplicationInsightsTelemetry();` 에 `ConfigureServices()` 에서 메서드 여 `Startup` 클래스입니다. 아래 전체 예제입니다.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. 계측 키를 설정 합니다.

    계측 키를 인수로 제공할 수 있지만 `AddApplicationInsightsTelemetry`, 구성에서 계측 키를 지정 하는 것이 좋습니다. 다음에서 계측 키를 지정 하는 방법을 보여 줍니다 `appsettings.json`합니다. 했는지 `appsettings.json` 게시 하는 동안 응용 프로그램 루트 폴더에 복사 됩니다.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    또는 다음 환경 변수에서 계측 키 지정 수 있습니다.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    예제:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` Azure Web Apps에 배포 된 응용 프로그램에 대 한 계측 키를 지정 하려면 일반적으로 사용 됩니다.

    > [!NOTE]
    > 환경 변수를 통해 코드 wins에 지정 된 계측 키 `APPINSIGHTS_INSTRUMENTATIONKEY`, 다른 옵션과 비교 wins입니다.

## <a name="run-your-application"></a>애플리케이션 실행

 응용 프로그램을 실행 하 고 요청을 확인 합니다. 원격 분석이 Application Insights로 이동 하는 것을 시작 됩니다. 다음과 같은 원격 분석은 Application Insights SDK에서 자동으로 수집 됩니다.

|요청/종속성 |세부 정보|
|---------------|-------|
|요청 | 응용 프로그램에 들어오는 웹 요청입니다. |
|Http/Https | 사용 하 여 호출 `HttpClient`합니다. |
|SQL | 사용 하 여 호출 `SqlClient`합니다. |
|[Azure 저장소](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage 클라이언트를 사용 하 여 호출 합니다. |
|[EventHub 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 버전 1.1.0 이상. |
|[ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 버전 3.0.0 이상. |
|Azure Cosmos DB | HTTP/HTTPS를 사용 하는 경우 자동으로 추적만 합니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다. |

### <a name="performance-counters"></a>성능 카운터

에 대 한 지원 [성능 카운터](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) ASP.NET Core는 다음으로 제한

   * SDK 버전 2.4.1 응용 프로그램은 Azure 웹 앱 (Windows)에서 실행 중인 경우 위의 성능 카운터를 수집 하 고
   * SDK 버전 2.7.0-beta3 응용 프로그램에서 Windows를 실행 하 고 대상으로 하는 경우 위의 성능 카운터를 수집 하 고 `NETSTANDARD2.0` 이상.
   * .NET Framework를 대상으로 하는 응용 프로그램에 성능 카운터는 모든 버전의 SDK에서 지원 됩니다.
   * 이 문서에서는 Linux에서 성능 카운터 지원을 추가 될 때 업데이트 됩니다.

### <a name="ilogger-logs"></a>ILogger 로그

[ILogger 로그](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) 심각도 `Warning` 또는 위에 자동으로 SDK 버전 2.7.0-beta3에서 캡처된 이상.

### <a name="live-metrics"></a>라이브 메트릭

원격 분석 포털에서 나타날 때까지 몇 분 정도 걸릴 수 있습니다. 신속 하 게 사용 하는 모든 것이 작동 하는 경우를 확인 하려면 [라이브 메트릭](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)반면을 위해 실행 중인 응용 프로그램에 요청 합니다.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 응용 프로그램에 대 한 클라이언트 쪽 원격 분석을 사용 하도록 설정

위의 단계는 서버 쪽 원격 분석 수집을 시작 하는 데 충분 합니다. 응용 프로그램의 클라이언트 쪽 구성 요소가 있으면 다음 수집을 시작 하려면 아래 단계를 수행 [사용량 원격 분석](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) 거기서에서 합니다.

1. `_ViewImports.cshtml`, 삽입을 추가 합니다.

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml`, HtmlHelper의 끝에 삽입 `<head>` 섹션 다른 스크립트 전에 합니다. 이 코드 조각은 후 주입 될 페이지에서 보고 하려는 모든 사용자 지정 JavaScript 원격 분석:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` 기본 MVC 응용 프로그램 템플릿에서 위에 참조 된 파일 이름입니다. 궁극적으로 제대로 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 사용 하도록 설정 해야에 JavaScript 코드 조각을 `<head>` 모니터링 하려는 응용 프로그램의 각 페이지의 섹션입니다. 에 Javascript 코드 조각을 추가 하는이 응용 프로그램 템플릿에 대 한 `_Layout.cshtml` 이 목표를 효과적으로 수행 합니다. 이 특정 파일 프로젝트에 없는 경우 추가할 수 있습니다 [클라이언트 쪽 모니터링](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)합니다. 필요에 추가 하면 JavaScript 제어 하는 동급의 파일에는 `<head>` 앱 또는 또는 내에서 모든 페이지는 여러 개별 조각을 유지 관리 하기 어려운이 있지만 페이지 아니며 일반적으로 추가할 수 것이 좋습니다.

## <a name="configuring-application-insights-sdk"></a>Application Insights SDK 구성

ASP.NET Core 용 application Insights SDK는 기본 구성을 변경 하려는 사용자 지정할 수 있습니다. Application Insights ASP.NET SDK의 사용자가 사용 하 여 구성을 익숙할 `ApplicationInsights.config`를 수정 하 여 `TelemetryConfiguration.Active`입니다. ASP.NET Core에 대 한 구성은 다른 방식으로 수행 됩니다. ASP.NET Core SDK를 응용 프로그램에 추가 하 고 ASP.NET Core의 기본 제공을 사용 하 여 구성할 [종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)합니다. 거의 모든 구성 변경 내용을 수행 되는 `ConfigureServices()` 메서드의 `Startup.cs` 언급이 클래스입니다. 자세한 내용은 아래 섹션을 따릅니다.

> [!NOTE]
>  수정 하 여 구성 변경 `TelemetryConfiguration.Active` ASP.NET Core 응용 프로그램에서 권장 되지 않습니다.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions를 사용 하 여 구성

전달 하 여 몇 가지 일반적인 설정을 수정할 수 있기 `ApplicationInsightsServiceOptions` 에 `AddApplicationInsightsTelemetry`입니다. 예제는 다음과 같습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

구성 가능한 설정의 정확한 목록을 `ApplicationInsightsServiceOptions` 찾을 수 있습니다 [여기](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)합니다.

### <a name="sampling"></a>샘플링

ASP.NET Core 용 application Insights SDK는 FixedRate 및 적응 샘플링을 지원합니다. 적응 샘플링은 기본적으로 사용 됩니다. 에 따라 우리의 [적응 샘플링에 대 한 지침](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)를 ASP.NET Core 응용 프로그램에 대 한 샘플링을 구성 하는 방법을 알아봅니다.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

[원격 분석 이니셜라이저](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) 모든 원격 분석을 사용 하 여 전송 되는 전역 속성을 정의 하려는 경우에 사용 됩니다.

새 `TelemetryInitializer`, 아래와 같이 DependencyInjection 컨테이너에 추가 합니다. `TelemetryInitializer`s DependencyInjection 컨테이너에 추가 됩니다 수 SDK에서 자동으로 선택 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

모두 제거 하려면 기본적으로 제공 되는 특정 TelemetryInitializers 다음 샘플 코드를 사용 하거나 **한 후** 호출 `AddApplicationInsightsTelemetry()`합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>TelemetryProcessors를 추가합니다.

사용자 지정 원격 분석 프로세서를 추가할 수는 `TelemetryConfiguration` 확장 메서드를 사용 하 여 `AddApplicationInsightsTelemetryProcessor` 에서 `IServiceCollection`합니다. 원격 분석 프로세서를 사용 하는 [고급 필터링 시나리오](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) 포함 되거나에서 Application Insights 서비스에 보내는 원격 분석에서 제외 보다 직접 제어할 수 있도록 합니다. 다음 예제를 사용 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>구성 또는 기본 TelemetryModules 제거

Application Insights 원격 분석 모듈을 사용 하 여 한 가지 방법으로 [유용한 정보 자동 수집](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) 추가 구성 없이 특정 워크 로드에 대 한 합니다.

다음 자동 수집 모듈 기본적으로 사용 되 고 자동으로 원격 분석을 수집 하는 일을 담당 합니다. 사용 하지 않도록 설정 및 기본 동작을 변경 하도록 구성 될 수 있습니다.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

모든 기본값을 구성 하려면 `TelemetryModule`, 확장 메서드를 사용 하 여 `ConfigureTelemetryModule<T>` 에서 `IServiceCollection` 아래 예와에서 같이 합니다.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>원격 분석 채널 구성

사용 되는 기본 채널은는 `ServerTelemetryChannel`합니다. 다음 예제에서는 아래에서 재정의할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>자동 수집 된 원격 분석 이외의 추가 원격 분석을 추적 하려고 합니다. 하 하려면 어떻게 해야 합니까?

인스턴스를 가져올 `TelemetryClient` 생성자 주입을 사용 하 고 필요한 호출 하 여 `TrackXXX()` 메서드를 합니다. 새로 만들려면 권장 되지 않습니다 `TelemetryClient` ASP.NET Core 응용 프로그램에서의 singleton 인스턴스로 인스턴스 `TelemetryClient` 공유 하는 DI 컨테이너에 이미 등록 되어 `TelemetryConfiguration` 원격 분석의 나머지 부분을 사용 하 여 합니다. 새 `TelemetryClient` 인스턴스에 원격 분석의 나머지 부분에서 별도 구성 하는 경우에 권장 됩니다. 다음 예제에서는 컨트롤러에서 추가 원격 분석을 추적 하는 방법을 보여 줍니다.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 가리킵니다 [Application Insights 사용자 지정 메트릭 API 참조](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) Application Insights에서 보고 하는 사용자 지정 데이터에 대 한 합니다.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Application Insights를 사용 하도록 설정 하려면 IWebHostBuilder에 UseApplicationInsights() 확장 메서드를 사용 하는 몇 가지 Visual Studio 템플릿. 이 사용량이 여전히 유효한?

이 메서드를 사용 하 여 Application Insights를 사용 하도록 설정 하면 유효 하 고 Visual Studio 온 보 딩을 및 Azure 웹 앱 확장도 사용 됩니다. 그러나 사용할 것 `services.AddApplicationInsightsTelemetry()` 일부 구성을 제어 하기 위한 오버 로드를 제공 합니다. 두 메서드는 내부적으로 동일한 작업을 수행할 적용할 사용자 지정 구성이 없는 경우, 호출 중 하나를 합니다.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>내 ASP.NET Core 응용 프로그램을 Azure Web Apps를 배포 합니다. 웹 앱에서 Application Insights 확장을 여전히 사용할 해야 있습니까?

이 문서에 표시 된 대로 빌드 시에는 SDK가 설치 하는 경우 App Service 포털에서 Application Insights 확장을 사용 하려면 않아도가 됩니다. 확장이 설치 된 경우에 SDK 응용 프로그램에 이미 추가 되어 있는지 검색할 때 백오프 됩니다. 확장 프로그램에서 Application Insights를 사용 하도록 설정 하면 설치 하 고 SDK 업데이트를 해제 합니다. 그러나이 문서에 따라 Application Insights를 사용 하도록 설정 하면는 아래 원인에 대 한 더 유연 합니다.
   * Application Insights 원격 분석에서 작업을 계속 합니다.
       * 모든 운영 체제-Windows, Linux, Mac.
       * 모든 게시 모드-독립적인 또는 프레임 워크 종속입니다.
       * 모든 대상 프레임 워크에서 전체.NET Framework를 포함 합니다.
       * 모든 호스팅 옵션-Azure 웹 앱, Vm, Linux, 컨테이너, AKS, 비 Azure.
   * Visual Studio에서 디버깅 하는 경우 원격 분석을 로컬에서 볼 수 있습니다.
   * 사용 하 여 추가 사용자 지정 원격 분석 추적을 사용 하면 `TrackXXX()` API.
   * 완벽히 구성 해야합니다.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Application Insights 상태 모니터와 같은 도구를 사용 하 여 모니터링을 사용할 수 있습니까?

아니요. [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) 예정 된 대체 [상태 모니터 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 현재 ASP.NET을 지 원하는 4.x만 해당 합니다.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>ASP.NET Core 2.0 응용 프로그램을 만들었습니다. 되지 Application Insights 자동으로 활성화 아무 작업도 하지 않았는데?

`Microsoft.AspNetCore.All` 2.0 메타 패키지는 Application Insights SDK (버전 2.1.0)를 포함 하 고 Visual Studio 디버거에서 응용 프로그램을 실행 하는 경우 Visual Studio Application Insights를 사용 하면 IDE 자체에서 로컬 원격 분석을 보여 줍니다. 계측 키를 명시적으로 지정 하지 않으면 Application Insights 서비스로 원격 분석 전송 되지 않았습니다. 2.0에 대해서도 Application Insights를 사용 하도록 설정 하려면이 문서의 지침에 따라 권장 앱.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Linux에서 응용 프로그램을 실행 하는 필자입니다. 모든 기능에서에서 지 Linux 에서도?

* 예. SDK에 대 한 지원 기능 다음 예외를 사용 하 여 모든 플랫폼에서 동일 합니다.

    * 성능 카운터는 비-Windows에서 아직 지원 되지 않습니다.
    * 경우에 `ServerTelemetryChannel` 사용은 기본적으로 응용 프로그램은 Linux 또는 MacOS에서 실행 중인 경우 채널 자동으로 만들어지지는지 않습니다 네트워크 문제가 있는 경우 원격 분석을 일시적으로 유지 하려면 로컬 저장소 폴더입니다. 이 제한은 일시적인 네트워크 또는 서버 문제 없는 경우 손실 될 원격 분석을 하면 됩니다. 이 문제를 해결 하려면 아래 표시 된 대로 채널의 로컬 폴더를 구성 하는 사용자입니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>오픈 소스 SDK
[코드를 읽고 기여합니다](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>비디오

- 처음부터 [.NET Core 및 애플리케이션으로 Visual Studio으로 Application Insight 구성](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)에 대한 외부의 단계별 비디오입니다.
- 에 대 한 외부 단계별 비디오 [.NET Core 및 Visual Studio Code를 사용 하 여 Application Insights 구성](https://youtu.be/ygGt84GDync) 부터.

## <a name="next-steps"></a>다음 단계
* [사용자 흐름을 탐색](../../azure-monitor/app/usage-flows.md)하여 사용자가 앱을 탐색하는 방식을 이해합니다.
* 예외가 throw되는 시점의 소스 코드 및 변수 상태를 확인하려면 [스냅숏 컬렉션을 구성](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)합니다.
* [API를 사용](../../azure-monitor/app/api-custom-events-metrics.md) 합니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
