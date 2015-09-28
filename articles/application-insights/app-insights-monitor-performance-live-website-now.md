<properties
	pageTitle="실행 중인 웹 사이트에서 성능 문제 진단 | Microsoft Azure"
	description="다시 배포하지 않고 웹 사이트의 성능을 모니터링합니다. 독립 실행형 또는 Application Insights SDK를 사용하여 종속성 원격 분석을 가져옵니다."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/10/2015"
	ms.author="awills"/>


# Application Insights 상태 모니터를 설치하여 웹 사이트 성능 모니터링

*Application Insights는 미리 보기 상태입니다.*

Visual Studio Application Insights의 상태 모니터를 사용하여 IIS 서버에서 실행되는 웹 응용 프로그램의 예외 및 성능 문제를 진단할 수 있습니다. 상태 모니터를 IIS 웹 서버에 설치하면 이 서버에서 찾은 ASP.NET 웹앱을 계측하여 검색하고 분석할 수 있도록 Application Insights 포털로 데이터를 보냅니다. 관리자 액세스 권한이 있는 물리적 또는 가상 서버에 설치할 수 있습니다.

![예제 차트](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Application Insights를 IIS 웹 응용 프로그램에 적용하는 세 가지 방법이 있습니다.

* **빌드 시간:** 웹앱 코드에 [Application Insights SDK를 추가][greenbrown]합니다. 그러면 다음 항목을 사용할 수 있습니다.
 * 다양한 표준 진단 및 사용 원격 분석입니다.
 * 사용 또는 진단 문제를 추적할 자체 원격 분석을 작성하려는 경우 [Application Insights API][api]를 사용할 수 있습니다.
* **런타임:** 상태 모니터를 사용하여 서버에서 웹앱을 계측할 수 있습니다.
 * 이미 실행 중인 웹앱 모니터링: 다시 작성하거나 다시 게시할 필요가 없습니다.
 * 다양한 표준 진단 및 사용 원격 분석입니다.
 * 종속성 진단&#151;앱이 데이터베이스, REST API 또는 다른 서비스 등의 다른 구성 요소를 사용하는 곳에서 오류 또는 성능 저하를 찾습니다.
 * 원격 분석 문제를 해결합니다.
* **둘 모두:** SDK를 웹앱 코드로 컴파일하고, 웹 서버에서 상태 모니터를 실행합니다. 두 가지 모두로부터 최상의 기능을 사용합니다.
 * 표준 진단 및 사용 원격 분석
 * 종속성 진단
 * API를 사용한 사용자 지정 원격 분석 작성
 * SDK 및 원격 분석의 모든 문제 해결



> [AZURE.TIP]사용 중인 앱이 [Azure 앱 서비스 웹앱](../app-service-web/websites-learning-map.md)인가요? Microsoft Azure의 앱 제어판에서 [Application Insights SDK를 추가][greenbrown]한 다음 [Application Insights Extension을 추가](../insights-perf-analytics.md)합니다.


## IIS 웹 서버에 Application Insights 상태 모니터를 설치합니다.

1. [Microsoft Azure](http://azure.com) 구독이 필요합니다.

1. IIS 웹 서버에서 관리자 자격 증명으로 로그인합니다.
2. [상태 모니터 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=506648)를 다운로드하고 실행합니다.

4. 설치 마법사에서 Microsoft Azure에 로그인합니다.

    ![Microsoft 계정 자격 증명으로 Azure에 로그인합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *연결 오류? [문제 해결](#troubleshooting)을 참조하세요.*

5. 모니터링할 설치되어 있는 웹 응용 프로그램 또는 웹 사이트를 선택한 다음, Application Insights 포털의 결과를 볼 리소스를 구성합니다.

    ![앱과 리소스를 선택합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    일반적으로 새 리소스 및 [리소스 그룹][roles]을 구성하도록 선택합니다.

    그렇지 않으면 사이트에 대해 [웹 테스트][availability] 또는 [웹 클라이언트 모니터링][client]을 이미 설정한 경우 기존 리소스를 사용합니다.

6. IIS를 다시 시작합니다.

    ![대화 상자의 위쪽에 있는 다시 시작을 선택합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    웹 서비스가 잠시 중단됩니다.

6. ApplicationInsights.config가 모니터링할 웹앱에 삽입되었습니다.

    ![웹앱의 코드 파일과 함께 .config 파일을 찾습니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   web.config에 대한 변경 내용이 몇 가지 있습니다.

### 나중에 다시 구성하시겠습니까?

마법사를 완료한 다음 원할 때마다 에이전트를 다시 구성할 수 있습니다. 에이전트를 설치했지만 초기 설정과 몇 가지 문제가 있는 경우에도 이를 사용할 수 있습니다.

![작업 표시줄의 Application Insights 아이콘 클릭](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## 성능 원격 분석 보기

[Azure Preview 포털](http://portal.azure.com)에 로그인하고 Application Insights를 찾아본 다음 만든 리소스를 엽니다.

![찾아보기, Application Insights를 선택한 후 앱을 선택합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

성능 블레이드를 열어서 종속성 및 기타 데이터를 확인합니다.

![성능](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

차트를 클릭하여 세부 정보를 확인합니다.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### 종속성

HTTP, SQL, AZUREBLOB 차트는 종속성에 대한 응답 시간 및 호출의 횟수, 즉 응용 프로그램이 사용하는 외부 서비스를 표시합니다.



#### 성능 카운터

성능 카운터 차트를 클릭하여 표시 항목을 변경합니다. 또는 새 차트를 추가할 수 있습니다.

#### 예외

![서버 예외 차트를 클릭합니다.](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

특정 예외로 드릴다운(지난 7일부터)하고 스택 추적 및 컨텍스트 데이터를 가져올 수 있습니다.


## 문제 해결

### 연결 오류

상태 모니터가 작동할 수 있도록 서버 방화벽에서 일부 나가는 포트를 열어야 합니다.

+ 원격 분석 - 항상 다음 항목이 필요합니다.
 +	`dc.services.visualstudio.com:80`
 +	`f5.services.visualstudio.com:80`
 +	`dc.services.visualstudio.com:443`
 +	`f5.services.visualstudio.com:443`
 +	`dc.services.vsallin.net:443`
+ 구성 - 변경하는 경우에만 필요합니다.
 -	`management.core.windows.net:443`
 -	`management.azure.com:443`
 -	`login.windows.net:443`
 -	`login.microsoftonline.com:443`
 -	`secure.aadcdn.microsoftonline-p.com:443`
 -	`auth.gfx.ms:443`
 -	`login.live.com:443`
+ 설치:
 +	`packages.nuget.org:443`
 +	`appinsightsstatusmonitor.blob.core.windows.net:80`

이 목록은 수시로 변경될 수 있습니다.

### 원격 분석이 없나요?

  * 사이트를 사용하여 일부 데이터를 생성합니다.
  * 데이터가 들어올 때까지 몇 분 정도 기다린 다음 **새로 고침**을 클릭합니다.
  * 진단 검색(검색 타일)을 열어 개별 이벤트를 봅니다. 이벤트는 집계 데이터가 차트에 표시되기 전에 진단 검색에 종종 표시됩니다.
  * 상태 모니터를 열고 왼쪽 창에서 응용 프로그램을 선택합니다. "구성 알림" 섹션에 이 응용 프로그램에 대한 진단 메시지가 있는지 확인합니다.

  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * 서버 방화벽이 위에 나열된 포트에서 나가는 트래픽을 허용하는지 확인합니다.
  * 서버에서 "권한 부족"에 대한 메시지가 표시되는 경우 다음을 시도합니다.
    * IIS 관리자에서 응용 프로그램 풀을 선택하고 **고급 설정**을 연 다음 **프로세스 모델**에서 ID를 확인합니다.
    * 컴퓨터 관리 제어판에서 성능 모니터 사용자 그룹에 이 ID를 추가합니다.
  * [문제 해결][qna]을 참조하세요.

## 시스템 요구 사항

Server에서 Application Insights 상태 모니터에 대한 OS 지원:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

최신 SP 및 .NET Framework 4.0, 4.5 포함

클라이언트 쪽 Windows 7, 8, 8.1에서, 역시 .NET Framework 4.0, 4.5 포함

IIS 지원: IIS 7, 7.5, 8, 8.5(IIS 필요)

## <a name="next"></a>다음 단계

* [웹 테스트를 만들어][availability] 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
* [이벤트 및 로그를 검색][diagnostic]하여 문제를 진단할 수 있습니다.
* [웹 클라이언트 원격 분석을 추가][usage]하여 웹 페이지 코드에서 예외를 확인하고 추적 호출을 삽입합니다.
* [Application Insights SDK를 웹 서비스 코드에 추가][greenbrown]하여 서버 코드에 추적 및 로그 호출을 삽입할 수 있도록 합니다.

## 비디오

#### 성능 모니터링

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Sept15_HO3-->