---
title: 유지 관리 기간
description: Azure SQL Database 및 관리 되는 인스턴스 유지 관리 기간을 구성할 수 있는 방법에 대해 설명 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/23/2021
ms.openlocfilehash: 9d7ab0498673ad7006087b66575eea9371b96d11
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565904"
---
# <a name="maintenance-window-preview"></a>유지 관리 기간 (미리 보기)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

유지 관리 기간 기능을 사용 하면 [Azure SQL Database](sql-database-paas-overview.md) 및 [Azure SQL 관리 되는 인스턴스](../managed-instance/sql-managed-instance-paas-overview.md) 리소스에 대 한 유지 관리 일정을 구성 하 여 작업에 대 한 예측 가능 하 고 중단을 줄일 수 있습니다. 

> [!Note]
> 유지 관리 기간 기능은 하드웨어 오류와 같은 계획 되지 않은 이벤트를 보호 하지 않으며,이로 인해 짧은 연결 중단이 발생할 수 있습니다.

## <a name="overview"></a>개요

Azure는 SQL Database 및 SQL 관리 되는 인스턴스 리소스의 [계획 된 유지 관리](planned-maintenance.md) 를 정기적으로 수행 합니다. Azure SQL 유지 관리 이벤트 중에는 데이터베이스를 완벽 하 게 사용할 수 있지만 [SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database) 및 [SQL 관리 되는 인스턴스의](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)각 가용성 sla 내에서 짧은 재구성 될 수 있습니다.

유지 관리 기간은 데이터베이스 또는 인스턴스 재구성 복원할 수 없는 프로덕션 작업을 위한 것 이며 계획 된 유지 관리 이벤트로 인해 발생 하는 단기 연결 중단을 발생 시킬 수 없습니다. 원하는 유지 관리 기간을 선택 하 여 사용량이 많은 업무 시간 외에 발생 하는 계획 된 유지 관리의 영향을 최소화할 수 있습니다. 복원 력 워크 로드와 비프로덕션 워크 로드는 Azure SQL의 기본 유지 관리 정책을 사용 합니다.

유지 관리 기간은 만들거나 기존 Azure SQL 리소스에 대해 구성할 수 있습니다. Azure Portal, PowerShell, CLI 또는 Azure API를 사용 하 여 구성할 수 있습니다.

> [!Important]
> 유지 관리 기간을 구성 하는 작업은 Azure SQL 리소스의 서비스 계층을 변경 하는 것과 유사한 장기 실행 비동기 작업입니다. 작업을 수행 하는 동안 작업을 수행 하는 동안 리소스를 사용할 수 있습니다. 단, 작업의 끝에서 발생 하는 짧은 재구성을 제외 하 고는 중단 된 장기 실행 트랜잭션이 발생 하는 경우에도 최대 8 초가 걸립니다 재구성의 영향을 최소화 하려면 사용량이 많은 시간 외에 작업을 수행 해야 합니다.

### <a name="gain-more-predictability-with-maintenance-window"></a>유지 관리 기간을 사용 하 여 예측 가능성 향상

