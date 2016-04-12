<properties 
	pageTitle="Application Insights에서 종속성 추적" 
	description="Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다." 
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
	ms.date="03/02/2016" 
	ms.author="awills"/>


# Application Insights 설정: 종속성 추적


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



*종속성*은 앱에서 호출하는 외부 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. Visual Studio Application Insights에서 응용 프로그램이 종속성을 기다리는 시간과 종속성 호출에 실패하는 빈도를 쉽게 확인할 수 있습니다.

![예제 차트](./media/app-insights-asp-net-dependencies/10-intro.png)

기본적으로 종속성 모니터는 현재 다음 유형의 종속성에 대한 호출을 보고합니다.

* ASP.NET
 * SQL 데이터베이스
 * ASP.NET 웹 및 HTTP 기반 바인딩을 사용하는 WCF 서비스
 * 로컬 또는 원격 HTTP 호출
 * Azure DocumentDb, 테이블, Blob 저장소 및 큐
* Java
 * MySQL, SQL Server, PostgreSQL 또는 SQLite 등의 [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) 드라이버를 통해 데이터베이스를 호출합니다.
* 웹 페이지의 JavaScript - [웹 페이지 SDK](app-insights-javascript.md)는 Ajax 호출을 자동으로 종속성으로 기록합니다.

[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)를 사용하여 다른 종속성을 모니터링하는 사용자 고유의 SDK 호출을 작성할 수 있습니다.


## 종속성 모니터링을 설정하려면

[Microsoft Azure](http://azure.com) 구독이 필요합니다.

### 앱이 IIS 서버에서 실행되는 경우

웹앱을 .NET 4.6 이상에서 실행하는 경우 앱에 [Application Insights SDK를 설치](app-insights-asp-net.md)하면 자동으로 종속성을 추적합니다. 다른 작업은 필요하지 않습니다.

그렇지 않으면 서버에 Application Insights 상태 모니터를 설치합니다.

1. IIS 웹 서버에서 관리자 자격 증명으로 로그인합니다.
2. [상태 모니터 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=506648)를 다운로드하고 실행합니다.
4. 설치 마법사에서 Microsoft Azure에 로그인합니다.

    ![Microsoft 계정 자격 증명으로 Azure에 로그인합니다.](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *연결 오류? [문제 해결](#troubleshooting)을 참조하세요.*

5. 모니터링할 설치되어 있는 웹 응용 프로그램 또는 웹 사이트를 선택한 다음, Application Insights 포털의 결과를 볼 리소스를 구성합니다.

    ![앱과 리소스를 선택합니다.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    일반적으로 새 리소스 및 [리소스 그룹][roles]을 구성하도록 선택합니다.

    그렇지 않으면 사이트에 대해 [웹 테스트][availability] 또는 [웹 클라이언트 모니터링][client]을 이미 설정한 경우 기존 리소스를 사용합니다.

6. IIS를 다시 시작합니다.

    ![대화 상자의 위쪽에 있는 다시 시작을 선택합니다.](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    웹 서비스가 잠시 중단됩니다.

6. ApplicationInsights.config가 모니터링할 웹앱에 삽입되었습니다.

    ![웹앱의 코드 파일과 함께 .config 파일을 찾습니다.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   web.config에 대한 변경 내용이 몇 가지 있습니다.

#### 나중에 다시 구성하시겠습니까?

마법사를 완료한 다음 원할 때마다 에이전트를 다시 구성할 수 있습니다. 에이전트를 설치했지만 초기 설정과 몇 가지 문제가 있는 경우에도 이를 사용할 수 있습니다.

![작업 표시줄의 Application Insights 아이콘 클릭](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### 앱이 Azure 웹앱으로 실행되는 경우

Azure 웹앱의 제어판에서 Application Insights 확장을 추가합니다.

![웹앱에서 설정, 확장, 추가, Application Insights](./media/app-insights-asp-net-dependencies/05-extend.png)


### Azure 클라우드 서비스 프로젝트 만들기인 경우

[웹 및 작업자 역할에 스크립트 추가](app-insights-cloudservices.md#dependencies) 또는 [.NET Framework 4.6 이상을 설치](../cloud-services/cloud-services-dotnet-install-dotnet.md)합니다.

## <a name="diagnosis"></a> 종속성 성능 문제 진단

서버에서 요청 성능을 평가하려면:

![Application Insights의 응용 프로그램 개요 페이지에서 성능 타일을 클릭합니다.](./media/app-insights-asp-net-dependencies/01-performance.png)

아래로 스크롤하여 요청 표를 확인합니다.

![평균 및 개수가 포함된 요청 목록](./media/app-insights-asp-net-dependencies/02-reqs.png)

맨 위의 요청은 시간이 아주 오래 걸립니다. 시간이 어디에 소비되는지 확인해 보겠습니다.

개별 요청 이벤트를 보려면 해당 행을 클릭합니다.


![요청 항목 목록](./media/app-insights-asp-net-dependencies/03-instances.png)

장기 실행 인스턴스를 클릭하면 세부 사항을 조사할 수 있습니다.

> [AZURE.NOTE] 인스턴스를 선택하려면 약간 아래로 스크롤합니다. 파이프라인의 대기 시간은 최상위 인스턴스의 데이터가 완료되지 않았음을 의미할 수 있습니다.

이 요청과 관련된 원격 종속성 호출까지 스크롤합니다.

![원격 종속성에 대한 호출 찾기, 특별한 기간 식별](./media/app-insights-asp-net-dependencies/04-dependencies.png)

이 요청을 서비스하는 데 걸린 시간은 대부분 로컬 서비스 호출에 소요된 시간인 것 같습니다.

해당 행을 선택하여 자세한 정보를 봅니다.


![해당 원격 종속성을 클릭하여 원인 식별](./media/app-insights-asp-net-dependencies/05-detail.png)

세부 정보에는 문제를 진단하는 데 충분한 정보가 들어 있습니다.



## 오류

실패한 요청이 있으면 차트를 클릭합니다.

![실패한 요청 차트 클릭](./media/app-insights-asp-net-dependencies/06-fail.png)

요청 유형 및 요청 인스턴스를 클릭하여 실패한 원격 종속성 호출을 찾습니다.


![요청 유형을 클릭하고, 인스턴스를 클릭하여 동일한 인스턴스의 다른 보기로 이동하고, 클릭하여 예외 세부 정보를 표시합니다.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## 사용자 지정 종속성 추적

표준 종속성 추적 모듈은 데이터베이스 및 REST API와 같은 외부 종속성을 자동으로 검색합니다. 하지만 일부 추가 구성 요소를 동일한 방식으로 취급할 수도 있습니다.

표준 모듈에 의해 사용되는 동일한[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)를 사용하여 종속성 정보를 보내는 코드를 작성할 수 있습니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

표준 종속성 추적 모듈을 해제하려는 경우, [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 DependencyTrackingTelemetryModule에 대한 참조를 삭제합니다.

## 다음 단계

- [예외](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [사용자 및 페이지 데이터](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=AcomDC_0309_2016-->