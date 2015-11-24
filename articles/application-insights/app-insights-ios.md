<properties
    pageTitle="iOS 앱에 대한 분석 | Microsoft Azure"
    description="iOS 앱의 사용 현황 및 성능을 분석할 수 있습니다."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# iOS 앱에 대한 분석

Visual Studio Application Insights를 사용하여 모바일 응용 프로그램의 사용 현황, 이벤트 및 충돌을 모니터링할 수 있습니다.


> [AZURE.NOTE]크래시 보고, 분석, 배포 및 피드백 관리를 가져오는 데 [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios)를 사용하는 것이 좋습니다.

## 요구 사항

필요한 사항:

* [Microsoft Azure](http://azure.com) 구독. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다.
* Xcode 6 이상.
* SDK은 iOS 6.0 이상이 설치된 장치에서 실행됩니다.

## Application Insights 리소스 만들기

[Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. iOS 옵션을 선택합니다.

![새로 만들기, 개발자 서비스, Application Insights를 차례대로 클릭](./media/app-insights-ios/11-new.png)

열리는 블레이드에서 앱의 성능 및 사용 데이터를 볼 수 있습니다. 다음에 Azure에 로그인할 때 다시 이 블레이드로 돌아가려면 시작 화면에서 해당 타일을 찾아야 합니다. 또는 찾아보기를 클릭하여 찾아야 합니다.

## 설정

앱에 SDK를 설치하려면 [설치 가이드](https://github.com/Microsoft/ApplicationInsights-iOS#setup)를 수행합니다.

## Application Insights에서 데이터 보기

앱을 실행하여 원격 분석을 생성합니다.

http://portal.azure.com으로 돌아가서 Application Insights 리소스를 찾습니다.

**검색**을 클릭하여 [진단 검색][diagnostic]을 엽니다. 여기에 첫 번째 이벤트가 표시됩니다. 아무 것도 표시되지 않으면 1-2분 정도 기다렸다가 **새로 고침**을 클릭합니다.

![진단 검색 클릭](./media/app-insights-ios/21-search.png)

앱을 사용하면 데이터 개요 블레이드에 데이터가 표시됩니다.

![개요 블레이드](./media/app-insights-ios/22-oview.png)

원하는 차트를 클릭하여 자세한 내용을 봅니다. 예를 들어 충돌 차트를 클릭하면 다음 내용이 표시됩니다.

![충돌 차트 클릭](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>다음 단계

[앱 사용량 추적][track]

[진단 검색][diagnostic]

[메트릭 탐색기][metrics]

[문제 해결][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->