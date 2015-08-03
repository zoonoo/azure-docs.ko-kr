<properties
   pageTitle="Azure 클라우드 서비스용 Application Insights"
   description="Application Insights를 사용하여 웹 및 작업자 역할을 효과적으로 모니터링"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="06/17/2015"
   ms.author="sdash"/>

# Azure 클라우드 서비스용 Application Insights


*Application Insights는 미리 보기 상태입니다.*

[Visual Studio Application Insights][start]를 사용하여 [Microsoft Azure 클라우드 서비스 앱](http://azure.microsoft.com/services/cloud-services/)의 가용성, 성능, 실패 및 사용 현황을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

![예](./media/app-insights-cloudservices/sample.png)

[Microsoft Azure](http://azure.com)를 구독해야 합니다. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다.


#### Application Insights를 사용하여 계측하는 샘플 응용 프로그램

Application Insights가 Azure에서 두 작업자 역할이 호스팅되는 클라우드 서비스에 추가되는 이 [샘플 응용 프로그램](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)을 살펴봅니다.

동일한 방식으로 사용자 자신의 클라우드 서비스 프로젝트를 조정하는 방법을 알려줍니다.

## 각 역할에 대한 Application Insights 리소스 만들기

Application Insights 리소스는 원격 분석 데이터를 분석하고 표시할 수 있는 위치입니다.

1.  [Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. 응용 프로그램 유형으로 ASP.NET 앱을 선택합니다. 

    ![새로 만들기, Application Insights 클릭](./media/app-insights-cloudservices/01-new.png)

2.  계측 키를 복사합니다. 잠시 후 SDK를 구성할 때 필요합니다.

    ![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-cloudservices/02-props.png)


일반적으로 각 웹 및 작업자 역할의 데이터에 대해 별도의 리소스를 만드는 것이 좋습니다.

대안으로 모든 역할의 데이터를 한 리소스로 보낼 수는 있지만 각 역할의 결과를 필터링 또는 그룹화할 수 있도록 [기본 속성][apidefaults]을 설정해야 합니다.

## <a name="sdk"></a>각 프로젝트에 SDK 설치


1. Visual Studio에서 클라우드 앱 프로젝트의 NuGet 패키지를 편집합니다.

    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/app-insights-cloudservices/03-nuget.png)

2. [Application Insights for Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 패키지를 추가합니다. 이 버전의 SDK는 역할 정보와 같은 서버 컨텍스트를 추가하는 모듈을 포함합니다.

    !["Application Insights" 검색](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Application Insights 리소스에 데이터를 보내도록 SDK를 구성합니다.

    `ApplicationInsights.config`를 열고 이 줄을 삽입합니다.

    `<InstrumentationKey>` *복사한 계측 키* `</InstrumentationKey>`

    Application Insights 리소스에서 복사한 계측 키를 사용합니다.

4. 항상 출력 디렉토리에 복사되도록 ApplicationInsights.config 파일을 설정합니다. 작업자 역할에만 필요합니다.


또는 코드에서 계측 키(iKey)를 설정할 수 있습니다. Azure 서비스 구성(CSCFG) 설정을 사용하여 해당 환경에 대한 계측 키를 관리하려는 경우 유용합니다. [샘플 응용 프로그램](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)은 iKey를 설정하는 방법을 보여줍니다.

* [웹 역할](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
* [작업자 역할](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
* [웹 페이지](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)

## SDK를 사용하여 원격 분석 보고
### 보고서 요청
 * 웹 역할에서 요청 모듈은 자동으로 HTTP 요청에 대한 데이터를 수집합니다. 기본 컬렉션 동작을 재정의할 수는 방법에 대한 예제는 [샘플 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)을 참조하세요. 
 * HTTP 요청과 같은 방법으로 요청을 추적하여 작업자 역할에 대한 호출의 성능을 캡처할 수 있습니다. Application Insights에서 요청 원격 분석 유형은 시간을 제한할 수 있고 독립적으로 성공 또는 실패할 수 있는 명명된 서버 쪽 작업의 단위를 측정합니다. HTTP 요청은 SDK에서 자동으로 캡처하지만 사용자 고유의 코드를 삽입하여 작업자 역할에 대한 요청을 추적할 수 있습니다.
 * 보고서 요청에서 대해 계측된 두 샘플 작업자 역할: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) 및 [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)를 참조하세요.

### 보고서 종속성
  * Application Insights SDK는 REST API 및 SQL 서버와 같은 외부 종속성에 대해 앱이 작성하는 호출을 보고할 수 있습니다. 이렇게 하면 특정 종속성으로 응답이 느린지 또는 오류를 일으키는지 여부를 확인할 수 있습니다.
  * 종속성을 추적하려면, "상태 모니터" 라고도 하는 [Application Insights 에이전트](app-insights-monitor-performance-live-website-now.md)로 웹/작업자 역할을 설정해야 합니다.
  * 웹/작업자 역할로 Application Insights Agent를 사용하려면
    * [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) 폴더 및 폴더 내의 두 파일을 웹/작업자 역할 프로젝트에 추가합니다. 출력 디렉토리에 항상 복사되도록 해당 빌드 속성을 설정해야 합니다. 이 파일은 에이전트를 설치합니다.
    * [여기](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18)에 표시된 것처럼 시작 작업을 CSDEF 파일에 추가합니다.
    * 참고: *작업자 역할*은 [여기](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44)에서 표시된 것처럼 세 환경 변수가 필요합니다. 웹 역할에는 필요하지 않습니다.

Application Insights 포털에서 표시된 예는 다음과 같습니다.

* 요청 및 종속성이 자동으로 상호 관련된 풍부한 진단:

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* 종속성 정보를 포함한 웹 역할의 성능:

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* 작업자 역할에 대한 요청 및 종속성 정에 관한 스크린샷은 다음과 같습니다.

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

### 보고 예외 사항

* 다른 웹 응용 프로그램 유형에서 처리되지 않은 예외를 수집할 수 있는 방법에 대한 자세한 내용은 [Application Insights에서 예외 모니터링](app-insights-asp-net-exceptions.md)을 참조하세요.
* 샘플 웹 역할에는 MVC5 및 Web API 2 컨트롤러에 있습니다. 2에서 처리되지 않은 예외는 다음으로 캡처됩니다.
    * MVC5 컨트롤러에 대해 [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12)에서 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) 설정
    * Web API 2 컨트롤러에 대해 [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25)에서 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) 설정
* 작업자 역할: 예외를 추적하는 두가지 방법이 있습니다.
    * TrackException(ex)
    * Application Insights 추적 수신기 NuGet 패키지를 추가한 경우 System.Diagnostics.Trace를 사용하여 예외를 기록합니다. [코드 예제.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

### 성능 카운터

다음 카운터가 기본적으로 수집됩니다.

    * \Process(??APP_WIN32_PROC??)% Processor Time
	* \Memory\Available Bytes
	* .NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)% Processor Time

또한 다음은 웹 역할에 대해서도 수집됩니다.

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

[여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)에 표시된 것처럼 추가 사용자 또는 다른 windows 성능 카운터를 지정할 수 있습니다.

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

### 작업자 역할에 대한 상호 관련된 원격 분석

실패 또는 높은 대기 시간을 초래하는 것이 무엇인지 알 수 있다면 풍부한 진단 경험이 있는 것입니다. 웹 역할과 함께 SDK는 관련된 원격 분석 간의 상관관계를 자동으로 설정합니다. 작업자 역할의 경우, 사용자 지정 원격 분석 이니셜라이저를 사용하여 모든 원격 분석이 이를 설정하도록 공통 Operation.Id 컨텍스트 특성을 설정할 수 있습니다. 이렇게 하면 대기 시간/실패 문제를 한 눈에 코드 또는 종속성으로 인해 발생했는지 여부를 확인할 수 있습니다!

방법은 다음과 같습니다.

* [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)에 표시된 것처럼 상관관계 ID를 CallContext로 설정합니다. 이 경우에 요청 ID를 상관관계 ID로 사용합니다.
* Operation.Id를 위에서 설정된 correlationId를 설정하는 사용자 지정 TelemetryInitializer 구현을 추가합니다. 다음이 표시됩니다. [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* 사용자 지정 원격 분석 이니셜라이저를 추가합니다. [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)에 표시된 것처럼 ApplicationInsights.config 파일 또는 코드에서 추가할 수 있습니다.

이것으로 끝입니다. 포털 경험이 이미 확보되었으므로 연관된 모든 원격 분석을 한 눈에 볼 수 있습니다.

![](./media/app-insights-cloudservices/bHxuUhd.png)

#### 데이터가 없나요?

* [검색][diagnostic] 타일을 열고 개별 이벤트를 봅니다.
* 응용 프로그램을 사용하여 여러 페이지를 열어 원격 분석을 생성해 봅니다.
* 몇 초 정도 기다렸다가 새로고침을 클릭합니다.
* [문제 해결][qna]을 참조하세요.


## 설치 완료

응용 프로그램의 모든 부분을 완벽하게 살펴보려면 몇 가지 할 일이 더 있습니다.


* [JavaScript SDK를 웹 페이지에 추가][client]하여 페이지 보기 수, 페이지 로드 시간, 스크립트 예외 사항과 같은 브라우저 기반 원격 분석을 가져오고 페이지 스크립트에서 사용자 지정 원격 분석을 작성합니다.
* 데이터베이스 또는 앱에서 사용하는 다른 구성 요소에 따른 문제를 진단하는 종속성 추적을 추가합니다.
 * [Azure 웹앱 또는 VM에서][azure]
 * [온-프레미스 IIS 서버에서][redfield]
* 즐겨찾는 로깅 프레임워크에서 [로그 추적 캡처][netlogs]
* [사용자 지정 이벤트 및 메트릭을 추적][api]하여 클라이언트나 서버 또는 둘 다에서 응용 프로그램이 어떻게 사용되는지 알아볼 수 있습니다.
* [웹 테스트를 설정][availability]하여 응용 프로그램이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.



## 예

[예제](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)는 웹 역할 및 두 작업자 역할이 포함되는 서비스를 모니터링합니다.



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=July15_HO4-->