기본적으로 Azure SQL 유지 관리 정책은 일상 업무 시간 동안 중단을 방지 하기 위해 오전 8 시 기간 동안 **현지 시간을 오후 5 시 하는 데** 사용 됩니다. 현지 시간은 리소스를 호스트 하는 [Azure 지역의](https://azure.microsoft.com/global-infrastructure/geographies/) 위치에 의해 결정 되며 현지 표준 시간대 정의에 따라 일광 절약 시간을 관찰할 수 있습니다. 

두 개의 추가 유지 관리 기간을 선택 하 여 Azure SQL 리소스에 적합 한 시간에 대 한 유지 관리 업데이트를 추가로 조정할 수 있습니다.
 
* 평일 창, 오후 10 시 ~ 오전 6 시 현지 시간 월요일-목요일
* 주말 창, 오전 10 시 ~ 오전 6 시 현지 시간 금요일-일요일

유지 관리 기간을 선택 하 고 서비스 구성이 완료 되 면 선택한 기간 동안에만 계획 된 유지 관리가 수행 됩니다.   

> [!Important]
> 중요 보안 패치를 적용 하는 것과 같이 작업의 연기 심각한 영향을 야기 하는 드문 경우에는 구성 된 유지 관리 기간이 일시적으로 재정의 될 수 있습니다. 

### <a name="cost-and-eligibility"></a>비용 및 자격

유지 관리 기간을 구성 하 고 사용 하는 것은 적합 한 모든 [제품 유형](https://azure.microsoft.com/support/legal/offer-details/)(종 량 제, CSP (클라우드 솔루션 공급자), microsoft 기업계약 또는 Microsoft 고객 계약)에 대 한 무료입니다.

> [!Note]
> Azure 제안은 사용자가 소유한 Azure 구독의 유형을 말합니다. 예를 들어 [종 량 제 요금이](https://azure.microsoft.com/offers/ms-azr-0003p/)있는 구독, [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/)및 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) 은 모두 Azure 제품입니다. 각 제품 또는 요금제에는 서로 다른 용어와 이점이 있습니다. 제안 또는 계획이 구독의 개요에 표시 됩니다. 구독을 다른 제품으로 전환 하는 방법에 대 한 자세한 내용은 [Azure 구독을 다른 제품으로 변경](../../cost-management-billing/manage/switch-azure-offer.md)을 참조 하세요.

## <a name="advance-notifications"></a>고급 알림

유지 관리 알림은 유지 관리 시간 및 유지 관리가 완료 될 때 Azure SQL Database 24 시간 동안의 예정 된 유지 관리 이벤트에 대해 경고 하도록 구성할 수 있습니다. 자세한 내용은 [고급 알림](advance-notifications.md)을 참조 하세요.

## <a name="availability"></a>가용성

### <a name="supported-service-level-objectives"></a>지원 되는 서비스 수준 목표

기본이 아닌 유지 관리 기간을 선택 하는 작업은 다음 **을 제외한** 모든 slo에서 사용할 수 있습니다.
* 하이퍼스케일 
* 인스턴스 풀
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
- 동아시아
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

* Azure SQL Database에서 프록시 연결 정책을 사용 하는 모든 연결은 선택 된 유지 관리 기간 및 게이트웨이 노드 유지 관리 기간의 영향을 받을 수 있습니다. 그러나 권장 되는 리디렉션 연결 정책을 사용 하는 클라이언트 연결은 게이트웨이 노드 유지 관리 재구성의 영향을 받지 않습니다. 

* Azure SQL 관리 되는 인스턴스에서 게이트웨이 노드는 [가상 클러스터 내에서](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 호스트 되 고 관리 되는 인스턴스와 동일한 유지 관리 기간이 있지만 유지 관리 이벤트 중에 중단 횟수를 최소화 하려면 연결 리디렉션 정책을 사용 하는 것이 좋습니다.

Azure SQL Database의 클라이언트 연결 정책에 대 한 자세한 내용은 [연결 정책 Azure SQL Database](../database/connectivity-architecture.md#connection-policy)을 참조 하세요. 

Azure SQL 관리 되는 인스턴스의 클라이언트 연결 정책에 대 한 자세한 내용은 [AZURE sql 관리 되는 인스턴스 연결 형식](../../azure-sql/managed-instance/connection-types-overview.md)을 참조 하세요.

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL 관리 되는 인스턴스에 대 한 고려 사항

Azure SQL 관리 되는 인스턴스는 고객의 가상 네트워크 서브넷 내에서 실행 되는 격리 된 전용 가상 머신의 전용 집합에 호스트 되는 서비스 구성 요소로 구성 됩니다. 이러한 가상 머신은 여러 관리 되는 인스턴스를 호스트할 수 있는 [가상 클러스터](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture) 를 형성 합니다. 한 서브넷의 인스턴스에 구성 된 유지 관리 기간은 가상 클러스터 간의 인스턴스 배포 및 서브넷 내의 가상 클러스터 수에 영향을 줄 수 있습니다. 몇 가지 효과를 고려해 야 할 수 있습니다.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>유지 관리 기간 구성이 장기 실행 작업입니다. 
가상 클러스터에서 호스트 되는 모든 인스턴스는 유지 관리 기간을 공유 합니다. 기본적으로 모든 관리 되는 인스턴스는 기본 유지 관리 기간을 사용 하 여 가상 클러스터에서 호스팅됩니다. 관리 되는 인스턴스를 만드는 동안 다른 유지 관리 기간을 지정 하는 것은 해당 유지 관리 기간이 있는 가상 클러스터에 배치 해야 함을 의미 합니다. 서브넷에 이러한 가상 클러스터가 없는 경우 인스턴스를 수용 하기 위해 먼저 새 가상 클러스터를 만들어야 합니다. 기존 가상 클러스터에서 추가 인스턴스를 수용 하려면 클러스터 크기 조정이 필요할 수 있습니다. 두 작업은 모두 관리 되는 인스턴스에 대해 유지 관리 기간을 구성 하는 기간에 적용 됩니다.
관리 되는 인스턴스에서 유지 관리 기간을 구성 하는 데 필요한 기간은 [인스턴스 관리 작업의 예상 기간](../managed-instance/management-operations-overview.md#duration)을 사용 하 여 계산할 수 있습니다.

> [!Important]
> 짧은 재구성은 유지 관리 작업이 끝날 때 발생 하며 일반적으로 장기 실행 중단 된 트랜잭션이 발생 하는 경우에도 최대 8 초까지 지속 됩니다. 재구성의 영향을 최소화 하려면 사용량이 많은 시간 외에 작업을 예약 해야 합니다.

### <a name="ip-address-space-requirements"></a>IP 주소 공간 요구 사항
서브넷의 새 가상 클러스터 마다 [가상 클러스터 ip 주소 할당](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size)에 따라 추가 IP 주소가 필요 합니다. 또한 기존 관리 되는 인스턴스에 대 한 유지 관리 기간을 변경 하려면 해당 서비스 계층에 대 한 vCores 크기 조정 시나리오에서와 마찬가지로 [임시 추가 IP 용량이](../managed-instance/vnet-subnet-determine-size.md#address-requirements-for-update-scenarios) 필요 합니다.

### <a name="ip-address-change"></a>IP 주소 변경
유지 관리 기간을 구성 하 고 변경 하면 서브넷의 IP 주소 범위 내에서 인스턴스의 IP 주소가 변경 됩니다.

> [!Important]
>  IP 주소 변경 후에 NSG 및 방화벽 규칙이 데이터 트래픽을 차단 하지 않는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계

* [고급 알림](advance-notifications.md)
* [유지 관리 기간 구성](maintenance-window-configure.md)

## <a name="learn-more"></a>자세한 정보

* [유지 관리 기간 FAQ](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL 관리 되는 인스턴스](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure SQL Database 및 Azure SQL 관리 되는 인스턴스의 Azure 유지 관리 이벤트 계획](planned-maintenance.md)