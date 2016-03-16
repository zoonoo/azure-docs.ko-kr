<properties
   pageTitle="Azure 클라우드 서비스용 Application Insights"
   description="Application Insights를 사용하여 웹 및 작업자 역할을 효과적으로 모니터링"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Azure 클라우드 서비스용 Application Insights


*Application Insights는 미리 보기 상태입니다.*

[Visual Studio Application Insights][start]를 사용하여 [Microsoft Azure 클라우드 서비스 앱](https://azure.microsoft.com/services/cloud-services/)의 가용성, 성능, 실패 및 사용 현황을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

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


2. [Application Insights for Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 패키지를 추가합니다. 이 버전의 SDK는 역할 정보와 같은 서버 컨텍스트를 추가하는 모듈을 포함합니다. 작업자 역할의 경우 Windows 서비스용 Application Insights를 사용합니다.

    !["Application Insights" 검색](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Application Insights 리소스에 데이터를 보내도록 SDK를 구성합니다.

    `ServiceConfiguration.Cloud.cscfg` 파일에서 구성 설정으로 계측 키를 설정합니다. ([샘플 코드](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).
 
    ```XML
     <Role name="WorkerRoleA"> 
      <Setting name="APPINSIGHTS_INSTRUMENTATIONKEY" value="YOUR IKEY" /> 
     </Role>
    ```
 
    적합한 시작 함수에서 구성 설정의 계측 키를 설정합니다.

    ```C#
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```

    구성 설정의 동일한 이름 `APPINSIGHTS_INSTRUMENTATIONKEY`가 Azure 진단 보고에서 사용됩니다.


    응용 프로그램에서 각 역할에 대해 이 작업을 수행합니다. 예제 참조:
 
 * [웹 역할](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [작업자 역할](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [웹 페이지](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. 항상 출력 디렉토리에 복사되도록 ApplicationInsights.config 파일을 설정합니다. 

    .config 파일에서 해당 위치에 계측 키를 배치할지 묻는 메시지가 표시됩니다. 그러나 클라우드 응용 프로그램의 경우에는 .cscfg 파일에서 설정하는 것이 좋습니다. 그래야 포털에서 역할이 정확하게 식별됩니다.


#### 앱 실행 및 게시

앱을 실행하고 Azure에 로그인합니다. 직접 만든 Application Insights 리소스를 열면 개별 데이터 요소가 [검색](app-insights-diagnostic-search.md)에 표시되고 집계 데이터가 [메트릭 탐색기](app-insights-metrics-explorer.md)에 표시됩니다.

원격 분석을 더 추가한 다음(아래 섹션 참조) 앱을 게시하여 라이브 진단 및 사용 피드백을 가져옵니다.


#### 데이터가 없나요?

* [검색][diagnostic] 타일을 열고 개별 이벤트를 봅니다.
* 응용 프로그램을 사용하여 여러 페이지를 열어 원격 분석을 생성해 봅니다.
* 몇 초 정도 기다렸다가 새로고침을 클릭합니다.
* [문제 해결][qna]을 참조하세요.



## 추가 원격 분석

다음 섹션에서는 응용 프로그램의 여러 측면에서 추가 원격 분석을 가져오는 방법을 보여 줍니다.


## 작업자 역할의 요청 추적

웹 역할에서 요청 모듈은 자동으로 HTTP 요청에 대한 데이터를 수집합니다. 기본 컬렉션 동작을 재정의할 수는 방법에 대한 예제는 [샘플 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)을 참조하세요.

HTTP 요청과 같은 방법으로 요청을 추적하여 작업자 역할에 대한 호출의 성능을 캡처할 수 있습니다. Application Insights에서 요청 원격 분석 유형은 시간을 제한할 수 있고 독립적으로 성공 또는 실패할 수 있는 명명된 서버 쪽 작업의 단위를 측정합니다. HTTP 요청은 SDK에서 자동으로 캡처하지만 사용자 고유의 코드를 삽입하여 작업자 역할에 대한 요청을 추적할 수 있습니다.

보고서 요청에서 대해 계측된 두 샘플 작업자 역할: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) 및 [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)를 참조하세요.

## Azure 진단

[Azure 진단](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 데이터에는 역할 관리 이벤트, 성능 카운터 및 응용 프로그램 로그가 포함됩니다. 이러한 내용을 Application Insights로 보내면 나머지 원격 분석과 함께 표시하여 문제를 더 쉽게 진단할 수 있습니다.

Azure 진단은 역할이 예기치 않게 실패하거나 시작되지 않을 경우에 특히 유용합니다.

1. 역할(프로젝트 아님!)을 마우스 오른쪽 단추로 클릭하여 해당 속성을 열고 **진단 사용**, **Application Insights에 진단 보내기**를 차례로 선택합니다.

    !["Application Insights" 검색](./media/app-insights-cloudservices/21-wad.png)

    **또는 앱이 이미 게시되어 실행되고 있는 경우** 서버 탐색기 또는 클라우드 탐색기를 열고 앱을 마우스 오른쪽 단추로 클릭한 다음 동일한 옵션을 선택합니다.

3.  다른 원격 분석과 동일한 Application Insights 리소스를 선택합니다.

    원할 경우 다른 서비스 구성(클라우드, 로컬)에서 다른 리소스를 설정하여 개발 데이터를 라이브 데이터와 별도로 유지할 수 있습니다.

3. 필요에 따라 Application Insights로 전달하려는 [Azure 진단 중 일부를 제외](app-insights-azure-diagnostics.md)합니다. 기본값은 모든 항목입니다.

### Azure 진단 이벤트 보기

진단 유틸리티를 찾을 수 있는 위치:

* 성능 카운터는 사용자 지정 메트릭으로 표시됩니다. 
* Windows 이벤트 로그는 추적 및 사용자 지정 이벤트로 표시됩니다.
* 응용 프로그램 로그, ETW 로그 및 진단 인프라 로그는 추적으로 표시됩니다.

성능 카운터 및 이벤트 개수를 보려면 [메트릭 탐색기](app-insights-metrics-explorer.md)를 열고 새 차트를 추가합니다.


![](./media/app-insights-cloudservices/23-wad.png)

[검색](app-insights-diagnostic-search.md)을 사용하여 Azure 진단에서 보낸 다양한 추적 로그를 검색합니다. 예를 들어 역할에 처리되지 않은 예외가 있어 역할이 충돌 및 재활용되는 경우 해당 정보가 Windows 이벤트 로그의 응용 프로그램 채널에 표시됩니다. 검색 기능을 사용하여 Windows 이벤트 로그 오류를 확인하고 예외에 대한 전체 스택 추적을 가져와서 문제의 근본 원인을 찾을 수 있습니다.


![](./media/app-insights-cloudservices/25-wad.png)

## 앱 진단

Azure 진단에는 앱이 System.Diagnostics.Trace를 사용하여 생성하는 로그 항목이 자동으로 포함됩니다.

그러나 Log4N 또는 NLog 프레임워크를 이미 사용하는 경우 [해당 로그 추적을 캡처][netlogs]할 수도 있습니다.

클라이언트나 서버 또는 둘 다에서 [사용자 지정 이벤트 및 메트릭을 추적][api]하여 응용 프로그램의 성능 및 사용에 대해 자세히 알아봅니다.

## 종속성

Application Insights SDK는 REST API 및 SQL 서버와 같은 외부 종속성에 대해 앱이 작성하는 호출을 보고할 수 있습니다. 이렇게 하면 특정 종속성으로 응답이 느린지 또는 오류를 일으키는지 여부를 확인할 수 있습니다.

종속성을 추적하려면, "상태 모니터" 라고도 하는 [Application Insights 에이전트](app-insights-monitor-performance-live-website-now.md)로 웹/작업자 역할을 설정해야 합니다.

웹/작업자 역할로 Application Insights Agent를 사용하려면

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

## 예외

다른 웹 응용 프로그램 유형에서 처리되지 않은 예외를 수집할 수 있는 방법에 대한 자세한 내용은 [Application Insights에서 예외 모니터링](app-insights-asp-net-exceptions.md)을 참조하세요.

샘플 웹 역할에는 MVC5 및 Web API 2 컨트롤러에 있습니다. 2에서 처리되지 않은 예외는 다음으로 캡처됩니다.

* MVC5 컨트롤러에 대해 [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12)에서 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) 설정
* Web API 2 컨트롤러에 대해 [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25)에서 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) 설정

작업자 역할: 예외를 추적하는 두 가지 방법이 있습니다.

* TrackException(ex)
* Application Insights 추적 수신기 NuGet 패키지를 추가한 경우 System.Diagnostics.Trace를 사용하여 예외를 기록합니다. [코드 예제.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## 성능 카운터

다음 카운터가 기본적으로 수집됩니다.

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

또한 다음은 웹 역할에 대해서도 수집됩니다.

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

[여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)에 표시된 것처럼 추가 사용자 또는 다른 windows 성능 카운터를 지정할 수 있습니다.

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

## 작업자 역할에 대한 상호 관련된 원격 분석

실패 또는 높은 대기 시간을 초래하는 것이 무엇인지 알 수 있다면 풍부한 진단 경험이 있는 것입니다. 웹 역할과 함께 SDK는 관련된 원격 분석 간의 상관관계를 자동으로 설정합니다. 작업자 역할의 경우, 사용자 지정 원격 분석 이니셜라이저를 사용하여 모든 원격 분석이 이를 설정하도록 공통 Operation.Id 컨텍스트 특성을 설정할 수 있습니다. 이렇게 하면 대기 시간/실패 문제를 한 눈에 코드 또는 종속성으로 인해 발생했는지 여부를 확인할 수 있습니다!

방법은 다음과 같습니다.

* [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)에 표시된 것처럼 상관관계 ID를 CallContext로 설정합니다. 이 경우에 요청 ID를 상관관계 ID로 사용합니다.
* Operation.Id를 위에서 설정된 correlationId를 설정하는 사용자 지정 TelemetryInitializer 구현을 추가합니다. 다음이 표시됩니다. [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* 사용자 지정 원격 분석 이니셜라이저를 추가합니다. [여기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)에 표시된 것처럼 ApplicationInsights.config 파일 또는 코드에서 추가할 수 있습니다.

이것으로 끝입니다. 포털 경험이 이미 확보되었으므로 연관된 모든 원격 분석을 한 눈에 볼 수 있습니다.

![](./media/app-insights-cloudservices/bHxuUhd.png)



## 클라이언트 원격 분석

[JavaScript SDK를 웹 페이지에 추가][client]하여 페이지 보기 수, 페이지 로드 시간, 스크립트 예외 사항과 같은 브라우저 기반 원격 분석을 가져오고 페이지 스크립트에서 사용자 지정 원격 분석을 작성합니다.

## 가용성 테스트

[웹 테스트를 설정][availability]하여 응용 프로그램이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.



## 예

[예제](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)는 웹 역할 및 두 작업자 역할이 포함되는 서비스를 모니터링합니다.

## Azure 클라우드 서비스에서 실행할 때의 "메서드를 찾을 수 없음" 예외

.NET 4.6용으로 빌드하셨나요? 4.6은 Azure 클라우드 서비스 역할에서 자동으로 지원되지 않습니다. 앱을 실행하기 전에 [각 역할에 4.6을 설치](../cloud-services/cloud-services-dotnet-install-dotnet.md)합니다.

## 관련된 항목

* [Application Insights에 Azure 진단을 보내도록 구성](app-insights-azure-diagnostics.md)
* [PowerShell을 사용하여 Azure 진단을 Application Insights에 보내기](app-insights-powershell-azure-diagnostics.md)



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
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0224_2016-->