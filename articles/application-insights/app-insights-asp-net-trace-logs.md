<properties 
	pageTitle="Application Insights에서 .NET 추적 로그 탐색" 
	description="추적, NLog 또는 Log4Net을 사용하여 생성된 로그를 검색합니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="awills"/>
 
# Application Insights에서 .NET 추적 로그 탐색  

ASP.NET 응용 프로그램에서 진단 추적에 NLog, log4Net 또는 System.Diagnostics.Trace를 사용하는 경우 [Visual Studio Application Insights][start]로 로그를 보내서 탐색 및 검색할 수 있습니다. 서비스를 제공하는 각 사용자 요청과 연결된 추적을 식별하고 다른 이벤트 및 예외 보고서와 상호 연결할 수 있도록 로그가 응용 프로그램에서 들어오는 다른 원격 분석과 병합됩니다.

또한 Application Insights SDK를 사용하여 로그 추적, 이벤트 및 예외 보고서를 작성할 수 있습니다.

아직 [프로젝트에 Application Insights를 설정][start]하지 않았다면 지금 설정하세요.


##  로깅 프레임워크 어댑터 설치

로깅 프레임워크(log4Net, NLog 또는 System.Diagnostics.Trace)를 사용하는 경우 다른 원격 분석과 함께 이러한 로그를 Application Insights로 전송하는 어댑터를 설치할 수 있습니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다. 
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
3. 온라인 > 모두를 선택하고, **시험판 포함**을 선택하고, "Microsoft.ApplicationInsights"를 검색합니다.

    ![적절한 어댑터의 시험판 버전 가져오기](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. 다음 패키지 중에서 적절한 패키지를 하나 선택합니다.
  + Microsoft.ApplicationInsights.TraceListener (to capture System.Diagnostics.Trace calls)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet 패키지는 필요한 어셈블리를 설치하고 web.config 또는 app.config를 수정합니다.

#### 진단 로그 호출 삽입

System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net 또는 NLog를 원할 경우

    logger.Warn("Slow response - database01");


## 직접 추적 API 사용

Application Insights 추적 API를 직접 호출할 수 있습니다. 로깅 어댑터는 이 API를 사용합니다.

예:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.


## 로그 탐색

[Application Insights 포털][portal]에서, 앱의 개요 블레이드에서 [검색][diagnostic]을 선택합니다.

![Application Insights에서 검색 선택](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![진단 검색](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

예를 들어 다음을 수행할 수 있습니다.

* 특정 속성이 있는 로그 추적 또는 항목을 필터링합니다.
* 특정 항목을 자세히 검사합니다.
* 동일한 사용자 요청에 관련된, 다시 말해서 OperationId가 같은 다른 원격 분석을 찾습니다. 
* 이 페이지의 구성을 즐겨찾기로 저장합니다.


## 다음 단계

[ASP.NET의 실패 및 예외 진단][exceptions]

[진단 검색에 대해 자세히 알아보세요][diagnostic].



## 문제 해결

### <a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.

Application Insights를 설치하지 않고 로깅 어댑터 Nuget 패키지를 설치한 것 같습니다.

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다. 이 경우 문제가 해결된 것입니다.

### 진단 검색에 추적은 보이지만 다른 이벤트가 보이지 않습니다.

모든 이벤트와 요청이 파이프라인을 통과할 때까지 다소 시간이 걸릴 수 있습니다.

### <a name="limits"></a>얼마나 많은 데이터가 보존되나요?

각 응용 프로그램에서 초당 최대 500개의 이벤트가 보존됩니다. 이벤트는 7일 동안 보존됩니다.

## <a name="add"></a>다음 단계

* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO6-->