---
title: 고가용성 - Azure SQL Database 서비스 | Microsoft Docs
description: Azure SQL Database 서비스 고가용성 기능에 대해 알아보세요.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 226b0c1cb11fc872cb7759e0d0e49275b9c2d9bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568148"
---
# <a name="high-availability-and-azure-sql-database"></a>고가용성 및 Azure SQL Database

Azure SQL Database에서 고가용성 아키텍처의 목표는 유지 관리 작업 및 중단의 영향을 걱정 하지 않고 데이터베이스가 99.99%의 시간 동안 실행 되도록 보장 하는 것입니다. Azure는 패치, 백업, Windows 및 SQL 업그레이드와 같은 중요 한 서비스 작업 뿐만 아니라 기본 하드웨어, 소프트웨어 또는 네트워크 오류와 같은 계획 되지 않은 이벤트를 자동으로 처리 합니다.  기본 SQL 인스턴스가 패치 되거나 장애 조치 (failover) 되 면 앱에서 [재시도 논리](sql-database-develop-overview.md#resiliency) 를 사용 하는 경우 가동 중지 시간이 눈에 띄지 않습니다. Azure SQL Database는 가장 심각한 상황에서도 신속한 복구가 가능하기 때문에 데이터를 항상 사용할 수 있습니다.

고가용성 솔루션은 커밋된 데이터가 오류로 인해 손실 되지 않도록 하 고, 유지 관리 작업이 작업에 영향을 주지 않으며, 데이터베이스가 소프트웨어 아키텍처에서 단일 실패 지점이 되지 않도록 설계 되었습니다. 데이터베이스를 업그레이드하거나 유지 관리하는 동안 워크로드를 중지해야 하는 유지 관리 기간이나 가동 중지 시간이 없습니다. 

Azure SQL Database에서 사용 되는 두 가지 고가용성 아키텍처 모델이 있습니다.

- 계산 및 저장소의 분리를 기반으로 하는 표준 가용성 모델입니다.  원격 저장소 계층의 고가용성 및 안정성을 기반으로 합니다. 이 아키텍처는 유지 관리 작업 중에 일부 성능 저하를 허용할 수 있는 예산 기반 비즈니스 응용 프로그램을 대상으로 합니다.
- 데이터베이스 엔진 프로세스의 클러스터를 기반으로 하는 프리미엄 가용성 모델입니다. 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 항상 존재 한다는 사실에 의존 합니다. 이 아키텍처는 높은 IO 성능을 가진 중요 업무용 응용 프로그램을 대상으로 하며, 트랜잭션 속도는 유지 관리 작업 중에 작업에 대 한 최소 성능 영향을 보장 합니다.

Azure SQL Database은 안정적인 최신 버전의 SQL Server 데이터베이스 엔진 및 Windows OS에서 실행 되며 대부분의 사용자는 업그레이드가 지속적으로 수행 되는 것을 알 수 없습니다.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>기본, 표준 및 범용 서비스 계층 가용성

이러한 서비스 계층은 표준 가용성 아키텍처를 활용 합니다. 다음 그림은 분리 된 계산 및 저장소 계층을 포함 하는 네 가지 노드를 보여 줍니다.

![컴퓨팅과 스토리지의 분리](media/sql-database-high-availability/general-purpose-service-tier.png)

표준 가용성 모델에는 두 개의 계층이 있습니다.

- 프로세스를 `sqlserver.exe` 실행 하 고 TempDB, model 데이터베이스, 계획 캐시, 버퍼 풀 및 열 저장소 풀과 같은 연결 된 SSD에 임시 및 캐시 된 데이터만 포함 하는 상태 비저장 계산 계층입니다. 이 상태 비저장 노드는 노드의 상태를 초기화 `sqlserver.exe`하 고 제어 하며 필요한 경우 다른 노드로 장애 조치 (failover)를 수행 하는 Azure Service Fabric에 의해 작동 됩니다.
- Azure Blob 저장소에 저장 된 데이터베이스 파일 (.mdf/.ldf)이 포함 된 상태 저장 데이터 계층입니다. Azure blob storage에는 기본 제공 되는 데이터 가용성 및 중복성 기능이 있습니다. SQL Server 프로세스가 충돌 하는 경우에도 데이터 파일에 있는 로그 파일 또는 페이지의 모든 레코드가 보존 되도록 보장 합니다.

데이터베이스 엔진 또는 운영 체제가 업그레이드 되거나 오류가 검색 될 때마다 Azure Service Fabric는 사용 가능한 용량이 충분 한 상태 비저장 SQL Server 프로세스를 다른 상태 비저장 계산 노드로 이동 합니다. Azure Blob storage의 데이터는 이동의 영향을 받지 않으며 데이터/로그 파일이 새로 초기화 된 SQL Server 프로세스에 연결 됩니다. 이 프로세스는 99.99%의 가용성을 보장 하지만, 새 SQL Server 인스턴스가 콜드 캐시로 시작 되기 때문에 많은 워크 로드에서 성능 저하가 발생할 수 있습니다.

## <a name="premium-and-business-critical-service-tier-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층 가용성

프리미엄 및 중요 비즈니스용 서비스 계층은 계산 리소스 (SQL Server 데이터베이스 엔진 프로세스)와 저장소 (로컬로 연결 된 SSD)를 단일 노드에 통합 하는 프리미엄 가용성 모델을 활용 합니다. 고가용성은 3 ~ 4 개 노드 클러스터를 만드는 추가 노드에 계산과 저장소를 복제 하 여 수행 됩니다. 

![데이터베이스 엔진 노드의 클러스터](media/sql-database-high-availability/business-critical-service-tier.png)

기본 데이터베이스 파일 (.mdf/.ldf)은 연결 된 SSD 저장소에 배치 되어 워크 로드에 매우 짧은 대기 시간 IO를 제공 합니다. 고가용성은 SQL Server [Always On 가용성 그룹과](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)비슷한 기술을 사용 하 여 구현 됩니다. 클러스터에는 읽기/쓰기 고객 작업에 액세스할 수 있는 단일 주 복제본 (SQL Server 프로세스) 및 데이터 복사본을 포함 하는 최대 3 개의 보조 복제본 (계산 및 저장소)이 포함 되어 있습니다. 주 노드는 계속 해 서 보조 노드에 대 한 변경 내용을 적용 하 고 각 트랜잭션을 커밋하기 전에 데이터를 하나 이상의 보조 복제본에 동기화 되도록 합니다. 이 프로세스를 통해 어떤 이유로 든 주 노드가 충돌 하는 경우 항상 완전히 동기화 된 노드는로 장애 조치 (failover) 됩니다. 장애 조치 (failover)는 Azure Service Fabric에 의해 시작 됩니다. 보조 복제본이 새 주 노드가 되 면 클러스터에 충분 한 노드 (쿼럼 집합)가 있는지 확인 하기 위해 다른 보조 복제본이 생성 됩니다. 장애 조치 (failover)가 완료 되 면 SQL 연결이 자동으로 새 주 노드로 리디렉션됩니다.

추가 혜택으로 프리미엄 가용성 모델에는 읽기 전용 SQL 연결을 보조 복제본 중 하나로 리디렉션하는 기능이 포함 되어 있습니다. 이 기능을 [읽기 확장](sql-database-read-scale-out.md)이라고 합니다. 주 복제본에서 분석 워크 로드와 같은 읽기 전용 작업을 오프 로드 하기 위해 추가 요금 없이 100% 추가 계산 용량을 제공 합니다.

## <a name="zone-redundant-configuration"></a>영역 중복 구성

기본적으로 프리미엄 가용성 모델에 대 한 노드 클러스터는 동일한 데이터 센터에 만들어집니다. [Azure 가용성 영역](../availability-zones/az-overview.md)의 도입으로 클러스터의 여러 복제본을 동일한 지역의 다른 가용성 영역에 저장할 수 SQL Database. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 프리미엄 또는 중요 비즈니스용 서비스 계층의 영역 중복 구성은 추가 데이터베이스 중복성을 만들지 않으므로 추가 비용 없이 사용 하도록 설정할 수 있습니다. 영역 중복 구성을 선택 하면 응용 프로그램 논리를 변경 하지 않고도 심각한 데이터 센터 중단을 포함 하 여 훨씬 더 큰 오류 집합에 대해 프리미엄 또는 중요 비즈니스용 데이터베이스를 탄력적으로 복원할 수 있습니다. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 데이터베이스에는 서로 다른 데이터 센터의 복제본이 있기 때문에 네트워크 대기 시간이 늘어나면 커밋 시간이 늘어날 수 있으므로 일부 OLTP 작업의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 일반 서비스 계층 업그레이드와 비슷한 온라인 작업입니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 영역 중복 데이터베이스 및 탄력적 풀은 현재 선택 영역에서 프리미엄 및 중요 비즈니스용 서비스 계층 에서만 지원 됩니다. 중요 비즈니스용 계층을 사용할 때 영역 중복 구성은 Gen5 계산 하드웨어를 선택한 경우에만 사용할 수 있습니다. 영역 중복 데이터베이스를 지 원하는 지역에 대 한 최신 정보는 [지역별 서비스 지원](../availability-zones/az-overview.md#services-support-by-region)을 참조 하세요.  

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![고가용성 아키텍처 영역 중복](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)

[ADR (가속화 된 데이터베이스 복구)](sql-database-accelerated-database-recovery.md) 는 특히 장기 실행 트랜잭션이 있을 때 데이터베이스 가용성을 크게 향상 시키는 새로운 SQL 데이터베이스 엔진 기능입니다. ADR은 현재 단일 데이터베이스, 탄력적 풀, Azure SQL Data Warehouse에 사용할 수 있습니다.

## <a name="conclusion"></a>결론

Azure SQL Database는 Azure 플랫폼과 긴밀 하 게 통합 되는 기본 제공 고가용성 솔루션을 갖추고 있습니다. 이는 장애 검색 및 복구, 데이터 보호를 위한 Azure Blob storage, 높은 내결함성에 대 한 가용성 영역에 대 한 Service Fabric에 따라 달라 집니다. 또한 Azure SQL database는 복제 및 장애 조치 (failover)를 위해 SQL Server에서 Always On 가용성 그룹 기술을 활용 합니다. 이러한 기술을 조합 하 여 응용 프로그램에서 혼합 된 저장소 모델의 이점을 완전히 실현 하 고 가장 까다로운 Sla를 지원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
- [Service Fabric](../service-fabric/service-fabric-overview.md) 알아보기
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 알아보기
- 고가용성 및 재해 복구의 추가 옵션은 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.
