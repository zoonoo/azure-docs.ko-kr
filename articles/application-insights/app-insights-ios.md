<properties
    pageTitle="iOS 앱용 Application Insights | Microsoft Azure"
    description="Application Insights를 사용하여 iOS 앱의 사용 현황 및 성능을 분석합니다."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="ronmart"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/27/2015"
    ms.author="awills"/>

# iOS 앱용 Application Insights

Visual Studio Application Insights를 사용하여 모바일 응용 프로그램의 사용 현황, 이벤트 및 충돌을 모니터링할 수 있습니다.

## 요구 사항

필요한 사항:

* [Microsoft Azure](http://azure.com) 구독. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다.
* Xcode 6 이상.
* SDK은 iOS 6.0 이상이 설치된 장치에서 실행됩니다.

## Application Insights 리소스 만들기

[Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. iOS 옵션을 선택합니다.

![새로 만들기, 개발자 서비스, Application Insights를 차례대로 클릭](./media/app-insights-ios/11-new.png)

열리는 블레이드에서 앱의 성능 및 사용 데이터를 볼 수 있습니다. 다음에 Azure에 로그인할 때 다시 이 블레이드로 돌아가려면 시작 화면에서 해당 타일을 찾아야 합니다. 또는 찾아보기를 클릭하여 찾아야 합니다.

## Mac용 Application Insights 다운로드

(아직 설치하지 않은 경우)

1. [Mac용 Application Insights](http://go.microsoft.com/fwlink/?LinkID=533209)를 다운로드합니다.

2. zip 파일의 압축을 풉니다.

3. 앱 아이콘을 클릭하여 Mac용 Application Insights를 시작합니다.

## <a name="signin"></a>Azure에 로그인

1. **로그인**을 클릭합니다.

2. Azure 계정으로 로그인합니다.

## 응용 프로그램에 SDK 설치

1. **통합**을 클릭하여 SDK 통합을 시작합니다.

2. 목록에서 Xcode 프로젝트를 선택하거나 **다른 프로젝트 열기**를 클릭하여 원하는 프로젝트를 찾은 후 **통합**을 클릭합니다.

3. Application Insights SDK를 설치할 폴더를 선택한 다음 **설치**를 클릭합니다.

4. 이 실행 스크립트를 빌드 단계 [실행 스크립트 추가 단계](http://hockeyapp.net/help/runscriptbuildphase/)에 추가합니다.

5. 누락된 프레임워크를 Xcode 프로젝트에 추가합니다.

6. Application Insights 프레임워크를 Xcode 프로젝트에 끌어 놓은 후 **다음**을 클릭합니다.

7. 대상에 대해 **SDK를 대상에 통합**을 선택합니다.

8. **새 구성 요소 만들기**를 클릭하여 Application Insights 포털에서 앱을 만듭니다.

9. 구독, 리소스 그룹을 선택하고 구성 요소 이름을 입력합니다. 대부분의 경우 앱 이름과 일치해야 합니다. **리소스 만들기** 단추를 눌러 확인합니다.

10. 올바른 구성 요소를 선택했는지 확인하고 **다음**을 클릭합니다.

11. 마법사에서 표시된 것처럼 소스 코드를 수정하고 **마침**을 클릭합니다.

12. **빌드 및 실행**을 클릭하여 iOS 시뮬레이터에서 앱을 시작합니다.

## 원격 분석 호출 삽입

`[MSAIApplicationInsights start]`가 호출되면 SDK에서 세션, 페이지 보기 및 모든 처리되지 않은 예외 또는 충돌을 추적하기 시작합니다.

다음과 같이 추가 이벤트를 추가할 수 있습니다.

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric"
                                        value:42.2];

## Application Insights에서 데이터 보기

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

<!---HONumber=Oct15_HO3-->