---
title: 고가용성 - Azure SQL Database 서비스 | Microsoft Docs
description: Azure SQL Database 서비스 고가용성 기능에 대해 알아보세요.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 0b2fa1541eafa3acf28690005a6d40fac76deba6
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353478"
---
# <a name="high-availability-and-azure-sql-database"></a>고가용성 및 Azure SQL Database

Azure SQL Database는 가용성이 뛰어난 데이터베이스 PaaS(Platform as a Service)로 유지 관리 및 가동 중지 시간에 관계없이 99.99%의 시간 동안 데이터베이스가 작동 및 실행되도록 보장합니다. 워크로드에 영향을 주지 않으면서 SQL Server 데이터베이스가 항상 업그레이/패치되도록 보장하는 Azure 클라우드에서 호스팅되는 완전히 관리되는 SQL Server 데이터베이스 엔진 프로세스입니다. 인스턴스가 패치되거나 장애 조치되면 앱에서 [재시도 논리를 사용](sql-database-develop-overview.md#resiliency)하는 경우 가동 중지 시간은 일반적으로 크지 않습니다. 장애 조치를 완료하는 시간이 60초 이상인 경우 지원 사례를 열어야 합니다. Azure SQL Database는 가장 심각한 상황에서도 신속한 복구가 가능하기 때문에 데이터를 항상 사용할 수 있습니다.

Azure 플랫폼은 모든 Azure SQL Database를 완전하게 관리하고 데이터 무손실 및 높은 데이터 가용성을 보장합니다. Azure는 패치, 백업, 복제, 오류 감지, 기본 하드웨어, 소프트웨어 또는 네트워크 오류, 배포 버그 픽스, 장애 조치(failover), 데이터베이스 업그레이드 및 기타 유지 관리 작업을 자동으로 처리합니다. SQL Server 엔지니어는 모든 유지 관리 작업이 데이터베이스 수명 중 0.01% 미만의 시간 내에 완료될 수 있도록 가장 많이 알려진 사례를 구현했습니다. 이 아키텍처는 커밋한 데이터가 손실되지 않고 워크로드에 영향을 주지 않으면서 유지 관리 작업이 수행되도록 설계되었습니다. 데이터베이스를 업그레이드하거나 유지 관리하는 동안 워크로드를 중지해야 하는 유지 관리 기간이나 가동 중지 시간이 없습니다. Azure SQL Database에 내장된 고가용성 덕분에 소프트웨어 아키텍처에서 데이터베이스가 단일 실패 지점이 되지 않도록 보장됩니다.

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 두 가지 고가용성 아키텍처 모델이 있습니다(둘 다 99.99% 가용성 보장).

- 계산과 저장소 분리를 기반으로 하는 표준/범용 서비스 계층 모델. 이 아키텍처 모델은 저장소 계층의 고가용성 및 안정성에 의존하지만 유지 보수 작업 중 약간의 잠재적인 성능 저하가 있을 수 있습니다.
- 데이터베이스 엔진 프로세스의 클러스터를 기반으로 하는 프리미엄/중요 비즈니스용 서비스 계층 모델. 이 아키텍처 모델은 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 있다는 사실에 의존하며 유지 보수 작업 중에도 워크로드에 최소한의 성능 영향을 줍니다.

Azure는 사용자의 가동 중단 시간을 최소화하면서 운영 체제, 드라이버 및 SQL Server 데이터베이스 엔진을 업그레이드하고 패치를 적용합니다. Azure SQL Database는 안정적인 최신 버전의 SQL Server 데이터베이스 엔진 및 Windows OS에서 실행되며 대부분의 사용자는 업그레이드가 지속적으로 수행되는 것을 알아채지 못합니다.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>기본, 표준 및 범용 서비스 계층 가용성

표준 가용성은 기본, 표준 및 범용 서비스 계층에서 적용되는 99.99% SLA를 말합니다. 이 아키텍처 모델의 고가용성은 계산 및 저장소 레이어의 분리 및 저장소 계층에서 데이터의 복제로 이루어집니다.

다음 그림은 분리된 계산 및 저장소 레이어가 있는 표준 아키텍처 모델의 4개 노드를 보여줍니다.

![계산과 저장소의 분리](media/sql-database-managed-instance/general-purpose-service-tier.png)

표준 가용성 모델에는 두 가지 레이어가 있습니다.

- 상태 비저장 계산 레이어는 `sqlserver.exe` 프로세스를 실행하고 일시적인 데이터와 캐시된 데이터(예: 계획 캐시, 버퍼 풀, 열 저장 풀)만 포함합니다. 상태 비저장 SQL Server 노드는 프로세스를 초기화하고 노드의 상태를 제어하며 필요한 경우 다른 위치로 장애 조치(failover)를 수행하는 Azure Service Fabric에서 운영됩니다.
- 상태 저장 데이터 레이어에는 데이터베이스 파일(.mdf/.ldf)이 있으며 Azure Premium Storage에 저장됩니다. Azure Storage는 데이터베이스 파일에 배치된 모든 레코드의 데이터가 손실되지 않도록 보장합니다. Azure Storage에는 SQL Server 프로세스가 중단 되더라도 로그 파일의 모든 레코드나 데이터 파일의 페이지가 보존되도록 하는 데이터 가용성/백업 기능이 기본 제공됩니다.

데이터베이스 엔진이나 운영 체제가 업그레이드되고, 기본 인프라의 일부 부분은 실패하거나 SQL Server 프로세스에서 심각한 문제가 감지될 때마다, Azure Service Fabric은 상태 비저장 SQL Server 프로세스를 다른 상태 비저장 계산 노드로 옮깁니다. 장애 조치(failover) 시간을 최소화하기 위해 장애 조치(failover) 시 새 계산 서비스를 실행하려고 대기하는 예비 노드 집합이 있습니다. Azure Storage 레이어의 데이터는 영향을 받지 않으며 데이터/로그 파일은 새로 초기화된 SQL Server 프로세스에 연결됩니다. 이 프로세스는 99.99%의 가용성을 보장하지만 전환 시간 및 새 SQL Server 노드가 콜드 캐시로 시작된다는 사실로 인해 실행 중인 과도한 워크로드의 성능에 영향을 미칠 수 있습니다.

## <a name="premium-and-business-critical-service-tier-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층 가용성

프리미엄 가용성은 Azure SQL Database의 프리미엄 및 중요 비즈니스용 서비스 계층에서 사용할 수 있으며 지속적인 유지 관리 작업으로 인해 성능에 미치는 영향을 감당할 수 없는 집약적인 워크로드에 맞게 설계되었습니다.

프리미엄 모델에서 Azure SQL 데이터베이스는 계산 및 저장소를 단일 노드에 통합합니다. 이 아키텍처 모델의 고가용성은 SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 4노드 클러스터에 배포된 계산(SQL Server 데이터베이스 엔진 프로세스) 및 저장소(로컬로 연결된 SSD)의 복제를 통해 이루어집니다.

![데이터베이스 엔진 노드의 클러스터](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL 데이터베이스 엔진 프로세스와 기본 mdf/ldf 파일이 SSD 저장소가 로컬에 연결되어 있는 동일한 노드에 배치되기 때문에 워크로드에 대한 대기 시간이 짧습니다. SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 구현됩니다. 모든 데이터베이스는 고객 워크로드에 액세스할 수 있는 주 데이터베이스 하나와 데이터 복사본을 포함하는 보조 프로세스 세 개가 있는 데이터베이스 노드의 클러스터입니다. 주 노드는 어떤 이유로든 주 노드의 작동이 중단되는 경우 보조 복제본의 데이터를 사용할 수 있도록 하기 위해 변경 내용을 보조 노드로 지속적으로 푸시합니다 장애 조치(failover)는 SQL Server 데이터베이스 엔진에 의해 처리됩니다. 보조 복제본 하나가 주 노드가 되고 클러스터에 충분한 노드를 보장하기 위해 새로운 보조 복제본이 만들어집니다. 워크로드는 새로운 주 노드에 자동으로 리디렉션됩니다.

또한 중요 비즈니스용 클러스터에는 기본 워크로드의 성능에 영향을 주면 안 되는 읽기 전용 쿼리(예: 보고서)를 실행하는 데 사용될 수 있는 기본 읽기 전용 노드를 무료로 제공하는 [읽기 확장](sql-database-read-scale-out.md) 기능이 기본으로 제공됩니다.

## <a name="zone-redundant-configuration"></a>영역 중복 구성

로컬 저장소 구성에 대한 쿼럼 집합 복제본은 기본적으로 동일한 데이터 센터에 만들어집니다. [Azure 가용성 영역](../availability-zones/az-overview.md)이 도입되면 쿼럼 집합의 여러 복제본을 동일한 지역의 서로 다른 가용성 영역에 배치할 수 있습니다. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 영역 중복 구성을 통해 데이터베이스 중복성을 추가로 만들지 않으므로 프리미엄 또는 중요 비즈니스용 서비스 계층에서 가용성 영역을 사용하는 경우, 추가 비용 없이 사용할 수 있습니다. 영역 중복 데이터베이스를 선택하면 응용 프로그램 논리를 변경하지 않고도 치명적인 데이터 센터 중단을 포함하여 훨씬 더 큰 실패 집합에 탄력적인 프리미엄 또는 중요 비즈니스용 데이터베이스를 만들 수 있습니다. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 쿼럼 집합에서는 복제본이 서로 간에 약간 떨어져 있는 서로 다른 데이터 센터에 있기 때문에, 네트워크 대기 시간이 늘어나면 커밋 시간이 늘어나고, 이에 따라 일부 OLTP 작업의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 데이터 작업의 크기이며 일반 서비스 계층 업데이트와 비슷합니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 영역 중복 데이터베이스 및 탄력적 풀은 현재 프리미엄 서비스 계층에서만 지원됩니다. 기본적으로 백업 및 감사 레코드는 RA-GRS 저장소에 저장되므로 전체 영역 중단 시 자동으로 사용되지 않을 수 있습니다. 

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![고가용성 아키텍처 영역 중복](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)

[ADR(가속 데이터베이스 복구)](sql-database-accelerated-database-recovery.md)은 SQL 데이터베이스 엔진 복구 프로세스를 다시 설계하여 특히 장기 실행 트랜잭션이 있는 경우 데이터베이스 가용성을 크게 향상하는 새로운 SQL 데이터베이스 엔진 기능입니다. ADR은 현재 단일 데이터베이스, 탄력적 풀, Azure SQL Data Warehouse에 사용할 수 있습니다.

## <a name="conclusion"></a>결론

Azure SQL Database는 Azure 플랫폼과 긴밀하게 통합되어 있으며, Azure Storage Blob(데이터 보호용) 및 가용성 영역(높은 내결함성용)에 대한 장애 검색 및 복구를 위한 Service Fabric에 따라 크게 달라집니다. 동시에 Azure SQL Database는 복제 및 장애 조치(failover)를 위해 SQL Server 제품군의 Always On 가용성 그룹 기술을 최대한 활용합니다. 이러한 기술의 조합을 통해 응용 프로그램은 혼합 저장소 모델의 이점을 완전히 실현하고 가장 까다로운 SLA를 지원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
- [Service Fabric](../service-fabric/service-fabric-overview.md) 알아보기
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 알아보기
- 고가용성 및 재해 복구의 추가 옵션은 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.
