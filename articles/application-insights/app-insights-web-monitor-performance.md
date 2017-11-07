---
title: "Application Insights로 앱 상태 및 사용 현황 모니터링"
description: "Application Insights를 시작합니다. 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mbullwin
ms.openlocfilehash: 32000f5a85c84913aa820df00f1bb7f877bf037f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-performance-in-web-applications"></a>웹 응용 프로그램의 성능 모니터링


응용 프로그램이 정상적으로 작동하는지 확인하고 오류가 발생하는지 신속하게 파악합니다. [Application Insights][start]는 성능 문제 및 예외에 대한 정보와, 이러한 현상에 대한 근본 원인을 확인하고 진단하는 기능을 제공합니다.

Application Insights에서 Java 및 ASP.NET 웹 응용 프로그램과 서비스, WCF 서비스를 모니터링할 수 있습니다. 온-프레미스, 가상 컴퓨터에서 또는 Microsoft Azure 웹 사이트로 호스트할 수 있습니다. 

Application Insights는 클라이언트 쪽에서 iOS, Android 및 Windows 스토어 앱을 포함한 다양한 장치 및 웹 페이지에서 원격 분석을 수행할 수 있습니다.

>[!Note]
> 웹 응용 프로그램에서 느리게 작동하는 페이지 찾기에 사용할 수 있는 새 환경을 만들었습니다. 액세스 권한이 없는 경우 [미리 보기 블레이드](app-insights-previews.md)로 미리 보기 옵션을 구성하여 활성화합니다. [대화형 성능 조사를 사용하여 성능 병목 현상 찾기 및 해결](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation)에서 이 새로운 환경에 대해 읽어 보세요.

## <a name="setup"></a>성능 모니터링 설정
Application Insights를 아직 프로젝트에 추가하지 않은 경우(프로젝트에 ApplicationInsights.config가 없음) 다음 방법 중 하나를 선택하여 작업을 시작합니다.

* [ASP.NET 웹 앱](app-insights-asp-net.md)
  * [예외 모니터링 추가](app-insights-asp-net-exceptions.md)
  * [종속성 모니터링 추가](app-insights-monitor-performance-live-website-now.md)
* [J2EE 웹앱](app-insights-java-get-started.md)
  * [종속성 모니터링 추가](app-insights-java-agent.md)

