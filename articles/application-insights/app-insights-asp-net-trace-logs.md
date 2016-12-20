---
title: "Application Insights에서 .NET 추적 로그 탐색"
description: "추적, NLog 또는 Log4Net을 사용하여 생성된 로그를 검색합니다."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: d46407da69184da6b1dba72aeb86e97cf1cae725


---
# <a name="explore-net-trace-logs-in-application-insights"></a>Application Insights에서 .NET 추적 로그 탐색
ASP.NET 응용 프로그램에서 진단 추적에 NLog, log4Net 또는 System.Diagnostics.Trace를 사용하는 경우 [Azure Application Insights][시작]으로 로그를 보내서 탐색 및 검색할 수 있습니다. 서비스를 제공하는 각 사용자 요청과 연결된 추적을 식별하고 다른 이벤트 및 예외 보고서와 상호 연결할 수 있도록 로그가 응용 프로그램에서 들어오는 다른 원격 분석과 병합됩니다.

> [!NOTE]
> 로그 캡처 모듈이 필요한가요? 타사 로거에 대한 유용한 어댑터지만 NLog, log4Net 또는 System.Diagnostics.Trace를 사용하지 않는 경우 [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) 를 직접 호출하는 것이 좋습니다.
> 
> 

## <a name="install-logging-on-your-app"></a>앱에 대한 로깅 설치
프로젝트에서 선택한 로깅 프레임워크를 설치합니다. 그러면 app.config 또는 web.config에 항목이 생성됩니다.

System.Diagnostics.Trace를 사용하는 경우 web.config에 항목을 추가해야 합니다.

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener" 
             type="System.Diagnostics.TextWriterTraceListener" 
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>로그를 수집하도록 Application Insights 구성
**[프로젝트에 Application Insights를 추가](app-insights-asp-net.md)**하지 않은 경우 지금 추가합니다. 로그 수집기를 포함하는 옵션이 나타납니다.

또는 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하여 **Application Insights를 구성** 합니다. 옵션을 **추적 컬렉션 구성**으로 선택합니다.

*Application Insights 메뉴 또는 로그 수집기 옵션이 없습니까?* [문제 해결](#troubleshooting)을 시도해 보세요.

## <a name="manual-installation"></a>수동 설치
프로젝트 형식이 Application Insights 설치 관리자에서 지원되지 않는 경우(예: Windows 데스크톱 프로젝트) 이 방법을 사용합니다. 

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다. 
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
3. "Application Insights" 검색
   
    ![적절한 어댑터의 시험판 버전 가져오기](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)
4. 다음 패키지 중에서 적절한 패키지를 하나 선택합니다.
   
   * Microsoft.ApplicationInsights.TraceListener (to capture System.Diagnostics.Trace calls)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

NuGet 패키지는 필요한 어셈블리를 설치하고 web.config 또는 app.config를 수정합니다.

## <a name="insert-diagnostic-log-calls"></a>진단 로그 호출 삽입
System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net 또는 NLog를 원할 경우

    logger.Warn("Slow response - database01");


## <a name="using-the-trace-api-directly"></a>직접 추적 API 사용
Application Insights 추적 API를 직접 호출할 수 있습니다. 로깅 어댑터는 이 API를 사용합니다. 

예:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다. 

또한 메시지에 심각도 수준을 추가할 수 있습니다. 또 다른 원격 분석처럼, 다른 추적 집합에 대한 필터링 또는 검색하는 데 사용할 수 있는 속성 값을 추가할 수 있습니다. 예:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

이를 통해 [검색][diagnostic]에서 특정 데이터베이스와 관련된 특정 심각도 수준의 모든 메시지를 쉽게 필터링할 수 있습니다.

## <a name="explore-your-logs"></a>로그 탐색
디버그 모드에서 앱을 실행하거나 실시간으로 배포합니다.

[Application Insights 포털][portal]의 앱의 개요 블레이드에서 [검색][diagnostic]을 선택합니다.

![Application Insights에서 검색 선택](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![검색](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

예를 들어 다음을 수행할 수 있습니다.

* 특정 속성이 있는 로그 추적 또는 항목을 필터링합니다.
* 특정 항목을 자세히 검사합니다.
* 동일한 사용자 요청에 관련된, 다시 말해서 OperationId가 같은 다른 원격 분석을 찾습니다. 
* 이 페이지의 구성을 즐겨찾기로 저장합니다.

> [!NOTE]
> **샘플링**  응용 프로그램이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에서의 Application Insights SDK를 사용하는 경우 적응 샘플링 기능이 작동하고 원격 분석의 백분율만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](app-insights-sampling.md)
> 
> 

## <a name="next-steps"></a>다음 단계
[ASP.NET의 실패 및 예외 진단][exceptions]

[검색에 대한 자세한 정보][diagnostic].

## <a name="troubleshooting"></a>문제 해결
### <a name="how-do-i-do-this-for-java"></a>Java의 경우 이 작업을 어떻게 수행하나요?
[Java 로그 어댑터](app-insights-java-trace-logs.md)를 사용합니다.

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>프로젝트 상황에 맞는 메뉴에 Application Insights 옵션이 없습니다.
* Application Insights 도구가 이 개발 컴퓨터에 설치되어 있는지 확인합니다. Visual Studio 메뉴 도구, 확장 및 업데이트에서 Application Insights 도구를 찾습니다. 설치됨 탭에 없는 경우 온라인 탭을 열고 설치합니다.
* Application Insights 도구에서 지원되지 않는 프로젝트 형식일 수 있습니다. [수동 설치](#manual-installation)를 사용합니다.

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>구성 도구에 로그 어댑터 옵션이 없습니다.
* 먼저 로깅 프레임워크를 설치해야 합니다.
* System.Diagnostics.Trace를 사용하는 경우 [`web.config`에서 구성](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)했는지 확인합니다.
* 최신 버전의 Application Insights 도구가 있으신가요? Visual Studio **도구** 메뉴에서 **확장 및 업데이트**를 선택하고 **업데이트** 탭을 엽니다. Application Insights 도구가 있으면 클릭하여 업데이트합니다.

### <a name="a-nameemptykeyai-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.
Application Insights를 설치하지 않고 로깅 어댑터 Nuget 패키지를 설치한 것 같습니다.

솔루션 탐색기에서 `ApplicationInsights.config` 를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다. 이 경우 문제가 해결된 것입니다.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>진단 검색에 추적은 보이지만 다른 이벤트가 보이지 않습니다.
모든 이벤트와 요청이 파이프라인을 통과할 때까지 다소 시간이 걸릴 수 있습니다.

### <a name="a-namelimitsahow-much-data-is-retained"></a><a name="limits"></a>얼마나 많은 데이터가 보존되나요?
각 응용 프로그램에서 초당 최대 500개의 이벤트가 보존됩니다. 이벤트는 7일 동안 보존됩니다.

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>예상되는 로그 항목의 일부가 표시되지 않습니다.
 응용 프로그램이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에서의 Application Insights SDK를 사용하는 경우 적응 샘플링 기능이 작동하고 원격 분석의 백분율만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](app-insights-sampling.md)

## <a name="a-nameaddanext-steps"></a><a name="add"></a>다음 단계
* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[시작]: app-insights-overview.md





<!--HONumber=Nov16_HO3-->


