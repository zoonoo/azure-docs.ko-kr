---
title: Azure Application Insights Funnels
description: "Funnels를 사용하여 고객이 응용 프로그램과 상호 작용하는 방법을 검색하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Application Insights Funnels를 사용하여 고객이 응용 프로그램을 사용하는 방법 검색

고객 환경을 이해하는 것이 비즈니스에 가장 중요합니다. 응용 프로그램이 여러 단계와 관련된 경우, 고객 대부분이 전체 프로세스를 거치는지 아니면 특정 시점에서 프로세스를 종료하는지를 알아야 합니다. 웹 응용 프로그램에서 일련의 단계를 거치는 것을 “깔때기”라고 합니다. Application Insights Funnels를 사용하여 사용자에 대한 정보를 얻고 단계별 전환율을 모니터링할 수 있습니다. 

## <a name="create-your-funnel"></a>깔때기 만들기
깔때기를 만들기 전에 먼저 답변하려는 질문에 관해 결정해야 합니다. 예를 들어, 고객 프로필을 보고 티켓을 만들어 몇 명의 사용자가 홈페이지를 보고 있는지 확인하려고 할 수 있습니다. 이 예제에서는 Fabrikam Fiber 회사 소유자가 성공적으로 고객 티켓을 생성한 고객의 비율을 확인하려고 합니다.

깔때기를 만들기 위해 수행하는 단계는 다음과 같습니다.

1. 깔때기 도구에서 새로 만들기 단추를 클릭합니다.
1. **시간 범위** 드롭다운에서 “지난 90일” 시간 범위를 선택합니다. “내 깔때기” 또는 “공유 깔때기”를 선택합니다.
1. **1단계** 드롭다운 목록에서 **인덱스** 이벤트를 선택합니다. 
1. **2단계** 드롭다운 목록에서 **고객** 이벤트를 선택합니다.
1. **3단계** 드롭다운 목록에서 **만들기** 이벤트를 선택합니다.
1. 깔때기에 이름을 추가하고 **저장**을 클릭합니다.

다음 그림에서는 깔때기 도구가 생성하는 데이터를 볼 수 있습니다. Fabrikam 소유자는 여기에서 지난 90일 동안 홈페이지를 방문한 고객 중 54.3%가 고객 티켓을 생성했다는 사실을 알 수 있습니다. 또한, 홈페이지에서 인덱스로 이동한 고객이 2,700명이 된다는 사실을 통해 새로 고침 문제가 있음을 짐작할 수 있습니다.


![깔때기 도구에 표시된 데이터](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>깔때기 기능
1. 앱이 샘플링된 경우 샘플링 배너가 표시됩니다. 배너를 클릭하면 샘플링을 끄는 방법이 표시된 컨텍스트 창이 열립니다. 
2. 깔때기를 [Power BI](app-insights-export-power-bi.md)로 내보낼 수 있습니다.
3. 단계를 클릭하면 오른쪽에서 더 많은 인사이트를 확인할 수 있습니다. 
4. 전환 기록에서 지난 90일 동안의 전환을 확인할 수 있습니다. 
5. 깔때기에서 사용자 도구로 이동하여 사용자에 대해 더 깊이 있게 파악할 수 있습니다. 각 단계마다 유용한 사용자 필터가 제공됩니다. 

## <a name="next-steps"></a>다음 단계
  * [사용 현황 개요](app-insights-usage-overview.md)
  * [사용자, 세션 및 이벤트](app-insights-usage-segmentation.md)
  * [보존](app-insights-usage-retention.md)
  * [통합 문서](app-insights-usage-workbooks.md)
  * [사용자 컨텍스트 추가](app-insights-usage-send-user-context.md)
  * [Power BI에 내보내기](app-insights-export-power-bi.md)

