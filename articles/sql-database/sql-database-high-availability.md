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
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698232"
---
# <a name="high-availability-and-azure-sql-database"></a>고가용성 및 Azure SQL Database

Azure SQL Database의 고가용성 아키텍처의 목표는 유지 관리 작업 및 중단의 영향에 대 한 걱정 없이 보장 하는 데이터베이스를 실행 중인 99.99%의 시간입니다. Azure는 자동으로 패치, 백업, Windows 및 SQL 업그레이드와 같은 기본 하드웨어, 소프트웨어 또는 네트워크 오류 등의 계획 되지 않은 이벤트의 중요 한 서비스 작업을 처리합니다.  기본 SQL 인스턴스는 패치 또는 장애 조치 하는 경우 가동 중지 시간이 눈에 띄지 않습니다 경우 있습니다 [재시도 논리가](sql-database-develop-overview.md#resiliency) 앱에서. Azure SQL Database는 가장 심각한 상황에서도 신속한 복구가 가능하기 때문에 데이터를 항상 사용할 수 있습니다.

고가용성 솔루션은 커밋된 데이터를 유지 관리 작업 워크 로드에 영향을 주지 않습니다 오류로 인해 손실 되지 및 데이터베이스 소프트웨어 아키텍처의 오류의 단일 지점이 되지 않도록 설계 되었습니다. 데이터베이스를 업그레이드하거나 유지 관리하는 동안 워크로드를 중지해야 하는 유지 관리 기간이나 가동 중지 시간이 없습니다. 

Azure SQL Database에 사용 되는 두 가지 고가용성 아키텍처 모델 가지가 있습니다.

- 계산과 저장소 분리를 기반으로 하는 표준 가용성 모델입니다.  원격 저장소 계층의 높은 가용성 및 안정성에 의존합니다. 이 아키텍처 유지 보수 활동 중 일부 성능 저하를 허용할 수 있는 예산 지향 비즈니스 응용 프로그램을 대상으로 합니다.
- 데이터베이스 엔진 프로세스의 클러스터를 기반으로 하는 프리미엄 가용성 모델입니다. 가 항상 사용 가능한 데이터베이스 엔진에 대 한 노드의 쿼럼 사실에 의존 합니다. 이 아키텍처를 대상으로 높은 IO 성능, 높은 트랜잭션 속도 및 보증을 사용 하 여 업무용 응용 프로그램 워크 로드에 맞게 유지 보수 활동 중 최소한의 성능에 미치는 영향입니다.

Azure SQL Database 안정적인 최신 버전의 SQL Server 데이터베이스 엔진 및 Windows OS에서 실행 하 고 대부분의 사용자 업그레이드 지속적으로 수행 됩니다 것을 확인할 수 없습니다.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>기본, 표준 및 범용 서비스 계층 가용성

이러한 서비스 계층은 표준 가용성 아키텍처를 활용 합니다. 다음 그림 분리 된 계산 및 저장소 계층을 사용 하 여 4 개의 다른 노드를 보여 줍니다.

![계산과 스토리지의 분리](media/sql-database-high-availability/general-purpose-service-tier.png)

표준 가용성 모델에 두 계층에 포함 됩니다.

- 실행 하는 상태 비저장 계산 계층은 `sqlserver.exe` 처리 하 고 TempDB, model 데이터베이스, 계획 캐시, 버퍼 풀 및 열 저장소 풀 등은 연결 된 SSD에만 임시 및 캐시 된 데이터를 포함 합니다. 초기화 하는 Azure Service Fabric에서 상태 비저장이 노드의 운영 `sqlserver.exe`를 노드의 상태를 제어 하 고 필요한 경우 다른 노드로 장애 조치를 수행 합니다.
- Azure Blob storage에 저장 된 데이터베이스 파일 (.mdf/.ldf)를 사용 하 여 상태 저장 데이터 계층입니다. Azure blob storage에 기본 제공 데이터 가용성 및 중복성 기능에 있습니다. SQL Server 프로세스가 충돌 하는 경우에 로그 파일 또는 데이터 파일에는 페이지의 모든 레코드는 유지를 보장 합니다.

데이터베이스 엔진 또는 운영 체제를 업그레이드 또는 오류가 감지 될 때마다 Azure Service Fabric는 충분 한 사용 가능한 용량이 있는 상태 비저장 SQL Server 프로세스를 다른 상태 비저장 계산 노드로 이동 됩니다. Azure Blob storage에서 데이터 이동의 영향을 받지 않습니다 및 데이터/로그 파일이 새로 초기화 된 SQL Server 프로세스에 연결 됩니다. 이 프로세스는 99.99%의 가용성을 보장 하지만 콜드 캐시를 사용 하 여 새 SQL Server 인스턴스 시작 이후 과도 한 워크 로드 전환 하는 동안 약간의 성능 저하가 발생할 수 있습니다.

## <a name="premium-and-business-critical-service-tier-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층 가용성

프리미엄 및 중요 비즈니스용 서비스 계층 활용 Premium 가용성 모델을 통합 하는 리소스 (SQL Server 데이터베이스 엔진 프로세스) 및 단일 노드에서 저장소 (로컬로 연결 된 SSD)를 계산 합니다. 고가용성은 3을 4-노드 클러스터를 만드는 추가 노드로 계산 및 저장소를 복제 하 여 수행 됩니다. 

![데이터베이스 엔진 노드의 클러스터](media/sql-database-high-availability/business-critical-service-tier.png)

기본 데이터베이스 파일 (.mdf/.ldf) 워크 로드에 매우 짧은 대기 시간과 IO 수 있도록 연결 된 SSD 저장소에 배치 됩니다. SQL Server와 유사 기술을 사용 하 여 고가용성은 구현 [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)합니다. 읽기-쓰기 고객 워크 로드 및 최대 3 개의 보조 복제본 (계산 및 저장소)에 액세스할 수 있는 단일 주 복제본 (SQL Server 프로세스)를 포함 하는 클러스터 데이터의 복사본을 포함 합니다. 지속적으로 주 노드 변경 내용을 순서로 보조 노드로 푸시하고 데이터가 각 트랜잭션을 커밋하기 전에 하나 이상의 보조 복제본에 동기화 되도록 합니다. 이 프로세스는 어떤 이유로 든 주 노드에 충돌할 경우 있는지 항상 완전히 동기화 된 노드를 장애 조치를 보장 합니다. Azure Service Fabric에서 장애 조치가 시작 됩니다. 보조 복제본이 새 주 노드가, 되 면 다른 보조 복제본 클러스터에 충분 한 노드가 (쿼럼 집합) 하도록 만들어집니다. 장애 조치 완료 되 면 새 주 노드에 SQL 연결을 자동으로 리디렉션됩니다.

추가 점으로 premium 가용성 모델은 보조 복제본 중 하나에 읽기 전용 SQL 연결을 리디렉션할 수를 포함 되어 있습니다. 이 기능은 이라고 [읽기 스케일 아웃](sql-database-read-scale-out.md)합니다. 100% 추가 계산 주 복제본에서의 분석 워크 로드 등의 읽기 전용 작업을 오프 로드 하기 위해 추가 비용 없이 용량을 제공 합니다.

## <a name="zone-redundant-configuration"></a>영역 중복 구성

Premium 가용성 모델에 대 한 노드의 클러스터는 기본적으로 동일한 데이터 센터에 만들어집니다. 되면서 [Azure Availability Zones](../availability-zones/az-overview.md), SQL Database 같은 지역의 다른 가용성 영역을 클러스터의 서로 다른 복제본을 배치할 수 있습니다. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 영역 중복 구성을 프리미엄 또는 중요 비즈니스용 서비스 계층의 추가 데이터베이스 중복성을 만들지 않으므로 설정할 수 있습니다.이 없이 추가 비용입니다. 영역 중복 구성을 선택 하 여 할 수 있습니다 프리미엄 또는 중요 비즈니스용 데이터베이스 복원 력 있는 훨씬 더 큰 실패 집합에 응용 프로그램 논리를 변경 하지 않고도 치명적인 데이터 센터 중단을 포함 하려면. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 데이터베이스 복제본 간에 약간 떨어져 있는 서로 다른 데이터 센터에 있는 때문에 향상 된 네트워크 대기 시간 커밋 시간을 늘리려면를 업데이트 하 고 따라서 일부 OLTP 워크 로드의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 온라인 작업 일반 서비스 계층 업그레이드와 비슷합니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 영역 중복 데이터베이스 및 탄력적 풀은 현재 프리미엄 및 중요 비즈니스용 서비스 계층 에서만 지원 됩니다. 기본적으로 백업 및 감사 레코드는 RA-GRS 저장소에 저장되므로 전체 영역 중단 시 자동으로 사용되지 않을 수 있습니다. 

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![고가용성 아키텍처 영역 중복](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)

[데이터베이스 복구 (ADR) 가속](sql-database-accelerated-database-recovery.md) 트랜잭션을 실행 시간이 긴 경우 특히 데이터베이스 가용성을 획기적으로 개선 하는 새 SQL 데이터베이스 엔진 기능입니다. ADR은 현재 단일 데이터베이스, 탄력적 풀, Azure SQL Data Warehouse에 사용할 수 있습니다.

## <a name="conclusion"></a>결론

Azure SQL Database에는 Azure 플랫폼과 긴밀 하 게 통합 되는 기본 제공 고가용성 솔루션을 갖추고 있습니다. 오류 감지 및 복구에 대 한 Service Fabric, 데이터 보호를 위해 Azure Blob storage 및 높은 내결함성에 대 한 가용성 영역에서 다릅니다. 또한 Azure SQL database는 복제 및 장애 조치에 대 한 SQL Server의 Always On 가용성 그룹 기술을 활용합니다. 이러한 기술의 조합을 통해 응용 프로그램을 모델 및 가장 까다로운 Sla를 지 원하는 혼합된 저장소의 이점을 완전히 실현을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../availability-zones/az-overview.md) 알아보기
- [Service Fabric](../service-fabric/service-fabric-overview.md) 알아보기
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 알아보기
- 고가용성 및 재해 복구의 추가 옵션은 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.
