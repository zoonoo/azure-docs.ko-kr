---
title: "Azure Monitor 활동 로그를 사용하여 Azure 서비스 상태 추적 | Microsoft 문서"
description: "Azure에서 성능 저하 또는 서비스 중단이 발생한 시기를 확인합니다. "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Azure Monitor 활동 로그를 사용하여 Azure 서비스 상태 추적
Azure는 서비스 중단 또는 성능 저하가 있을 때마다 이 사실을 알립니다. Azure 포털에서 이러한 이벤트를 찾아볼 수 있으며, [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 프로그래밍 방식으로 전체 이벤트 집합에 액세스할 수도 있습니다.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>구독에 대한 서비스 상태 로그 찾아보기
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **홈**에서 **서비스 상태**라는 타일이 표시됩니다. 타일을 클릭합니다.
   
    ![홈](./media/insights-service-health/Insights_Home.png)
3. Azure에 모든 지역 목록이 표시됩니다. 지역을 클릭하면 최근 24시간 동안 구독에 영향을 준 서비스 인시던트를 보여 주는 활동 로그 쿼리가 표시됩니다.
   
    ![활동 로그 구독 서비스 상태](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. 테이블에서 해당 이벤트를 클릭하여 개별 인시던트에 대한 세부 사항을 볼 수 있습니다.
5. **Timespan**을 변경하면 더 긴 기간이 표시됩니다.

## <a name="next-steps"></a>다음 단계
* [웹 페이지의 가용성 및 응답성을 모니터링](../application-insights/app-insights-monitor-web-app-availability.md) 합니다.




<!--HONumber=Nov16_HO3-->


