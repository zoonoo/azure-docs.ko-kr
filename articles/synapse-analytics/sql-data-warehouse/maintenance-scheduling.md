---
title: 시냅스 SQL 풀의 유지 관리 일정
description: 유지 관리 일정을 통해 고객은 Azure Synapse Analytics에서 새로운 기능, 업그레이드 및 패치를 롤아웃하는 데 사용하는 필요한 예약된 유지 관리 이벤트를 계획할 수 있습니다.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 43fc32e910c51e8b70e15aa49584a18e5b703fca
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631602"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>유지 관리 일정을 사용하여 서비스 유지 관리 및 업데이트 관리

유지 관리 일정 기능은 Azure Synapse Analytics 내에서 서비스 상태 계획 유지 관리 알림, 리소스 상태 확인 모니터 및 유지 관리 일정 서비스를 통합합니다.

유지 관리 일정을 사용하여 새 기능, 업그레이드 및 패치를 편리하게 받을 수 있는 시간 창을 선택해야 합니다. 7일 이내에 기본 및 보조 유지 관리 기간을 선택해야 하며 각 창은 별도의 일 범위 내에 있어야 합니다.

예를 들어 토요일 22:00~일요일 01:00의 기본 창을 예약한 다음 수요일 19:00~22:00의 보조 창을 예약할 수 있습니다. 기본 유지 관리 기간 중에 유지 관리를 수행할 수 없는 경우 보조 유지 관리 기간 동안 유지 관리를 다시 시도합니다. 서비스 유지 관리는 주 및 보조 창 모두에서 발생할 수 있습니다. 모든 유지 보수 작업을 신속하게 완료하기 위해 DW400c 및 낮은 데이터 웨어하우스 계층은 지정된 유지 관리 기간 외부에서 유지 관리를 완료할 수 있습니다.

새로 생성된 모든 데이터 웨어하우스 인스턴스에는 배포 중에 시스템 정의 유지 관리 일정이 적용됩니다. 배포가 완료되는 즉시 일정을 편집할 수 있습니다.

유지 관리 기간은 3~8시간 사이일 수 있지만 데이터 웨어하우스가 기간 동안 오프라인 상태가 되는 것은 아닙니다. 유지 관리는 해당 기간 내에서 언제든지 발생할 수 있으며 서비스가 데이터 웨어하우스에 새 코드를 배포할 때 해당 기간 동안 ~5-6분 동안 단일 연결이 끊어지도록 예상해야 합니다. DW400c 이하 이하는 유지 관리 기간 동안 다양한 시간에 연결에 여러 번 짧은 손실이 발생할 수 있습니다. 유지 관리가 시작되면 모든 활성 세션이 취소되고 커밋되지 않은 트랜잭션이 롤백됩니다. 인스턴스 가동 중지 시간을 최소화하려면 선택한 유지 관리 기간 전에 데이터 웨어하우스에 장기 실행 트랜잭션이 없어야 합니다.

시간에 민감한 업데이트를 배포해야 하는 경우가 아니면 지정된 유지 관리 기간 내에서 모든 유지 관리 작업이 완료되어야 합니다. 데이터 웨어하우스는 예약된 유지 관리 중에 일시 중지되는 경우 다시 시작 작업 중에 업데이트됩니다. 데이터 웨어하우스 유지 관리가 완료된 즉시 알림을 받게 됩니다.

## <a name="alerts-and-monitoring"></a>경고 및 모니터링

서비스 상태 알림 및 리소스 상태 검사 모니터와의 통합으로 고객은 예정된 유지 관리 활동의 정보를 지속적으로 파악할 수 있습니다. 이 자동화는 Azure 모니터를 활용합니다. 예정된 유지 관리 이벤트의 알림을 어떻게 받을 것인지 결정할 수 있습니다. 또한 가동 중지 시간을 관리하고 운영 상의 영향을 최소화하는 데 도움이 되는 자동화된 흐름을 선택할 수 있습니다.

24시간 사전 알림은 DWC400c 및 하위 계층에 적합하지 않은 모든 유지 관리 이벤트 앞에 옵니다.

> [!NOTE]
> 시간 중요한 업데이트를 배포해야 하는 경우 고급 알림 시간이 크게 단축될 수 있습니다.

유지 관리가 수행된다는 사전 알림을 받았지만 알림에서 해당 기간 동안 유지 관리를 수행할 수 없는 경우 취소 알림을 받게 됩니다. 그러면 유지 관리는 예약된 다음 유지 관리 기간에 다시 시작됩니다.

모든 활성 유지 관리 이벤트는 **서비스 상태 - 계획된 유지 관리** 섹션에 표시됩니다. 서비스 상태 기록에는 지난 이벤트의 전체 레코드가 포함됩니다. 활성 이벤트가 진행되는 중에 Azure Service Health 검사 포털 대시보드를 통해 유지 관리를 모니터링할 수 있습니다.

