---
title: 중요 비즈니스 서비스 계층
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance에 대 한 중요 비즈니스용 서비스 계층에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 0067811316a8afd26828050d81215ecb5748c841
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986694"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>중요 비즈니스용 계층 Azure SQL Database 및 Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 중요 비즈니스용 계층은 DTU 구매 모델에서 프리미엄 이라고 합니다. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](purchasing-models.md)를 참조하세요.

Azure SQL Database 및 Azure SQL Managed Instance는 인프라 오류가 발생 하는 경우에도 99.99%의 가용성을 보장 하기 위해 클라우드 환경에 맞게 조정 된 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 사용 되는 세 가지 아키텍처 모델이 있습니다.
- 범용/표준 
- 중요 비즈니스용/프리미엄
- 하이퍼스케일

프리미엄/중요 비즈니스용 서비스 계층 모델은 데이터베이스 엔진 프로세스의 클러스터를 기반으로 합니다. 이 아키텍처 모델은 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 있다는 사실에 의존하며 유지 보수 작업 중에도 워크로드에 최소한의 성능 영향을 줍니다. 하이퍼 크기 조정 서비스 계층은 현재 Azure SQL Database (SQL Managed Instance 아님)에 대해서만 사용할 수 있으며, Azure 아키텍처를 활용 하 여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 한도를 초과 하 여 Azure SQL Database 데이터베이스에 대 한 저장소 및 계산 리소스를 확장 하는 확장성이 뛰어난 저장소 및 계산 성능 계층입니다.

Azure는 최종 사용자에 게 최소한의 작동 중지 시간으로 기본 운영 체제, 드라이버 및 SQL Server 데이터베이스 엔진을 업그레이드 하 고 패치 합니다. 

프리미엄 가용성은 프리미엄 및 중요 비즈니스용 서비스 계층에서 사용 하도록 설정 되며 지속적인 유지 관리 작업으로 인 한 성능 영향을 허용할 수 없는 집약적 워크 로드를 위해 설계 되었습니다.

계산 및 저장소는 프리미엄 모델의 단일 노드에 통합 됩니다. 이 아키텍처 모델의 고가용성은 SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)와 비슷한 기술을 사용 하 여 4 개 노드 클러스터에 배포 된 계산 (SQL Server 데이터베이스 엔진 프로세스) 및 저장소 (로컬로 연결 된 SSD) 복제를 통해 달성 됩니다.

![데이터베이스 엔진 노드의 클러스터](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server 데이터베이스 엔진 프로세스와 기본 .mdf/.ldf 파일은 모두 로컬에 연결 된 SSD 저장소와 동일한 노드에 배치 되어 워크 로드에 짧은 대기 시간을 제공 합니다. 고가용성은 SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)와 유사한 기술을 사용 하 여 구현 됩니다. 모든 데이터베이스는 고객 작업에 액세스할 수 있는 하나의 주 데이터베이스와 데이터 복사본을 포함 하는 세 개의 보조 프로세스로 이루어진 데이터베이스 노드의 클러스터입니다. 주 노드는 어떤 이유로 든 주 노드가 실패 하는 경우 보조 복제본에서 데이터를 사용할 수 있도록 하기 위해 보조 노드에 대 한 변경 내용을 지속적으로 푸시합니다. 장애 조치 (Failover)는 SQL Server 데이터베이스 엔진에 의해 처리 됩니다. 즉, 하나의 보조 복제본이 주 노드가 되 고 클러스터에 충분 한 노드가 있도록 새 보조 복제본이 생성 됩니다. 워크로드는 새로운 주 노드에 자동으로 리디렉션됩니다.

또한 중요 비즈니스용 클러스터에는 기본 워크로드의 성능에 영향을 주면 안 되는 읽기 전용 쿼리(예: 보고서)를 실행하는 데 사용될 수 있는 기본 읽기 전용 노드를 무료로 제공하는 [읽기 확장](read-scale-out.md) 기능이 기본으로 제공됩니다.

## <a name="when-to-choose-this-service-tier"></a>이 서비스 계층을 선택하는 시기

중요 비즈니스용 서비스 계층은 기본 SSD 저장소 (평균 1-2 밀리초)에서 짧은 대기 시간 응답을 요구 하는 응용 프로그램을 위해 설계 되었거나, 기본 인프라가 실패 한 경우 빠른 복구를 수행 하거나, 주 데이터베이스의 무료로 읽을 수 있는 보조 복제본에 대해 보고서, 분석 및 읽기 전용 쿼리를 해제 해야 하는 경우에 적합 합니다.

