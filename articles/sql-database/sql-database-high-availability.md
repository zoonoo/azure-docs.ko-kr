---
title: "고가용성 - Azure SQL Database 서비스 | Microsoft Docs"
description: "Azure SQL Database 서비스 고가용성 기능에 대해 알아보세요."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>고가용성 및 Azure SQL Database
Microsoft는 Azure SQL Database PaaS 제품을 출시한 이후 기본적으로 HA(고가용성)를 서비스에 제공하여 고객이 HA를 운영하거나 HA에 특별한 논리를 추가하거나 HA 관련 의사 결정을 내릴 필요가 없도록 하겠다고 고객에게 약속한 바 있습니다. Microsoft는 고객에게 SLA를 제공하고 HA 시스템 구성 및 운영에 대한 완전한 제어를 유지 관리합니다. HA SLA는 지역에 있는 SQL 데이터베이스에 적용되며, 합리적인 제어 범위를 벗어난 요인(예: 자연 재해, 전쟁, 테러, 폭동, 정부 조치, 데이터 센터 외부(고객 사이트 또는 고객 사이트와 데이터 센터 사이 등)에 있는 네트워크 또는 장치 장애)으로 인한 전체 지역 장애가 발생하는 경우 보호를 제공하지 않습니다.

HA의 문제 영역을 단순화하기 위해 Microsoft는 다음 가정을 사용합니다.
1.  하드웨어 및 소프트웨어 장애가 불가피함
2.  운영 직원의 실수로 장애가 발생함
3.  계획된 서비스 운영으로 인해 정전 발생 

이러한 개별 이벤트는 자주 일어나지 않지만 클라우드 규모에서는 거의 매주 발생합니다. 

## <a name="fault-tolerant-sql-databases"></a>내결함성 SQL 데이터베이스
고객은 자신의 데이터베이스 복원력에 가장 관심이 있으며 전체적인 SQL Database 서비스의 복원력에는 관심이 덜합니다. “내 데이터베이스”가 다운된 데이터베이스의 0.01%에 속하는 경우 서비스 가동 시간 99.99%는 의미가 없습니다. 모든 데이터베이스에 내결함성이 있어야 하며 결함 완화가 커밋된 트랜잭션의 손실로 이어지지 않아야 합니다. 

데이터의 경우 SQL Database는 직접 연결된 디스크/VHD 기반의 LS(로컬 저장소)와 Azure Premium Storage 페이지 Blob 기반의 RS(원격 저장소)를 모두 사용합니다. 
- 로컬 저장소는 IOPS 요구 사항이 높은 OLTP 응용 프로그램용으로 설계된 프리미엄 데이터베이스 및 풀에서 사용됩니다. 
- 원격 저장소는 저장소가 필요하며 독립적 확장을 위한 전력을 계산하는 소형, 냉각 또는 대형 데이터베이스용으로 설계된 기본 및 표준 서비스 계층용으로 사용됩니다. 원격 저장소는 데이터베이스 및 로그 파일용 단일 페이지 BLOB을 사용하며, 기본 제공된 저장소 복제 및 장애 조치(failover) 메커니즘을 사용합니다.

두 경우 모두 SQL Database의 복제, 장애 감지 및 장애 조치(failover) 메커니즘이 완전히 자동화되어 사용자의 개입 없이 작동합니다. 이 아키텍처는 커밋된 데이터가 손실되지 않으며 데이터 내구성이 최우선시되도록 설계되었습니다.

주요 이점:
- 고객은 복잡한 하드웨어, 소프트웨어, 운영 체제 또는 가상화 환경을 구성하거나 유지하지 않고도 복제된 데이터베이스를 최대한 활용할 수 있습니다.
- 관계형 데이터베이스의 전체 ACID 속성은 시스템에서 유지 관리합니다.
- 장애 조치(failover)는 커밋된 데이터의 손실 없이 완전히 자동화됩니다.
- 주 복제본에 대한 연결 라우팅은 응용 프로그램 논리가 필요 없는 서비스에 의해 동적으로 관리됩니다.
- 자동화된 중복성의 상위 수준이 추가 요금 없이 제공됩니다.

> [!NOTE]
> 설명된 고가용성 아키텍처는 통지 없이 변경될 수 있습니다. 

## <a name="data-redundancy"></a>데이터 중복

