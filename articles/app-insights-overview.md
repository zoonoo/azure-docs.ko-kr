<properties 
	pageTitle="Application Insights란?" 
	description="라이브 웹 또는 장치 응용 프로그램의 사용 현황 및 성능을 추적합니다. 문제를 감지, 심사 및 진단합니다. 지속적인 모니터링을 통해 사용자와 더불어 성공할 수 있도록 개선합니다." 
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
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# Application Insights란?

Visual Studio Application Insights는 라이브 웹 또는 장치 응용 프로그램의 성능 및 사용 현황을 추적할 수 있습니다.

* 성능 문제를 [감지, 심사 및 진단][detect]하여 대부분의 사용자가 인식하기 전에 해결합니다.
 *  성능 변화 또는 충돌에 대한 경고
 *  응답 시간, CPU 사용량, 종속성 추적 등의 성능 문제 진단에 도움을 주는 메트릭
 *  웹 앱의 가용성 테스트
 *  충돌 및 예외에 대한 보고서 및 경고
 *  강력한 진단 로그 검색(선호하는 로깅 프레임워크의 로그 추적 포함)
* 사용자가 수행하는 작업을 파악하여 [응용 프로그램을 지속적으로 개선][knowUsers]합니다. 
 * 페이지 보기 수, 신규 사용자 및 다시 방문하는 사용자, 기타 핵심 사용 분석
 * 사용자 고유의 이벤트를 추적하여 사용 패턴 및 각 기능의 성공 여부 평가

## 작동 원리

응용 프로그램에 작은 SDK를 설치하고 Application Insights 포털에서 계정을 설정합니다. SDK에서 앱을 모니터링하여 포털에 원격 분석 데이터를 보냅니다. 포털에서 통계 차트를 표시하고 모든 문제를 진단하는 데 도움이 되는 강력한 검색 도구를 제공합니다.

![앱의 Application Insights SDK는 Azure 포털의 Application Insights 리소스로 원격 분석을 보냅니다.](./media/app-insights-overview/01-scheme.png)

SDK에는 사용자, 세션 및 성능을 계산하는 모듈을 비롯하여 원격 분석을 수집하는 여러 모듈이 있습니다. 또한 포털에 원격 분석 데이터를 보내도록 사용자 지정 코드를 작성할 수도 있습니다. 사용자 지정 원격 분석은 사용자 스토리를 추적할 때 특히 유용합니다. 단추 클릭, 특정 목표 달성, 사용자 실수 등의 이벤트를 계산할 수 있습니다.

ASP.NET 서버 및 Azure 웹 앱의 경우 두 가지 용도로 사용되는 [상태 모니터][redfield]도 설치할 수 있습니다. 상태 모니터의 기능은 다음과 같습니다.

* 웹 앱을 다시 작성하거나 다시 설치하지 않고 모니터링합니다.
* 종속 모듈에 대한 호출을 추적합니다.

## 함께 작동하는 앱으로 어떤 것이 있나요?

현재 다음 앱을 위한 SDK가 있습니다.

* 웹 앱
 * Azure 또는 IIS 서버의 [ASP.NET][greenbrown]
 * JRE의 [Java][java] 
 * [웹 페이지][client]: HTML + JavaScript
* 장치 앱
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [기타 플랫폼][platforms]


## 어떻게 사용하나요?

* [문제 감지, 심사 및 진단][detect]
* [앱 사용 현황 분석][knowUsers]


## 사용하려면 무엇이 필요한가요?

* Microsoft Azure를 구독해야 합니다. Application Insights는 Azure의 여러 클라우드 서비스 중 하나이며 클라우드 서비스에는 웹사이트, 데이터베이스, VM 등이 포함됩니다. 하지만 Application Insights를 사용하여 모든 응용 프로그램을 모니터링할 수 있습니다. 앱을 반드시 Azure에서 실행할 필요가 없습니다. 

 * 조직에서 계정을 갖고 있을 수도 있습니다.


## 어떻게 시작하나요?

왼쪽에 있는 시작 메뉴에서 플랫폼을 선택합니다.

어떤 플랫폼을 선택하든 기본 절차는 다음과 같습니다.

1. [Azure][portal]에서 Application Insights 리소스를 만들고 계측 키를 가져옵니다.
2. 적절한 SDK를 사용하여 응용 프로그램을 계측하고 계측 키를 사용하여 구성합니다.
3. 디버그 모드 또는 라이브 모드에서 응용 프로그램을 실행합니다.
4. [Azure][portal]에서 리소스의 결과를 봅니다.

경우에 따라 Visual Studio 또는 Eclipse 같은 IDE에 처음 두 단계를 자동으로 수행하는 플러그 인이 제공됩니다. 하지만 언제든지 절차를 수동으로 진행할 수 있습니다.

앱이 웹 사이트 또는 서비스인 경우 기본 절차 이외에도 선택 가능한 추가 절차와 변형된 절차가 있습니다.

* 서버 쪽 응용 프로그램과 클라이언트 [장치][windows] 또는 [웹 페이지][client] 모두에 SDK를 추가합니다. 두 엔드의 이벤트를 상호 연결할 수 있도록 원격 분석 데이터는 포털에서 병합됩니다.
* 전세계 지점에서 사이트 가용성을 모니터링하도록 웹 테스트를 설정합니다.
* 응용 프로그램을 다시 작성하거나 다시 배포하지 않고 라이브 상태로 서버 쪽 응용 프로그램을 계측합니다. 이 기능은 [IIS 서버][redfield] 및 [Azure 웹 앱][azure]에 제공됩니다.
* 앱에서 데이터베이스 같은 다른 구성 요소에 대해 만드는 또는 REST API를 통해 만드는 종속성 호출을 모니터링합니다. [IIS 서버][redfield] 및 [Azure 웹 앱][azure]에 제공됩니다.


<!--Link references-->

[android]: app-insights-android.md
[azure]: insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58-->