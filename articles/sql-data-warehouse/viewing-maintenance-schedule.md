---
title: Azure 유지 관리 일정(미리 보기) | Microsoft Docs
description: 고객은 유지 관리 예약 기능을 사용하여 Azure SQL Data Warehouse 서비스에서 새 기능, 업그레이드 및 패치를 출시하는 데 사용하는 필요한 예약된 유지 관리 이벤트를 계획할 수 있습니다.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839978"
---
# <a name="view-a-maintenance-schedule"></a>유지 관리 일정 보기 

## <a name="portal"></a>포털

기본적으로 새로 만드는 모든 Azure SQL Data Warehouse 인스턴스에는 배포 중에 8시간의 기본 및 보조 유지 관리 기간이 적용됩니다. 기간은 배포가 완료되는 즉시 변경할 수 있습니다. 사전 알림 없이 지정된 유지 관리 기간 외에는 유지 관리가 수행되지 않습니다.

데이터 웨어하우스에 적용된 유지 관리 일정을 확인하려면 다음 단계를 완료합니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  확인하려는 데이터 웨어하우스를 선택합니다. 
3.  개요 블레이드에서 선택한 데이터 웨어하우스가 열립니다. 데이터 웨어하우스에 적용 되는 유지 관리 일정 아래에 나타납니다 **유지 관리 일정**합니다.

![개요 블레이드](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>다음 단계
- Azure Monitor를 사용하여 경고를 작성, 확인 및 관리하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage).
- 로그 경고 규칙용 웹후크 작업에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
- 작업 그룹을 만들고 관리하는 방법을 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).
- Azure Service Health에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/service-health/service-health-overview).