일반적인 용도 계층 대신 중요 비즈니스용 서비스 계층을 선택 해야 하는 주요 이유는 다음과 같습니다.
-   **낮은 i/o 대기 시간 요구 사항** – 저장소 계층에서 fast 응답이 필요한 워크 로드 (평균 1-2 밀리초)는 중요 비즈니스용 계층을 사용 해야 합니다. 
-   **응용 프로그램과 데이터베이스 간의 빈번한 통신**. 응용 프로그램 계층 캐싱 또는 [요청 일괄](../performance-improve-use-batching.md) 처리를 사용할 수 없으며 신속 하 게 처리 해야 하는 많은 SQL 쿼리를 전송 해야 하는 응용 프로그램은 중요 비즈니스용 계층에 적합 합니다.
-   **많은 수의 업데이트** – 삽입, 업데이트 및 삭제 작업을 사용 하 여 데이터 파일에 저장 해야 하는 메모리 (더티 페이지)의 데이터 페이지를 수정 합니다 `CHECKPOINT` . 잠재적인 데이터베이스 엔진 프로세스 충돌 또는 많은 수의 더티 페이지가 있는 데이터베이스의 장애 조치 (failover)로 인해 일반 용도의 계층에서 복구 시간이 늘어날 수 있습니다. 많은 메모리 내 변경을 유발 하는 작업이 있는 경우 중요 비즈니스용 계층을 사용 합니다. 
-   **데이터를 수정 하는 장기 실행 트랜잭션입니다**. 오랜 시간 동안 열리는 트랜잭션은 로그 파일 잘림을 방지 하 여 로그 크기와 [가상 로그 파일 (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)수를 늘릴 수 있습니다. Vlf의 수가 높으면 장애 조치 (failover) 후 데이터베이스의 복구 속도가 느려질 수 있습니다.
-   무료 보조 읽기 전용 복제본으로 리디렉션될 수 있는 **보고 및 분석 쿼리가 포함 된 작업** 입니다.
- **복원 력이 향상 되 고 오류를 신속 하 게 복구**합니다. 시스템 오류가 발생 하는 경우 주 인스턴스의 데이터베이스가 사용 하지 않도록 설정 되 고 보조 복제본 중 하나는 쿼리를 처리할 준비가 된 새로운 읽기/쓰기 주 데이터베이스가 됩니다. 데이터베이스 엔진은 로그 파일에서 트랜잭션을 분석 하 고 다시 실행 하 고 메모리 버퍼의 모든 데이터를 로드할 필요가 없습니다.
- **고급 데이터 손상 방지**. 중요 비즈니스용 계층은 비즈니스 연속성을 위해 백그라운드에서 데이터베이스 복제본을 활용 하므로 서비스는 SQL Server 데이터베이스 [미러링 및 가용성 그룹](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)에 사용 되는 것과 동일한 기술인 자동 페이지 복구도 활용 합니다. 복제본이 데이터 무결성 문제로 인해 페이지를 읽을 수 없는 경우 다른 복제본에서 페이지의 새 복사본을 검색 하 여 데이터 손실 또는 고객의 가동 중지 시간 없이 읽을 수 없는 페이지를 대체 합니다. 이 기능은 데이터베이스에 지역 보조 복제본이 있는 경우 일반적인 용도 계층에서 적용할 수 있습니다.
- Multi-factor configuration의 **고가용성** -중요 비즈니스용 계층은 일반적인 용도의 계층의 99.99%에 비해 99.995%의 가용성을 보장 합니다.
- 지역 복제를 사용 하 여 구성 된 **빠른 지역 복구** -중요 비즈니스용 계층에는 배포 된 시간 100%에 대 한 보장 된 RPO (복구 지점 목표)가 5 초이 고 복구 시간 목표 (RTO)가 30 초입니다.

## <a name="next-steps"></a>다음 단계

- [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics)에서 중요 비즈니스용 계층의 리소스 특성 (코어 수, i/o, 메모리), [vcore 모델](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) 또는 [dtu 모델](resource-limits-dtu-single-databases.md#premium-service-tier)의 단일 데이터베이스 또는 [Vcore 모델](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) 및 [dtu 모델](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)의 탄력적 풀을 찾습니다.
- [범용](service-tier-general-purpose.md) 및 [하이퍼스케일](service-tier-hyperscale.md) 계층에 대해 알아봅니다.
- [Service Fabric](../../service-fabric/service-fabric-overview.md)에 대해 알아봅니다.
- 고가용성 및 재해 복구에 대 한 추가 옵션은 [비즈니스 연속성](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조 하세요.
