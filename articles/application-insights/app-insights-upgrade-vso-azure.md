<properties 
	pageTitle="Application Insights의 Visual Studio Online 이전 버전에서 업그레이드" 
	description="기존 프로젝트 업그레이드"
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="awills"/>
 
#Application Insights의 Visual Studio Online 이전 버전에서 업그레이드

이 문서는 Visual Studio Online의 일부인 Application Insights 이전 버전을 아직 사용하는 프로젝트인 경우에만 해당됩니다. 해당 버전은 적절할 때 교체되어야 하므로 Microsoft Azure 내의 서비스인 새로운 버전으로 업그레이드하는 것이 좋습니다.

##사용하고 있는 버전

Visual Studio 2013 Update 3 이상을 사용하여 프로젝트에 Application Insights를 추가한 경우 대부분 새 Azure 버전을 사용할 것입니다.

ApplicationInsights.config를 엽니다. 노드 `ActiveProfile` 및 `Profiles`가 있는 경우 이는 이전 버전이며 업그레이드해야 합니다.

또는 Visual Studio 솔루션 탐색기에서 프로젝트를 살펴 보고 참조에서 Microsoft.ApplicationInsights를 선택합니다. 속성 창에서 버전을 찾습니다. 0.12 이전인 경우 업그레이드해야 합니다.

##Visual Studio 프로젝트가 있는 경우...

1. Visual Studio 2013 업데이트 3 이상에서 프로젝트를 엽니다.
2. ApplicationInsights.config 삭제 
3. 프로젝트에서 Application Insights NuGet 패키지를 제거합니다. 이렇게 하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다.
4. SDK: 프로젝트를 마우스 오른쪽 단추로 클릭하고 [Application Insights 추가를 선택][greenbrown]합니다. 이는 프로젝트에 SDK를 추가하고 Azure에서 새 Application Insights 리소스도 만듭니다.
5. 로깅: 코드에 LogEvent()와 같은 이전 API에 대한 호출을 포함되는 경우 솔루션을 구축 시 탐색합니다. [새 API를 사용][track]하려면 업데이트합니다.
6. 웹 페이지: 프로젝트에 웹 페이지가 포함되는 경우 <head> 섹션에서 스크립트를 교체합니다. 일반적으로 Views\\Shared\_Layout.cshtml 같은 마스터 페이지에 복사본을 하나만 있습니다. [Azure에서 Application Insights의 퀵 스타트 블레이드에서 새 스크립트를 가져옵니다][usage]. 웹 페이지에 logEvent 또는 logPage와 같은 본문에 원격 분석 호출이 포함되는 경우 [이를 업데이트하여 새 API를 사용][track]합니다.
7. 서버 모니터: 앱이 IIS에서 실행 중인 서비스인 경우, 서버에서 Microsoft Monitoring Agent를 제거했다가 [Application Insights 상태 모니터를 설치][redfield]합니다.
8. 웹 테스트: 웹 가용성 테스트를 사용하는 경우 경고와 함께 [새 포털에서 다시 만듭니다][availability].
9. 경고: Azure 포털에서 [메트릭에 대한 경고][alerts]를 설정합니다.
10. 성능 카운터: 성능 카운터를 사용하는 경우 자신만의 코드를 작성하여 카운터를 주기적으로 샘플링하고 [TrackMetric()][track]을 사용하여 일르 보낼 수 있습니다.

##Java 웹 서비스가 있는 경우...

1. 서버 컴퓨터의 경우, 웹 서비스 시작 파일에서 APM 에이전트에 대한 참조를 제거하여 이전 에이전트를 사용하지 않도록 설정합니다. TomCat 서버에서 Catalina.bat를 편집합니다. JBoss 서버에서 Run.bat를 편집합니다. 
2. 웹 서비스에서 다시 시작합니다.
3. Microsoft Azure 포털에서 [새 Application Insights 리소스를 추가][java]합니다. 개발 컴퓨터에서 [Java SDK][java]를 웹 프로젝트에 추가합니다. 이제 서버 코드에서 [사용자 지정 원격 분석을 전송][track]할 수 있습니다.
4. 웹 페이지의 <head> 섹션에서 스크립트를 교체합니다. (서버 쪽에서 포함하는 복사본을 하나만 있을 수 있습니다.) [Azure에서 새 Application Insights 리소스의 퀵 스타트 블레이드에서 새 스크립트를 가져옵니다][usage]. 웹 페이지에 logEvent 또는 logPage와 같은 본문에 원격 분석 호출이 포함되는 경우 [이를 업데이트하여 새 API를 사용][track]합니다.



<!--Link references-->

[alerts]: app-insights-alerts.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54--> 