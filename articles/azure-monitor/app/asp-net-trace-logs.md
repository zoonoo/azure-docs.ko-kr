---
title: Application Insights에서 .NET 추적 로그 탐색
description: 추적, NLog 또는 Log4Net에서 생성 된 로그를 검색 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/08/2019
ms.openlocfilehash: 352e31e2a2f1a88a33e82134460e6df0911dbd2e
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677640"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Application Insights에서 .NET/.NET Core 및 Python 추적 로그 살펴보기

ILogger, NLog, log4Net 또는 ASP.NET/ASP.NET Core 응용 프로그램에 대 한 진단 추적 로그를 [Azure 애플리케이션 Insights][start]에 보냅니다. Python 응용 프로그램의 경우 Azure Monitor OpenCensus Python에서 AzureLogHandler를 사용 하 여 진단 추적 로그를 보냅니다. 그런 다음 탐색 하 고 검색할 수 있습니다. 이러한 로그는 응용 프로그램의 다른 로그 파일과 병합 되므로 각 사용자 요청에 연결 된 추적을 식별 하 고 다른 이벤트 및 예외 보고서와 상호 연결할 수 있습니다.

> [!NOTE]
> 로그 캡처 모듈이 필요 한가요? 타사로 거에 대 한 유용한 어댑터입니다. 그러나 NLog, log4Net 또는 System.object를 아직 사용 하지 않는 경우에는 [**Application Insights 기능 추적 ()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 을 직접 호출 하는 것이 좋습니다.
>
>
## <a name="install-logging-on-your-app"></a>앱에 대한 로깅 설치
프로젝트에 선택한 로깅 프레임 워크를 설치 합니다. 그러면 app.config 또는 web.config에 항목이 생성 됩니다.

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
아직 수행 하지 않은 경우 [프로젝트에 Application Insights를 추가](../../azure-monitor/app/asp-net.md) 합니다. 로그 수집기를 포함하는 옵션이 나타납니다.

또는 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 **Application Insights를 구성**합니다. **추적 컬렉션 구성** 옵션을 선택 합니다.

> [!NOTE]
> Application Insights 메뉴 또는 로그 수집기 옵션이 있나요? [문제 해결](#troubleshooting)을 시도해 보세요.

## <a name="manual-installation"></a>수동 설치
프로젝트 형식이 Application Insights 설치 관리자에서 지원되지 않는 경우(예: Windows 데스크톱 프로젝트) 이 방법을 사용합니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다.
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **NuGet 패키지 관리**를 선택 합니다.
3. "Application Insights"를 검색 합니다.
4. 다음 패키지 중 하나를 선택합니다.

   - ILogger의 경우 [:](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) [![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)

   - NLog: [NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net: [Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - [TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 의 경우.
   - [DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 패키지는 필요한 어셈블리를 설치 하 고 web.config 또는 app.config를 수정 합니다 (해당 하는 경우).

## <a name="ilogger"></a>ILogger

콘솔 응용 프로그램 및 ASP.NET Core에서 Application Insights ILogger 구현을 사용 하는 예제는 [ApplicationInsightsLoggerProvider for .Net Core ILogger logs](ilogger.md)를 참조 하십시오.

## <a name="insert-diagnostic-log-calls"></a>진단 로그 호출 삽입
System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net 또는 NLog를 선호 하는 경우 다음을 사용 합니다.

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>EventSource 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트를 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 패키지를 설치합니다. 그런 다음 `TelemetryModules`ApplicationInsights.config[ 파일의 ](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 섹션을 편집합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **이름** 수집할 EventSource의 이름을 지정 합니다.
 * **수준** 은 수집할 로깅 수준을 지정 합니다. *중요*, *오류*, *정보*, *logalways*, *Verbose*또는 *Warning*을 지정 합니다.
 * **키워드** (옵션) 사용할 키워드 조합의 정수 값을 지정 합니다.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 이벤트를 구성할 수 있습니다. 먼저 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 패키지를 설치합니다. 그런 다음 [Applicationinsights .config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일의 "TelemetryModules" 섹션을 편집 합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

추적 하려는 각 DiagnosticSource에 대해 **name** 특성이 설정 된 항목을 DiagnosticSource 이름으로 추가 합니다.

## <a name="use-etw-events"></a>ETW 이벤트 사용
ETW(Windows용 이벤트 추적) (ETW) 이벤트를 Application Insights 추적으로 전송 되도록 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EtwCollector` NuGet 패키지를 설치합니다. 그런 다음 [Applicationinsights .config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일의 "TelemetryModules" 섹션을 편집 합니다.

> [!NOTE] 
> SDK를 호스트 하는 프로세스가 성능 로그 사용자 또는 관리자의 구성원 인 id에서 실행 되는 경우에만 ETW 이벤트를 수집할 수 있습니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **ProviderName** 은 수집할 ETW 공급자의 이름입니다.
 * **ProviderGuid** 은 수집할 ETW 공급자의 GUID를 지정 합니다. `ProviderName`대신 사용할 수 있습니다.
 * **수준** 수집할 로깅 수준을 설정 합니다. *중요*, *오류*, *정보*, *logalways*, *Verbose*또는 *Warning*일 수 있습니다.
 * **키워드** (옵션) 사용할 키워드 조합의 정수 값을 설정 합니다.

## <a name="use-the-trace-api-directly"></a>추적 API 직접 사용
Application Insights 추적 API를 직접 호출할 수 있습니다. 로깅 어댑터는 이 API를 사용합니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.

메시지에 심각도 수준을 추가할 수도 있습니다. 다른 원격 분석과 마찬가지로, 다른 추적 집합을 필터링 하거나 검색 하는 데 도움이 되는 속성 값을 추가할 수 있습니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

이렇게 하면 특정 데이터베이스와 관련 된 특정 심각도 수준의 모든 메시지를 [검색][diagnostic] 에서 쉽게 필터링 할 수 있습니다.

## <a name="azureloghandler-for-opencensus-python"></a>OpenCensus Python 용 AzureLogHandler
Azure Monitor 로그 처리기를 사용 하 여 Python 로그를 Azure Monitor으로 내보낼 수 있습니다.

Azure Monitor 용 [OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md) 를 사용 하 여 응용 프로그램을 계측 합니다.

이 예에서는 Azure Monitor에 경고 수준 로그를 보내는 방법을 보여 줍니다.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>로그 탐색
디버그 모드에서 앱을 실행 하거나 라이브로 배포 합니다.

[Application Insights 포털][portal]의 앱 개요 창에서 [검색][diagnostic]을 선택 합니다.

예를 들어 다음을 수행할 수 있습니다.

* 로그 추적 또는 특정 속성을 가진 항목에 대해 필터링 합니다.
* 특정 항목을 자세히 검사합니다.
* 동일한 사용자 요청에 관련 된 다른 시스템 로그 데이터를 찾습니다 (동일한 OperationId가 있음).
* 페이지의 구성을 즐겨찾기로 저장 합니다.

> [!NOTE]
>응용 프로그램에서 많은 데이터를 전송 하 고 ASP.NET version 2.0.0-beta3 이상용 Application Insights SDK를 사용 하는 경우 *적응 샘플링* 기능이 작동 하 여 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>문제 해결
### <a name="how-do-i-do-this-for-java"></a>Java의 경우 이 작업을 어떻게 수행하나요?
[Java 로그 어댑터](../../azure-monitor/app/java-trace-logs.md)를 사용합니다.

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>프로젝트 상황에 맞는 메뉴에 Application Insights 옵션이 없습니다.
* Developer Analytics Tools가 개발 컴퓨터에 설치 되어 있는지 확인 합니다. Visual Studio **Tools** > **확장 및 업데이트**에서 **Developer Analytics Tools**를 찾습니다. **설치 됨** 탭에 없으면 **온라인** 탭을 열고 설치 합니다.
* 이는 분석 도구에서 지 원하는 프로젝트 형식일 수 있습니다. [수동 설치](#manual-installation)를 사용합니다.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>구성 도구에는 로그 어댑터 옵션이 없습니다.
* 먼저 로깅 프레임 워크를 설치 합니다.
* System.web. Trace를 사용 하는 경우 [ *web.config*에 구성](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)되어 있는지 확인 합니다.
* 최신 버전의 Application Insights 있는지 확인 합니다. Visual Studio에서 **도구** > **확장 및 업데이트**로 이동 하 고 **업데이트** 탭을 엽니다. **Developer Analytics Tools** 있으면이를 선택 하 여 업데이트 합니다.

### <a name="emptykey"></a>"계측 키를 비워 둘 수 없습니다." 오류 메시지가 표시 됨
Application Insights를 설치 하지 않고 로깅 어댑터 Nuget 패키지를 설치 했을 수 있습니다. 솔루션 탐색기에서 *Applicationinsights .config*를 마우스 오른쪽 단추로 클릭 하 고 **Application Insights 업데이트**를 선택 합니다. Azure에 로그인 하 고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용 하 라는 메시지가 표시 됩니다. 이렇게 하면 문제를 해결 해야 합니다.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>진단 검색에서 추적은 볼 수 있지만 다른 이벤트는 볼 수 없음
파이프라인을 통해 모든 이벤트와 요청이 발생 하는 데 다소 시간이 걸릴 수 있습니다.

### <a name="limits"></a>얼마나 많은 데이터가 보존되나요?
유지 되는 데이터의 양에 영향을 주는 여러 요인이 있습니다. 자세한 내용은 고객 이벤트 메트릭 페이지의 [제한](../../azure-monitor/app/api-custom-events-metrics.md#limits) 섹션을 참조 하세요.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>예상한 일부 로그 항목이 표시 되지 않습니다.
응용 프로그램이 voluminous 양의 데이터를 전송 하 고 ASP.NET version 2.0.0-beta3 이상에서 Application Insights SDK를 사용 하는 경우 적응 샘플링 기능이 작동 하 여 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>다음 단계

* [ASP.NET에서 오류 및 예외 진단][exceptions]
* [검색에 대 한 자세한 정보][diagnostic]
* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md