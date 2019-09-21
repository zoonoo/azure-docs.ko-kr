---
title: ASP.NET Core 응용 프로그램에 대 한 Azure 애플리케이션 정보 | Microsoft Docs
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
ms.openlocfilehash: 9e14a9f3f2f27112a591f14e9a93580f66aadef7
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169557"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램에 대 한 Application Insights

이 문서에서는 [ASP.NET Core](https://docs.microsoft.com/aspnet/core) 응용 프로그램에 대해 Application Insights를 사용 하도록 설정 하는 방법을 설명 합니다. 이 문서의 지침을 완료 하면 Application Insights는 ASP.NET Core 응용 프로그램에서 요청, 종속성, 예외, 성능 카운터, 하트 비트 및 로그를 수집 합니다.

여기서 사용 하는 예제는를 대상 `netcoreapp2.2`으로 하는 [MVC 응용 프로그램](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) 입니다. 모든 ASP.NET Core 응용 프로그램에 이러한 지침을 적용할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

[ASP.NET Core에 대 한 APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 는 응용 프로그램의 실행 위치에 관계 없이 응용 프로그램을 모니터링할 수 있습니다. 응용 프로그램이 실행 중이 고 Azure에 네트워크로 연결 되어 있는 경우 원격 분석을 수집할 수 있습니다. Application Insights 모니터링은 .NET Core가 지원 되는 모든 위치에서 지원 됩니다. 지원 내용:
* **운영 체제**: Windows, Linux 또는 Mac입니다.
* **호스팅 메서드**: 처리 중입니다.
* **배포 방법**: 프레임 워크 종속 또는 자체 포함.
* **웹 서버**: IIS (Internet Information Server) 또는 Kestrel.
* **호스팅 플랫폼**: Azure App Service, Azure VM, Docker, Azure Kubernetes 서비스 (AKS) 등의 Web Apps 기능입니다.
* **IDE**: Visual Studio, VS Code 또는 명령줄입니다.

> [!NOTE]
> Application Insights와 함께 ASP.NET Core 3.0-미리 보기를 사용 하는 경우 [2.8.0-beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta3) 버전 이상을 사용 하세요. 이 버전은 ASP.NET Core 3.0에서 잘 작동 하는 것으로 알려져 있습니다. 또한 ASP.NET Core 3.0 앱에 대해서는 Visual Studio 기반 온 보 딩이 아직 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 작동 하는 ASP.NET Core 응용 프로그램입니다. ASP.NET Core 응용 프로그램을 만들어야 하는 경우이 [ASP.NET Core 자습서](https://docs.microsoft.com/aspnet/core/getting-started/)를 따르세요.
- 유효한 Application Insights 계측 키입니다. Application Insights에 원격 분석을 보내려면이 키가 필요 합니다. 계측 키를 가져오기 위해 새 Application Insights 리소스를 만들어야 하는 경우 [Application Insights 리소스 만들기](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)를 참조 하세요.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>서버 쪽 원격 분석 Application Insights 사용 (Visual Studio)

1. Visual Studio에서 프로젝트를 엽니다.

    > [!TIP]
    > 원할 경우 프로젝트에 대 한 소스 제어를 설정 하 여 Application Insights에서 수행 하는 모든 변경 내용을 추적할 수 있습니다. 소스 제어를 사용 하도록 설정 하려면 **파일** > **원본 제어에 추가**를 선택 합니다.

2. **프로젝트** > **Application Insights 원격 분석 추가**를 선택합니다.

3. **시작**을 선택합니다. 이 선택의 텍스트는 사용자의 Visual Studio 버전에 따라 다를 수 있습니다. 일부 이전 버전에서는 대신 **시작** 사용 단추를 사용 합니다.

4. 구독을 선택합니다. 그런 다음 **리소스** > **레지스터**를 선택 합니다.

5. 프로젝트에 Application Insights를 추가한 후 최신 안정적인 SDK 릴리스를 사용 하 고 있는지 확인 합니다. **Project** > **NuGet**패키지관리 > **AspNetCore**로 이동 합니다. 필요한 경우 **업데이트**를 선택 합니다.

     ![업데이트할 Application Insights 패키지를 선택할 수 있는 위치를 보여 주는 스크린샷](./media/asp-net-core/update-nuget-package.png)

6. 선택적 팁을 따르고 프로젝트를 소스 제어에 추가한 경우**팀 탐색기** > **변경 내용** **보기** > 로 이동 합니다. 그런 다음 각 파일을 선택 하 Application Insights 원격 분석에서 변경한 내용에 대 한 차이 보기를 확인 합니다.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>서버 쪽 원격 분석 Application Insights 사용 (Visual Studio 없음)

1. [ASP.NET Core에 대 한 APPLICATION INSIGHTS SDK NuGet 패키지](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)를 설치 합니다. 항상 안정적인 최신 버전을 사용 하는 것이 좋습니다. [오픈 소스 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)에서 SDK에 대 한 전체 릴리스 정보를 찾습니다.

    다음 코드 샘플에서는 프로젝트의 `.csproj` 파일에 추가 되는 변경 내용을 보여 줍니다.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. 다음 `services.AddApplicationInsightsTelemetry();` 예제와 `ConfigureServices()` 같이 `Startup` 클래스의 메서드에를 추가 합니다.

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

    계측 키를에 대 `AddApplicationInsightsTelemetry`한 인수로 제공할 수 있지만 구성에서 계측 키를 지정 하는 것이 좋습니다. 다음 코드 샘플에서는에서 `appsettings.json`계측 키를 지정 하는 방법을 보여 줍니다. 게시 하 `appsettings.json` 는 동안이 응용 프로그램 루트 폴더에 복사 되었는지 확인 합니다.

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

    또는 다음 환경 변수 중 하나에서 계측 키를 지정 합니다.

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    예를 들어 다음과 같은 가치를 제공해야 합니다.

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    일반적으로 Web Apps에 배포 된 응용 프로그램에 대 한 계측 키를 지정합니다.`APPINSIGHTS_INSTRUMENTATIONKEY`

    > [!NOTE]
    > 코드에 지정 된 계측 키가 다른 옵션을 통해 `APPINSIGHTS_INSTRUMENTATIONKEY`적용 되는 환경 변수를 통해 이깁니다.

## <a name="run-your-application"></a>애플리케이션 실행

응용 프로그램을 실행 하 고 요청을 만듭니다. 이제 원격 분석이 Application Insights로 흐릅니다. Application Insights SDK는 다음과 같은 원격 분석을 자동으로 수집 합니다.

|요청/종속성 |설명|
|---------------|-------|
|요청 | 응용 프로그램에 대 한 들어오는 웹 요청입니다. |
|HTTP 또는 HTTPS | 를 사용 하 `HttpClient`여 호출 합니다. |
|SQL | 를 사용 하 `SqlClient`여 호출 합니다. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage 클라이언트를 사용 하 여 호출 합니다. |
|[EventHubs 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 버전 1.1.0 이상 |
|[ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 버전 3.0.0 이상 |
|Azure Cosmos DB | HTTP/HTTPS를 사용 하는 경우에만 자동으로 추적 됩니다. Application Insights는 TCP 모드를 캡처하지 않습니다. |

### <a name="performance-counters"></a>성능 카운터

ASP.NET Core의 [성능 카운터](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) 에 대 한 지원은 제한 되어 있습니다.

* SDK 버전 2.4.1 이상에서는 응용 프로그램이 Azure Web Apps (Windows)에서 실행 되는 경우 성능 카운터를 수집 합니다.
* SDK 버전 2.7.1 이상에서는 응용 프로그램이 Windows 및 대상 `NETSTANDARD2.0` 이상에서 실행 되는 경우 성능 카운터를 수집 합니다.
* .NET Framework를 대상으로 하는 응용 프로그램의 경우 모든 버전의 SDK에서 성능 카운터를 지원 합니다.
* SDK 버전 2.8.0-beta3 이상에서는 Linux의 cpu/메모리 카운터를 지원 합니다. 다른 카운터는 Linux에서 지원 되지 않습니다. Linux 및 기타 비 Windows 환경에서 시스템 카운터를 가져오는 권장 방법은 [Eventcounters](#eventcounter) 를 사용 하는 것입니다.

### <a name="eventcounter"></a>EventCounter

[Eventcounter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)는 .NET/.net Core에서 카운터를 게시 하 고 사용 하는 플랫폼 간 메서드입니다. 이 기능은 이전에 있었지만 이러한 카운터를 게시 한 기본 제공 공급자는 없었습니다. .NET Core 3.0부터 몇 가지 카운터가 CLR 카운터, ASP.NET Core 카운터 등의 상자에서 게시 됩니다.

SDK 버전 2.8.0-beta3 이상에서는 EventCounters 컬렉션을 지원 합니다. 기본적으로 SDK는 다음 카운터를 수집 합니다. 이러한 카운터는 메트릭 탐색기에서 또는 PerformanceCounter 테이블 아래의 Analytics 쿼리를 사용 하 여 쿼리할 수 있습니다. 카운터 이름은 "Category | 형식이 됩니다. Counter ".

|범주 | 카운터|
|---------------|-------|
|시스템 런타임 | cpu 사용량 |
|시스템 런타임 | 작업 집합 |
|시스템 런타임 | gc-힙 크기 |
|시스템 런타임 | gen-0-gc-개수 |
|시스템 런타임 | gen-1-gc-개수 |
|시스템 런타임 | gen-2-gc-개수 |
|시스템 런타임 | gc 시간 |
|시스템 런타임 | gen-0-크기 |
|시스템 런타임 | gen-1-크기 |
|시스템 런타임 | gen-2-크기 |
|시스템 런타임 | loh-크기 |
|시스템 런타임 | 할당 율 |
|시스템 런타임 | 어셈블리 수 |
|시스템 런타임 | 예외-개수 |
|시스템 런타임 | threadpool-스레드 수 |
|시스템 런타임 | 모니터-잠금-경합 수 |
|시스템 런타임 | threadpool-큐 길이 |
|시스템 런타임 | threadpool-완료-항목 수 |
|시스템 런타임 | 활성-타이머-개수 |
|AspNetCore | 초당 요청 수 |
|AspNetCore | total-requests |
|AspNetCore | 현재 요청 |
|AspNetCore | 실패-요청 |

### <a name="ilogger-logs"></a>ILogger 로그

심각도`Warning` 이상의 [ILogger 로그](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) 는 SDK 버전 합니다-beta3 이상에서 자동으로 캡처됩니다.

### <a name="live-metrics"></a>라이브 메트릭

원격 분석이 포털에 표시 되기 시작 하는 데 몇 분 정도 걸릴 수 있습니다. 모든 것이 제대로 작동 하는지 신속 하 게 확인 하려면 실행 중인 응용 프로그램에 요청을 할 때 [라이브 메트릭을](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) 사용 하는 것이 가장 좋습니다.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 응용 프로그램에 대 한 클라이언트 쪽 원격 분석 사용

위의 단계는 서버측 원격 분석 수집을 시작 하는 데 충분 합니다. 응용 프로그램에 클라이언트 쪽 구성 요소가 있는 경우 다음 단계에 따라 [사용 원격 분석](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)수집을 시작 합니다.

1. 에서 `_ViewImports.cshtml`삽입을 추가 합니다.

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 에서 `_Layout.cshtml` 섹션`<head>` 의 `HtmlHelper` 끝에, 다른 스크립트 앞에를 삽입 합니다. 페이지에서 사용자 지정 JavaScript 원격 분석을 보고 하려는 경우이 코드 조각 뒤에 삽입 합니다.

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

이전에 참조 한 파일이름은기본MVC응용프로그램템플릿에서가져온것입니다.`.cshtml` 궁극적으로 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 적절 하 게 사용 하려면 모니터링할 응용 프로그램의 각 페이지에 `<head>` 있는 섹션에 JavaScript 코드 조각이 표시 되어야 합니다. 에 `_Layout.cshtml`JavaScript 코드 조각을 추가 하 여이 응용 프로그램 템플릿에 대 한이 목표를 달성할 수 있습니다. 

프로젝트에가 포함 `_Layout.cshtml`되지 않은 경우에도 [클라이언트 쪽 모니터링](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)을 추가할 수 있습니다. 이렇게 하려면 응용 프로그램 내 모든 페이지의을 `<head>` 제어 하는 동일한 파일에 JavaScript 코드 조각을 추가 합니다. 또는 여러 페이지에 코드 조각을 추가할 수 있지만이 솔루션은 유지 관리 하기 어렵고 일반적으로 권장 하지 않습니다.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 구성

ASP.NET Core에 대 한 Application Insights SDK를 사용자 지정 하 여 기본 구성을 변경할 수 있습니다. Application Insights ASP.NET SDK의 사용자는 또는을 ( `ApplicationInsights.config` `TelemetryConfiguration.Active`를) 사용 하 여 구성을 변경 하는 방법을 익힐 수 있습니다. ASP.NET Core에 대 한 구성을 다르게 변경 합니다. ASP.NET Core SDK를 응용 프로그램에 추가 하 고 ASP.NET Core 기본 제공 [종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)을 사용 하 여 구성 합니다. 달리 지정 하지 않는 한 `ConfigureServices()` `Startup.cs` 클래스의 메서드에서 거의 모든 구성 변경을 수행 합니다. 다음 섹션에서는 자세한 정보를 제공 합니다.

> [!NOTE]
> ASP.NET Core 응용 프로그램에서는를 수정 `TelemetryConfiguration.Active` 하 여 구성을 변경 하는 것이 지원 되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions 사용

다음 예와 같이에 `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`전달 하 여 몇 가지 일반적인 설정을 수정할 수 있습니다.

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

의 전체 설정 목록`ApplicationInsightsServiceOptions`

|설정 | 설명 | 기본값
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics 기능 사용/사용 안 함 | true
|EnableAdaptiveSampling | 적응 샘플링 사용/사용 안 함 | true
|EnableHeartbeat 비트 | 하트 비트 기능 사용/사용 안 함-주기적 (15 분 기본값)은 ' HeartBeatState ' 라는 사용자 지정 메트릭을 .NET 버전, Azure 환경 정보 (해당 하는 경우) 등의 런타임에 대 한 정보로 보냅니다. | true
|AddAutoCollectedMetricExtractor | 샘플링을 수행 하기 전에 요청/종속성에 대 한 미리 집계 된 메트릭을 전송 하는 TelemetryProcessor AutoCollectedMetrics 추출기를 사용/사용 안 함으로 설정 합니다. | true
|RequestCollectionOptions. 사이 예외 | 요청 수집 모듈에서 처리 되지 않은 예외 추적의 보고를 사용 하거나 사용 하지 않도록 설정 합니다. | ApplicationInsightsLoggerProvider를 사용 하 여 예외를 추적 하므로 NETSTANDARD 2.0의 경우 false이 고, 그렇지 않으면 true입니다.

최신 목록에 대해서는 [의 `ApplicationInsightsServiceOptions` 구성 가능한 설정을](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) 참조 하세요.

### <a name="sampling"></a>샘플링

ASP.NET Core에 대 한 Application Insights SDK는 고정 비율과 적응 샘플링을 모두 지원 합니다. 적응 샘플링은 기본적으로 사용 하도록 설정 되어 있습니다. 

자세한 내용은 [ASP.NET Core 응용 프로그램에 대 한 적응 샘플링 구성](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)을 참조 하세요.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

모든 원격 분석과 함께 전송 되는 전역 속성을 정의 하려면 [원격 분석 이니셜라이저](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) 를 사용 합니다.

다음 코드에 `TelemetryInitializer` 표시 된 `DependencyInjection` 것 처럼 컨테이너에 새를 추가 합니다. SDK는 `DependencyInjection` 컨테이너에 추가 된 `TelemetryInitializer` 모든를 자동으로 선택 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

원격 분석 이니셜라이저는 기본적으로 제공 됩니다. 모든 또는 특정 원격 분석 이니셜라이저를 제거 하려면를 호출한 `AddApplicationInsightsTelemetry()` *후* 다음 샘플 코드를 사용 합니다.

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

### <a name="adding-telemetry-processors"></a>원격 분석 프로세서 추가

에서 `TelemetryConfiguration` `AddApplicationInsightsTelemetryProcessor` 확장 메서드를 사용 하 여 사용자 지정 원격 분석 프로세서를에 추가할 수 있습니다. `IServiceCollection` [고급 필터링 시나리오](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) 에서 원격 분석 프로세서를 사용 하 여 Application Insights 서비스로 보내는 원격 분석에서 포함 되거나 제외 된 항목을 더 직접적으로 제어할 수 있습니다. 다음 예제를 사용 합니다.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 TelemetryModules 구성 또는 제거

Application Insights 원격 분석 모듈을 사용 하 여 사용자별 수동 추적을 요구 하지 않고 특정 작업에 대 한 유용한 원격 분석을 자동으로 수집 합니다.

다음 자동 컬렉션 모듈은 기본적으로 사용 하도록 설정 되어 있습니다. 이러한 모듈은 원격 분석을 자동으로 수집 합니다. 기본 동작을 변경 하도록 사용 하지 않도록 설정 하거나 구성할 수 있습니다.

* `RequestTrackingTelemetryModule`-들어오는 웹 요청에서 RequestTelemetry 분석을 수집 합니다.
* `DependencyTrackingTelemetryModule`-나가는 http 호출 및 sql 호출에서 DependencyTelemetry을 수집 합니다.
* `PerformanceCollectorModule`-Windows PerformanceCounters을 수집 합니다.
* `QuickPulseTelemetryModule`-라이브 메트릭 포털에 표시 하기 위한 원격 분석을 수집 합니다.
* `AppServicesHeartbeatTelemetryModule`-응용 프로그램이 호스트 되는 Azure App Service 환경에 대해 하트 비트 (사용자 지정 메트릭으로 전송 됨)를 수집 합니다.
* `AzureInstanceMetadataTelemetryModule`-응용 프로그램이 호스트 되는 Azure VM 환경에 대해 하트 비트 (사용자 지정 메트릭으로 전송 됨)를 수집 합니다.
* `EventCounterCollectionModule`- [Eventcounters를](#eventcounter)수집 합니다. 이 모듈은 새로운 기능이 며 SDK 버전 2.8.0-beta3 이상에서 사용할 수 있습니다.

기본값 `TelemetryModule`을 구성 하려면 다음 예제와 같이에 `ConfigureTelemetryModule<T>` `IServiceCollection`확장 메서드를 사용 합니다.

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

        // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
                            (module, o) =>
                            {
                                module.Counters.Clear();
                                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
                            }
                        );

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-a-telemetry-channel"></a>원격 분석 채널 구성

기본 채널은 `ServerTelemetryChannel`입니다. 다음 예제와 같이이를 재정의할 수 있습니다.

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

### <a name="disable-telemetry-dynamically"></a>동적 원격 분석 사용 안 함

조건부로 또는 동적으로 원격 분석을 사용 하지 않도록 설정 하려는 경우 `TelemetryConfiguration` 코드의 어느 곳에서 나 ASP.NET Core 종속성 주입 컨테이너를 사용 `DisableTelemetry` 하 여 인스턴스를 확인 하 고 플래그를 설정할 수 있습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

위의 경우 자동 수집 모듈이 원격 분석을 수집 하는 것을 방지할 수 없습니다. Application Insights에 대 한 원격 분석 보내기가 위의 방법으로 사용 하지 않도록 설정 됩니다. 특정 자동 수집 모듈을 원하지 않는 경우 [원격 분석 모듈을 제거](#configuring-or-removing-default-telemetrymodules) 하는 것이 가장 좋습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집 되지 않는 원격 분석을 추적 하려면 어떻게 해야 하나요?

생성자 주입을 사용 `TelemetryClient` 하 여의 인스턴스를 가져온 다음 필요한 `TrackXXX()` 메서드를 호출 합니다. ASP.NET Core 응용 프로그램에서는 새 `TelemetryClient` 인스턴스를 만들지 않는 것이 좋습니다. 의 `TelemetryClient` 단일 인스턴스는 나머지 원격 분석과 공유 `TelemetryConfiguration` 하 `DependencyInjection` 는 컨테이너에 이미 등록 되어 있습니다. 새 `TelemetryClient` 인스턴스를 만드는 것은 원격 분석의 나머지 부분과 분리 된 구성이 필요한 경우에만 권장 됩니다. 

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

Application Insights의 사용자 지정 데이터 보고에 대 한 자세한 내용은 [Application Insights 사용자 지정 메트릭 API 참조](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)를 참조 하세요.

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>일부 Visual Studio 템플릿은 IWebHostBuilder에서 UseApplicationInsights () 확장 메서드를 사용 하 여 Application Insights를 사용 하도록 설정 했습니다. 이 사용량이 여전히 유효 한가요?

예,이 메서드를 사용 하 여 Application Insights를 사용할 수 있습니다. 이 기술은 Visual Studio 온 보 딩 및 Web Apps 확장에서 사용 됩니다. 그러나 일부 구성을 제어 하 `services.AddApplicationInsightsTelemetry()` 는 오버 로드를 제공 하기 때문에를 사용 하는 것이 좋습니다. 두 방법 모두 내부적으로 동일한 작업을 수행 하므로 사용자 지정 구성을 적용할 필요가 없는 경우 두 메서드 중 하나를 호출할 수 있습니다.

`IWebHostBuilder`는 ASP.NET Core 3.0 `IHostBuilder` 에서로 바뀌고 Application Insights 혼동을 피하기 위해 beta3 2.8.0 버전은 useapplicationinsights () 메서드를 사용 되지 않는 것으로 표시 하 고 다음 주 버전에서는 제거 됩니다.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Web Apps에 ASP.NET Core 응용 프로그램을 배포 합니다. Web Apps에서 Application Insights 확장을 계속 사용 하도록 설정 해야 하나요?

이 문서에 표시 된 것 처럼 SDK가 빌드 시간에 설치 된 경우 App Service 포털에서 [Application Insights 확장](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) 을 사용 하도록 설정할 필요가 없습니다. 확장이 설치 된 경우에도 SDK가 응용 프로그램에 이미 추가 된 것을 감지 하면 해당 확장이 다시 시작 됩니다. 확장에서 Application Insights를 사용 하도록 설정 하는 경우 SDK를 설치 하 고 업데이트할 필요가 없습니다. 그러나이 문서의 지침에 따라 Application Insights를 사용 하도록 설정 하면 다음과 같은 이유로 유연성이 향상 됩니다.

   * Application Insights 원격 분석은에서 계속 작동 합니다.
       * Windows, Linux, Mac 등의 모든 운영 체제
       * 자체 포함 또는 프레임 워크 종속을 비롯 한 모든 게시 모드
       * 전체 .NET Framework를 포함 하는 모든 대상 프레임 워크입니다.
       * Web Apps, Vm, Linux, 컨테이너, Azure Kubernetes 서비스 및 비 Azure 호스팅을 비롯 한 모든 호스팅 옵션입니다.
       * Preview 버전을 포함 한 모든 .NET Core 버전
   * Visual Studio에서 디버깅 하는 경우 원격 분석을 로컬로 볼 수 있습니다.
   * API를 `TrackXXX()` 사용 하 여 추가 사용자 지정 원격 분석을 추적할 수 있습니다.
   * 구성에 대 한 모든 권한을 가집니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>상태 모니터 같은 도구를 사용 하 여 Application Insights 모니터링을 사용 하도록 설정할 수 있나요?

아니요. [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) 및 [상태 모니터 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 는 현재 ASP.NET 4.x만 지원 합니다.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights ASP.NET Core 2.0 응용 프로그램에 대해 자동으로 사용 하도록 설정 되어 있습니까?

2\.0 `Microsoft.AspNetCore.All` 메타 패키지에는 Application Insights SDK (버전 2.1.0)가 포함 되어 있습니다. Visual Studio 디버거에서 응용 프로그램을 실행 하는 경우 Visual Studio는 Application Insights을 사용 하도록 설정 하 고 원격 분석을 IDE 자체에서 로컬로 표시 합니다. 계측 키가 지정 되지 않은 경우 원격 분석을 Application Insights 서비스로 보내지 못했습니다. 2\.0 앱의 경우에도 Application Insights을 사용 하도록 설정 하려면이 문서의 지침을 따르는 것이 좋습니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux에서 응용 프로그램을 실행 하는 경우 모든 기능이 지원 되나요?

예. SDK에 대 한 기능 지원은 모든 플랫폼에서 동일 하지만 다음과 같은 경우는 예외입니다.

* 성능 카운터는 Windows 에서만 지원 됩니다.
* `ServerTelemetryChannel` 가 기본적으로 사용 되는 경우에도 응용 프로그램이 Linux 또는 macos에서 실행 되는 경우, 네트워크 문제가 있는 경우 채널이 일시적으로 원격 분석을 유지 하기 위해 로컬 저장소 폴더를 자동으로 만들지 않습니다. 이러한 제한으로 인해 임시 네트워크 또는 서버 문제가 있으면 원격 분석이 손실 됩니다. 이 문제를 해결 하려면 채널에 대 한 로컬 폴더를 구성 합니다.

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

- [.Net Core 및 Visual Studio를 사용 하 여 Application Insights](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) 를 처음부터 구성 하려면이 외부 단계별 비디오를 확인 하세요.
- 이 외부 단계별 비디오를 확인 하 여 [.Net Core를 사용 하 여 Application Insights를 구성 하 고](https://youtu.be/ygGt84GDync) 처음부터 Visual Studio Code 합니다.

## <a name="next-steps"></a>다음 단계

* 사용자 [흐름을 탐색](../../azure-monitor/app/usage-flows.md) 하 여 사용자가 앱을 탐색 하는 방법을 파악 합니다.
* 예외가 throw 될 때 소스 코드 및 변수의 상태를 확인 하도록 [스냅숏 컬렉션을 구성](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 합니다.
* [API를 사용](../../azure-monitor/app/api-custom-events-metrics.md) 하 여 앱의 성능 및 사용 현황에 대 한 자세한 보기에 대 한 사용자 고유의 이벤트 및 메트릭을 보냅니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
* [ASP.NET Core 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
