---
title: 고가용성
description: Azure SQL Database 서비스 고가용성 기능에 대해 알아보세요.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: 1c4ed77112e8c06db1946d756239e02cb187f3ef
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618465"
---
# <a name="high-availability-and-azure-sql-database"></a>고가용성 및 Azure SQL Database

Azure SQL Database의 고가용성 아키텍처의 목표는 데이터베이스가 최소 99.99%의 시간 동안 가동중임을 보장하는 것입니다(다른 계층에 대한 특정 SLA에 대한 자세한 내용은 유지 관리 작업 및 중단의 영향을 걱정하지 않고 [Azure SQL Database에 대한 SLA를](https://azure.microsoft.com/support/legal/sla/sql-database/)참조하십시오). Azure는 패치, 백업, Windows 및 SQL 업그레이드와 같은 중요한 서비스 작업뿐만 아니라 기본 하드웨어, 소프트웨어 또는 네트워크 오류와 같은 계획되지 않은 이벤트를 자동으로 처리합니다.  기본 SQL 인스턴스가 패치되거나 장애 조치되면 앱에서 [재시도 논리를 사용하는](sql-database-develop-overview.md#resiliency) 경우 가동 중지 시간이 눈에 띄지 않습니다. Azure SQL Database는 가장 심각한 상황에서도 신속한 복구가 가능하기 때문에 데이터를 항상 사용할 수 있습니다.

고가용성 솔루션은 오류로 인해 커밋된 데이터가 손실되지 않고 유지 관리 작업이 워크로드에 영향을 주지 않으며 데이터베이스가 소프트웨어 아키텍처에서 단일 실패 지점이 되지 않도록 설계되었습니다. 데이터베이스를 업그레이드하거나 유지 관리하는 동안 워크로드를 중지해야 하는 유지 관리 기간이나 가동 중지 시간이 없습니다. 

Azure SQL Database에 사용되는 두 가지 고가용성 아키텍처 모델이 있습니다.

- 계산과 스토리지의 분리를 기반으로 하는 표준 가용성 모델입니다.  원격 스토리지 계층의 고가용성과 안정성에 의존합니다. 이 아키텍처는 유지 관리 작업 중에 일부 성능 저하를 견딜 수 있는 예산 지향 비즈니스 응용 프로그램을 대상으로 합니다.
- 데이터베이스 엔진 프로세스 의 클러스터를 기반으로 하는 프리미엄 가용성 모델입니다. 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 항상 있다는 사실에 의존합니다. 이 아키텍처는 높은 IO 성능, 높은 트랜잭션 속도로 미션 크리티컬 애플리케이션을 대상으로 하며 유지 관리 작업 중 워크로드에 미치는 성능 영향을 최소화합니다.

Azure SQL Database는 SQL Server 데이터베이스 엔진 및 Windows OS의 최신 안정 버전에서 실행되며 대부분의 사용자는 업그레이드가 지속적으로 수행된다는 것을 알지 못합니다.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>기본, 표준 및 범용 서비스 계층 가용성

이러한 서비스 계층은 표준 가용성 아키텍처를 활용합니다. 다음 그림은 분리된 계산 및 저장소 계층이 있는 네 개의 서로 다른 노드를 보여 주십습니다.

![컴퓨팅과 스토리지의 분리](media/sql-database-high-availability/general-purpose-service-tier.png)

표준 가용성 모델에는 다음 두 계층이 포함됩니다.

- 프로세스를 `sqlservr.exe` 실행하고 TempDB, 연결된 SSD의 모델 데이터베이스, 메모리에 캐시, 버퍼 풀 및 열 저장소 풀과 같은 일시적 및 캐시된 데이터만 포함하는 상태 비저장 계산 계층입니다. 이 상태 비수 노드는 Azure Service Fabric에서 `sqlservr.exe`운영되며, 노드의 상태를 초기화하고, 노드의 상태를 제어하며, 필요한 경우 다른 노드로 장애 조치(failover)를 수행합니다.
- Azure Blob 저장소에 저장된 데이터베이스 파일(.mdf/.ldf)이 있는 상태 저장 데이터 계층입니다. Azure Blob 저장소에는 기본 제공 데이터 가용성 및 중복 기능이 있습니다. SQL Server 프로세스가 충돌하더라도 데이터 파일또는 페이지의 모든 레코드가 보존되도록 보장합니다.

데이터베이스 엔진이나 운영 체제가 업그레이드되거나 오류가 감지될 때마다 Azure Service Fabric은 상태 비수기 SQL Server 프로세스를 충분한 사용 가능한 용량의 다른 상태 비관리 계산 노드로 이동합니다. Azure Blob 저장소의 데이터는 이동의 영향을 받지 않으며 데이터/로그 파일은 새로 초기화된 SQL Server 프로세스에 연결됩니다. 이 프로세스는 99.99%의 가용성을 보장하지만 새 SQL Server 인스턴스가 콜드 캐시로 시작되므로 전환 중에 작업 부하가 많이 발생할 수 있습니다.

## <a name="premium-and-business-critical-service-tier-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층 가용성

프리미엄 및 비즈니스 크리티컬 서비스 계층은 단일 노드에 컴퓨팅 리소스(SQL Server Database Engine 프로세스) 및 저장소(로컬로 연결된 SSD)를 통합하는 프리미엄 가용성 모델을 활용합니다. 3~4개의 노드 클러스터를 만드는 추가 노드에 계산과 스토리지를 모두 복제하여 고가용성을 달성할 수 있습니다. 

![데이터베이스 엔진 노드의 클러스터](media/sql-database-high-availability/business-critical-service-tier.png)

기본 데이터베이스 파일(.mdf/.ldf)은 연결된 SSD 저장소에 배치되어 워크로드에 매우 낮은 대기 시간 IO를 제공합니다. 고가용성은 SQL Server Always On 가용성 그룹과 유사한 기술을 사용하여 [구현됩니다.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 클러스터에는 읽기-쓰기 고객 워크로드에 액세스할 수 있는 단일 기본 복제본(SQL Server 프로세스)과 데이터 복사본을 포함하는 최대 3개의 보조 복제본(계산 및 저장소)이 포함됩니다. 기본 노드는 지속적으로 변경 내용을 순서대로 푸시하고 각 트랜잭션을 커밋하기 전에 데이터가 하나 이상의 보조 복제본에 동기화되도록 합니다. 이 프로세스는 어떤 이유로든 기본 노드가 충돌하는 경우 장애 조치할 수 있는 완전히 동기화된 노드가 항상 있음을 보장합니다. 장애 조치(failover)는 Azure 서비스 패브릭에 의해 시작됩니다. 보조 복제본이 새 기본 노드가 되면 클러스터에 충분한 노드(쿼럼 집합)가 있는지 확인하기 위해 다른 보조 복제본이 만들어집니다. 장애 조치(failover)가 완료되면 SQL 연결이 자동으로 새 기본 노드로 리디렉션됩니다.

추가 적인 이점으로 프리미엄 가용성 모델에는 읽기 전용 SQL 연결을 보조 복제본 중 하나로 리디렉션하는 기능이 포함됩니다. 이 기능을 [읽기 배율 조정이라고](sql-database-read-scale-out.md)합니다. 기본 복제본에서 분석 워크로드와 같은 오프로드 읽기 전용 작업에 추가 비용 없이 100% 추가 컴퓨팅 용량을 제공합니다.

## <a name="hyperscale-service-tier-availability"></a>하이퍼스케일 서비스 계층 가용성

하이퍼스케일 서비스 계층 아키텍처는 [분산 함수 아키텍처에 설명되어 있습니다.](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) 

![하이퍼스케일 기능 아키텍처](./media/sql-database-hyperscale/hyperscale-architecture.png)

하이퍼스케일의 가용성 모델에는 다음 네 가지 계층이 포함됩니다.

- 프로세스를 `sqlservr.exe` 실행하고 연결된 SSD에서 비커버형 RBPEX 캐시, TempDB, 모델 데이터베이스 등과 같은 일시적 및 캐시된 데이터만 포함하고 메모리에 캐시, 버퍼 풀 및 열저장소 풀을 계획하는 상태 비저장 계산 계층입니다. 이 상태 비수기 계층에는 기본 계산 복제본과 장애 조치 대상역할을 할 수 있는 여러 보조 계산 복제본이 포함됩니다.
- 페이지 서버별로 구성된 상태 비저장 저장소 계층입니다. 이 계층은 계산 복제본에서 실행되는 프로세스에 `sqlservr.exe` 대한 분산 저장소 엔진입니다. 각 페이지 서버에는 연결된 SSD의 RBPEX 캐시를 덮고 메모리에 캐시된 데이터 페이지와 같은 일시적 및 캐시된 데이터만 포함됩니다. 각 페이지 서버에는 부하 분산, 중복성 및 고가용성을 제공하기 위해 활성-활성 구성의 페어링된 페이지 서버가 있습니다.
- 로그 서비스 프로세스, 트랜잭션 로그 방문 영역 및 트랜잭션 로그 장기 저장소를 실행하는 계산 노드에 의해 형성된 상태 저장 트랜잭션 로그 저장소 계층입니다. 방문 영역 및 장기 저장소는 트랜잭션 로그에 대한 가용성 및 [중복성을](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 제공하는 Azure Storage를 사용하여 커밋된 트랜잭션에 대한 데이터 내구성을 보장합니다.
- Azure 저장소에 저장되고 페이지 서버에서 업데이트되는 데이터베이스 파일(.mdf/.ndf)이 있는 상태 저장 데이터 저장소 계층입니다. 이 계층은 Azure Storage의 데이터 가용성 및 [중복](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 기능을 사용합니다. 하이퍼스케일 아키텍처의 다른 계층의 프로세스가 충돌하거나 계산 노드가 실패하더라도 데이터 파일의 모든 페이지가 보존되도록 보장합니다.

모든 하이퍼스케일 계층의 노드 계산은 Azure Service Fabric에서 실행되며, 각 노드의 상태를 제어하고 필요에 따라 사용 가능한 정상 노드에 대한 장애 조치(failover)를 수행합니다.

하이퍼스케일의 고가용성에 대한 자세한 내용은 [하이퍼스케일의 데이터베이스 고가용성](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)을 참조하십시오.

## <a name="zone-redundant-configuration"></a>영역 중복 구성

기본적으로 프리미엄 가용성 모델에 대한 노드 클러스터는 동일한 데이터 센터에서 만들어집니다. Azure 가용성 [영역이](../availability-zones/az-overview.md)도입되면서 SQL Database는 비즈니스 중요 데이터베이스의 다른 복제본을 동일한 지역의 다른 가용성 영역에 배치할 수 있습니다. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 프리미엄 또는 비즈니스 중요 서비스 계층의 영역 중복 구성은 추가 데이터베이스 중복성을 만들지 않으므로 추가 비용 없이 활성화할 수 있습니다. 영역 중복 구성을 선택하면 응용 프로그램 논리를 변경하지 않고 치명적인 데이터 센터 중단을 포함하여 훨씬 더 큰 오류 집합에 대해 프리미엄 또는 비즈니스 중요 데이터베이스를 복원할 수 있습니다. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 데이터베이스에는 서로 다른 데이터 센터에 복제본이 있고 그 사이에 어느 정도 거리가 있으므로 네트워크 대기 시간이 증가하면 커밋 시간이 증가하여 일부 OLTP 워크로드의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 일반 서비스 계층 업그레이드와 유사한 온라인 작업입니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 영역 중복 데이터베이스 및 탄력적 풀은 현재 일부 지역의 프리미엄 및 비즈니스 중요 서비스 계층에서만 지원됩니다. 비즈니스 중요 계층을 사용하는 경우 영역 중복 구성은 Gen5 계산 하드웨어를 선택한 경우에만 사용할 수 있습니다. 영역 중복 데이터베이스를 지원하는 지역에 대한 최신 정보는 [지역별 서비스 지원을](../availability-zones/az-overview.md#services-support-by-region)참조하십시오.  
> 관리되는 인스턴스에서는 이 기능을 사용할 수 없습니다.

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![고가용성 아키텍처 영역 중복](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)

[ADR(가속 데이터베이스 복구)은](sql-database-accelerated-database-recovery.md) 특히 장기 실행 트랜잭션이 있는 경우 데이터베이스 가용성을 크게 향상시키는 새로운 SQL 데이터베이스 엔진 기능입니다. ADR은 현재 단일 데이터베이스, 탄력적 풀, Azure SQL Data Warehouse에 사용할 수 있습니다.

## <a name="testing-application-fault-resiliency"></a>응용 프로그램 오류 복원력 테스트

고가용성은 데이터베이스 응용 프로그램에 대해 투명하게 작동하는 Azure SQL Database 플랫폼의 기본 부분입니다. 그러나 계획되거나 계획되지 않은 이벤트 중에 시작된 자동 장애 조치 작업이 프로덕션환경에 배포하기 전에 응용 프로그램에 미치는 영향을 테스트할 수 있습니다. 특수 API를 호출하여 데이터베이스 또는 탄력적 풀을 다시 시작하여 장애 조치(failover)를 트리거할 수 있습니다. 영역 중복 데이터베이스 또는 탄력적 풀의 경우 API 호출은 이전 기본의 가용성 영역과 다른 가용성 영역에서 클라이언트 연결을 새 기본으로 리디렉션합니다. 따라서 장애 조치(failover)가 기존 데이터베이스 세션에 미치는 영향을 테스트하는 것 외에도 네트워크 대기 시간 의 변경으로 인해 종단 간 성능이 변경되는지 확인할 수도 있습니다. 다시 시작 작업은 방해가 되고 많은 수의 작업이 플랫폼에 스트레스를 줄 수 있으므로 각 데이터베이스 또는 탄력적 풀에 대해 30분마다 하나의 장애 조치 호출만 허용됩니다. 

REST API 또는 PowerShell을 사용하여 장애 조치(failover)를 시작할 수 있습니다. REST API의 경우 [데이터베이스 장애 조치](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) 및 [탄력적 풀 장애 조치.를](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover)참조하십시오. PowerShell의 경우 [호출-AzSqlDatabase장애 조치 및](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) [호출-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)를 참조하십시오. REST API 호출은 [az rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) 명령을 사용하여 Azure CLI에서 만들 수도 있습니다.

> [!IMPORTANT]
> 장애 조치 명령은 현재 하이퍼스케일 서비스 계층 및 관리되는 인스턴스에 사용할 수 없습니다.

## <a name="conclusion"></a>결론

Azure SQL Database는 Azure 플랫폼과 깊이 통합된 기본 제공 고가용성 솔루션을 제공합니다. 오류 검색 및 복구를 위한 서비스 패브릭, 데이터 보호를 위한 Azure Blob 저장소 및 더 높은 내결함성을 위한 가용성 영역에 따라 달라집니다. 또한 Azure SQL 데이터베이스는 복제 및 장애 조치(failover)를 위해 SQL Server의 Always On 가용성 그룹 기술을 활용합니다. 이러한 기술의 조합을 통해 애플리케이션은 혼합 스토리지 모델의 이점을 완벽하게 인식하고 가장 까다로운 SLA를 지원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
- 서비스 [패브릭에](../service-fabric/service-fabric-overview.md) 대해 알아보기
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 알아보기
- 고가용성 및 재해 복구의 추가 옵션은 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.
