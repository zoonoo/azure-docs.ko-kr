---
title: Azure 유지 관리 일정(미리 보기) | Microsoft Docs
description: 고객은 유지 관리 예약 기능을 사용하여 Azure SQL 데이터 웨어하우스 서비스에서 새 기능, 업그레이드 및 패치를 출시하는 데 사용하는 필요한 예약 유지 관리 이벤트를 계획할 수 있습니다.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228196"
---
# <a name="change-a-maintenance-schedule"></a>유지 관리 일정 변경 

## <a name="portal"></a>포털
유지 관리 일정은 언제든지 업데이트하거나 변경할 수 있습니다. 그러나 선택한 인스턴스에서 현재 활성 유지 관리 주기가 진행되고 있으면 설정은 저장되며 확인된 다음 유지 관리 기간 중에만 활성화됩니다. 활성 유지 관리 이벤트 중에 데이터 웨어하우스를 모니터링하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/service-health/resource-health-overview). 

미리 보기에서는 7일 동안 유지 관리 기간 2개를 선택해야 합니다. 각 유지 관리 기간은 3~8시간 사이로 지정할 수 있으며 현재 사용 가능한 가장 짧은 기간 옵션은 3시간입니다. 유지 관리는 확인된 유지 관리 기간 내에 언제라도 수행될 수 있지만 확인된 해당 기간(사전 알림이 제공됨)이 아닐 때는 수행되지 않습니다. 그리고 이 기간 동안에는 서비스가 데이터 웨어하우스로 새 코드를 배포하므로 연결이 잠시 끊깁니다. 

## <a name="identifying-the-primary-and-secondary-windows"></a>기본 및 보조 기간 확인

기본 기간과 보조 기간은 각각 별도의 기간으로 지정해야 합니다. 예를 들어 기본 기간(화요일~목요일), 보조 기간(토요일~일요일)과 같이 지정해야 합니다.

포털에서 데이터 웨어하우스에 적용된 유지 관리 일정을 변경하려면 다음 단계를 완료합니다.
1.  [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2.  업데이트하려는 데이터 웨어하우스를 선택합니다. 개요 블레이드에서 페이지가 열립니다. 
3.  개요 블레이드에서 유지 관리 일정(미리 보기) 요약 링크를 클릭하거나, 왼쪽 리소스 메뉴에서 유지 관리 일정 옵션을 사용하여 유지 관리 일정 설정 페이지에 액세스할 수 있습니다.  

    ![개요 블레이드 옵션](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. 페이지 위쪽의 라디오 단추를 사용하여 기본 유지 관리 기간의 기본 설정 범위(일)를 확인할 수 있습니다. 여기서 선택한 범위에 따라 기본 기간이 평일인지 주말인지가 결정됩니다. 기간을 선택하면 아래쪽 드롭다운 값이 선택 항목에 따라 업데이트됩니다. 미리 보기 기간에는 사용 가능한 요일 옵션의 전체 집합이 아직 지원되지 않는 지역도 있을 수 있습니다. 이러한 값은 향후 업데이트될 예정입니다.

   ![유지 관리 설정 블레이드](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 아래쪽의 요일, 시작 시간 및 기간 드롭다운을 사용하여 기본 설정 기본/보조 유지 관리 기간을 선택합니다. 선택한 드롭다운 값에 따라 블레이드 맨 아래의 일정 요약이 업데이트됩니다.

#### <a name="dropdown-options"></a>드롭다운 옵션
- 요일: 선택한 기간 중에 유지 관리를 수행할 기본 설정 요일입니다.
- 시작 시간: 기본 설정 유지 관리 기간 시작 시간입니다.
- 기간: 기간의 기본 설정 지속 시간입니다.

  기본 설정 유지 관리 기간을 선택한 후 저장을 클릭합니다. 새 일정이 활성화되지 않았다는 확인 메시지가 표시됩니다. 유지 관리 예약을 아직 지원하지 않는 지역에서 일정을 저장하는 경우에는 다음 메시지가 표시됩니다. 선택한 지역에서 기능이 사용 가능해지면 설정이 저장 및 활성화됩니다.    

    ![해당 지역에서 활성화되지 않음 알림](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>다음 단계
- 로그 경고 규칙용 웹후크 작업에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
- [Azure Service Health](https://docs.microsoft.com/azure/service-health/service-health-overview)에 대해 자세히 알아봅니다.


