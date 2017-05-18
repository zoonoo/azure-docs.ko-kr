---
title: "Azure Application Insights를 사용한 웹 응용 프로그램의 사용자 재방문 주기 분석 | Microsoft Docs"
description: "앱으로 돌아온 사용자는 몇 명이나 되나요?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Application Insights를 사용한 웹 응용 프로그램의 사용자 재방문 주기 분석

[Azure Application Insights](app-insights-overview.md)의 재방문 주기 블레이드는 앱으로 돌아온 사용자 수와 특정 작업을 수행하거나 목표를 달성하는 빈도를 분석하는 데 도움을 줍니다. 예를 들어 게임 사이트를 실행하는 경우 게임에서 진 후에 사이트로 돌아오는 사용자 수와 이긴 후에 돌아온 사용자 수를 비교할 수 있습니다. 이러한 지식이 있으면 사용자 환경 및 비즈니스 전략을 둘 다 향상시킬 수 있습니다.

## <a name="get-started"></a>시작

Application Insights 포털의 재방문 주기 블레이드에서 아직 데이터가 표시되지 않으면 [사용 도구 시작 방법을 알아봅니다](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>재방문 주기 도구

![재방문 주기 도구](./media/app-insights-usage-retention/retention.png)

a. 개요 페이지는 항상 있습니다. 또한 여러 다른 매개 변수 설정을 사용하여 명명된 보고서를 저장할 수 있습니다. 리소스에 대한 액세스 권한이 있는 다른 사용자가 보고서를 볼 수 있도록 공유에 저장합니다.

b. 기본적으로 차트는 앱에서 수신된 사용자 지정 이벤트 또는 페이지 보기를 사용한 모든 사용자 수를 계산합니다. 사용자 하나 또는 서브넷을 선택하여 특정 사용자 활동을 집중적으로 확인합니다. 

c. 속성에 대해 하나 이상의 필터를 추가합니다. 예를 들어 특정 국가 또는 하위 지역에 있는 사용자에 집중할 수 있습니다. 필터를 설정한 후에 **업데이트**를 클릭합니다.

d. **기본값 복원**을 클릭하면 항상 사용자 지정 필터 및 이벤트 필터가 지워집니다.

e. 요약 차트는 선택한 기간에 걸친 합계를 보여 줍니다.

f. 이 표는 특정 기간 내에 돌아와서 선택한 작업을 반복한 사용자의 수를 표시합니다. 각 행은 표시된 기간에 선택된 작업 중 하나를 실행한 사용자의 코호트를 표시합니다. 행의 각 셀은 이후 기간에 1번 이상 돌아온 해당 코호트 수를 표시합니다. 일부 사용자는 둘 이상의 기간에 돌아올 수 있습니다.

차트의 시간 범위 동안 앱을 사용한 모든 사람은 차트에서 정확히 하나의 행에 표시됩니다. 각 사용자는 차트의 시간 범위 내에서 선택한 작업을 처음 수행했던 기간에 계산됩니다. 따라서 첫 번째 행에 더 큰 수가 포함되게 됩니다.


## <a name="use-business-events-to-track-retention"></a>비즈니스 이벤트를 사용하여 재방문 주기 추적

유용한 재방문 주기 분석 기능을 최대한 활용하려면 유의한 비즈니스 활동을 나타내는 이벤트를 측정합니다. 

예를 들어 많은 사용자는 앱에서 페이지를 열기만 하고 표시되는 게임을 재생하지 않을 수 있습니다. 따라서 페이지 보기만 추적하면 이전에 게임을 즐긴 후에 돌아와서 게임을 재생한 사용자 수를 정확히 추정할 수 없습니다. 돌아온 플레이어를 명확히 분선하기 위해 앱은 사용자가 실제로 게임을 할 때 사용자 지정 이벤트를 전송해야 합니다.  

주요 비즈니스 작업을 나타내는 사용자 지정 이벤트를 코딩하고 재방문 주기 분석에 사용하는 것이 좋습니다. 게임 결과를 캡처하려면 Application Insights에 사용자 지정 이벤트를 전송하는 코드 줄을 작성해야 합니다. 웹 페이지 코드 또는 Node.JS에서 작성할 경우 다음과 같습니다.

```JavaScript
    appinsights.trackEvent("won game");
```

또는 ASP.NET 서버 코드:

```C#
   telemetry.TrackEvent("won game");
```

[사용자 지정 이벤트 작성에 대해 자세히 알아봅니다](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>다음 단계

* [사용 현황 개요](app-insights-usage-overview.md)
* [사용자 및 세션](app-insights-usage-segmentation.md)
* [사용자 지정 이벤트 코딩](app-insights-api-custom-events-metrics.md)


