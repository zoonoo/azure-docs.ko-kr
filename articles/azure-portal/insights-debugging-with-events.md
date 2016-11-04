---
title: 이벤트 및 감사 로그 보기
description: Azure 구독에서 발생한 이벤트를 모두 확인하는 방법을 알아봅니다.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2015
ms.author: robb

---
# 이벤트 및 감사 로그 보기
생성 및 삭제부터 액세스 권한 부여 또는 취소까지 Azure 리소스에 대해 수행하는 모든 작업은 Azure 리소스 관리자에 의해 완전히 감사됩니다. Azure 포털에서 이러한 로그를 찾아볼 수 있으며, [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 프로그래밍 방식으로 전체 이벤트 집합에 액세스할 수도 있습니다.

## Azure 구독에 영향을 주는 이벤트 찾기
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **찾아보기**를 클릭하고 **감사 로그**를 선택합니다. ![찾아보기 허브](./media/insights-debugging-with-events/Insights_Browse.png)
3. 지난 7일간 구독에 영향을 준 이벤트를 모두 보여 주는 블레이드가 열립니다. 맨 위에는 수준별 데이터를 보여 주는 차트가 있고, 그 아래에는 전체 로그 목록이 있습니다. ![모든 이벤트](./media/insights-debugging-with-events/Insights_AllEvents.png)

> [!NOTE]
> Azure 포털에서는 지정된 구독에 대한 최근 500개 이벤트만 볼 수 있습니다.
> 
> 

1. 로그 항목을 클릭하면 해당 항목을 구성하는 이벤트를 확인할 수 있습니다. 예를 들어 리소스 그룹에 항목을 배포할 때 다양한 리소스가 생성되거나 수정될 수 있습니다. 각 항목에 대해 다음을 확인할 수 있습니다.
   
   * 이벤트 **수준** - 예를 들어 추적할 사항(**Informational**)이거나, 문제가 발생한 경우 알아야 할 사항일 수 있습니다(**Error**).
   * **상태** - 최종 상태는 일반적으로 **Succeeded** 또는 **Failed**지만, 장기 실행 작업의 경우 **Accepted**일 수도 있습니다.
   * 이벤트가 발생한 *시기*
   * 작업을 수행한 *사용자*(있는 경우). 일부 작업은 사용자가 아니라 백 엔드 서비스에 의해 수행되므로 **호출자**가 없습니다.
   * 이벤트의 **상관 관계 ID** - 이 작업 집합의 고유 식별자입니다.
2. 여기서 세부 정보 블레이드로 이동하여 이벤트의 세부 정보를 확인할 수 있습니다.
   
    ![리소스 그룹](./media/insights-debugging-with-events/Insights_EventDetails.png)
   
    **실패** 이벤트의 경우 이 페이지에는 일반적으로 디버깅 목적의 유용한 세부 정보가 포함된 **하위 상태** 및 **속성** 섹션이 있습니다.

## 특정 로그로 필터링
특정 엔터티에 적용되거나 특정 유형인 이벤트를 보기 위해 **Filter** 명령을 클릭하여 감사 로그 블레이드를 필터링할 수 있습니다. 필터 블레이드를 사용하여 감사 로그 블레이드의 **시간 범위**를 변경할 수도 있습니다.

이 명령을 클릭하면 새 블레이드가 열립니다.

![Filter](./media/insights-debugging-with-events/Insights_EventFilter.png)

필터에는 다음 네 가지 유형이 있습니다.

1. 구독별
2. **리소스 그룹**별
3. **리소스 종류**별
4. 특정 **리소스**별 - 이 필터의 경우 관심 있는 리소스의 전체 *리소스 ID*를 붙여넣어야 합니다.

이벤트를 수행한 사람 또는 이벤트 수준을 기준으로 이벤트를 필터링할 수도 있습니다.

보려는 항목의 선택을 마쳤으면 블레이드 맨 아래에 있는 **업데이트** 단추를 클릭합니다.

## 특정 리소스에 영향을 주는 이벤트 모니터링
1. **찾아보기**를 클릭하여 관심 있는 리소스를 찾습니다. 전체 **리소스 그룹**에 대한 로그를 모두 표시할 수도 있습니다.
2. 리소스 블레이드에서 **이벤트** 타일이 표시될 때까지 아래로 스크롤합니다. ![이벤트 타일](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. 해당 타일을 클릭하여 선택한 리소스로만 필터링된 이벤트를 표시합니다. **Filter** 명령을 사용하여 시간 범위를 변경하거나 더 구체적인 필터를 적용할 수 있습니다.

## 다음 단계
* 이벤트가 발생할 때마다 [경고 알림을 수신](insights-receive-alert-notifications.md)합니다.
* [서비스 메트릭을 모니터링](insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* [서비스 상태를 추적](insights-service-health.md)하여 Azure에서 성능 저하 또는 서비스 중단이 발생한 시기를 확인합니다.

<!---HONumber=AcomDC_0810_2016-->