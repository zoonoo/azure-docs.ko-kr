---
title: "Azure Application Insights의 라이브 메트릭 스트림 | Microsoft Docs"
description: "웹앱 성능을 실시간으로 모니터링하여 릴리스 또는 다른 변경 작업의 효과를 관찰합니다."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc6acf7b24bb017e4a93df1c15ed493a4442d6b5
ms.openlocfilehash: 917261e32845b53f2f2f3d12f60ca5fa87d441c7
ms.lasthandoff: 02/27/2017


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>라이브 메트릭 스트림: 면밀한 모니터링을 위한 인스턴트 메트릭
라이브 메트릭 스트림은 거의 실시간에 가까운&1;초의 대기 시간으로 [Application Insights](app-insights-overview.md) 메트릭을 바로 표시합니다. 이 즉시 모니터링 기능은 새 빌드를 릴리스하고 예상한 대로 작동하는지 확인하려고 할 때 또는 실시간으로 인시던트를 조사할 때 매우 유용합니다.

[메트릭 탐색기](app-insights-metrics-explorer.md)와 달리, 라이브 메트릭 스트림은 고정된 메트릭 집합을 표시합니다. 해당 데이터는 차트에 있는 동안만 지속된 후 삭제됩니다.

라이브 메트릭 스트림 데이터는 무료이므로 청구서에 비용이 추가되지 않습니다.

![라이브 메트릭 스트림 동영상](./media/app-insights-live-stream/youtube.png) [라이브 메트릭 스트림 동영상](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

![개요 블레이드에서 라이브 스트림 클릭](./media/app-insights-live-stream/live-stream.png)



## <a name="live-failures"></a>라이브 실패

실패 또는 예외가 기록된 경우 라이브 스트림은 샘플을 선택합니다. **일시 중지**를 클릭하여 특정 샘플을 유지하고 세부 정보 표시할 이벤트를 선택합니다.

![샘플링된 라이브 실패](./media/app-insights-live-stream/live-stream-failures.png)


라이브 메트릭 스트림은 최신 버전의 [웹용 Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)에서 사용할 수 있습니다.

## <a name="filter-by-server-instance"></a>서버 인스턴스별 필터링

특정 서버 역할 인스턴스를 모니터링하려는 경우 서버별로 필터링할 수 있습니다.

![샘플링된 라이브 실패](./media/app-insights-live-stream/live-stream-filter.png)


## <a name="troubleshooting"></a>문제 해결

데이터가 없나요? 라이브 메트릭 스트림은 다른 Application Insights 원격 분석과 다른 포트를 사용합니다. [이러한 포트](app-insights-ip-addresses.md)가 방화벽에서 열려 있는지 확인하세요.



## <a name="next-steps"></a>다음 단계
* [Application Insights를 사용하여 사용량 모니터링](app-insights-overview-usage.md)
* [진단 검색 사용](app-insights-diagnostic-search.md)


