---
title: Application Insights에서 .NET 추적 로그 탐색
description: 추적, NLog 또는 Log4Net에서 생성 된 로그를 검색 합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: d366f363b7bd1d5306d598c9b38258eb78076b7c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472063"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Application Insights에서 .NET/.NET Core 추적 로그 검색

ILogger, NLog, log4Net 또는 System.Diagnostics.Trace를에서 ASP.NET/ASP.NET Core 응용 프로그램에 대 한 진단 추적 로그를 보낼 [Azure Application Insights][start]합니다. 그런 다음 탐색 하 고 검색할 수 있습니다. 이러한 로그는 각 사용자 요청과 관련 된 기타 이벤트 및 예외 보고서를 사용 하 여 상관 관계를 추적을 식별할 수 있도록 응용 프로그램에서 다른 로그 파일을 사용 하 여 병합 됩니다.

> [!NOTE]
> 로그 캡처 모듈이 필요 한가요? 타사로 거에 대 한 유용한 어댑터는 것입니다. NLog, log4Net 또는 System.Diagnostics.Trace를 사용 하지 않는 경우 호출 하는 것 살펴보겠습니다 [ **Application Insights tracktrace ()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 직접.
>
>
## <a name="install-logging-on-your-app"></a>앱에 대한 로깅 설치
App.config 또는 web.config에 항목을 생성 해야 하는 프로젝트에서 선택한 로깅 프레임 워크를 설치 합니다.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>로그를 수집하도록 Application Insights 구성
[프로젝트에 Application Insights 추가](../../azure-monitor/app/asp-net.md) 아직 수행 하지 않은 경우. 로그 수집기를 포함하는 옵션이 나타납니다.

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 **Application Insights 구성**합니다. 선택 된 **추적 컬렉션 구성** 옵션입니다.

> [!NOTE]
> Application Insights 메뉴 또는 로그 수집기 옵션이 없습니다. [문제 해결](#troubleshooting)을 시도해 보세요.

## <a name="manual-installation"></a>수동 설치
프로젝트 형식이 Application Insights 설치 관리자에서 지원되지 않는 경우(예: Windows 데스크톱 프로젝트) 이 방법을 사용합니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다.
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **NuGet 패키지 관리**합니다.
3. "Application Insights" 검색
4. 다음 패키지 중 하나를 선택합니다.

   - ILogger의 경우: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog의 경우: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net의 경우: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - System.Diagnostics의 경우: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 패키지는 필요한 어셈블리를 설치 하 고 적용 가능한 경우 web.config 또는 app.config를 수정 합니다.

## <a name="ilogger"></a>ILogger

Application Insights ILogger 구현을 사용 하 여 콘솔 응용 프로그램 및 ASP.NET Core를 사용 하 여 예제를 참조 하세요 [ApplicationInsightsLoggerProvider.NET Core ILogger에 대 한 로그](ilogger.md)합니다.

## <a name="insert-diagnostic-log-calls"></a>진단 로그 호출 삽입
System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net 또는 NLog를 원할 경우 사용 합니다.

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>EventSource 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트를 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 패키지를 설치합니다. 그런 다음 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일의 `TelemetryModules` 섹션을 편집합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **이름** 은 수집할 EventSource의 이름을 지정 합니다.
 * **수준** 수집할 로깅 수준을 지정 합니다. *중요 한*, *오류*, *정보 제공 용 이므로*를 *LogAlways*를 *Verbose*, 또는 *경고*.
 * **키워드** (선택 사항)를 사용 하 여 키워드 조합의 정수 값을 지정 합니다.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 이벤트를 구성할 수 있습니다. 먼저 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 패키지를 설치합니다. 다음의 "TelemetryModules" 섹션을 편집 합니다 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일입니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

추적 하려는 각 DiagnosticSource를 사용 하 여 항목 추가 합니다 **이름을** 특성에 DiagnosticSource의 이름으로 설정 합니다.

## <a name="use-etw-events"></a>사용 하 여 ETW 이벤트
이벤트 추적에 대 한 Windows (ETW) 이벤트 추적으로 Application Insights로 보내도록 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EtwCollector` NuGet 패키지를 설치합니다. 다음의 "TelemetryModules" 섹션을 편집 합니다 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 파일입니다.

> [!NOTE] 
> ETW 이벤트 성능 로그 사용자 또는 관리자의 구성원 인 id에서 SDK를 호스팅하는 프로세스를 실행 하는 경우에 수집할 수 있습니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **ProviderName** 수집할 ETW 공급자의 이름입니다.
 * **ProviderGuid** 수집할 ETW 공급자의 GUID를 지정 합니다. 대신 사용할 수 있습니다 `ProviderName`합니다.
 * **수준** 수집할 로깅 수준을 설정 합니다. 수 *위험*, *오류*, *Informational*를 *LogAlways*를 *Verbose*, 또는 *경고*합니다.
 * **키워드** (선택 사항) 키워드 조합 사용 하 여 정수 값을 설정 합니다.

## <a name="use-the-trace-api-directly"></a>추적 API를 직접 사용
Application Insights 추적 API를 직접 호출할 수 있습니다. 로깅 어댑터는 이 API를 사용합니다.

예를 들면 다음과 같습니다.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.

또한 메시지에 심각도 수준을 추가할 수 있습니다. 및 다른 원격 분석과 같은 다른 추적 집합에 대해 필터링 또는 검색 하는 데 속성 값을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

이 쉽게에서 필터링을 통해 [검색] [ diagnostic] 특정 데이터베이스에 관련 된 특정 심각도 수준의 모든 메시지입니다.

## <a name="explore-your-logs"></a>로그 탐색
디버그 모드에서 앱을 실행 하거나 실시간으로 배포 합니다.

앱의 개요 창에서 [Application Insights 포털][portal]를 선택 [Search][diagnostic]합니다.

예를 들어 다음을 수행할 수 있습니다.

* 로그 추적 또는 특정 속성을 사용 하 여 항목을 필터링 합니다.
* 특정 항목을 자세히 검사합니다.
* 동일한 사용자 요청에 연결 하는 다른 시스템 로그 데이터 찾기 (동일한 OperationId 있음).
* 페이지의 구성을 즐겨찾기로 저장 합니다.

> [!NOTE]
>응용 프로그램에서 많은 양의 데이터를 전송 하 고 ASP.NET 버전 2.0.0-beta3 이상용, Application Insights SDK를 사용 하는 경우는 *적응 샘플링* 기능에서 작동 하 고 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>문제 해결
### <a name="how-do-i-do-this-for-java"></a>Java의 경우 이 작업을 어떻게 수행하나요?
[Java 로그 어댑터](../../azure-monitor/app/java-trace-logs.md)를 사용합니다.

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>프로젝트 상황에 맞는 메뉴에 Application Insights 옵션이 없습니다.
* Developer Analytics Tools 개발 컴퓨터에 설치 되어 있는지 확인 합니다. Visual Studio에서 **도구가** > **확장 및 업데이트**, 검색할 **Developer Analytics Tools**합니다. 에 없으면를 **설치 됨** 탭을 열고 합니다 **온라인** 탭 하 고 설치 합니다.
* Devloper 분석 도구를 지원 하지 않는 프로젝트 형식 수 있습니다. [수동 설치](#manual-installation)를 사용합니다.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>구성 도구에 로그 어댑터 옵션이 없습니다.
* 먼저 로깅 프레임 워크를 설치 합니다.
* System.Diagnostics.Trace를 사용 하는 경우 있는지 확인 하 고 [에서 구성한 *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)합니다.
* Application Insights의 최신 버전이 있는지 확인 합니다. Visual Studio로 이동 **도구** > **확장 및 업데이트**을 연 합니다 **업데이트** 탭 합니다. 하는 경우 **Developer Analytics Tools** 는 업데이트를 선택 합니다.

### <a name="emptykey"></a>"계측 키는 비워둘 수 없습니다" 오류 메시지가
아마도 Application Insights를 설치 하지 않고 로깅 어댑터 Nuget 패키지를 설치 합니다. 솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 *ApplicationInsights.config*, 선택한 **Application Insights 업데이트**합니다. Azure에 로그인 하 고 Application Insights 리소스 만들기 또는 기존 항목을 다시 사용 하 라는 메시지가 표시 됩니다. 문제를 해결 해야 하는 합니다.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>추적 되지만 진단 검색에서 다른 이벤트 없습니다 볼 수 있습니다.
모든 이벤트 및 파이프라인을 통해 가져오기에 대 한 요청 시간이 걸릴 수 있습니다.

### <a name="limits"></a>얼마나 많은 데이터가 보존되나요?
여러 가지 요인에 유지 되는 데이터 양에 영향을 줍니다. 자세한 내용은 참조는 [제한](../../azure-monitor/app/api-custom-events-metrics.md#limits) 고객 이벤트 메트릭 페이지의 섹션입니다.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>예상 하는 로그 항목이 보이지 않는 경우
응용 프로그램에서 방대한 양의 데이터를 전송 하 고 ASP.NET 버전 2.0.0-beta3 이상용 Application Insights SDK를 사용 하는 경우 적응 샘플링 기능이 작동 하 고 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>다음 단계

* [ASP.NET의 실패 및 예외 진단][exceptions]
* [검색에 자세히 알아보기][diagnostic]
* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md