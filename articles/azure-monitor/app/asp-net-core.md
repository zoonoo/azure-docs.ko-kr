---
title: ASP.NET Core 응용 프로그램에 대 한 azure Application Insights | Microsoft Docs
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
ms.openlocfilehash: 7fe5a4f5a5d1d254918f1b4f997acfb9cf67a75b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272446"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램용 application Insights

이 문서에 대 한 Application Insights를 사용 하는 방법에 설명 합니다는 [ASP.NET Core](https://docs.microsoft.com/aspnet/core) 응용 프로그램입니다. 이 문서의 지침을 완료 하면 Application Insights에서 ASP.NET Core 응용 프로그램 요청, 종속성, 예외, 성능 카운터, 하트 비트 및 로그 수집 됩니다. 

여기에서는 예제는 [MVC 응용 프로그램](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) 대상으로 하는 `netcoreapp2.2`합니다. 모든 ASP.NET Core 응용 프로그램에 이러한 지침을 적용할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

합니다 [ASP.NET Core 용 Application Insights SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 실행 하는 방법 또는 위치에 관계 없이 응용 프로그램을 모니터링할 수 있습니다. 응용 프로그램 실행 중이 Azure로 네트워크 연결 되어 있는 경우에 원격 분석을 수집할 수 있습니다. Application Insights 모니터링에.NET Core는 지원 everywhere 지원 됩니다. 지원 범위에 포함 합니다.
* **운영 체제**: Windows, Linux 또는 Mac.
* **메서드를 호스팅**: 프로세스 또는 프로세스를 벗어났습니다. 
* **배포 방법**: 종속 또는 자체 포함 된 프레임 워크입니다.
* **웹 서버**: IIS (인터넷 정보 서비스) 또는 Kestrel 합니다. 
* **호스팅 플랫폼**: Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS) 및 등의 Web Apps 기능입니다.
* **IDE**: Visual Studio, VS Code 또는 명령줄입니다.

## <a name="prerequisites"></a>필수 조건

- 작동 ASP.NET Core 응용 프로그램입니다. ASP.NET Core 응용 프로그램을 만들려는 경우이 따라 [ASP.NET Core 자습서](https://docs.microsoft.com/aspnet/core/getting-started/)합니다.
- 올바른 Application Insights 계측 키입니다. 이 키를 Application Insights로 모든 원격 분석을 전송 해야 합니다. 새 Application Insights 리소스는 계측 키를 참조 하세요를 만들어야 하는 경우 [Application Insights 리소스 만들기](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)합니다.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights 서버 쪽 원격 분석 (Visual Studio)를 사용 하도록 설정

1. Visual Studio에서 프로젝트를 엽니다.

    > [!TIP]
    > 원한다 면 설정할 수 있습니다 원본 제어 프로젝트에 대 한 Application Insights를 사용 하면 모든 변경 내용을 추적할 수 있습니다. 소스 제어를 사용 하도록 설정 하려면 선택 **파일** > **소스 제어에 추가**합니다.

2. **프로젝트** > **Application Insights 원격 분석 추가**를 선택합니다.

3. **시작**을 선택합니다. 이 선택 영역의이 텍스트는 Visual Studio의 버전에 따라 달라질 수 있습니다. 일부 이전 버전 사용을 **무료 시작** 단추를 대신 합니다.

4. 구독을 선택합니다. 선택한 **자원** > **등록**합니다.

5. 프로젝트에 Application Insights를 추가한 후 SDK의 안정적인 최신 릴리스를 사용 하는 것인지 확인 합니다. 로 이동 **프로젝트** > **NuGet 패키지 관리** > **Microsoft.ApplicationInsights.AspNetCore**합니다. 선택 해야 할 경우 **업데이트**합니다.

     ![업데이트에 대 한 Application Insights 패키지를 선택 하는 위치를 보여 주는 스크린샷](./media/asp-net-core/update-nuget-package.png)

6. 뒤에 선택적 팁을 소스 제어에 프로젝트를 추가 하는 경우 이동할 **뷰** > **팀 탐색기** > **변경**합니다. 그런 다음 Application Insights 원격 분석에서 변경한 내용의 차이 뷰를 확인 하려면 각 파일을 선택 합니다.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Application Insights 서버 쪽 원격 분석 (Visual Studio 없음)를 사용 하도록 설정

1. 설치 합니다 [ASP.NET Core 용 Application Insights SDK NuGet 패키지](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)합니다. 항상 안정적인 최신 버전을 사용 하는 것이 좋습니다. SDK에 대 한 전체 릴리스 정보를 찾을 합니다 [오픈 소스 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)합니다.

    다음 코드 샘플 프로젝트에 추가할 변경 내용을 보여 줍니다 `.csproj` 파일입니다.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. 추가 `services.AddApplicationInsightsTelemetry();` 에 `ConfigureServices()` 에서 메서드 여 `Startup` 이 예제와 같이 클래스:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. 계측 키를 설정 합니다.

    계측 키를 인수로 제공할 수 있지만 `AddApplicationInsightsTelemetry`, 구성에서 계측 키를 지정 하는 것이 좋습니다. 다음 코드 샘플에서 계측 키를 지정 하는 방법을 보여 줍니다 `appsettings.json`합니다. 했는지 `appsettings.json` 게시 하는 동안 응용 프로그램 루트 폴더에 복사 됩니다.

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

    또는 다음 환경 변수에서 계측 키를 지정 합니다.

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    예를 들면 다음과 같습니다.

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    일반적으로 `APPINSIGHTS_INSTRUMENTATIONKEY` Web Apps에 배포 된 응용 프로그램에 대 한 계측 키를 지정 합니다.

    > [!NOTE]
    > 환경 변수를 통해 코드 wins에 지정 된 계측 키 `APPINSIGHTS_INSTRUMENTATIONKEY`, 다른 옵션과 비교 wins입니다.

## <a name="run-your-application"></a>애플리케이션 실행

응용 프로그램을 실행 하 고 요청을 확인 합니다. 이제 Application Insights로 원격 분석 흐름입니다. Application Insights SDK는 자동으로 같은 원격 분석 데이터를 수집합니다.

|요청/종속성 |세부 정보|
|---------------|-------|
|요청 | 응용 프로그램에 들어오는 웹 요청입니다. |
|HTTP 또는 HTTPS | 사용 하 여 호출 `HttpClient`합니다. |
|SQL | 사용 하 여 호출 `SqlClient`합니다. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage 클라이언트를 사용 하 여 호출 합니다. |
|[Event Hubs 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 버전 1.1.0 이상. |
|[Service Bus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 버전 3.0.0 이상. |
|Azure Cosmos DB | HTTP/HTTPS를 사용 하는 경우에 자동으로 추적 합니다. Application Insights는 TCP 모드를 캡처하지 않습니다. |

### <a name="performance-counters"></a>성능 카운터

에 대 한 지원 [성능 카운터](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) ASP.NET Core에서 제한 됩니다.

   * SDK 버전 2.4.1 및 응용 프로그램은 웹 앱 (Windows)에서 실행 중인 경우 나중에 수집 성능 카운터
   * 응용 프로그램은 Windows 및 대상에서 실행 중인 경우 SDK 버전 2.7.0-beta3 및 이후 수집 성능 카운터 `NETSTANDARD2.0` 이상.
   * .NET Framework를 대상으로 응용 프로그램에 대 한 모든 버전의 SDK는 성능 카운터를 지원 합니다.
 
이 문서에서는 Linux에서 성능 카운터 지원을 추가 될 때 업데이트 됩니다.

### <a name="ilogger-logs"></a>ILogger 로그

[ILogger 로그](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) 심각도 `Warning` 이상 SDK 버전 2.7.0-beta3 이상에 자동으로 캡처됩니다.

### <a name="live-metrics"></a>라이브 메트릭

포털에 표시 되는 원격 분석 시작 하기 전에 몇 분이 걸릴 수 있습니다. 모두 작동 하는지 확인 신속 하 게, 것이 좋습니다는 데 [라이브 메트릭](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) 실행 중인 응용 프로그램에 요청을 만들면 됩니다.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 응용 프로그램에 대 한 클라이언트 쪽 원격 분석을 사용 하도록 설정

앞의 단계는 서버 쪽 원격 분석 수집을 시작 하는 데 충분 합니다. 응용 프로그램의 클라이언트 쪽 구성 요소에 수집을 시작 하려면 다음 단계를 수행 [사용량 원격 분석](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)합니다.

1. `_ViewImports.cshtml`, 삽입을 추가 합니다.

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml`를 삽입 `HtmlHelper` 끝에 `<head>` 섹션 다른 스크립트 전에 합니다. 페이지에서 모든 사용자 지정 JavaScript 원격 분석을 보고 하려는 경우이 코드 조각은 후 삽입 합니다.

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` 기본 MVC 응용 프로그램 템플릿에서 앞에서 참조 하는 파일 이름입니다. JavaScript 코드 조각에 표시 되어야 제대로 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 사용 하도록 설정 하려는 경우에 궁극적으로 `<head>` 모니터링 하려는 응용 프로그램의 각 페이지의 섹션입니다. 이 응용 프로그램 템플릿에 대 한이 목표는 JavaScript 코드 조각을 추가 하 여 수행할 수 있습니다 `_Layout.cshtml`합니다. 

프로젝트에 포함 되지 않은 경우 `_Layout.cshtml`를 추가할 수 있습니다 [클라이언트 쪽 모니터링](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)합니다. 제어 하는 동급의 파일에 JavaScript 코드 조각을 추가 하 여이 수행할 수는 `<head>` 앱 내의 모든 페이지의 합니다. 또는 여러 페이지에는 코드 조각을 추가할 수 있지만이 솔루션은 유지 관리 하기가 및 일반적으로 권장 하지 않습니다.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK를 구성 합니다.

기본 구성을 변경 하려면 ASP.NET Core 용 Application Insights SDK를 사용자 지정할 수 있습니다. Application Insights ASP.NET SDK의 사용자를 사용 하 여 구성을 변경 하는 일을 알고 있을 `ApplicationInsights.config` 하거나 수정 하 여 `TelemetryConfiguration.Active`입니다. ASP.NET Core에 대 한 다른 방식으로 구성을 변경할 수 있습니다. 응용 프로그램에 ASP.NET Core SDK를 추가 하 고 기본 제공 ASP.NET Core를 사용 하 여 구성 [종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)합니다. 거의 모든 구성 변경 합니다 `ConfigureServices()` 메서드의 프로그램 `Startup.cs` 으로 배포할지에 고, 그렇지 않으면 클래스입니다. 다음 섹션에서는 자세한 정보를 제공 합니다.

> [!NOTE]
> ASP.NET Core 응용 프로그램을 수정 하 여 구성 변경에 `TelemetryConfiguration.Active` 권장 되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions를 사용 하 여

전달 하 여 몇 가지 일반적인 설정을 수정할 수 있습니다 `ApplicationInsightsServiceOptions` 에 `AddApplicationInsightsTelemetry`이 예제와 같이:

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

자세한 내용은 참조는 [의 구성 가능한 설정 `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)합니다.

### <a name="sampling"></a>샘플링

ASP.NET Core 용 Application Insights SDK는 고정 비율 및 적응 샘플링을 지원합니다. 적응 샘플링은 기본적으로 사용 됩니다. 

자세한 내용은 [ASP.NET Core 응용 프로그램에 대 한 적응 샘플링 구성](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)합니다.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

사용 하 여 [원격 분석 이니셜라이저](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) 모든 원격 분석을 사용 하 여 전송 되는 전역 속성을 정의 하려는 경우.

새로 추가 `TelemetryInitializer` 에 `DependencyInjection` 다음 코드 에서처럼 컨테이너입니다. SDK는 자동으로 모든 선택 `TelemetryInitializer` 에 추가 되는 `DependencyInjection` 컨테이너입니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

원격 분석 이니셜라이저는 기본적으로 제공 합니다. 모두 제거 하 하거나 특정 원격 분석 이니셜라이저를 사용 하 여 다음 샘플 코드 *한 후* 호출 `AddApplicationInsightsTelemetry()`합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

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

### <a name="adding-telemetry-processors"></a>원격 분석 프로세서를 추가합니다.

사용자 지정 원격 분석 프로세서를 추가할 수 있습니다 `TelemetryConfiguration` 확장 메서드를 사용 하 여 `AddApplicationInsightsTelemetryProcessor` 에서 `IServiceCollection`합니다. 원격 분석 프로세서를 사용 하 여 [고급 필터링 시나리오](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) 에서 Application Insights 서비스에 보내는 원격 분석에서 제외 되거나 포함에 무엇 보다 직접 제어할 수 있도록 합니다. 다음 예제를 사용 합니다.

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

Application Insights 원격 분석 모듈을 사용 하 여 [유용한 정보를 자동으로 수집](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) 추가 구성이 필요 하지 않고 특정 워크 로드에 대 한 합니다.

다음 자동 수집 모듈은 기본적으로 활성화 됩니다. 이러한 모듈은 자동으로 원격 분석을 수집 하는 일을 담당 합니다. 사용 하지 않도록 설정 하거나 해당 기본 동작을 변경 하도록 구성할 수 있습니다.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

모든 기본값을 구성 하려면 `TelemetryModule`, 확장 메서드를 사용 하 여 `ConfigureTelemetryModule<T>` 에서 `IServiceCollection`다음 예제에서와 같이 합니다.

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

### <a name="configuring-a-telemetry-channel"></a>원격 분석 채널을 구성합니다.

기본 채널은 `ServerTelemetryChannel`합니다. 다음 예제와 같이 재정의할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집 되는 원격 분석을 어떻게 추적할 수 있습니까?

인스턴스를 가져올 `TelemetryClient` 생성자 주입을 사용 하 고 필요한 호출 하 여 `TrackXXX()` 메서드를 합니다. 새로 만들기 하는 것이 좋습니다 `TelemetryClient` ASP.NET Core 응용 프로그램의 인스턴스. singleton 인스턴스로 `TelemetryClient` 에 이미 등록 되어 합니다 `DependencyInjection` 컨테이너를 공유 하는 `TelemetryConfiguration` 원격 분석의 나머지 부분을 사용 하 여 합니다. 새 `TelemetryClient` 인스턴스만 권장 되는 원격 분석의 나머지 부분을 별도로 구성 해야 하는 경우. 

다음 예제에서는 컨트롤러에서 추가 원격 분석을 추적 하는 방법을 보여 줍니다.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Application Insights에서 보고 하는 사용자 지정 데이터에 대 한 자세한 내용은 참조 하세요. [Application Insights 사용자 지정 메트릭 API 참조](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)합니다.

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Application Insights를 사용 하도록 설정 하려면 IWebHostBuilder에 UseApplicationInsights() 확장 메서드를 사용 하는 몇 가지 Visual Studio 템플릿. 이 사용량이 여전히 유효한?

예,이 메서드를 사용 하 여 Application Insights를 사용 하도록 설정 되었습니다. 이 방법은 Visual Studio 온 보 딩에 웹 앱 확장에 사용 됩니다. 하지만 사용 하 여 권장 `services.AddApplicationInsightsTelemetry()` 일부 구성을 제어 하기 위한 오버 로드를 제공 하기 때문입니다. 두 메서드 모두 사용자 지정 구성을 적용할 필요가 없으면 든 호출할 수 있도록 내부적으로 동일한 작업을 수행 합니다.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>내 ASP.NET Core 응용 프로그램을 Web Apps를 배포 합니다. 웹 앱에서 Application Insights 확장을 여전히 사용할 해야 있습니까?

이 문서에 표시 된 대로 빌드 시에는 SDK가 설치 하는 경우 App Service 포털에서 Application Insights 확장을 사용할 필요가 없습니다. 확장이 설치 된 경우에 SDK 응용 프로그램에 이미 추가 되어 있는지 검색할 때 백오프 됩니다. 확장 프로그램에서 Application Insights를 사용 하는 경우 설치 하 고 SDK를 업데이트할 필요가 없습니다. 있으 나이 문서의 지침에 따라 Application Insights를 사용 하면 보다 유연 하 게 되므로:

   * Application Insights 원격 분석에서 작업을 계속 합니다.
       * Windows, Linux 및 Mac.를 비롯 한 모든 운영 체제
       * 모든 게시 모드를 포함 하 여 자체 포함 또는 프레임 워크 종속입니다.
       * 모든 대상 프레임 워크에서 전체.NET Framework를 포함 합니다.
       * 모든 호스팅 옵션은 웹 앱, Vm, Linux, 컨테이너, Azure Kubernetes Service 및 비 Azure 호스트를 포함 합니다.
   * Visual Studio에서 디버깅할 때 로컬 원격 분석을 볼 수 있습니다.
   * 사용 하 여 추가 사용자 지정 원격 분석을 추적할 수 있습니다는 `TrackXXX()` API.
   * 완벽히 구성 해야합니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Application Insights 상태 모니터와 같은 도구를 사용 하 여 모니터링을 사용할 수 있습니까?

아니요. [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) 하 고 [상태 모니터 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 현재 ASP.NET을 지 원하는 4.x만 해당 합니다.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights 자동으로 활성화 됩니다 내 ASP.NET Core 2.0 응용 프로그램에 대 한?

`Microsoft.AspNetCore.All` 2.0 메타 패키지는 Application Insights SDK (버전 2.1.0)를 포함 합니다. Visual Studio 디버거에서 응용 프로그램을 실행 하는 경우 Visual Studio Application Insights를 사용 하도록 설정 하 고 IDE 자체에서 로컬 원격 분석을 보여 줍니다. 계측 키를 지정 하지 않으면 원격 분석이 Application Insights 서비스에 전송 되지 않았습니다. 2\.0에 대해서도 Application Insights를 사용 하도록 설정 하려면이 문서의 지침에 따라 권장 앱.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux에서 내 응용 프로그램을 실행 하면 모든 기능이 지원 되나요?

예. SDK에 대 한 기능이 다음 예외를 사용 하 여 모든 플랫폼에서 동일합니다.

* 성능 카운터는 Windows 에서만에서 지원 됩니다.
* 경우에 `ServerTelemetryChannel` 사용은 기본적으로 응용 프로그램은 Linux 또는 MacOS에서 실행 중인 경우 채널 자동으로 만들지 않습니다 네트워크 문제가 있는 경우 원격 분석을 일시적으로 유지 하려면 로컬 저장소 폴더입니다. 임시 서버나 네트워크 문제가 있는 경우이 제한으로 인해 원격 분석을 손실 됩니다. 이 문제를 해결 하려면 채널에 대 한 로컬 폴더를 구성 합니다.

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
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>오픈 소스 SDK

[코드를 읽고 기여합니다](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>비디오

- 이 외부 단계별 비디오를 확인해 [.NET Core 및 Visual Studio를 사용 하 여 Application Insights 구성](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) 부터.
- 이 외부 단계별 비디오를 확인해 [.NET Core 및 Visual Studio Code를 사용 하 여 Application Insights 구성](https://youtu.be/ygGt84GDync) 부터.

## <a name="next-steps"></a>다음 단계

* [사용자 흐름을 탐색](../../azure-monitor/app/usage-flows.md) 사용자가 앱을 탐색 하는 방법을 알아야 합니다.
* [스냅숏 컬렉션 구성](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 예외가 현재 소스 코드 및 변수의 상태를 파악할 수 있습니다.
* [API를 사용 하 여](../../azure-monitor/app/api-custom-events-metrics.md) 사용자 고유의 이벤트 및 앱의 성능 및 사용량에 대해 자세한 보기에 대 한 메트릭을 보내도록 합니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
