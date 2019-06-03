---
title: Application Insights의 성능 카운터 | Microsoft Docs
description: Application Insights에서 시스템 및 사용자 지정 .NET 성능 카운터를 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: 0ec64a5ae412fb4a1900021fefcb7d9112b1b019
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255335"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights의 시스템 성능 카운터

Windows는 광범위한 [성능 카운터](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters)(예: CPU 점유율, 메모리, 디스크, 네트워크 사용량 등)를 제공합니다. 고유한 성능 카운터를 정의할 수도 있습니다. 성능 카운터 수집에는 온-프레미스 호스트 또는 가상 머신에 대 한 관리 액세스 권한이 있는 응용 프로그램은 IIS에서 실행 되는. 하지만 응용 프로그램을 Azure Web Apps에는 성능 카운터에 직접 액세스할 수 없는 실행에 사용 가능한 카운터의 하위 집합은 Application Insights에서 수집 합니다.

## <a name="view-counters"></a>카운터 보기

[메트릭] 창에는 기본 성능 카운터 세트가 표시됩니다.

![Application Insights에서 보고하는 시스템 성능 카운터](./media/performance-counters/performance-counters.png)

현재 기본 카운터 ASP.NET/ASP.NET Core 웹 응용 프로그램에 대 한 수집 하도록 구성 되는 다음과 같습니다.

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

## <a name="add-counters"></a>카운터 추가

원하는 성능 카운터가 메트릭 목록에 포함되지 않은 경우 추가할 수 있습니다.