## <a name="view"></a>성능 메트릭 탐색
[Azure 포털](https://portal.azure.com)에서 응용 프로그램에 대해 설정한 Application Insights 리소스를 찾습니다. 개요 블레이드에 기본 성능 데이터가 표시됩니다.

무엇이든 클릭하면 추가 세부 정보와 장기간에 걸친 결과를 확인할 수 있습니다. 예를 들어 요청 타일을 클릭하고 시간 범위를 선택합니다.

![클릭하여 추가 데이터를 표시한 다음 시간 범위 선택](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

차트를 클릭하여 표시되는 메트릭을 선택하거나 새 차트를 추가하고 해당 메트릭을 선택합니다.

![그래프를 클릭하여 메트릭 선택](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **모든 메트릭의 선택을 취소합니다** . 메트릭은 그룹으로 구분되며 그룹 내의 모든 멤버를 선택하면 해당 그룹의 다른 멤버만 표시됩니다.

## <a name="metrics"></a>성능 메트릭의 의미 성능 타일 및 보고서
다양한 성능 메트릭을 가져올 수 있습니다. 먼저 응용 프로그램 블레이드에 기본적으로 표시되는 메트릭부터 살펴보겠습니다.

### <a name="requests"></a>요청
지정한 기간에 수신된 HTTP 요청의 수입니다. 이 메트릭을 다른 보고서의 결과와 비교하여 로드의 변화에 따른 앱의 동작 방식을 확인합니다.

HTTP 요청에는 페이지, 데이터 및 이미지에 대한 모든 GET 또는 POST 요청이 포함됩니다.

특정 URL에 대한 요청 수를 가져오려면 타일을 클릭합니다.

### <a name="average-response-time"></a>평균 응답 시간
웹 요청이 응용 프로그램에 도착하는 시점과 응답이 반환되는 시점 사이의 시간을 측정합니다.

점은 이동 평균을 보여 줍니다. 요청 수가 많으면 그래프에서는 요청 수가 크게 늘어나거나 줄어들지 않아도 일부 점이 평균에서 벗어날 수 있습니다.

요청 수의 비정상적인 증가를 확인합니다. 일반적으로는 요청 수가 증가하면 응답 시간은 길어집니다. 그러나 이처럼 요청 수와 응답 시간이 비례하지 않는 경우에는 앱이 사용하는 서비스의 용량 또는 CPU와 같은 리소스 제한에 도달해서 일 수 있습니다.

특정 URL에 대한 요청 시간을 가져오려면 타일을 클릭합니다.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>가장 느린 요청
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

성능 튜닝이 필요할 수 있는 요청을 표시합니다.

### <a name="failed-requests"></a>실패한 요청
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

catch되지 않은 예외를 throw한 요청의 수입니다.

타일을 클릭하면 특정 오류의 세부 정보를 확인할 수 있으며 개별 요청을 선택하면 해당 세부 정보를 볼 수 있습니다. 

오류의 대표 샘플만 개별적으로 조사할 수 있도록 보관됩니다.

### <a name="other-metrics"></a>기타 메트릭
표시할 수 있는 기타 메트릭을 보려면 그래프를 클릭하고 모든 메트릭의 선택을 취소하여 사용 가능한 전체 집합을 표시합니다. (i)를 클릭하면 각 메트릭의 정의가 표시됩니다.

![모든 메트릭의 선택을 취소하여 전체 집합 표시](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

메트릭을 선택하면 같은 차트에 표시할 수 없는 다른 메트릭은 사용하지 않도록 설정됩니다.

## <a name="set-alerts"></a>경고 설정
메트릭의 비정상적인 값에 대한 알림을 메일로 받으려면 경고를 추가합니다. 계정 관리자나 특정 메일 주소로 메일을 보내도록 선택할 수 있습니다.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

다른 속성에 앞서 리소스를 설정합니다. 성능 또는 사용 메트릭에 대한 경고를 설정하려면 webtest 리소스를 선택하지 마세요.

임계값을 입력하라는 단위에 주의하세요.

*경고 추가 단추가 보이지 않습니다.* -이것은 읽기 전용 액세스 권한이 있는 그룹 계정입니까? 계정 관리자에게 확인하세요.

## <a name="diagnosis"></a>문제 진단
아래에는 성능 문제를 찾고 진단하기 위한 몇 가지 팁이 나와 있습니다.

* 웹 사이트의 작동이 중단되거나 응답이 잘못되거나 속도가 느려지는 경우 경고를 받도록 [웹 테스트][availability]를 설정합니다. 
* 요청 수를 다른 메트릭과 비교하여 오류 또는 느린 응답이 부하와 관련되어 있는지 확인합니다.
* 코드에서 [검사 추적 문을 삽입 및 검색][diagnostic]하여 문제를 파악합니다.
* [라이브 메트릭 스트림][livestream]을 사용하여 작업에서 웹앱을 모니터링합니다.
* [스냅숏 디버거][snapshot]를 사용하여 .Net 응용 프로그램의 상태를 캡처합니다.

>[!Note]
> Application Insights 성능 조사를 대화형 전체 화면 환경으로 전환하는 중입니다. 다음 설명서는 먼저 새 환경에 대해 설명한 다음, 전환 상태 전반에서 계속 사용할 수 있는 이전 환경에 여전히 액세스해야 하는 경우 그에 대해 검토합니다.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>대화형 전체 화면 성능 조사를 통하여 성능 병목 현상 찾기 및 해결

새 Application Insights 대화형 성능 조사를 사용하여 웹앱에서 느리게 작동하는 작업을 검토합니다. 느린 특정 작업을 신속하게 선택하고 [프로파일러](app-insights-profiler.md)를 사용하여 코드에 대한 작업 속도를 저하시키는 근본 원인을 파악할 수 있습니다. 선택한 작업이 표시된 새 기간 분포를 사용하면 해당 환경이 얼마나 고객에게 나쁜지를 빠르게 즉시 가늠할 수 있습니다. 실제 느린 작업마다 얼마나 많은 사용자 상호 작용이 영향을 받는지 확인할 수 있습니다. 다음 예제에서는 고객/세부 정보 가져오기 작업에 대한 환경을 자세히 살펴보겠습니다. 기간 배포에서 세 가지 스파이크가 있음을 볼 수 있습니다. 맨 왼쪽 스파이크는 약 400ms이며 탁월한 응답 환경을 나타냅니다. 중간 스파이크는 약 1.2s로 보통의 환경을 나타냅니다. 마지막으로 3.6s에서 99 백분위수 환경을 나타내는 작은 스파이크는 고객 불만족으로 이어질 가능성이 높습니다. 그러한 환경은 동일한 작업에 대해 탁월한 환경보다 10배 더 느립니다. 

![고객/세부 정보 가져오기 세 가지 기간 스파이크](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

이 작업에 대한 사용자 환경을 더 잘 이해하기 위해 더 긴 시간 범위를 선택할 수 있습니다. 그런 다음 작업이 특별히 느려지는 특정 기간에서 시간 범위를 좁힐 수 있습니다. 다음 예제에서는 기본 24시간의 시간 범위에서 7일 시간 범위로 전환한 다음, 화요일 12일부터 수요일 13일 사이의 9:47 ~ 12:47 기간으로 확대하였습니다. 기간 배포와의 샘플 및 프로파일러 추적의 수는 모두 오른쪽에 업데이트되었습니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

느린 환경에서 범위를 좁히기 위해 95와 99 백분위수 사이로 떨어지는 기간으로 확대합니다. 이는 특히 느려진 사용자 상호 작용의 4%를 나타냅니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

이제 샘플 단추를 클릭하거나 대표 프로파일러 추적에서 프로파일러 추적 단추를 클릭하여 대표 샘플 중 하나를 살펴볼 수 있습니다. 이 예제에는 관심 기간 및 범위 기간에서 고객/세부 정보 가져오기에 대해 수집된 4개의 추적이 있습니다.

사용자의 코드가 아니라 호출을 코딩하는 종속성에서 문제가 나타나는 경우가 있습니다. 성능 심사 보기에서 종속성 탭을 전환하여 그러한 느린 종속성을 조사할 수 있습니다. 기본적으로 성능 보기는 추세 평균이지만, 실제 살펴보려는 것은 95 백분위수입니다(또는 99 백분위수, 매우 완성도 높은 서비스를 모니터링하는 경우). 다음 예제에서는 PUT fabrikamaccount를 호출하는 느린 Azure BLOB 종속성에 초점을 맞춥니다. 양호한 환경은 약 40ms로 클러스터링하지만, 동일한 종속성에 대한 느린 호출은 약 120ms로 클러스터링하여 세 배 더 느립니다. 해당 작업 속도가 현저하게 느려지는 원인이 되므로 이러한 호출은 많이 만들지 않습니다. 작업 탭을 사용할 수 있는 것처럼 대표 샘플 및 프로파일러 추적을 자세히 살펴볼 수 있습니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

대화형 전체 화면 성능 조사에 새롭게 추가된 매우 강력한 또다른 기능은 정보를 사용한 통합입니다. Application Insights는 정보를 통해 응답성 회귀를 검색하고 노출할 수 있으며, 사용자가 집중하려는 샘플 설정에서 공용 속성을 식별하는 데 도움이 됩니다. 사용 가능한 모든 정보를 살펴보기에 가장 좋은 방법은 30일 시간 범위로 전환한 다음, 전체를 선택하여 지난 달 동안의 모든 작업에 걸쳐 정보를 확인하는 것입니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

새 성능 심사 보기의 Application Insights는 문자 그대로 손쉽게 웹앱 사용자를 위한 좋지 않은 환경으로 이어지는 정보를 수많은 데이터 속에서 찾을 수 있습니다.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>사용되지 않음: 좁은 블레이드 레거시 성능 조사를 사용하여 성능 병목 현상 찾기 및 해결

레거시 Application Insights 블레이드 성능 조사를 사용하여 전반적인 성능이 저하되는 웹앱의 영역을 찾을 수 있습니다. 저하된 특정 페이지를 찾을 수 있으며, [프로파일러](app-insights-profiler.md)를 사용하여 코드에 대한 이러한 문제의 근본 원인을 추적할 수 있습니다. 

### <a name="create-a-list-of-slow-performing-pages"></a>느리게 작동하는 페이지 목록 만들기 

성능 문제를 찾기 위한 첫 번째 단계는 느린 응답 페이지의 목록을 가져오는 것입니다. 아래 스크린샷은 성능 블레이드를 사용하여 추가 조사를 위해 잠재적인 페이지의 목록을 가져오는 방법을 보여 줍니다. 이 페이지에서 약 오후 6시와 약 오후 10시에 다시 앱의 응답 시간에 저하가 있었음을 신속하게 확인할 수 있습니다. 또한 고객/세부 정보 가져오기 작업에 507.05밀리초의 중앙 응답 시간으로 일부 장기 실행 작업이 있었음을 확인할 수 있습니다. 

![Application Insights 대화형 성능](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>특정 페이지에 대해 드릴다운

앱의 성능에 대한 스냅숏을 만든 후 느리게 작동하는 특정 작업에 대한 자세한 세부 정보를 얻을 수 있습니다. 아래와 같이 세부 정보를 보려면 목록에서 모든 작업을 클릭합니다. 차트에서 성능이 종속성을 기반으로 했는지 확인할 수 있습니다. 또한 다양한 응답 시간을 경험한 사용자 수를 확인할 수도 있습니다. 

![Application insights 작업 블레이드](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>특정 기간에 대해 드릴다운

조사할 특정 시점을 식별한 후 성능 저하를 발생시켰을 수도 있는 특정 작업을 살펴보도록 더 자세히 드릴다운합니다. 특정 시점을 클릭할 때 다음과 같은 페이지의 세부 정보를 가져옵니다. 아래 예제에서 서버 응답 코드 및 작업 기간과 함께 지정된 기간에 대해 나열된 작업을 볼 수 있습니다. 또한 이 정보를 개발 팀에 전송해야 하는 경우 TFS 작업 항목을 열기 위한 url이 있습니다.

![Application Insights 시간 조각](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>특정 작업에 대해 드릴다운

조사할 특정 시점을 식별한 후 성능 저하를 발생시켰을 수도 있는 특정 작업을 살펴보도록 더 자세히 드릴다운합니다. 아래와 같이 작업의 세부 정보를 보려면 목록에서 작업을 클릭합니다. 이 예제에서 작업이 실패하고 Application Insights에서 응용 프로그램에서 throw했던 예외의 세부 정보를 제공한 것을 볼 수 있습니다. 다시, 이 블레이드에서 TFS 작업 항목을 쉽게 만들 수 있습니다.

![Application insights 작업 블레이드](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>다음 단계
[웹 테스트][availability] - 전 세계에서 웹 요청이 일정한 간격으로 응용 프로그램에 전송되도록 합니다.

[진단 추적 캡처 및 검색][diagnostic] - 추적 호출을 삽입하고 결과를 확인하여 문제를 파악합니다.

[사용 현황 추적][usage] - 사용자의 응용 프로그램 사용 방식을 파악합니다.

[문제 해결][qna] - 질문과 대답



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