SQL Database의 고가용성 솔루션은 SQL Server의 [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 기술을 기반으로 하며, LS 데이터베이스와 RS 데이터베이스 모두에 대해 최소 수준의 차이로 작동합니다. AlwaysON이 LS 구성에서는 지속성에 사용되는 반면 RS에서는 가용성에 사용됩니다(낮은 RTO). 

## <a name="local-storage"></a>로컬 저장소

LS의 경우 각 데이터베이스는 제어 링 내에서 MS(관리 서비스)에 의해 온라인 상태가 됩니다. 하나의 주 복제본과 둘 이상의 보조 복제본(쿼럼 집합)은 같은 데이터 센터 내 세 개의 독립적인 물리적 하위 시스템에 걸쳐 있는 테넌트 링 내에 있습니다. 모든 읽기 및 쓰기는 GW(게이트웨이)에서 주 복제본으로 전송되고, 쓰기는 보조 복제본에 비동기로 복제됩니다. SQL Database는 트랜잭션이 커밋되기 전에 주 복제본 및 하나 이상의 보조 복제본에 데이터가 기록되는 쿼럼 기반 커밋 체계를 사용합니다.

[Service Fabric](/azure/service-fabric/service-fabric-overview.md) 장애 조치(failover) 시스템은 노드 실패 시 자동으로 복제본을 다시 빌드하며, 노드가 시스템에서 떠날 때와 시스템에 추가될 때 쿼럼 집합 멤버 자격을 유지 관리합니다. 계획된 유지 관리는 쿼럼 집합이 최소 복제본 수(일반적으로 2) 미만이 되지 않도록 신중하게 조정됩니다. 이 모델은 프리미엄 데이터베이스에 적합하지만, 컴퓨팅 및 저장소 구성 요소 모두의 중복성이 필요하므로 비용이 더 많이 듭니다.

## <a name="remote-storage"></a>원격 저장소

원격 저장소 구성(기본 및 표준 계층)의 경우, 정확히 하나의 데이터베이스 사본이 원격 BLOB 저장소에서 유지 관리되어 저장소 시스템의 내구성, 중복성 및 bit-rot 감지 기능을 활용합니다. 

고가용성 아키텍처는 다음 다이어그램에 설명되어 있습니다.
 
![고가용성 아키텍처](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>장애 조치(failover) 감지 및 복구 
대규모 분산 시스템에는 장애를 안정적으로 빠르게, 그리고 고객에게 최대한 가깝게 감지할 수 있는 안정적인 장애 감지 시스템이 필요합니다. SQL Database의 경우 이 시스템은 Azure Service Fabric을 기반으로 합니다. 

주 복제본이 있는 경우 주 복제본에 장애가 발생하여 작업을 계속할 수 없으면 이것이 즉시 명백해집니다. 모든 읽기 및 쓰기가 주 복제본에서 먼저 발생하기 때문입니다. 보조 복제본을 주 복제본 상태로 승격하는 이 프로세스에서는 RTO(복구 시간 목표)가 30초이고 RPO(복구 지점 목표)가 0입니다. 30초 RTO의 영향을 줄이기 위한 모범 사례는 연결 실패 시도에 대해 더 짧은 대기 시간으로 여러 번 연결을 다시 시도하는 것입니다.

보조 복제본이 실패하면 데이터베이스는 예비 없이 최소 쿼럼 집합으로 줄어듭니다. Service Fabric은 주 복제본 장애 이후 발생하는 프로세스와 유사한 재구성 프로세스를 시작하므로, 오류가 영구적인지를 확인하기 위해 잠시 대기한 후 다른 보조 복제본이 만들어집니다. 운영 체제 장애 또는 업그레이드와 같은 일시적 서비스 상태가 발생하는 경우, 실패한 노드가 다시 시작되도록 허용하기 위해 새 복제본이 즉시 빌드되지는 않습니다. 

원격 저장소 구성의 경우 SQL Database는 AlwaysON 기능을 사용하여 업그레이드 중에 데이터베이스를 장애 조치(failover)합니다. 이를 수행하기 위해 새 SQL 인스턴스는 계획된 업그레이드 이벤트의 일부로 미리 시작되지 않으며, 원격 저장소에서 데이터베이스 파일을 첨부하고 복구합니다. 프로세스 충돌 또는 기타 계획되지 않은 이벤트의 경우 Windows Fabric은 인스턴스 가용성을 관리하고 마지막 복구 단계로 원격 데이터베이스 파일을 연결합니다.

## <a name="conclusion"></a>결론
Azure SQL DB는 Azure 플랫폼과 긴밀하게 통합되어 있으며, 장애 감지와 복구를 위해 Service Fabric에 크게 의존하고 데이터 보호를 위해 Azure Storage Blob에 크게 의존합니다. 동시에 Azure SQL 데이터베이스는 복제 및 장애 조치(failover)를 위해 SQL Server 상자 제품의 AlwaysOn 기술을 사용합니다. 이러한 기술의 조합을 통해 응용 프로그램은 혼합 저장소 모델의 이점을 완전히 실현하고 가장 까다로운 SLA를 지원할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Service Fabric](/azure/service-fabric/service-fabric-overview.md) 알아보기
- [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) 알아보기 
