---
title: "Application Insights로 앱 상태 및 사용 현황 모니터링"
description: "Application Insights를 시작합니다. 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 7dd1289453fe146b446322570fcb1c0df74b4db2


---
# <a name="monitor-performance-in-web-applications"></a>웹 응용 프로그램의 성능 모니터링


응용 프로그램이 정상적으로 작동하는지 확인하고 오류가 발생하는지 신속하게 파악합니다. [Application Insights][시작]는 성능 문제 및 예외에 대한 정보와, 이러한 현상에 대한 근본 원인을 확인하고 진단하는 기능을 제공합니다.

Application Insights에서 Java 및 ASP.NET 웹 응용 프로그램과 서비스, WCF 서비스를 모니터링할 수 있습니다. 온-프레미스, 가상 컴퓨터에서 또는 Microsoft Azure 웹 사이트로 호스팅할 수 있습니다. 

Application Insights는 클라이언트쪽에서 iOS, Android 및 Windows 스토어 앱을 포함하여 다양한 장치 및 웹 페이지에서 원격 분석을 수행할 수 있습니다.

## <a name="a-namesetupaset-up-performance-monitoring"></a><a name="setup"></a>성능 모니터링 설정
Application Insights를 아직 프로젝트에 추가하지 않은 경우(프로젝트에 ApplicationInsights.config가 없음) 다음 방법 중 하나를 선택하여 작업을 시작합니다.

* [ASP.NET 웹 앱](app-insights-asp-net.md)
  * [예외 모니터링 추가](app-insights-asp-net-exceptions.md)
  * [종속성 모니터링 추가](app-insights-monitor-performance-live-website-now.md)
* [J2EE 웹앱](app-insights-java-get-started.md)
  * [종속성 모니터링 추가](app-insights-java-agent.md)

## <a name="a-nameviewaexploring-performance-metrics"></a><a name="view"></a>성능 메트릭 탐색
[Azure 포털](https://portal.azure.com)에서 응용 프로그램에 대해 설정한 Application Insights 리소스를 찾습니다. 개요 블레이드에 기본 성능 데이터가 표시됩니다.

무엇이든 클릭하면 추가 세부 정보와 장기간에 걸친 결과를 확인할 수 있습니다. 예를 들어 요청 타일을 클릭하고 시간 범위를 선택합니다.

![클릭하여 추가 데이터를 표시한 다음 시간 범위 선택](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

차트를 클릭하여 표시되는 메트릭을 선택하거나 새 차트를 추가하고 해당 메트릭을 선택합니다.

![그래프를 클릭하여 메트릭 선택](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **모든 메트릭의 선택을 취소합니다** . 메트릭은 그룹으로 구분되며 그룹 내의 모든 멤버를 선택하면 해당 그룹의 다른 멤버만 표시됩니다.
> 
> 

## <a name="a-namemetricsawhat-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>성능 메트릭의 의미 성능 타일 및 보고서
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

## <a name="a-namediagnosisadiagnosing-issues"></a><a name="diagnosis"></a>문제 진단
아래에는 성능 문제를 찾고 진단하기 위한 몇 가지 팁이 나와 있습니다.

* 웹 사이트의 작동이 중단되거나 응답이 잘못되거나 속도가 느려지는 경우 경고를 받도록 [웹 테스트][availability]를 설정합니다. 
* 요청 수를 다른 메트릭과 비교하여 오류 또는 느린 응답이 부하와 관련되어 있는지 확인합니다.
* 코드에서 [검사 추적 문을 삽입 및 검색][diagnostic]하여 문제를 파악합니다.

## <a name="a-namenextanext-steps"></a><a name="next"></a>다음 단계
[웹 테스트][availability] - 전 세계에서 웹 요청이 일정한 간격으로 응용 프로그램에 전송되도록 합니다.

[진단 추적 캡처 및 검색][diagnostic] - 추적 호출을 삽입하고 결과를 확인하여 문제를 파악합니다.

[사용 현황 추적][usage] - 사용자의 응용 프로그램 사용 방식을 파악합니다.

[문제 해결][qna] - 질문과 대답

## <a name="video"></a>비디오
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[시작]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md





<!--HONumber=Nov16_HO3-->


