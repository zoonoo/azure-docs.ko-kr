---
title: "ASP.NET Core용 Azure Application Insights | Microsoft Docs"
description: "응용 프로그램의 가용성, 성능 및 사용 현황을 모니터링합니다."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: d86495eea467977f6c079de72e2b49a2a1da2b60
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core용 Application Insights
[Application Insights](app-insights-overview.md)를 사용하여 웹 응용 프로그램의 가용성, 성능 및 사용량을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

![예](./media/app-insights-asp-net-core/sample.png)

[Microsoft Azure](http://azure.com)를 구독해야 합니다. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다. 팀에서 Azure를 단체 구독할 수도 있습니다. 소유자에게 Microsoft 계정을 사용하여 추가해 달라고 요청하세요.

## <a name="getting-started"></a>시작

* Visual Studio 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights 구성** 또는 **추가 > Application Insights**를 선택합니다. [자세히 알아봅니다](app-insights-asp-net.md).
* 이러한 메뉴 명령이 보이지 않으면 [수동 시작 가이드](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)를 따릅니다. Visual Studio 2017 이전 버전을 사용하여 프로젝트를 만든 경우 이 작업을 수행해야 할 수도 있습니다.

## <a name="using-application-insights"></a>Application Insights 사용
[Microsoft Azure Portal](https://portal.azure.com)에 로그인하고, **모든 리소스** 또는 **Application Insights**를 선택하고, 만든 리소스를 선택하여 앱을 모니터링합니다.

별도의 브라우저 창에서 잠시 앱을 사용합니다. Application Insights 차트에 데이터가 표시되는 것을 볼 수 있습니다. (새로 고침을 클릭해야 할 수 있습니다.) 개발하는 동안은 소량의 데이터만 표시되지만, 앱을 게시하고 많은 사용자가 확보되면 차트가 흥미로워집니다. 

개요 페이지에는 서버 응답 시간, 페이지 로드 시간, 실패한 요청의 수 등 핵심 성능 차트가 표시됩니다. 차트와 데이터를 더 보려면 아무 차트나 클릭합니다.

포털의 보기는 다음 세 가지 주요 범주로 나뉩니다.

* [메트릭 탐색기](app-insights-metrics-explorer.md)에는 응답 시간, 실패율 또는 사용자가 [API](app-insights-api-custom-events-metrics.md)로 만든 메트릭 및 개수의 테이블과 그래프가 표시됩니다. 속성 값에 따라 데이터를 필터링하고 분할하여 앱 및 사용자에 대한 이해를 향상시킵니다.
* [검색 탐색기](app-insights-diagnostic-search.md)에는 특정 요청, 예외, 로그 추적 또는 [API](app-insights-api-custom-events-metrics.md)로 만든 이벤트와 같은 개별적인 이벤트가 나열됩니다. 이벤트를 필터링하고 검색하여 관련된 이벤트를 탐색하여 문제를 조사합니다.
* [분석](app-insights-analytics.md) 은 원격 분석에 대해 SQL 유사 쿼리를 실행할 수 있도록 하는 강력한 분석 및 진단 도구입니다.

## <a name="alerts"></a>경고
* 실패율 및 기타 메트릭의 비정상적인 변경 내용을 알려 주는 [사전 진단 경고](app-insights-proactive-diagnostics.md)를 자동으로 가져옵니다.
* 전 세계에서 지속적으로 웹 사이트를 테스트하고 테스트가 실패하면 즉시 전자 메일을 받으려면 [가용성 테스트](app-insights-monitor-web-app-availability.md) 를 설정합니다.
* 응답 시간 또는 예외 속도 같은 메트릭이 허용 한도를 벗어나는지 파악하려면 [메트릭 경고](app-insights-monitor-web-app-availability.md) 를 설정합니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>공개 소스
[코드를 읽고 참여하기](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>다음 단계
* [웹 페이지에 원격 분석을 추가](app-insights-javascript.md) 하여 페이지 사용 현황 및 성능을 모니터링합니다.
* [종속성을 모니터링](app-insights-asp-net-dependencies.md) 합니다.
* [API를 사용](app-insights-api-custom-events-metrics.md) 합니다.
* [가용성 테스트](app-insights-monitor-web-app-availability.md) 는 사용자의 앱을 전 세계에서 지속적으로 확인합니다. 

