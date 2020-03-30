---
title: ASP.NET 핵심 응용 프로그램에 대한 Azure 응용 프로그램 인사이트 | 마이크로 소프트 문서
description: ASP.NET Core 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: d6a0e507022452f1491e71651ba3bc8db3d1c090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284792"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET 핵심 애플리케이션을 위한 애플리케이션 인사이트

이 문서에서는 [ASP.NET 핵심](https://docs.microsoft.com/aspnet/core) 응용 프로그램에 대한 응용 프로그램 인사이트를 사용하도록 설정하는 방법에 대해 설명합니다. 이 문서의 지침을 완료하면 Application Insights는 ASP.NET 핵심 응용 프로그램에서 요청, 종속성, 예외, 성능 카운터, 하트비트 및 로그를 수집합니다.

여기서 사용할 예제는 `netcoreapp2.2`대상MVC 응용 [프로그램입니다.](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) 이 지침은 모든 ASP.NET 핵심 응용 프로그램에 적용할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

[ASP.NET 코어용 애플리케이션 인사이트 SDK는](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 응용 프로그램이 실행되는 위치 나 방법에 관계없이 응용 프로그램을 모니터링할 수 있습니다. 응용 프로그램이 실행 중이고 Azure에 대한 네트워크 연결이 있는 경우 원격 분석을 수집할 수 있습니다. 응용 프로그램 인사이트 모니터링은 모든 곳에서 지원됩니다.NET 코어가 지원됩니다. 지원 커버:
* **운영 체제**: 윈도우, 리눅스, 또는 맥.
* **호스팅 방법**: 프로세스 또는 프로세스 중.
* **배포 방법**: 프레임워크 종속 또는 독립형입니다.
* **웹 서버**: IIS (인터넷 정보 서버) 또는 케스트렐.
* **호스팅 플랫폼**: Azure 앱 서비스, Azure VM, Docker, Azure Kubernetes 서비스(AKS) 등의 웹 앱 기능입니다.
* **.NET 코어 런타임 버전**: 1.XX, 2.XX 또는 3.XX
* **IDE**: 비주얼 스튜디오, VS 코드 또는 명령줄.

> [!NOTE]
> 응용 프로그램 인사이트와 함께 ASP.NET 코어 3.X를 사용하는 경우 [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) 버전 이상에서 사용하십시오. 이것은 ASP.NET 코어 3.X를 지원하는 유일한 버전입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 작동 ASP.NET 코어 응용 프로그램입니다. ASP.NET 코어 응용 프로그램을 만들어야 하는 경우 이 [ASP.NET 코어 자습서를](https://docs.microsoft.com/aspnet/core/getting-started/)따르십시오.
- 유효한 애플리케이션 인사이트 계측 키입니다. 이 키는 모든 원격 분석을 응용 프로그램 인사이트로 전송하는 데 필요합니다. 계측 키를 얻으려면 새 응용 프로그램 인사이트 리소스를 만들어야 하는 경우 [응용 프로그램 인사이트 만들기 리소스를](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)참조하십시오.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>애플리케이션 인사이트 서버 측 원격 분석(Visual Studio) 사용

1. Visual Studio에서 새 프로젝트를 엽니다.

    > [!TIP]
    > 원하는 경우 Application Insights에서 변경한 내용을 모두 추적할 수 있도록 프로젝트에 대한 소스 제어를 설정할 수 있습니다. 소스 제어를 사용하려면 **File** > 소스**컨트롤에 파일 추가 를**선택합니다.

2. 응용**프로그램 인사이트 원격 분석**추가 **프로젝트** > 선택.

3. **시작**을 선택합니다. 이 선택 항목의 텍스트는 Visual Studio 버전에 따라 다를 수 있습니다. 일부 이전 버전에서는 **무료 시작** 단추를 대신 사용합니다.

4. 구독을 선택합니다. 그런 다음 **리소스** > **레지스터를**선택합니다.

5. 프로젝트에 응용 프로그램 인사이트를 추가한 후 SDK의 최신 안정적인 릴리스를 사용하고 있는지 확인합니다. **프로젝트** > **관리 NuGet 패키지** > **Microsoft.ApplicationInsights.AspNetCore로**이동합니다. 필요한 경우 **업데이트를**선택합니다.

     ![업데이트할 응용 프로그램 인사이트 패키지를 선택할 위치를 보여주는 스크린샷](./media/asp-net-core/update-nuget-package.png)

6. 선택적 팁을 따르고 프로젝트를 소스 제어에 추가한 경우**팀 탐색기** > **변경** **보기로** > 이동합니다. 그런 다음 각 파일을 선택하여 Application Insights 원격 분석에서 변경한 내용을 대한 diff 보기를 확인합니다.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>애플리케이션 인사이트 서버 측 원격 분석 사용(Visual Studio 없음)

1. ASP.NET [코어에 대한 응용 프로그램 인사이트 SDK NuGet 패키지를](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)설치합니다. 항상 최신 안정 버전을 사용하는 것이 좋습니다. [오픈 소스 GitHub 리포지토리에서](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)SDK에 대한 전체 릴리스 메모를 찾을 수 있습니다.

    다음 코드 샘플에서는 프로젝트 `.csproj` 파일에 추가할 변경 내용을 보여 줍니다.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. 이 `services.AddApplicationInsightsTelemetry();` 예제와 같이 클래스의 `ConfigureServices()` 메서드에 추가합니다. `Startup`

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

3. 계측 키를 설정합니다.

    계측 키를 인수로 `AddApplicationInsightsTelemetry`제공할 수 있지만 구성에서 계측 키를 지정하는 것이 좋습니다. 다음 코드 샘플에서는 에서 계측 키를 지정하는 방법을 보여 주며 있습니다. `appsettings.json` 게시하는 `appsettings.json` 동안 응용 프로그램 루트 폴더에 복사되어 있는지 확인합니다.

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

    또는 다음 환경 변수 중 하나에 계측 키를 지정합니다.

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    예를 들어:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    일반적으로 `APPINSIGHTS_INSTRUMENTATIONKEY` Azure Web Apps에 배포된 응용 프로그램에 대한 계측 키를 지정합니다.

    > [!NOTE]
    > 코드에 지정된 계측 키는 환경 `APPINSIGHTS_INSTRUMENTATIONKEY`변수를 이긴다.

## <a name="run-your-application"></a>애플리케이션 실행

응용 프로그램을 실행하고 요청을 합니다. 이제 원격 분석이 응용 프로그램 인사이트로 이동합니다. 응용 프로그램 인사이트 SDK는 다음 원격 분석과 함께 응용 프로그램에 들어오는 웹 요청을 자동으로 수집합니다.

### <a name="live-metrics"></a>라이브 메트릭

[라이브 메트릭을](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) 사용하여 애플리케이션 인사이트 모니터링이 올바르게 구성되었는지 신속하게 확인할 수 있습니다. 원격 분석이 포털 및 분석에 나타나기 까지 몇 분 정도 걸릴 수 있지만 라이브 메트릭은 실행 중인 프로세스의 CPU 사용량을 거의 실시간으로 표시합니다. 또한 요청, 종속성, 추적 등과 같은 다른 원격 분석을 표시할 수도 있습니다.

### <a name="ilogger-logs"></a>ILogger 로그

심각도 `Warning` 또는 그 `ILogger` 이상을 통해 내보낸 로그는 자동으로 캡처됩니다. [ILogger 문서를](ilogger.md#control-logging-level) 따라 응용 프로그램 인사이트로 캡처되는 로그 수준을 사용자 지정합니다.

### <a name="dependencies"></a>종속성

종속성 컬렉션은 기본적으로 활성화되어 있습니다. [이](asp-net-dependencies.md#automatically-tracked-dependencies) 문서에서는 자동으로 수집되는 종속성을 설명하고 수동 추적을 수행하는 단계도 포함되어 있습니다.

### <a name="performance-counters"></a>성능 카운터

ASP.NET [코어의 성능 카운터에](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) 대한 지원은 제한되어 있습니다.

* SDK 버전 2.4.1 이후 응용 프로그램이 Azure 웹 앱(Windows)에서 실행 중인 경우 성능 카운터를 수집합니다.
* SDK 버전 2.7.1 이상 은 응용 프로그램이 Windows및 대상 `NETSTANDARD2.0` 에서 실행되는 경우 성능 카운터를 수집합니다.
* .NET Framework를 대상으로 하는 응용 프로그램의 경우 모든 버전의 SDK는 성능 카운터를 지원합니다.
* SDK 버전 2.8.0 이상 리눅스에서 CPU / 메모리 카운터를 지원합니다. 다른 카운터는 Linux에서 지원되지 않습니다. Linux(및 기타 Windows 이외의 환경)에서 시스템 카운터를 얻는 데 권장되는 방법은 [EventCounters를](#eventcounter) 사용하는 것입니다.

### <a name="eventcounter"></a>이벤트 카운터

`EventCounterCollectionModule`은 기본적으로 활성화되어 있으며 .NET Core 3.X 앱에서 기본 카운터 집합을 수집합니다. [EventCounter](eventcounters.md) 자습서에는 수집된 기본 카운터 집합이 나열됩니다. 또한 목록을 사용자 지정하는 방법에 대한 지침도 있습니다.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 응용 프로그램에 대한 클라이언트 측 원격 분석 사용

위의 단계는 서버 측 원격 분석 수집을 시작하는 데 충분합니다. 응용 프로그램에 클라이언트 쪽 구성 요소가 있는 경우 다음 단계를 수행하여 [사용 원격 분석](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)수집을 시작합니다.

1. 에서 `_ViewImports.cshtml`삽입 을 추가합니다.

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 에서 `_Layout.cshtml`섹션의 `HtmlHelper` `<head>` 끝에 있지만 다른 스크립트 앞에 삽입합니다. 페이지에서 사용자 지정 JavaScript 원격 분석을 보고하려면 다음 코드 조각 후에 삽입합니다.

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
또는 SDK `FullScript` `ScriptBody` v2.14에서 부터 사용할 수 있는 것을 사용하는 것입니다. 콘텐츠 보안 정책을 설정하기 `<script>` 위해 태그를 제어해야 하는 경우 다음을 사용합니다.

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

이전에 `.cshtml` 참조된 파일 이름은 기본 MVC 응용 프로그램 템플릿에서 가져옵니다. 궁극적으로 응용 프로그램에 대한 클라이언트 쪽 모니터링을 올바르게 사용하려면 모니터링하려는 응용 프로그램의 `<head>` 각 페이지의 섹션에 JavaScript 스니펫이 표시되어야 합니다. 에 JavaScript 스니펫을 추가하여 이 응용 프로그램 `_Layout.cshtml`템플릿에 대해 이 목표를 달성할 수 있습니다. 

프로젝트에 을 포함하지 `_Layout.cshtml`않는 경우에도 클라이언트 [측 모니터링을](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)추가할 수 있습니다. 앱 내의 모든 페이지를 제어하는 동등한 파일에 JavaScript 스니펫을 `<head>` 추가하여 이 작업을 수행할 수 있습니다. 또는 여러 페이지에 스니펫을 추가할 수 있지만 이 솔루션은 유지 관리가 어렵고 일반적으로 권장하지 않습니다.

## <a name="configure-the-application-insights-sdk"></a>애플리케이션 인사이트 SDK 구성

ASP.NET 코어에 대한 응용 프로그램 인사이트 SDK를 사용자 지정하여 기본 구성을 변경할 수 있습니다. 응용 프로그램 인사이트 ASP.NET SDK 사용자는 `ApplicationInsights.config` `TelemetryConfiguration.Active`을 사용하거나 수정하여 구성을 변경하는 데 익숙할 수 있습니다. ASP.NET 코어에 대해 구성을 다르게 변경합니다. 응용 프로그램에 ASP.NET 코어 SDK를 추가하고 ASP.NET 코어 기본 [제공 종속성 주입을](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)사용하여 구성합니다. 달리 지시되지 않는 `ConfigureServices()` 한 클래스의 메서드에서 거의 모든 구성을 변경합니다. `Startup.cs` 다음 섹션에서는 자세한 정보를 제공합니다.

> [!NOTE]
> ASP.NET Core 응용 프로그램에서는 수정을 `TelemetryConfiguration.Active` 통해 구성을 변경하는 것이 지원되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>응용 프로그램 인사이트서비스 옵션 사용

이 예제에서와 같이 에 `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`전달하여 몇 가지 일반적인 설정을 수정할 수 있습니다.

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

에서 설정의 전체 목록`ApplicationInsightsServiceOptions`

|설정 | 설명 | 기본값
|---------------|-------|-------
|인에이블퍼포먼스카운터컬렉션모듈  | 사용/사용 안 함`PerformanceCounterCollectionModule` | true
|인에이블요청추적텔레메트리모듈   | 사용/사용 안 함`RequestTrackingTelemetryModule` | true
|인에이블이벤트카운터컬렉션모듈   | 사용/사용 안 함`EventCounterCollectionModule` | true
|인에이블리퍼실렌디시트래킹텔레메트리모듈   | 사용/사용 안 함`DependencyTrackingTelemetryModule` | true
|인에이블앱서비스하트비트텔레메트리모듈  |  사용/사용 안 함`AppServicesHeartbeatTelemetryModule` | true
|인에이블Azure인스턴스메타메타메타텔레메트리모듈   |  사용/사용 안 함`AzureInstanceMetadataTelemetryModule` | true
|인에이블퀵펄스메트릭스트림 | 라이브메트릭 사용/비활성화 기능 | true
|인에이블적응샘플링 | 어댑티브 샘플링 사용/비활성화 | true
|하트비트 사용 | 주기적으로 (15분 기본값) 하트비트 활성화/비활성화 기능으로 .NET 버전, Azure 환경 정보(해당하는 경우) 등과 같은 런타임에 대한 정보가 포함된 'HeartBeatState'라는 사용자 지정 메트릭을 보냅니다. | true
|추가 자동 수집메트릭 추출기 | 자동 수집메트릭 제거 추출기는 샘플링이 수행되기 전에 요청/종속성에 대한 사전 집계된 메트릭을 보내는 원격 측정 프로세서입니다. | true
|요청컬렉션옵션.트랙예외 | 요청 수집 모듈에서 처리되지 않은 예외 추적의 보고 활성화/비활성화 | NETSTANDARD2.0에서 false(예외는 응용 프로그램인사이트로추적하기 때문에공급자)는 그렇지 않으면 true입니다.

최신 목록에 대한 [구성 `ApplicationInsightsServiceOptions` 가능한 설정을](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) 참조하십시오.

### <a name="sampling"></a>샘플링

ASP.NET 코어용 애플리케이션 인사이트 SDK는 고정 속도 및 적응형 샘플링을 모두 지원합니다. 적응 형 샘플링은 기본적으로 활성화됩니다. 

자세한 내용은 [ASP.NET 핵심 응용 프로그램에 대한 적응 샘플링 구성을](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)참조하십시오.

### <a name="adding-telemetryinitializers"></a>원격 측정 초기화추가

모든 원격 분석과 함께 전송되는 전역 속성을 정의하려는 경우 원격 [분석 초기화자를](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) 사용합니다.

다음 코드와 `DependencyInjection` 같이 컨테이너에 새 `TelemetryInitializer` 새 를 추가합니다. SDK는 컨테이너에 추가된 모든 `TelemetryInitializer` 것을 `DependencyInjection` 자동으로 선택합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>원격 분석 초기라이저 제거

원격 분석 초기화자는 기본적으로 존재합니다. 모든 또는 특정 원격 분석 초기화자를 제거하려면 *after* 다음 샘플 `AddApplicationInsightsTelemetry()`코드를 호출한 후 사용합니다.

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

에서 확장 메서드를 `TelemetryConfiguration` `AddApplicationInsightsTelemetryProcessor` 사용하여 사용자 지정 원격 분석 프로세서를 추가할 수 있습니다. `IServiceCollection` [고급 필터링 시나리오에서](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)원격 분석 프로세서를 사용합니다. 다음 예제를 사용합니다.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 원격 분석 모듈 구성 또는 제거

Application Insights는 원격 분석 모듈을 사용하여 사용자가 수동으로 추적할 필요 없이 특정 워크로드에 대한 유용한 원격 분석을 자동으로 수집합니다.

다음 자동 수집 모듈은 기본적으로 활성화되어 있습니다. 이러한 모듈은 원격 분석을 자동으로 수집합니다. 기본 동작을 변경하도록 사용하지 않도록 설정하거나 구성할 수 있습니다.

* `RequestTrackingTelemetryModule`- 들어오는 웹 요청에서 RequestTelemetry를 수집합니다.
* `DependencyTrackingTelemetryModule`- 나가는 http 호출 및 SQL 호출에서 종속성 텔레메트리를 수집합니다.
* `PerformanceCollectorModule`- 윈도우 성능 카운터를 수집합니다.
* `QuickPulseTelemetryModule`- 라이브 메트릭 포털에 표시하기위한 원격 분석을 수집합니다.
* `AppServicesHeartbeatTelemetryModule`- 응용 프로그램이 호스팅되는 Azure App Service 환경에 대해 하트 비트(사용자 지정 메트릭으로 전송됨)를 수집합니다.
* `AzureInstanceMetadataTelemetryModule`- 응용 프로그램이 호스팅되는 Azure VM 환경에 대해 하트 비트(사용자 지정 메트릭으로 전송됨)를 수집합니다.
* `EventCounterCollectionModule`- [이벤트 카운터를 수집합니다.](eventcounters.md) 이 모듈은 새로운 기능이며 SDK 버전 2.8.0 이상에서 사용할 수 있습니다.

기본값을 `TelemetryModule`구성하려면 다음 예제와 `IServiceCollection`같이 에서 확장 메서드를 `ConfigureTelemetryModule<T>` 사용합니다.

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

2.12.2 버전으로 시작하여 [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) 기본 모듈중 어느 쪽도 사용하지 않도록 설정하는 쉬운 옵션이 포함되어 있습니다.

### <a name="configuring-a-telemetry-channel"></a>원격 분석 채널 구성

기본 채널은 `ServerTelemetryChannel`. 다음 예제에서 볼 수 있듯이 재정의할 수 있습니다.

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

### <a name="disable-telemetry-dynamically"></a>원격 분석을 동적으로 사용하지 않도록 설정

원격 분석을 조건부 및 동적으로 사용하지 않도록 설정하려면 코드의 아무 곳이나 ASP.NET Core 종속성 주입 컨테이너를 사용하여 인스턴스를 해결하고 `TelemetryConfiguration` 플래그를 설정할 `DisableTelemetry` 수 있습니다.

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

위의 경우 자동 수집 모듈이 원격 분석을 수집하는 것을 방지하지 는 않습니다. 위의 방법을 사용하여 원격 분석을 응용 프로그램 인사이트로 보내는 경우에만 사용할 수 없습니다. 특정 자동 수집 모듈을 사용하지 않는 경우 [원격 분석 모듈을 제거하는](#configuring-or-removing-default-telemetrymodules) 것이 가장 좋습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-application-insights-support-aspnet-core-3x"></a>애플리케이션 인사이트가 코어 3.X를 ASP.NET 지원합니까?

예. ASP.NET 코어 버전 2.8.0 [이상에 대한 응용 프로그램 인사이트 SDK로](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 업데이트합니다. 이전 버전의 SDK는 ASP.NET 코어 3.X를 지원하지 않습니다.

또한 [여기에서](#enable-application-insights-server-side-telemetry-visual-studio)Visual Studio 기반 지침을 사용하는 경우 최신 버전의 Visual Studio 2019(16.3.0)를 온보드로 업데이트합니다. 이전 버전의 Visual Studio에서는 ASP.NET Core 3.X 앱에 대한 자동 온보딩을 지원하지 않습니다.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집되지 않은 원격 분석을 추적하면 어떻게 해야 합니까?

생성자 주입을 사용하여 인스턴스를 `TelemetryClient` 얻고 필요한 `TrackXXX()` 메서드를 호출합니다. ASP.NET Core 응용 프로그램에서 `TelemetryClient` 새 인스턴스를 만드는 것은 권장되지 않습니다. 단일 `TelemetryClient` 인스턴스는 이미 `DependencyInjection` 컨테이너에 등록되어 있으며 `TelemetryConfiguration` 나머지 원격 분석과 공유합니다. 새 `TelemetryClient` 인스턴스를 만드는 것은 나머지 원격 분석과 는 별개의 구성이 필요한 경우에만 권장됩니다.

다음 예제에서는 컨트롤러에서 추가 원격 분석을 추적하는 방법을 보여 주었습니다.

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

애플리케이션 인사이트 사용자 지정 데이터 보고에 대한 자세한 내용은 [Application Insights 사용자 지정 메트릭 API 참조를](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)참조하십시오.

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>일부 Visual Studio 템플릿은 IWebHostBuilder에서 UseApplicationInsights() 확장 메서드를 사용하여 응용 프로그램 인사이트를 활성화했습니다. 이 사용법은 여전히 유효합니까?

확장 메서드는 `UseApplicationInsights()` 계속 지원되지만 응용 프로그램 인사이트 SDK 버전 2.8.0 이후에서는 더 이상 사용되지 않는 것으로 표시됩니다. SDK의 다음 주요 버전에서 제거됩니다. 응용 프로그램 인사이트 원격 분석을 사용하도록 `AddApplicationInsightsTelemetry()` 설정하는 권장 방법은 일부 구성을 제어하는 오버로드를 제공하기 때문에 사용하는 것입니다. 또한 ASP.NET Core 3.X `services.AddApplicationInsightsTelemetry()` 앱에서 응용 프로그램 통찰력을 활성화하는 유일한 방법입니다.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET 핵심 응용 프로그램을 웹 앱에 배포하고 있습니다. 웹 앱에서 응용 프로그램 인사이트 확장을 계속 사용하도록 설정해야 합니까?

이 문서에 표시된 대로 빌드 시간에 SDK가 설치된 경우 앱 서비스 포털에서 [응용 프로그램 인사이트 확장을](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) 사용하도록 설정할 필요가 없습니다. 확장이 설치되어 있더라도 SDK가 응용 프로그램에 이미 추가된 것을 감지하면 다시 꺼집니다. 확장 프로그램에서 응용 프로그램 인사이트를 사용하도록 설정하면 SDK를 설치하고 업데이트할 필요가 없습니다. 그러나 이 문서의 지침에 따라 응용 프로그램 인사이트를 사용하도록 설정하면 다음과 같은 이유로 더 많은 유연성을 얻을 수 있습니다.

   * 응용 프로그램 인사이트 원격 분석에서 계속 작동합니다.
       * 윈도우, 리눅스 및 Mac을 포함한 모든 운영 체제.
       * 독립형 또는 프레임워크를 포함한 모든 게시 모드가 종속됩니다.
       * 전체 .NET 프레임워크를 포함한 모든 대상 프레임워크입니다.
       * 웹 앱, VM, Linux, 컨테이너, Azure Kubernetes 서비스 및 비 Azure 호스팅을 포함한 모든 호스팅 옵션입니다.
       * 미리 보기 버전을 포함한 모든 .NET 코어 버전.
   * Visual Studio에서 디버깅할 때 로컬로 원격 분석을 볼 수 있습니다.
   * API를 사용하여 추가 사용자 지정 `TrackXXX()` 원격 분석을 추적할 수 있습니다.
   * 구성을 완전히 제어할 수 있습니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>상태 모니터와 같은 도구를 사용하여 애플리케이션 인사이트 모니터링을 활성화할 수 있습니까?

아니요. [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) 및 [상태 모니터 v2는](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 현재 4.x ASP.NET 지원합니다.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>ASP.NET 코어 2.0 애플리케이션에 애플리케이션 인사이트가 자동으로 활성화되어 있습니까?

`Microsoft.AspNetCore.All` 2.0 메타패키지에는 애플리케이션 인사이트 SDK(버전 2.1.0)가 포함되어 있습니다. Visual Studio 디버거에서 응용 프로그램을 실행하는 경우 Visual Studio는 응용 프로그램 통찰력을 활성화하고 IDE 자체에서 로컬원격 분석을 표시합니다. 계측 키가 지정되지 않은 경우 원격 분석이 응용 프로그램 인사이트 서비스로 전송되지 않았습니다. 2.0 앱에 대해서도 응용 프로그램 인사이트를 사용하도록 설정하려면 이 문서의 지침을 따르는 것이 좋습니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux에서 응용 프로그램을 실행하는 경우 모든 기능이 지원되나요?

예. SDK에 대한 기능 지원은 모든 플랫폼에서 동일하지만 다음과 같은 예외는 다음과 같습니다.

* 성능 [카운터는](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) Windows에서만 지원되므로 SDK는 Linux에서 이벤트 [카운터를 수집합니다.](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) 대부분의 메트릭은 동일합니다.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>이 SDK는 새로운 .NET Core 3.X 작업자 서비스 템플릿 응용 프로그램에 지원합니까?

이 SDK에는 이 SDK가 필요하며 `HttpContext`.NET Core 3.X 작업자 서비스 응용 프로그램을 비롯한 HTTP 이외의 응용 프로그램에서는 작동하지 않습니다. 새로 릴리스된 Microsoft.ApplicationInsights.WorkerService SDK를 사용하여 이러한 응용 프로그램에서 응용 프로그램 통찰력을 활성화하려면 [이](worker-service.md) 문서를 참조하십시오.

## <a name="open-source-sdk"></a>오픈 소스 SDK

[코드를 읽고 기여합니다.](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates)

## <a name="video"></a>비디오

- 이 외부 단계별 비디오를 확인하여 [.NET Core 및 Visual Studio를](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) 사용하여 응용 프로그램 인사이트를 처음부터 구성합니다.
- 이 외부 단계별 비디오를 확인하여 .NET Core 및 Visual Studio 코드를 처음부터 [사용하여 응용 프로그램 인사이트를 구성합니다.](https://youtu.be/ygGt84GDync)

## <a name="next-steps"></a>다음 단계

* [사용자 흐름을 탐색하여](../../azure-monitor/app/usage-flows.md) 사용자가 앱을 탐색하는 방법을 이해합니다.
* [스냅샷 컬렉션을 구성하여](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 예외가 throw되는 순간 소스 코드 및 변수의 상태를 확인합니다.
* [API를 사용하여](../../azure-monitor/app/api-custom-events-metrics.md) 앱의 성능 및 사용량에 대한 자세한 보기를 위해 고유한 이벤트 및 메트릭을 보냅니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
* [ASP.NET 코어의 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
