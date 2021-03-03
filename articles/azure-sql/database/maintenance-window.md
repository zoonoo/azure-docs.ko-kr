---
title: 유지 관리 기간
description: Azure SQL Database 및 Managed Instance 유지 관리 기간을 구성할 수 있는 방법을 이해 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 9dc4d17ea95362dd915bd1dfdfd82f4cdec611b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692813"
---
# <a name="maintenance-window-preview"></a>유지 관리 기간 (미리 보기)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

유지 관리 기간 기능을 사용 하면 [Azure SQL Database](sql-database-paas-overview.md) 및 [SQL 관리 되는 인스턴스에](../managed-instance/sql-managed-instance-paas-overview.md)대 한 예측 가능한 유지 관리 기간 일정을 구성할 수 있습니다. 

유지 관리 이벤트에 대 한 자세한 내용은 [Azure SQL Database 및 AZURE SQL Managed Instance에서 azure 유지 관리 이벤트 계획](planned-maintenance.md)을 참조 하세요.

## <a name="overview"></a>개요

Azure는 Azure SQL Database 및 SQL Managed Instance 리소스에 대 한 계획 된 유지 관리 업데이트를 정기적으로 수행 하 여 기본 하드웨어, OS (운영 체제) 및 SQL 엔진을 포함 한 소프트웨어에 대 한 업데이트를 포함 합니다. 유지 관리 업데이트 중에는 리소스를 완전히 사용할 수 있고 액세스할 수 있지만 일부 유지 관리 업데이트에는 유지 관리 업데이트를 적용 하는 짧은 시간 (평균 시간 동안 8 초) 동안 인스턴스가 오프 라인으로 전환 되기 때문에 장애 조치 (failover)가 필요 합니다.  계획 된 유지 관리 업데이트는 평균적으로 35 일 마다 한 번씩 발생 합니다. 즉, 고객은 Azure SQL Database 또는 SQL 관리 되는 인스턴스 마다 매월 계획 된 유지 관리 이벤트를 예상 하 고, 고객에 의해 선택 된 유지 관리 기간 동안에만 발생 합니다.   

유지 관리 기간은 예정 된 유지 관리 이벤트로 인해 발생할 수 있는 일시적인 연결 문제에 대해 복원 력이 없는 비즈니스 작업을 위한 것입니다.

유지 관리 기간은 Azure Portal, PowerShell, CLI 또는 Azure API를 사용 하 여 구성할 수 있습니다. 만들거나 기존 SQL database 및 SQL 관리 되는 인스턴스에 대해 구성할 수 있습니다.

### <a name="gain-more-predictability-with-maintenance-window"></a>유지 관리 기간을 사용 하 여 예측 가능성 향상

기본적으로 모든 Azure SQL Database 및 관리 되는 인스턴스 데이터베이스는 매일 오후 5 시 하는 동안에만 업데이트 되어 업무 시간에 대 한 최대 중단을 방지 합니다. 현지 시간은 리소스를 호스트 하는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/) 에 의해 결정 됩니다. 두 가지 추가 유지 관리 기간을 선택 하 여 데이터베이스에 적합 한 시간에 대 한 유지 관리 업데이트를 추가로 조정할 수 있습니다.

* **기본** 창, 오후 5 시 to 오전 8 시 Local time 월요일-일요일 
* 평일 창, 오전 10 시 ~ 오전 6 시 현지 시간 월요일 – 목요일
* 주말 창, 오전 10 시 ~ 오전 6 시 현지 시간 금요일-일요일

유지 관리 기간을 선택 하면 계획 된 모든 유지 관리 업데이트가 선택한 기간 동안에만 수행 됩니다.   

> [!Note]
> 계획 된 유지 관리 업데이트 외에도, 드문 경우 지만 계획 되지 않은 유지 관리 이벤트로 인해 가용성이 저하 될 수 있습니다. 

### <a name="cost-and-eligibility"></a>비용 및 자격