1. 로컬 서버에서 다음 PowerShell 명령을 사용하여 서버에서 사용할 수 있는 카운터를 확인합니다.

    `Get-Counter -ListSet *`

    ([`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx) 참조)
2. ApplicationInsights.config를 엽니다.

   * 앱에 개발 중인 Application Insights를 추가한 경우 프로젝트에서 ApplicationInsights.config를 편집한 후 서버에 다시 배포합니다.
3. 다음과 같이 성능 수집기 지시문을 편집합니다.

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> ASP.NET Core 응용 프로그램 권한이 없습니다 `ApplicationInsights.config`, 및 위 메서드는 ASP.NET Core 응용 프로그램에 대 한 유효 하지 않습니다.

표준 카운터 및 사용자가 직접 구현한 카운터를 모두 캡처할 수 있습니다. `\Objects\Processes`는 모든 Windows 시스템에서 사용할 수 있는 표준 카운터의 한 예입니다. `\Sales(photo)\# Items Sold`는 웹 서비스에서 구현할 수 있는 사용자 지정 카운터의 한 예입니다.

형식은 `\Category(instance)\Counter"`이며, 인스턴스가 없는 범주인 경우에는 `\Category\Counter`입니다.

`ReportAs`는 `[a-zA-Z()/-_ \.]+`와 일치하지 않는 카운터 이름에 필요합니다. 즉 문자, 둥근 괄호, 슬래시, 하이픈, 밑줄, 공백, 점이 아닌 글자를 포함합니다.

인스턴스를 지정하면 보고된 메트릭의 "CounterInstanceName" 차원으로 수집됩니다.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>ASP.NET 웹 응용 프로그램 또는.NET/.NET Core 콘솔 응용 프로그램에 대 한 코드에서 성능 카운터를 수집합니다.
시스템 성능 카운터를 수집하고 Application Insights에 보내려면 다음과 같은 코드 조각을 사용할 수 있습니다.


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

또는 만든 사용자 지정 메트릭과 동일한 작업을 수행할 수 있습니다.

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>ASP.NET Core 웹 응용 프로그램에 대 한 코드에서 성능 카운터를 수집합니다.

수정할 `ConfigureServices` 의 메서드 여 `Startup.cs` 아래와 같이 클래스입니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>분석에서의 성능 카운터
[분석](../../azure-monitor/app/analytics.md)에서 성능 카운터 보고서를 검색하고 표시할 수 있습니다.

**performanceCounters** 스키마는 `category`, `counter` 이름 및 각 성능 카운터의 `instance` 이름을 노출합니다.  각 애플리케이션에 대한 원격 분석 데이터에는 해당 애플리케이션에 대한 카운터에만 표시됩니다. 예를 들면 어떤 카운터를 사용할 수 있는지 알아보기: 

![Application Insights 분석의 성능 카운터](./media/performance-counters/analytics-performance-counters.png)

(여기서 ‘인스턴스’는 역할이나 서버 머신 인스턴스가 아니라 성능 카운터 인스턴스를 나타냅니다. 성능 카운터 인스턴스 이름은 일반적으로 프로세스 또는 애플리케이션의 이름을 기준으로 프로세서 시간과 같은 카운터를 분할합니다.)

최근 기간 동안 사용 가능한 메모리의 차트를 가져오려면: 

![Application Insights 분석의 메모리 시간 차트](./media/performance-counters/analytics-available-memory.png)

다른 원격 분석과 마찬가지로 **performanceCounters**에도 앱이 실행되는 호스트 서버 인스턴스의 ID를 나타내는 `cloud_RoleInstance` 열이 있습니다. 예를 들어, 서로 다른 컴퓨터에서 앱의 성능을 비교하려면: 

![Application Insights 분석에서 역할 인스턴스에 의해 분할된 성능](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET 및 Application Insights 개수

*예외 속도와 예외 메트릭 간의 차이점은 무엇인가요?*

* *예외 속도* 는 시스템 성능 카운터입니다. CLR은 발생하는 처리된 예외 및 처리되지 않은 예외를 모두 계산하고 샘플링 간격의 합계를 간격 길이로 나눕니다. Application Insights SDK는 이 결과를 수집하여 포털에 보냅니다.

* *예외* 는 차트의 샘플링 간격에 포털이 받은 TrackException 보고서 개수입니다. 코드에서 TrackException 호출을 작성한 처리된 예외만 포함하며, [처리되지 않은 예외](../../azure-monitor/app/asp-net-exceptions.md)는 모두 포함되지 않습니다. 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Azure Web Apps에서 실행 중인 응용 프로그램에 대 한 성능 카운터

Azure Web Apps에 배포 하는 ASP.NET 및 ASP.NET Core 응용 프로그램은 특수 한 샌드박스 환경에서 실행 합니다. 이 환경은 시스템 성능 카운터에 대 한 직접 액세스를 허용 하지 않습니다. 설명 된 대로 카운터의 제한 된 하위 환경 변수로 노출 되는 반면 [여기](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)합니다. ASP.NET 및 ASP.NET Core 용 application Insights SDK는 이러한 특수 한 환경 변수에서 Azure Web Apps에서 성능 카운터를 수집합니다. 카운터의 하위 집합이이 환경에서 사용할 수 있으며 전체 목록을 찾을 수 있습니다만 [여기 있습니다.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램에서 성능 카운터

* [ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.4.1 응용 프로그램은 Azure 웹 앱 (Windows)에서 실행 중인 경우 위의 성능 카운터를 수집 하 고

* SDK 버전 2.7.0-beta3 응용 프로그램에서 Windows를 실행 하 고 대상으로 하는 경우 위의 성능 카운터를 수집 하 고 `NETSTANDARD2.0` 이상.
* .NET Framework를 대상으로 하는 응용 프로그램에 성능 카운터는 모든 버전의 SDK에서 지원 됩니다.
* 이 문서에서는 비 Windows에서 성능 카운터 지원을 추가 될 때 업데이트 됩니다.

## <a name="alerts"></a>경고
다른 메트릭과 마찬가지로 성능 카운터에서 지정한 제한을 벗어나는 경우 경고 메시지를 표시하도록 [경고를 설정](../../azure-monitor/app/alerts.md)할 수 있습니다. [경고] 창을 열고 [경고 추가]를 클릭합니다.

## <a name="next"></a>다음 단계

* [종속성 추적](../../azure-monitor/app/asp-net-dependencies.md)
* [예외 추적](../../azure-monitor/app/asp-net-exceptions.md)