### <a name="maintenance-schedule-availability"></a>유지 관리 일정 사용 가능성

선택한 지역에서 유지 관리 예약 기능이 아직 제공되지 않더라도 언제든지 유지 관리 일정을 확인하고 편집할 수 있습니다. 해당 지역에서 유지 관리 일정을 사용할 수 있게 되면 Synapse SQL 풀에서 식별된 일정이 즉시 활성화됩니다.

## <a name="view-a-maintenance-schedule"></a>유지 관리 일정 보기

기본적으로 새로 생성된 모든 데이터 웨어하우스 인스턴스에는 배포 중에 8시간의 기본 및 보조 유지 관리 기간이 적용됩니다. 위에서 언급한 대로 배포가 완료되는 즉시 창을 변경할 수 있습니다. 사전 알림 없이는 지정된 유지 관리 기간이 아닐 때 유지 관리가 수행되지 않습니다.

Synapse SQL 풀에 적용된 유지 관리 일정을 보려면 다음 단계를 완료하십시오.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 보려는 Synapse SQL 풀을 선택합니다.
3. 선택한 Synapse SQL 풀은 개요 블레이드에서 열립니다. 데이터 웨어하우스에 적용되는 유지 관리 일정은 **유지 관리 일정**아래에 나타납니다.

![개요 블레이드](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>유지 관리 일정 변경

유지 관리 일정은 언제든지 업데이트하거나 변경할 수 있습니다. 선택한 인스턴스가 활성 유지 관리 주기를 통해 진행되는 경우 설정이 저장됩니다. 식별된 다음 유지 관리 기간 중에 활성화됩니다. 활성 유지 관리 이벤트 중에 데이터 웨어하우스를 모니터링하는 방법에 대해 [자세히 알아보세요](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="identifying-the-primary-and-secondary-windows"></a>기본 및 보조 기간 확인

기본 및 보조 기간은 개별적인 날짜 범위여야 합니다. 예를 들어 기본 기간을 화요일 - 목요일로, 보조 기간을 토요일 - 일요일로 할 수 있습니다.

Synapse SQL 풀의 유지 관리 일정을 변경하려면 다음 단계를 완료하십시오.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 업데이트할 Synapse SQL 풀을 선택합니다. 개요 블레이드에서 페이지가 열립니다.
개요 블레이드에서 **유지 관리 일정 요약** 링크를 선택하여 유지 관리 일정 설정에 대한 페이지를 엽니다. 또는 왼쪽 리소스 메뉴에서 **유지 관리 일정** 옵션을 선택합니다.

    ![개요 블레이드 옵션](./media/maintenance-scheduling/maintenance-change-option.png)

3. 페이지 위쪽의 옵션을 사용하여 기본 유지 관리 기간의 기본 설정 범위(일)를 확인합니다. 여기서 선택한 범위에 따라 기본 기간이 평일인지 주말인지가 결정됩니다. 기간을 선택하면 드롭다운 값이 업데이트됩니다.
미리 보기 기간에는 사용 가능한 **요일** 옵션의 전체 집합이 아직 지원되지 않는 지역도 있을 수 있습니다.

   ![유지 관리 설정 블레이드](./media/maintenance-scheduling/maintenance-settings-page.png)

4. 드롭다운 목록 상자를 사용하여 기본 설정된 기본 및 보조 유지 관리 기간을 선택합니다.
   - **요일**: 선택한 기간 중에 유지 관리를 수행할 기본 설정 요일입니다.
   - **시작 시간**: 유지 관리 기간에 대한 기본 설정 시작 시간입니다.
   - **시간 범위**: 시간 범위의 기본 설정 지속 시간입니다.

   선택한 값에 따라 블레이드 맨 아래의 **일정 요약**이 업데이트됩니다.
  
5. **저장**을 선택합니다. 새 일정이 현재 활성화되었음을 확인하는 메시지가 표시됩니다.

   유지 관리 예약을 아직 지원하지 않는 지역에서 일정을 저장하는 경우에는 다음 메시지가 표시됩니다. 선택한 지역에서 기능이 사용 가능해지면 설정이 저장 및 활성화됩니다.

   ![지역 가용성에 대한 메시지](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하여 경고를 작성, 확인 및 관리하는 방법에 대해 [자세히 알아봅니다](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
- 로그 경고 규칙에 대한 웹후크 작업에 대해 [자세히 알아보세요.](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- 작업 그룹을 만들고 관리하는 방법을 [자세히 알아봅니다](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
- Azure Service Health에 대해 [자세히 알아봅니다](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