유지 관리 기간을 선택 하는 것은 종 량 제, CSP (클라우드 솔루션 공급자), Microsoft Enterprise 또는 Microsoft 고객 계약과 같은 구독 [제공 유형에](https://azure.microsoft.com/support/legal/offer-details/)대 한 무료입니다.

> [!Note]
> Azure 제안은 사용자가 소유한 Azure 구독의 유형을 말합니다. 예를 들어 [종 량 제 요금이](https://azure.microsoft.com/offers/ms-azr-0003p/)있는 구독, [Azure in Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)및 [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) 은 모두 Azure 제품입니다. 각 제품 또는 요금제에는 서로 다른 용어와 이점이 있습니다. 제안 또는 계획이 구독의 개요에 표시 됩니다. 구독을 다른 제품으로 전환 하는 방법에 대 한 자세한 내용은 [Azure 구독을 다른 제품으로 변경](/azure/cost-management-billing/manage/switch-azure-offer)을 참조 하세요.

## <a name="advance-notifications"></a>고급 알림

유지 관리 알림을 구성 하 여 향후 예정 된 유지 관리 이벤트에 대해 24 시간을 유지 하 고 유지 관리 시간 및 유지 관리 기간을 완료할 수 있습니다. 자세한 내용은 [고급 알림](advance-notifications.md)을 참조 하세요.

## <a name="availability"></a>가용성

### <a name="supported-service-level-objectives"></a>지원 되는 서비스 수준 목표

기본이 아닌 유지 관리 기간을 선택 하는 작업은 다음 **을 제외한** 모든 slo에서 사용할 수 있습니다.
* 하이퍼스케일 
* 레거시 Gen4 vCore
* 기본, S0 및 S1 
* DC, Fsv2, M 시리즈

### <a name="azure-region-support"></a>Azure 지역 지원

기본이 아닌 유지 관리 기간을 선택 하는 작업은 현재 다음 지역에서 사용할 수 있습니다.

- 오스트레일리아 동부
- 오스트레일리아 남동쪽
- 브라질 남부
- 캐나다 중부
- 미국 중부
- 미국 동부
- 미국 동부2
- 일본 동부
- 미국 NorthCentral
- 북유럽
- 미국 미국
- 동남 아시아
- 영국 남부
- 서유럽
- 미국 서부
- 미국 서부2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL Database에 대 한 게이트웨이 유지 관리

유지 관리 기간에 대 한 최대 혜택을 얻으려면 클라이언트 응용 프로그램에서 연결 리디렉션 정책을 사용 하는지 확인 합니다. 리디렉션은 클라이언트에서 데이터베이스를 호스팅하는 노드에 직접 연결을 설정 하 여 대기 시간을 줄이고 처리량을 향상 시킬 수 있는 권장 연결 정책입니다.  

* Azure SQL Database에서 프록시 연결 정책을 사용 하는 모든 연결은 선택 된 유지 관리 기간 및 게이트웨이 노드 유지 관리 기간의 영향을 받을 수 있습니다. 그러나 권장 되는 리디렉션 연결 정책을 사용 하는 클라이언트 연결은 게이트웨이 노드 유지 관리 장애 조치 (failover)의 영향을 받지 않습니다. 

* Azure SQL 관리 되는 인스턴스에서 게이트웨이 노드는 [가상 클러스터 내](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 에 있고 관리 되는 인스턴스와 동일한 유지 관리 기간을 가지 므로 프록시 연결 정책을 사용 하면 추가 유지 관리 기간에 대 한 연결이 잠재적으로 노출 되지 않습니다.

의 클라이언트 연결 정책에 대 한 자세한 내용은 [Azure SQL Database 연결 정책](../database/connectivity-architecture.md#connection-policy)을 참조 Azure SQL Database. 

Azure SQL 관리 되는 인스턴스의 클라이언트 연결 정책에 대 한 자세한 내용은 [AZURE sql Managed Instance 연결 유형](../../azure-sql/managed-instance/connection-types-overview.md)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

* [고급 알림](advance-notifications.md)
* [유지 관리 기간 구성](maintenance-window-configure.md)

## <a name="learn-more"></a>자세한 정보

* [유지 관리 기간 FAQ](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure SQL Database 및 Azure SQL Managed Instance에서 Azure 유지 관리 이벤트 계획](planned-maintenance.md)




