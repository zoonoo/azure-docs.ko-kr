---
title: Application Insights에서 .NET 추적 로그 탐색
description: 추적, NLog 또는 Log4Net에서 생성된 검색 로그입니다.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276271"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>응용 프로그램 인사이트에서 .NET/.NET 코어 및 파이썬 추적 로그 살펴보기

ILogger, NLog, log4Net 또는 System.Diagnostics.Trace에서 ASP.NET/ASP.NET 핵심 응용 프로그램에 대한 진단 추적 로그를 [Azure 응용 프로그램 인사이트로][start]보냅니다. 파이썬 응용 프로그램의 경우 Azure 모니터용 OpenCensus 파이썬에서 AzureLogHandler를 사용하여 진단 추적 로그를 보냅니다. 그런 다음 탐색하고 검색할 수 있습니다. 이러한 로그는 응용 프로그램의 다른 로그 파일과 병합되므로 각 사용자 요청과 연결된 추적을 식별하고 다른 이벤트 및 예외 보고서와 상호 연관시킬 수 있습니다.

> [!NOTE]
> 로그 캡처 모듈이 필요하십니까? 타사 로거에 유용한 어댑터입니다. 그러나 NLog, log4Net 또는 System.Diagnostics.Trace를 아직 사용하지 않는 경우 [**응용 프로그램 인사이트 TrackTrace()를**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 직접 호출하는 것이 좋습니다.
>
>
## <a name="install-logging-on-your-app"></a>앱에 대한 로깅 설치
프로젝트에 선택한 로깅 프레임워크를 설치하면 app.config 또는 web.config에 항목이 표시됩니다.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>로그를 수집하도록 Application Insights 구성
[프로젝트에 Application Insights를 추가](../../azure-monitor/app/asp-net.md)하지 않은 경우 지금 추가합니다. 로그 수집기를 포함하는 옵션이 나타납니다.

또는 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하여 **응용 프로그램 인사이트를 구성합니다.** 추적 **수집 구성** 옵션을 선택합니다.

> [!NOTE]
> Application Insights 메뉴 또는 로그 수집기 옵션이 없습니까? [문제 해결](#troubleshooting)을 시도해 보세요.

## <a name="manual-installation"></a>수동 설치
프로젝트 형식이 Application Insights 설치 관리자에서 지원되지 않는 경우(예: Windows 데스크톱 프로젝트) 이 방법을 사용합니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다.
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리를 선택합니다.**
3. "응용 프로그램 인사이트"를 검색합니다.
4. 다음 패키지 중 하나를 선택합니다.

   - ILogger에 대 한: [마이크로소프트.확장.로깅.응용 프로그램 통찰력](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog의 경우: [마이크로소프트.애플리케이션인사이트.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net용: [마이크로소프트.애플리케이션인사이트.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 시스템.진단: [마이크로소프트.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [마이크로소프트.애플리케이션인사이트.진단소스인리터](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![누겟](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [마이크로소프트.어플리케이션인사이트.에트컬렉터](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![누겟](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [마이크로소프트.애플리케이션인사이트.이벤트소스인리터](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![누겟](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 패키지는 필요한 어셈블리를 설치하고 해당하는 경우 web.config 또는 app.config를 수정합니다.

## <a name="ilogger"></a>ILogger

콘솔 응용 프로그램 및 ASP.NET 코어와 함께 응용 프로그램 인사이트 ILogger 구현을 사용하는 예는 [.NET 코어 ILogger 로그에 대한 ApplicationInsightsLogger 공급자를](ilogger.md)참조하십시오.

## <a name="insert-diagnostic-log-calls"></a>진단 로그 호출 삽입
System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net 또는 NLog를 선호하는 경우 다음을 사용하십시오.

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>이벤트 소스 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트를 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 패키지를 설치합니다. 그런 다음 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일의 `TelemetryModules` 섹션을 편집합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **이름은** 수집할 EventSource의 이름을 지정합니다.
 * **수준은** 수집할 로깅 수준을 지정합니다: *중요,* *오류,* *정보,* *LogAlways,* *자세한*내용, 또는 *경고*.
 * **키워드(선택 사항)는** 사용할 키워드 조합의 정수 값을 지정합니다.

## <a name="use-diagnosticsource-events"></a>진단 소스 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 이벤트를 구성할 수 있습니다. 먼저 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 패키지를 설치합니다. 그런 다음 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일의 "원격 분석 모듈" 섹션을 편집합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

추적하려는 각 DiagnosticSource에 대해 DiagnosticSource의 이름에 **Set의 Name** 특성이 있는 항목을 추가합니다.

## <a name="use-etw-events"></a>ETW 이벤트 사용
ETW(Windows용 이벤트 추적) 이벤트를 응용 프로그램 인사이트로 추적으로 전송하도록 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EtwCollector` NuGet 패키지를 설치합니다. 그런 다음 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일의 "원격 분석 모듈" 섹션을 편집합니다.

> [!NOTE] 
> ETW 이벤트는 SDK를 호스팅하는 프로세스가 성능 로그 사용자 또는 관리자의 구성원인 ID로 실행되는 경우에만 수집할 수 있습니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **공급자이름은** 수집할 ETW 공급자의 이름입니다.
 * **ProviderGuid는** 수집할 ETW 공급자의 GUID를 지정합니다. 대신 사용할 수 `ProviderName`있습니다.
 * **수준은** 수집할 로깅 수준을 설정합니다. *중요*, *오류*, *정보*, *LogAlways,* *자세한*내용 또는 *경고일*수 있습니다.
 * **키워드(선택 사항)는** 사용할 키워드 조합의 정수 값을 설정합니다.

## <a name="use-the-trace-api-directly"></a>추적 API를 직접 사용
Application Insights 추적 API를 직접 호출할 수 있습니다. 로깅 어댑터는 이 API를 사용합니다.

예를 들어:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.

메시지에 심각도 수준을 추가할 수도 있습니다. 또한 다른 원격 분석과 마찬가지로 속성 값을 추가하여 다양한 추적 집합을 필터링하거나 검색할 수 있습니다. 예를 들어:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

이렇게 하면 특정 데이터베이스와 관련된 특정 심각도 수준의 모든 메시지를 [검색에서][diagnostic] 쉽게 필터링할 수 있습니다.

## <a name="azureloghandler-for-opencensus-python"></a>오픈인구파이썬용 AzureLogHandler
Azure 모니터 로그 처리기를 사용하면 파이썬 로그를 Azure 모니터로 내보낼 수 있습니다.

Azure 모니터에 대한 [OpenCensus 파이썬 SDK로](../../azure-monitor/app/opencensus-python.md) 응용 프로그램을 계측합니다.

이 예제에서는 Azure Monitor에 경고 수준 로그를 보내는 방법을 보여 주며 있습니다.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>로그 탐색
디버그 모드에서 앱을 실행하거나 라이브로 배포합니다.

[응용 프로그램 인사이트 포털의][portal]앱 개요 창에서 [검색을][diagnostic]선택합니다.

예를 들어 다음을 수행할 수 있습니다.

* 로그 추적 또는 특정 속성이 있는 항목을 필터링합니다.
* 특정 항목을 자세히 검사합니다.
* 동일한 사용자 요청과 관련된 다른 시스템 로그 데이터를 찾습니다(OperationId가 동일).
* 페이지의 구성을 즐겨찾기로 저장합니다.

> [!NOTE]
>응용 프로그램에서 많은 데이터를 보내고 ASP.NET 버전 2.0.0-beta3 이상에 응용 프로그램 인사이트 SDK를 사용하는 경우 *적응 샘플링* 기능이 작동하고 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>문제 해결
### <a name="how-do-i-do-this-for-java"></a>Java의 경우 이 작업을 어떻게 수행하나요?
[Java 로그 어댑터](../../azure-monitor/app/java-trace-logs.md)를 사용합니다.

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>프로젝트 상황에 맞는 메뉴에 Application Insights 옵션이 없습니다.
* 개발자 분석 도구가 개발 컴퓨터에 설치되어 있는지 확인합니다. Visual Studio **도구** > **확장 및 업데이트에서** **개발자 분석 도구를**찾습니다. **설치된** 탭에 없는 경우 **온라인** 탭을 열고 설치합니다.
* 데플로퍼 애널리틱스 도구가 지원하지 않는 프로젝트 유형일 수 있습니다. [수동 설치](#manual-installation)를 사용합니다.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>구성 도구에는 로그 어댑터 옵션이 없습니다.
* 로깅 프레임워크를 먼저 설치합니다.
* System.Diagnostics.Trace를 사용하는 경우 [ *web.config*에서 구성했는지](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)확인합니다.
* 최신 버전의 응용 프로그램 인사이트가 있는지 확인합니다. Visual Studio에서 **도구** > **확장 및 업데이트로**이동하여 **업데이트** 탭을 엽니다. **개발자 분석 도구가** 있는 경우 이를 선택하여 업데이트합니다.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>"계측 키를 비실 수 없습니다" 오류 메시지가 표시됩니다.
응용 프로그램 인사이트를 설치하지 않고 로깅 어댑터 Nuget 패키지를 설치했을 수 있습니다. 솔루션 탐색기에서 *ApplicationInsights.config를*마우스 오른쪽 단추로 클릭하고 **응용 프로그램 인사이트 업데이트를**선택합니다. Azure에 로그인하여 응용 프로그램 인사이트 리소스를 만들거나 기존 리소스를 다시 사용하라는 메시지가 표시됩니다. 즉, 문제를 해결해야합니다.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>진단 검색에서 추적을 볼 수 있지만 다른 이벤트는 볼 수 없습니다.
모든 이벤트 및 요청이 파이프라인을 통과하는 데 시간이 걸릴 수 있습니다.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>얼마나 많은 데이터가 보존되나요?
보존되는 데이터의 양에는 여러 가지 요인이 영향을 미칩니다. 자세한 내용은 고객 이벤트 측정항목 페이지의 [제한](../../azure-monitor/app/api-custom-events-metrics.md#limits) 섹션을 참조하세요.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>예상한 로그 항목이 표시되지 않습니다.
응용 프로그램에서 방대한 양의 데이터를 보내고 ASP.NET 버전 2.0.0-beta3 이상에 응용 프로그램 인사이트 SDK를 사용하는 경우 적응 샘플링 기능이 작동하고 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>다음 단계

* [ASP.NET의 실패 및 예외 진단][exceptions]
* [검색에 대해 자세히 알아보기][diagnostic]
* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
