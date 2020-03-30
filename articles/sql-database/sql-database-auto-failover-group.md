---
title: 장애 조치(failover) 그룹
description: 자동 장애 조치(failover) 그룹은 관리되는 인스턴스의 모든 데이터베이스 또는 SQL Database 서버에서 데이터베이스 그룹의 복제 및 자동/조정된 를 관리할 수 있도록 하는 SQL Database 기능입니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269836"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치(failover)를 사용할 수 있습니다.

자동 장애 조치 그룹은 SQL Database 서버의 데이터베이스 그룹 또는 관리되는 인스턴스의 모든 데이터베이스의 복제 및 장애 조치(failover)를 다른 지역으로 관리할 수 있는 SQL Database 기능입니다. 대규모의 지리적 복제 데이터베이스의 배포 및 관리를 단순화하도록 설계된 기존 [활성 지역 복제](sql-database-active-geo-replication.md) 기능 위에 선언적 추상화입니다. 수동으로 장애 조치(failover)를 시작하거나, 사용자 정의 정책에 따라 SQL Database 서비스에 위임할 수 있습니다. 사용자 정의 정책 옵션을 사용하면 치명적인 오류 또는 계획되지 않은 다른 이벤트가 발생하여 주 지역에서 SQL Database 서비스의 가용성이 완전히 또는 부분적으로 상실될 경우 보조 지역에서 여러 관련 데이터베이스를 자동으로 복구할 수 있습니다. 장애 조치 그룹에는 일반적으로 동일한 응용 프로그램에서 사용되는 하나 이상의 데이터베이스가 포함될 수 있습니다. 또한 읽을 수 있는 보조 데이터베이스를 사용하여 읽기 전용 쿼리 워크로드를 오프로드할 수 있습니다. 자동 장애 조치 그룹에 여러 데이터베이스가 포함되기 때문에 주 서버에서 이러한 데이터베이스를 구성해야 합니다. 자동 장애 조치 그룹은 그룹의 모든 데이터베이스를 다른 지역에 있는 하나의 보조 서버로만 복제할 수 있도록 지원합니다.

> [!NOTE]
> SQL Database 서버에서 단일 또는 풀링된 데이터베이스를 사용 중이며 동일하거나 다른 지역에 여러 개의 보조 데이터베이스를 만들려는 경우 [활성 지역 복제](sql-database-active-geo-replication.md)를 사용합니다. 

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 그룹 내 데이터베이스 중 하나 이상에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 일반적으로 이러한 인시던트는 기본 제공 자동 고가용성 작업으로 자체 완화할 수 없는 인시던트입니다. 장애 조치 트리거의 예로는 여러 계산 노드의 OS 커널 메모리 누수로 인해 SQL 테넌트 링 또는 제어 링이 다운된 인시던트 또는 하나 이상의 테넌트 링이 중단되어 네트워크 케이블이 끊어졌기 때문에 인시던트 링이 다운된 경우등이 있습니다. 일상적인 하드웨어 서비스 해제.  자세한 내용은 [SQL 데이터베이스 고가용성](sql-database-high-availability.md)을 참조하십시오.

또한 자동 장애 조치 그룹은 장애 조치하는 동안 변경되지 않는 읽기-쓰기 및 읽기 전용 수신기 끝점을 제공합니다. 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계 없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치(failover)가 완료되면 엔드포인트를 새 지역으로 리디렉션하도록 DNS 레코드가 자동으로 업데이트됩니다. 특정 RPO 및 RTO 데이터에 대해서는 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 SQL Database 서버 또는 관리되는 인스턴스의 데이터베이스에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 다음을 사용하여 자동 장애 조치(failover) 그룹을 관리할 수 있습니다.

- [Azure 포털](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: 장애 조치 그룹](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: 그룹 장애 조치](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: 장애 조치(failover) 그룹](/rest/api/sql/failovergroups)

장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 스토리지 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계를](sql-database-designing-cloud-solutions-for-disaster-recovery.md)참조하십시오.

## <a name="auto-failover-group-terminology-and-capabilities"></a>자동 장애 조치(failover) 그룹 용어 및 기능

- **장애 조치 그룹(FOG)**

  장애 조치 그룹은 단일 SQL Database 서버 또는 단일 관리 인스턴스 내에서 관리되는 명명된 데이터베이스 그룹으로, 기본 지역의 가동 중단으로 인해 일부 주 데이터베이스를 모두 또는 일부 기본 데이터베이스를 사용할 수 없게 되는 경우 다른 지역으로 의 단위로 장애 조치할 수 있습니다. 관리되는 인스턴스에 대해 생성된 경우 장애 조치 그룹에는 인스턴스의 모든 사용자 데이터베이스가 포함되므로 인스턴스에서 하나의 장애 조치 그룹만 구성할 수 있습니다.
  
  > [!IMPORTANT]
  > 장애 조치 그룹의 이름은 도메인 내에서 전역적으로 `.database.windows.net` 고유해야 합니다.

- **SQL Database 서버**

     SQL Database 서버를 사용하면 단일 SQL Database 서버의 사용자 데이터베이스를 일부 또는 모두 장애 조치(failover) 그룹에 배치할 수 있습니다. 또한 SQL Database 서버는 단일 SQL Database 서버에서 여러 개의 장애 조치(failover) 그룹을 지원합니다.

- **주**

  장애 조치 그룹의 기본 데이터베이스를 호스트하는 SQL Database 서버 또는 관리되는 인스턴스입니다.

- **보조**

  장애 조치 그룹의 보조 데이터베이스를 호스트하는 SQL Database 서버 또는 관리인스턴스입니다. 보조 서버는 주 서버와 동일한 지역에 있을 수 없습니다.

- **장애 조치(failover) 그룹에 단일 데이터베이스 추가**

  동일한 SQL Database 서버에 있는 여러 개의 단일 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 장애 조치(failover) 그룹에 단일 데이터베이스를 추가하면 동일한 버전과 컴퓨팅 크기를 사용하는 보조 데이터베이스가 보조 서버에 자동으로 만들어집니다.  장애 조치(failover) 그룹을 만들 때 해당 서버를 지정했습니다. 보조 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 새 보조 데이터베이스가 보조 서버에 만들어집니다.

  > [!IMPORTANT]
  > 보조 서버가 기존 보조 데이터베이스가 아니면 이름이 같은 데이터베이스가 없는지 확인합니다. 관리되는 인스턴스에 대한 장애 조치 그룹에서는 모든 사용자 데이터베이스가 복제됩니다. 장애 조치(failover) 그룹에서 복제를 위해 사용자 데이터베이스 하위 집합을 선택할 수 없습니다.

- **장애 조치(failover) 그룹에 탄력적 풀의 데이터베이스 추가**

  탄력적 풀에 있는 전체 또는 여러 개의 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 탄력적 풀에 주 데이터베이스가 있는 경우 동일한 이름을 가진 보조 데이터베이스가 탄력적 풀에 자동으로 만들어집니다(보조 풀). 장애 조치(failover) 그룹에서 만들 보조 데이터베이스를 호스트하기에 충분한 여유 용량과 정확히 동일한 이름을 가진 탄력적 풀이 보조 서버에 있는지 확인해야 합니다. 보조 풀에 보조 데이터베이스가 이미 있는 데이터베이스를 풀에 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 보조 데이터베이스가 서버에 이미 있는 데이터베이스를 추가하는 경우 새 보조 데이터베이스가 보조 풀에 만들어집니다.
  
- **초기 시드** 

  데이터베이스, 탄력적 풀 또는 관리되는 인스턴스를 장애 조치 그룹에 추가할 때 데이터 복제가 시작되기 전에 초기 시드 단계가 있습니다. 초기 시드 단계는 가장 길고 비용이 많이 드는 작업입니다. 초기 시드가 완료되면 데이터가 동기화되고 이후 데이터 변경 내용만 복제됩니다. 초기 시드가 완료되는 데 걸리는 시간은 데이터 크기, 복제된 데이터베이스 수 및 장애 조치 그룹의 엔터티 간의 링크 속도에 따라 달라집니다. 일반적인 시드 속도는 단일 데이터베이스 또는 탄력적 풀의 경우 시간당 50~500GB, 관리되는 인스턴스의 경우 시간당 18~35GB입니다. 시드는 모든 데이터베이스에 대해 병렬로 수행됩니다. 명시된 시드 속도와 데이터베이스 수 및 총 데이터 크기를 사용하여 데이터 복제가 시작되기 전에 초기 시드 단계가 걸리는 예상 을 예측할 수 있습니다.

  관리되는 인스턴스의 경우 초기 시드 단계의 시간을 추정할 때 두 인스턴스 간의 고속 경로 링크 속도도 고려해야 합니다. 두 인스턴스 간의 링크 속도가 필요한 것보다 느리면 시드 시간이 특히 영향을 받을 수 있습니다. 명시된 시드 속도, 데이터베이스 수, 총 데이터 크기 및 링크 속도를 사용하여 데이터 복제가 시작되기 전에 초기 시드 단계가 걸리는 예상 속도를 예측할 수 있습니다. 예를 들어 단일 100GB 데이터베이스의 경우 링크가 시간당 35GB를 푸시할 수 있는 경우 초기 시드 단계는 2.8~5.5시간 정도 걸립니다. 링크가 시간당 10GB만 전송할 수 있는 경우 100GB 데이터베이스를 시드하는 데 약 10시간이 걸립니다. 복제할 데이터베이스가 여러 개 있는 경우 시드가 병렬로 실행되고 느린 링크 속도와 결합하면 특히 모든 데이터베이스의 데이터의 병렬 시드가 사용 가능한 데이터보다 많은 경우 초기 시드 단계가 상당히 오래 걸릴 수 있습니다. 링크 대역폭. 두 인스턴스 간의 네트워크 대역폭이 제한되어 있고 여러 관리되는 인스턴스를 장애 조치 그룹에 추가하는 경우 여러 관리되는 인스턴스를 장애 조치 그룹에 순차적으로 하나씩 추가하는 것이 좋습니다.

  
- **DNS 영역**

  새 인스턴스를 만들 때 자동으로 생성되는 고유 ID입니다. 이 인스턴스에 대한 SAN(다중 도메인) 인증서는 동일한 DNS 영역의 모든 인스턴스에 대한 클라이언트 연결을 인증하도록 프로비전됩니다. 동일한 장애 조치 그룹의 관리되는 두 인스턴스는 DNS 영역을 공유해야 합니다.
  
  > [!NOTE]
  > SQL Database 서버에 대해 생성된 장애 조치 그룹에는 DNS 영역 ID가 필요하지 않습니다.

- **장애 조치 그룹 읽기-쓰기 수신기**

  현재 기본 의 URL을 가리키는 DNS CNAME 레코드입니다. 장애 조치 그룹이 만들어지면 자동으로 만들어지며 장애 조치 후 기본 데이터베이스가 변경될 때 읽기-쓰기 SQL 워크로드가 기본 데이터베이스에 투명하게 다시 연결될 수 있습니다. 장애 조치 그룹이 SQL Database 서버에서 만들어지면 수신기 URL에 대한 DNS CNAME 레코드가 로 `<fog-name>.database.windows.net`형성됩니다. 관리되는 인스턴스에서 장애 조치 그룹이 만들어지면 수신기 URL에 대한 DNS CNAME 레코드가 로 `<fog-name>.zone_id.database.windows.net`형성됩니다.

- **장애 조치 그룹 읽기 전용 수신기**

  보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기를 가리키는 DNS CNAME 레코드가 생성됩니다. 장애 조치 그룹이 만들어지면 자동으로 만들어지며 읽기 전용 SQL 워크로드가 지정된 로드 균형 규칙을 사용하여 보조 워크로드에 투명하게 연결할 수 있습니다. 장애 조치 그룹이 SQL Database 서버에서 만들어지면 수신기 URL에 대한 DNS CNAME 레코드가 로 `<fog-name>.secondary.database.windows.net`형성됩니다. 관리되는 인스턴스에서 장애 조치 그룹이 만들어지면 수신기 URL에 대한 DNS CNAME 레코드가 로 `<fog-name>.zone_id.secondary.database.windows.net`형성됩니다.

- **자동 장애 조치 정책**

  기본적으로 장애 조치(failover) 그룹은 자동 장애 조치(failover) 정책을 사용하여 구성됩니다. SQL Database 서비스는 오류가 검색되고 유예 기간이 만료된 후에 장애 조치(failover)를 트리거합니다. 영향의 규모로 인해 [SQL Database 서비스의 기본 제공 고가용성 인프라](sql-database-high-availability.md)를 통해 중단을 완화할 수 없음을 시스템에서 확인해야 합니다. 애플리케이션에서 장애 조치 워크플로를 제어하려는 경우 자동 장애 조치를 해제할 수 있습니다.
  
  > [!NOTE]
  > 가동 중단 규모와 얼마나 빨리 완화될 수 있는지 를 확인하는 것은 운영 팀의 인적 행동을 수반하기 때문에 유예 기간은 1시간 미만으로 설정할 수 없습니다.  이 제한은 데이터 동기화 상태에 관계없이 장애 조치 그룹의 모든 데이터베이스에 적용됩니다. 

- **읽기 전용 장애 조치 정책**

  기본적으로 읽기 전용 수신기에 대한 장애 조치가 사용되지 않습니다. 이렇게 하면 보조 서버가 오프라인 상태일 때 주 서버의 성능이 영향을 받지 않습니다. 그러나 보조 서버가 복구될 때까지 읽기 전용 세션이 연결할 수 없음을 의미합니다. 읽기 전용 세션에 대한 가동 중지 시간을 허용할 수 없고 기본 세션의 잠재적 성능 저하를 희생하여 읽기 전용 및 읽기 쓰기 트래픽 모두에 대해 주 를 일시적으로 사용하는 `AllowReadOnlyFailoverToPrimary` 것이 정상인 경우 속성을 구성하여 읽기 전용 수신기에 대한 장애 중지를 활성화할 수 있습니다. 이 경우 보조 트래픽을 사용할 수 없는 경우 읽기 전용 트래픽이 자동으로 기본 트래픽으로 리디렉션됩니다.

- **계획된 장애 조치(failover)**

   계획된 장애 조치(failover)는 보조 역할이 주 역할로 전환되기 전에 주 데이터베이스와 보조 데이터베이스 간에 전체 동기화를 수행합니다. 이렇게 하면 데이터 손실이 발생하지 않습니다. 계획된 장애 조치(failover)는 다음과 같은 시나리오에서 사용합니다.

  - 데이터 손실이 허용되지 않을 때 프로덕션에서 DR(재해 복구) 드릴 수행
  - 데이터베이스를 다른 지역으로 재배치
  - 중단이 완화된 후 데이터베이스를 주 지역으로 반환합니다(장애 복구(failback)).

- **계획되지 않은 장애 조치(failover)**

   계획되지 않은 장애 조치 또는 강제 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 보조 역할을 주 역할로 즉시 전환합니다. 이 작업으로 인해 데이터가 손실됩니다. 계획되지 않은 장애 조치(failover)는 주 데이터베이스에 액세스할 수 없는 중단 중에 복구 방법으로 사용됩니다. 원래 기본이 다시 온라인 상태가 되면 동기화 없이 자동으로 다시 연결되고 새 보조 데이터베이스가 됩니다.

- **수동 장애 조치**

  자동 장애 조치 구성에 관계 없이 언제든지 장애 조치를 수동으로 시작할 수 있습니다. 자동 장애 조치(failover) 정책이 구성되지 않은 경우 장애 조치 그룹의 데이터베이스를 보조 데이터베이스에 복구하려면 수동 장애 조치(failover)를 수행해야 합니다. 강제 장애 조치 또는 친숙한 장애 조치(전체 데이터 동기화 사용)를 시작할 수 있습니다. 친숙한 장애 조치를 사용하면 주 데이터베이스를 보조 지역에 재배치할 수 있습니다. 장애 조치(failover)가 완료되면 새로운 주 데이터베이스에 대한 연결을 보장하기 위해 DNS 레코드가 자동으로 업데이트됩니다.

- **데이터 손실이 있는 유예 기간**

  주 및 보조 데이터베이스가 비동기 복제를 사용하여 동기화되기 때문에 장애 조치로 인해 데이터가 손실될 수 있습니다. 애플리케이션의 데이터 손실 허용 오차를 반영하도록 자동 장애 조치 정책을 사용자 지정할 수 있습니다. `GracePeriodWithDataLossHours`을 구성하면 데이터 손실이 발생할 수 있는 장애 조치(failover)를 시작하기 전에 시스템이 대기하는 데 걸리는 시간도 제어할 수 있습니다.

- **여러 장애 조치 그룹**

  동일한 서버 쌍에 대해 여러 장애 조치 그룹을 구성하여 장애 조치의 크기를 제어할 수 있습니다. 각 그룹은 독립적으로 장애 조치됩니다. 다중 테넌트 애플리케이션에서 탄력적 풀을 사용하는 경우 이 기능을 사용하여 각 풀에 주 및 보조 데이터베이스를 혼합할 수 있습니다. 이렇게 하면 테넌트의 절반에 대해서만 가동 중단에 따른 영향을 줄일 수 있습니다.

  > [!NOTE]
  > Managed Instance는 여러 개의 장애 조치(failover) 그룹을 지원하지 않습니다.
  
## <a name="permissions"></a>사용 권한

장애 조치 그룹에 대한 권한은 [RBAC(역할 기반 액세스 제어)를](../role-based-access-control/overview.md)통해 관리됩니다. [SQL Server 기여자](../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할에는 장애 조치 그룹을 관리하는 데 필요한 모든 권한이 있습니다.

### <a name="create-failover-group"></a>장애 조치 그룹 만들기

장애 조치 그룹을 만들려면 기본 서버와 보조 서버 서버 및 장애 조치 그룹의 모든 데이터베이스에 대한 RBAC 쓰기 액세스 가 필요합니다. 관리되는 인스턴스의 경우 기본 및 보조 관리 인스턴스모두에 대한 RBAC 쓰기 액세스가 필요하지만 개별 관리되는 인스턴스 데이터베이스를 장애 조치 그룹에 추가하거나 제거할 수 없으므로 개별 데이터베이스에 대한 사용 권한은 관련이 없습니다. 

### <a name="update-a-failover-group"></a>장애 조치 그룹 업데이트

장애 조치 그룹을 업데이트하려면 장애 조치 그룹에 대한 RBAC 쓰기 액세스 및 현재 주 서버 또는 관리되는 인스턴스의 모든 데이터베이스가 필요합니다.  

### <a name="failover-a-failover-group"></a>장애 조치 그룹 장애 조치

장애 조치 그룹을 장애 조치(failover) 그룹에 장애 조치(failover)하려면 새 주 서버 또는 관리되는 인스턴스에서 장애 조치 그룹에 대한 RBAC 쓰기 액세스가 필요합니다.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀로 장애 조치(failover) 그룹을 사용하는 방법의 모범 사례

자동 장애 조치(failover) 그룹은 주 SQL Database 서버에서 구성되어야 하며 다른 Azure 지역의 보조 SQL Database 서버에 연결됩니다. 그룹에는 이러한 서버에 있는 데이터베이스가 모두 또는 일부 포함될 수 있습니다. 다음 다이어그램은 여러 데이터베이스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> [장애 조치 그룹에 단일 데이터베이스 추가를](sql-database-single-database-failover-group-tutorial.md) 참조하여 장애 조치 그룹에 단일 데이터베이스를 추가하는 자세한 단계별 자습서를 참조하세요.

비즈니스 연속성을 고려하여 서비스를 설계하는 경우 다음과 같은 일반 지침을 따르세요.

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>하나 이상의 장애 조치 그룹을 사용하여 여러 데이터베이스의 장애 조치 관리

다른 하위 지역의 두 서버(기본 및 보조 서버) 간에 하나 이상의 장애 조치 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다. 장애 조치(failover) 그룹을 사용하는 경우 주 데이터베이스와 서비스 목표가 동일한 지역 보조 데이터베이스가 작성됩니다. 장애 조치(failover) 그룹에 기존의 지역 복제 관계를 추가하는 경우 보조 데이터베이스가 주 데이터베이스와 동일한 서비스 계층 및 컴퓨팅 크기로 구성되었는지 확인합니다.
  
> [!IMPORTANT]
> 다른 구독의 두 서버 간에 장애 조치 그룹을 만드는 것은 현재 단일 데이터베이스 및 탄력적 풀에 대해 지원되지 않습니다. 장애 조치 그룹을 만든 후 기본 또는 보조 서버를 다른 구독으로 이동하면 장애 조치 요청 및 기타 작업이 실패할 수 있습니다.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP 워크로드에 읽기-쓰기 수신기 사용

OLTP 작업을 수행할 때 `<fog-name>.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새 주 데이터베이스로 리디렉션됩니다.

### <a name="using-read-only-listener-for-read-only-workload"></a>읽기 전용 워크로드에 읽기 전용 수신기 사용

특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 읽기 전용 세션의 경우 `<fog-name>.secondary.database.windows.net`을 서버 URL로 사용하면 자동으로 보조 데이터베이스에 연결됩니다. 또한 을 사용하여 `ApplicationIntent=ReadOnly`연결 문자열 읽기 의도를 나타내는 것이 좋습니다. 장애 조치 후 또는 보조 서버가 오프라인 상태가 되는 경우 읽기 전용 워크로드가 다시 연결할 `AllowReadOnlyFailoverToPrimary` 수 있도록 하려면 장애 조치 정책의 속성을 구성해야 합니다.

### <a name="preparing-for-performance-degradation"></a>성능 저하 준비

일반적인 Azure 응용 프로그램은 여러 Azure 서비스를 사용하며 여러 구성 요소로 구성됩니다. 장애 조치 그룹의 자동 장애 조치(failover)는 Azure SQL 구성 요소만 있는 상태에 따라 트리거됩니다. 기본 지역의 다른 Azure 서비스는 중단의 영향을 받지 않을 수 있으며 해당 구성 요소는 해당 지역에서 계속 사용할 수 있습니다. 기본 데이터베이스가 DR 영역으로 전환되면 종속 구성 요소 간의 대기 시간이 증가할 수 있습니다. 응용 프로그램의 성능에 대한 대기 시간이 증가하지 않도록 하려면 DR 지역의 모든 응용 프로그램 구성 요소의 중복성을 확인하고 이러한 [네트워크 보안 지침을](#failover-groups-and-network-security)따릅니다.

### <a name="preparing-for-data-loss"></a>데이터 손실 대비

중단이 감지되면 SQL은 `GracePeriodWithDataLossHours`에서 지정한 기간을 기다립니다. 기본값은 1시간입니다. 데이터 손실을 감당할 수 없는 `GracePeriodWithDataLossHours` 경우 24시간과 같이 충분히 많은 수로 설정해야 합니다. 수동 그룹 장애 조치(failover)를 사용하여 보조 데이터베이스에서 주 데이터베이스로 장애 복구할 수 있습니다.

> [!IMPORTANT]
> 지역 복제를 사용하는 800개 이하의 DTU 및 250개 초과의 데이터베이스가 있는 탄력적 풀에는 계획된 장애 조치 지연 및 성능 저하가 포함된 문제가 발생할 수 있습니다.  이러한 문제는 지역에서 복제 엔드포인트가 지리적으로 광범위하게 분리되어 있거나 여러 보조 엔드포인트가 각 데이터베이스에 대해 사용되는 경우 쓰기 집약적 작업에 발생할 가능성이 높습니다.  이러한 문제의 증상은 지역에서 복제 지연이 시간에 따라 증가하는 경우에 표시됩니다.  이러한 지연은 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 사용하여 모니터링할 수 있습니다.  이러한 문제가 발생하는 경우 완화 방안에는 풀 DTU의 수를 늘리거나 동일한 풀에 지역에서 복제된 데이터베이스 수를 줄이는 방법이 있습니다.

### <a name="changing-secondary-region-of-the-failover-group"></a>장애 조치 그룹의 보조 영역 변경

변경 순서를 설명하기 위해 서버 A가 기본 서버이고 서버 B가 기존 보조 서버이고 서버 C가 세 번째 리전의 새 보조 서버라고 가정합니다.  전환을 수행하려면 다음 단계를 따르십시오.

1.  [활성 지역 복제를](sql-database-active-geo-replication.md)사용하여 서버 A에서 서버 C까지 각 데이터베이스의 추가 보조 데이터베이스를 만듭니다. 서버 A의 각 데이터베이스에는 서버 B와 서버 C에 각각 하나씩 두 개의 보조 데이터베이스가 있습니다. 이렇게 하면 전환 중에 기본 데이터베이스가 보호된 상태로 유지됩니다.
2.  장애 조치 그룹을 삭제합니다. 이 시점에서 로그인실패합니다. 이는 장애 조치 그룹 리스너에 대한 SQL 별칭이 삭제되었으며 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문입니다.
3.  서버 A와 C 간에 이름이 같은 장애 조치 그룹을 다시 만듭니다. 이 시점에서 로그인 실패를 중지 합니다.
4.  서버 A의 모든 기본 데이터베이스를 새 장애 조치 그룹에 추가합니다.
5.  서버 B를 삭제합니다. B의 모든 데이터베이스는 자동으로 삭제됩니다. 


### <a name="changing-primary-region-of-the-failover-group"></a>장애 조치 그룹의 기본 영역 변경

변경 순서를 설명하기 위해 서버 A가 기본 서버이고 서버 B가 기존 보조 서버이고 서버 C가 세 번째 영역의 새 기본 서버라고 가정합니다.  전환을 수행하려면 다음 단계를 따르십시오.

1.  계획된 장애 조치(failover)를 수행하여 기본 서버를 B. 서버 A로 전환하면 새 보조 서버가 됩니다. 장애 조치(failover)로 인해 몇 분의 가동 중지 시간이 발생할 수 있습니다. 실제 시간은 장애 조치 그룹의 크기에 따라 달라집니다.
2.  [활성 지역 복제를](sql-database-active-geo-replication.md)사용하여 서버 B에서 서버 C에 대한 각 데이터베이스의 추가 보조 데이터베이스를 만듭니다. 서버 B의 각 데이터베이스에는 서버 A와 서버 C에 각각 하나씩 두 개의 보조 데이터베이스가 있습니다. 이렇게 하면 전환 중에 기본 데이터베이스가 보호된 상태로 유지됩니다.
3.  장애 조치 그룹을 삭제합니다. 이 시점에서 로그인실패합니다. 이는 장애 조치 그룹 리스너에 대한 SQL 별칭이 삭제되었으며 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문입니다.
4.  서버 A와 C 간에 이름이 같은 장애 조치 그룹을 다시 만듭니다. 이 시점에서 로그인 실패를 중지 합니다.
5.  B의 모든 기본 데이터베이스를 새 장애 조치 그룹에 추가합니다. 
6.  장애 조치 그룹의 계획된 장애 조치(Failover)를 수행하여 B및 C를 전환합니다. 이제 서버 C가 기본 및 B가 됩니다. 서버 A의 모든 보조 데이터베이스는 C의 프라이머리에 자동으로 연결됩니다. 1단계에서와 마찬가지로 장애 조치(failover)는 몇 분의 가동 중지 시간이 발생할 수 있습니다.
6.  서버 A를 삭제합니다. A의 모든 데이터베이스는 자동으로 삭제됩니다.

> [!IMPORTANT]
> 장애 조치 그룹이 삭제되면 수신기 끝점에 대한 DNS 레코드도 삭제됩니다. 이 시점에서 다른 사람이 동일한 이름의 장애 조치 그룹 또는 서버 별칭을 만들 확률이 0이 아닌 경우 다시 사용하지 못하게 됩니다. 위험을 최소화하려면 제네릭 장애 조치 그룹 이름을 사용하지 마십시오.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>관리되는 인스턴스를 사용하여 장애 조치 그룹을 사용하는 모범 사례

자동 장애 조치(failover) 그룹은 주 인스턴스에서 구성되어야 하며 다른 Azure 지역의 보조 인스턴스에 연결됩니다.  인스턴스의 모든 데이터베이스가 보조 인스턴스에 복제됩니다.

다음 다이어그램은 관리되는 인스턴스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> [장애 조치 그룹에 관리되는 인스턴스 추가를](sql-database-managed-instance-failover-group-tutorial.md) 참조하여 장애 조치 그룹을 사용하도록 관리되는 인스턴스를 추가하는 자세한 단계별 자습서를 참조하세요.

응용 프로그램에서 관리되는 인스턴스를 데이터 계층으로 사용하는 경우 비즈니스 연속성을 위해 디자인할 때 다음 일반 지침을 따릅니다.

### <a name="creating-the-secondary-instance"></a>보조 인스턴스 만들기 

장애 조치(failover) 후 주 인스턴스에 대한 무중단 연결을 보장하려면 주 인스턴스와 보조 인스턴스가 동일한 DNS 영역에 있어야 합니다. 장애 조치 그룹의 두 인스턴스 중 하나에 대한 클라이언트 연결을 인증하기 위해 동일한 SAN(다중 도메인) 인증서를 사용할 수 있습니다. 애플리케이션이 프로덕션 배포에 사용할 준비가 되면 다른 지역에 보조 인스턴스를 만들고 주 인스턴스와 DNS 영역을 공유하는지 확인합니다. Azure 포털, PowerShell `DNS Zone Partner` 또는 REST API를 사용하여 선택적 매개 변수를 지정하여 수행할 수 있습니다.

> [!IMPORTANT]
> 서브넷에서 생성된 첫 번째 인스턴스는 동일한 서브넷의 모든 후속 인스턴스에 대한 DNS 영역을 결정합니다. 즉, 동일한 서브넷의 두 인스턴스가 다른 DNS 영역에 속할 수 없습니다.

기본 인스턴스와 동일한 DNS 영역에서 보조 인스턴스를 만드는 것에 대한 자세한 내용은 [보조 관리 인스턴스 만들기를](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance)참조하십시오.

### <a name="enabling-replication-traffic-between-two-instances"></a>두 인스턴스 간의 복제 트래픽 사용

각 인스턴스가 자체 VNet에 격리되므로 이러한 VNet 간의 양방향 트래픽을 허용해야 합니다. [Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>다른 구독에서 관리되는 인스턴스 간에 장애 조치 그룹 만들기

두 개의 서로 다른 구독에서 관리되는 인스턴스 간에 장애 조치 그룹을 만들 수 있습니다. PowerShell API를 사용하는 경우 보조 인스턴스에 `PartnerSubscriptionId` 대한 매개 변수를 지정하여 수행할 수 있습니다. REST API를 사용하는 경우 매개 `properties.managedInstancePairs` 변수에 포함된 각 인스턴스 ID에는 자체 subscriptionID가 있을 수 있습니다.
  
> [!IMPORTANT]
> Azure 포털은 다른 구독에서 장애 조치 그룹 생성을 지원하지 않습니다. 또한 다른 구독 및/또는 리소스 그룹에서 기존 장애 조치 그룹의 경우 기본 인스턴스의 포털을 통해 장애 조치(failover)를 수동으로 시작할 수 없습니다. 대신 지역 보조 인스턴스에서 시작합니다.

### <a name="managing-failover-to-secondary-instance"></a>장애 조치(장애 조치)에서 보조 인스턴스로 관리

장애 조치(failover) 그룹은 인스턴스에 있는 모든 데이터베이스의 장애 조치(failover)를 관리합니다. 그룹을 만들면 인스턴스의 각 데이터베이스가 자동으로 보조 인스턴스에 지역 복제됩니다. 장애 조치(failover) 그룹을 사용하여 데이터베이스 하위 집합의 부분 장애 조치(failover)를 시작할 수 없습니다.

> [!IMPORTANT]
> 주 인스턴스에서 데이터베이스를 제거하면 지역 보조 인스턴스에서도 자동으로 삭제됩니다.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP 워크로드에 읽기-쓰기 수신기 사용

OLTP 작업을 수행할 때 `<fog-name>.zone_id.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새로운 주 데이터베이스로 리디렉션됩니다. 보조 인스턴스는 DNS 영역을 기본 인스턴스와 공유하기 때문에 클라이언트 응용 프로그램은 동일한 SAN 인증서를 사용하여 DNS 영역에 다시 연결할 수 있습니다.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>읽기 전용 수신기를 사용하여 보조 인스턴스에 연결

특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 지역 복제된 보조 데이터베이스에 직접 연결하려는 경우 `server.secondary.zone_id.database.windows.net`을 서버 URL로 사용하면 지역 복제된 보조 데이터베이스에 직접 연결됩니다.

> [!NOTE]
> 특정 서비스 계층에서 Azure SQL Database는 [읽기 전용 복제본](sql-database-read-scale-out.md)을 사용하여 읽기 전용 복제본의 용량 및 연결 문자열의 `ApplicationIntent=ReadOnly` 매개 변수를 통해 읽기 전용 쿼리 워크로드를 부하를 분산할 수 있도록 지원합니다. 지역 복제된 보조 데이터베이스를 구성한 경우 이 기능을 사용하여 주 위치 또는 지역 복제된 위치에 있는 읽기 전용 복제본에 연결할 수 있습니다.
> - 주 위치의 읽기 전용 복제본에 연결하려면 `<fog-name>.zone_id.database.windows.net`을 사용합니다.
> - 보조 위치에서 읽기 전용 복제본에 연결하려면 `<fog-name>.secondary.zone_id.database.windows.net`을 사용합니다.

### <a name="preparing-for-performance-degradation"></a>성능 저하 준비

일반적인 Azure 응용 프로그램은 여러 Azure 서비스를 사용하며 여러 구성 요소로 구성됩니다. 장애 조치 그룹의 자동 장애 조치(failover)는 Azure SQL 구성 요소만 있는 상태에 따라 트리거됩니다. 기본 지역의 다른 Azure 서비스는 중단의 영향을 받지 않을 수 있으며 해당 구성 요소는 해당 지역에서 계속 사용할 수 있습니다. 기본 데이터베이스가 DR 영역으로 전환되면 종속 구성 요소 간의 대기 시간이 증가할 수 있습니다. 응용 프로그램의 성능에 대한 대기 시간이 증가하지 않도록 하려면 DR 지역의 모든 응용 프로그램 구성 요소의 중복성을 확인하고 이러한 [네트워크 보안 지침을](#failover-groups-and-network-security)따릅니다.

### <a name="preparing-for-data-loss"></a>데이터 손실 대비

가동 중단이 감지되는 경우 SQL은 중요한 지식에 대한 데이터 손실이 없으면 읽기 쓰기 장애 조치를 자동으로 트리거합니다. 그렇지 않으면 `GracePeriodWithDataLossHours`에서 지정한 기간 동안 대기합니다. `GracePeriodWithDataLossHours`를 지정한 경우 데이터 손실을 준비합니다. 일반적으로 중단 시에 Azure에서는 가용성을 우선으로 합니다. 데이터 손실을 방지하려는 경우 GracePeriodWithDataLossHours를 24시간과 같은 충분히 큰 숫자로 설정해야 합니다.

읽기/쓰기 수신기의 DNS 업데이트는 장애 조치(failover)가 시작된 후 즉시 발생합니다. 이 작업으로 인한 데이터 손실은 없습니다. 그러나 일반적인 조건에서 데이터베이스 역할을 전환하는 데 최대 5분 정도 걸릴 수 있습니다. 완료될 때까지 새로운 주 인스턴스의 일부 데이터베이스는 계속 읽기 전용입니다. PowerShell을 사용하여 장애 조치(failover)가 시작되는 경우 전체 작업이 동기식입니다. Azure 포털을 사용하여 시작된 경우 UI는 완료 상태를 나타냅니다. REST API를 사용하여 시작하는 경우, 표준 Azure Resource Manager의 폴링 메커니즘을 사용하여 완료되었는지 모니터링합니다.

> [!IMPORTANT]
> 수동 그룹 장애 조치(failover)를 사용하여 주 데이터베이스를 원래 위치로 다시 이동할 수 있습니다. 장애 조치(failover)를 유발한 중단이 완화되면 주 데이터베이스를 원래 위치로 이동할 수 있습니다. 이렇게 하려면 그룹의 수동 장애 조치(failover)를 시작해야 합니다.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>장애 조치 그룹의 보조 영역 변경

인스턴스 A가 기본 인스턴스이고 인스턴스 B가 기존 보조 인스턴스이고 인스턴스 C가 세 번째 리전의 새 보조 인스턴스라고 가정해 보겠습니다.  전환을 수행하려면 다음 단계를 따르십시오.

1.  A와 크기가 같고 동일한 DNS 영역에 인스턴스 C를 만듭니다. 
2.  인스턴스 A와 B 사이의 장애 조치 그룹을 삭제합니다. 이 시점에서 장애 조치 그룹 리스너에 대한 SQL 별칭이 삭제되고 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문에 로그인이 실패합니다. 보조 데이터베이스는 기본 데이터베이스와 연결이 끊어지고 읽기 쓰기 데이터베이스가 됩니다. 
3.  인스턴스 A와 C 사이에 이름이 같은 [장애 조치](sql-database-managed-instance-failover-group-tutorial.md)그룹을 만듭니다. 이는 데이터 크기 작업이며 인스턴스 A의 모든 데이터베이스가 시드되고 동기화될 때 완료됩니다.
4.  불필요한 요금을 피하기 위해 필요하지 않은 경우 인스턴스 B를 삭제합니다.

> [!NOTE]
> 2단계 이후 3단계가 완료될 때까지 A는 인스턴스 A의 치명적인 오류로부터 보호되지 않습니다.

### <a name="changing-primary-region-of-the-failover-group"></a>장애 조치 그룹의 기본 영역 변경

인스턴스 A가 기본 인스턴스이고 인스턴스 B가 기존 보조 인스턴스이고 인스턴스 C가 세 번째 리전의 새 기본 인스턴스라고 가정해 보겠습니다.  전환을 수행하려면 다음 단계를 따르십시오.

1.  B와 크기가 같고 DNS 영역이 같은 인스턴스 C를 만듭니다. 
2.  인스턴스 B에 연결하고 수동으로 장애 조치(failover)를 통해 기본 인스턴스를 B로 전환합니다. 인스턴스 A는 자동으로 새 보조 인스턴스가 됩니다.
3.  인스턴스 A와 B 사이의 장애 조치 그룹을 삭제합니다. 이 시점에서 장애 조치 그룹 리스너에 대한 SQL 별칭이 삭제되고 게이트웨이가 장애 조치 그룹 이름을 인식하지 못하기 때문에 로그인이 실패합니다. 보조 데이터베이스는 기본 데이터베이스와 연결이 끊어지고 읽기 쓰기 데이터베이스가 됩니다. 
4.  인스턴스 A와 C 사이에 이름이 같은 [장애 조치](sql-database-managed-instance-failover-group-tutorial.md)그룹을 만듭니다. 이는 데이터 크기 작업이며 인스턴스 A의 모든 데이터베이스가 시드되고 동기화될 때 완료됩니다.
5.  불필요한 요금을 피하기 위해 필요하지 않은 경우 인스턴스 A를 삭제합니다.

> [!NOTE] 
> 3단계 이후 4단계가 완료될 때까지 A는 인스턴스 A의 치명적인 오류로부터 보호되지 않습니다.

> [!IMPORTANT]
> 장애 조치 그룹이 삭제되면 수신기 끝점에 대한 DNS 레코드도 삭제됩니다. 이 시점에서 다른 사람이 동일한 이름의 장애 조치 그룹 또는 서버 별칭을 만들 확률이 0이 아닌 경우 다시 사용하지 못하게 됩니다. 위험을 최소화하려면 제네릭 장애 조치 그룹 이름을 사용하지 마십시오.

## <a name="failover-groups-and-network-security"></a>장애 조치 그룹 및 네트워크 보안

일부 응용 프로그램의 경우 보안 규칙에 따라 데이터 계층에 대한 네트워크 액세스가 VM, 웹 서비스 등과 같은 특정 구성 요소 또는 구성 요소로 제한됩니다. 이 요구 사항은 비즈니스 연속성 설계 및 장애 조치 그룹의 사용에 몇 가지 과제를 제시합니다. 이러한 제한된 액세스를 구현할 때 다음 옵션을 고려하십시오.

### <a name="using-failover-groups-and-virtual-network-rules"></a>장애 조치 그룹 및 가상 네트워크 규칙 사용

[Virtual Network 서비스 엔드포인트 및 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 사용하여 SQL 데이터베이스에 대한 액세스를 제한하는 경우 각 Virtual Network 서비스 엔드포인트가 하나의 Azure 지역에만 적용됩니다. 엔드포인트를 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다. 따라서 동일한 지역에 배포된 클라이언트 애플리케이션만 주 데이터베이스에 연결할 수 있습니다. 장애 조치가 SQL 클라이언트 세션을 다른 (보조) 지역의 서버로 다시 라우팅하는 결과를 발생시키므로 해당 지역 외부에 있는 클라이언트에서 시작된 경우 이러한 세션에 실패합니다. 이런 이유로 참여하는 서버가 Virtual Network 규칙에 포함된 경우 자동 장애 조치(failover) 정책을 사용할 수 없습니다. 수동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. 보조 지역에서 애플리케이션(웹 서비스, 가상 머신 등) 프런트 엔드 구성 요소의 중복 복사본을 프로비전합니다.
2. 기본 및 보조 서버에 대한 [가상 네트워크 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 개별적으로 구성합니다.
3. [트래픽 관리자 구성을 사용하여 프런트 엔드 장애 조치](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)를 사용하도록 설정합니다.
4. 중단이 검색되면 수동 장애 조치(failover)를 시작합니다. 이 옵션은 프런트 엔드와 데이터 계층 간에 일관된 대기 시간을 필요로 하는 애플리케이션에 대해 최적화되고 프런트 엔드, 데이터 계층 또는 둘 모두가 중단의 영향을 받는 경우 복구를 지원합니다.

> [!NOTE]
> **읽기 전용 수신기**를 사용하여 읽기 전용 워크로드의 부하를 분산하는 경우 이 워크로드가 보조 데이터베이스에 연결할 수 있도록 보조 지역의 VM 또는 다른 리소스에서 실행되는지 확인합니다.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>장애 조치 그룹 및 SQL 데이터베이스 방화벽 규칙 사용

비즈니스 연속성 계획이 자동 장애 조치에서 그룹을 사용하는 장애 조치가 필요한 경우 기존 방화벽 규칙을 사용하여 SQL 데이터베이스에 대한 액세스를 제한할 수 있습니다. 자동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. [공용 IP 만들기](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [공용 부하 분산 장치를 만들고](../load-balancer/quickstart-load-balancer-standard-public-portal.md) 여기에 공용 IP를 할당합니다.
3. 프런트 엔드 구성 요소에 대한 [가상 네트워크 및 가상 머신을 만듭니다.](../load-balancer/quickstart-load-balancer-standard-public-portal.md)
4. [네트워크 보안 그룹을 만들고](../virtual-network/security-overview.md) 인바운드 연결을 구성합니다.
5. 'Sql' [서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하여 아웃바운드 연결이 Azure SQL 데이터베이스에 대해 열려 있는지 확인합니다.
6. [SQL 데이터베이스 방화벽 규칙](sql-database-firewall-configure.md)을 만들어서 1단계에서 만든 공용 IP 주소에서 인바운드 트래픽을 허용합니다.

아웃바운드 액세스를 구성하는 방법 및 방화벽 규칙에서 사용할 IP에 대한 자세한 내용은 [부하 분산 장치 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md)을 참조하세요.

위의 구성에서는 자동 장애 조치가 프런트 엔드 구성 요소에서 연결을 차단하지 않는지 확인하고 애플리케이션이 프런트 엔드와 데이터 계층 간의 긴 대기 시간을 허용할 수 있다고 가정합니다.

> [!IMPORTANT]
> 지역 중단에 대한 비즈니스 연속성을 보장하기 위해 프런트 엔드 구성 요소와 데이터베이스 모두에 대한 지리적 복제를 확인해야 합니다.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>관리되는 인스턴스와 VNet 간의 지역 복제 활성화

두 개의 서로 다른 리전에서 기본 인스턴스와 보조 관리 되는 인스턴스 간에 장애 조치 그룹을 설정 하면 각 인스턴스는 독립적인 가상 네트워크를 사용 하 여 격리 됩니다. 이러한 VNet 간의 복제 트래픽을 허용하려면 이러한 필수 구성 조건이 충족되도록 합니다.

1. 관리되는 두 인스턴스는 서로 다른 Azure 지역에 있어야 합니다.
2. 관리되는 두 인스턴스는 동일한 서비스 계층이어야 하며 저장소 크기가 동일해야 합니다.
3. 보조 관리 인스턴스는 비어 있어야 합니다(사용자 데이터베이스 없음).
4. 관리되는 인스턴스에서 사용하는 가상 네트워크는 VPN [게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [익스프레스 경로를](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)통해 연결되어야 합니다. 두 개의 가상 네트워크가 온-프레미스 네트워크를 통해 연결되면 포트 5022 및 11000-11999를 차단하는 방화벽 규칙이 없는지 확인합니다. 전역 VNet 피어링은 지원되지 않습니다.
5. 두 관리되는 인스턴스 VNet에는 겹치는 IP 주소가 있을 수 없습니다.
6. 포트 5022와 범위 11000~ 12000이 다른 관리되는 인스턴스의 서브넷에서 연결하기 위해 열린 인바운드 및 아웃바운드가 되도록 NSG(네트워크 보안 그룹)를 설정해야 합니다. 이는 인스턴스 간에 복제 트래픽을 허용하기 위한 것입니다.

   > [!IMPORTANT]
   > NSG 보안 규칙을 잘못 구성하면 데이터베이스 복사 작업이 중단됩니다.

7. 보조 인스턴스는 올바른 DNS 영역 ID로 구성됩니다. DNS 영역은 관리되는 인스턴스 및 가상 클러스터의 속성이며 해당 ID는 호스트 이름 주소에 포함됩니다. 영역 ID는 각 VNet에서 첫 번째 관리되는 인스턴스를 만들고 동일한 하위 넷의 다른 모든 인스턴스에 동일한 ID가 할당될 때 임의의 문자열로 생성됩니다. 할당되면 DNS 영역을 수정할 수 없습니다. 동일한 장애 조치 그룹에 포함된 관리되는 인스턴스는 DNS 영역을 공유해야 합니다. 보조 인스턴스를 만들 때 기본 인스턴스의 영역 ID를 DnsZonePartner 매개 변수의 값으로 전달하여 이 작업을 수행합니다. 

   > [!NOTE]
   > 관리되는 인스턴스를 사용하여 장애 조치 그룹을 구성하는 방법에 대한 자세한 자습서는 [관리되는 인스턴스를 장애 조치 그룹에 추가참조.](sql-database-managed-instance-failover-group-tutorial.md)

## <a name="upgrading-or-downgrading-a-primary-database"></a>주 데이터베이스 업그레이드 또는 다운그레이드

보조 데이터베이스와의 연결을 끊지 않고도 주 데이터베이스를 다른 컴퓨팅 크기(동일한 서비스 계층 내, 범용 및 중요 비즈니스용 사이 아님)로 업그레이드하거나 다운그레이드할 수 있습니다. 업그레이드할 때는 모든 보조 데이터베이스를 먼저 업그레이드한 다음 기본 데이터베이스를 업그레이드하는 것이 좋습니다. 다운그레이드할 때 순서를 반대로 합니다: 먼저 기본 데이터베이스를 다운그레이드한 다음 모든 보조 데이터베이스를 다운그레이드합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

이 시퀀스는 하위 SKU의 보조 데이터베이스가 오버로드되고 업그레이드 또는 다운그레이드 프로세스 중에 다시 시드해야 하는 문제를 방지하는 데 특히 권장됩니다. 또한 기본 읽기 전용으로 만들어 모든 읽기 쓰기 워크로드에 대한 영향을 줄 수 있으므로 문제를 방지할 수 있습니다.

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. 호출은 `sp_wait_for_database_copy_sync` 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송될 때까지 호출 스레드를 차단합니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. `sp_wait_for_database_copy_sync`은 특정 연속 복사 링크로 범위가 조정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> `sp_wait_for_database_copy_sync`장애 조치 후 데이터 손실을 방지하지만 읽기 액세스에 대한 전체 동기화를 보장하지는 않습니다. 프로시저 호출로 `sp_wait_for_database_copy_sync` 인한 지연은 중요할 수 있으며 호출 시 트랜잭션 로그의 크기에 따라 다릅니다.

## <a name="failover-groups-and-point-in-time-restore"></a>장애 조치(failover) 그룹 및 지정 시간 복원

장애 조치(failover) 그룹과 함께 지정 시간 복원을 사용하는 방법에 대한 자세한 내용은 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="limitations-of-failover-groups"></a>장애 조치 그룹의 제한 사항

다음과 같은 제한 사항을 고려해야 합니다.

- 장애 조치 그룹은 동일한 Azure 지역의 두 서버 또는 인스턴스 간에 만들 수 없습니다.
- 장애 조치 그룹의 이름을 바꿀 수 없습니다. 그룹을 삭제하고 다른 이름으로 다시 만들어야 합니다. 
- 장애 조치 그룹의 인스턴스에서는 데이터베이스 이름 변경이 지원되지 않습니다. 데이터베이스 이름을 바꾸려면 장애 조치 그룹을 일시적으로 삭제해야 합니다.

## <a name="programmatically-managing-failover-groups"></a>프로그래밍 방식으로 장애 조치(failover) 그룹 관리

앞에서 설명한 대로, 자동 장애 조치(failover) 그룹과 활성 지역 복제는 Azure PowerShell 및 REST API를 사용하여 프로그래밍 방식으로 관리할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)을 참조하세요.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [제거-아즈Sql데이터베이스 장애 조치 그룹](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 서버에서 장애 조치 그룹을 제거합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 장애 조치 그룹의 구성 검색 |
| [세트-아즈Sql데이터베이스장애조치그룹](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |장애 조치 그룹의 구성을 수정합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 장애 조치 그룹의 장애 조치(failover)를 보조 서버로 트리거합니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|장애 조치 그룹에 하나 이상의 데이터베이스를 추가합니다.|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>관리되는 인스턴스를 사용하여 SQL 데이터베이스 장애 조치 그룹 관리

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |이 명령은 장애 조치 그룹을 만들고 기본 인스턴스와 보조 인스턴스 모두에 등록합니다.|
| [세트-아즈Sql데이터베이스인스턴스장애그룹](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 구성을 수정합니다.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 구성 검색|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 장애 조치(failover)를 보조 인스턴스로 트리거합니다.|
| [제거-아즈Sql데이터베이스인스턴스장애그룹](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 장애 조치(failover) 그룹을 제거합니다.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| 명령 | 설명 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [az sql 장애 조치 그룹 삭제](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [az sql 장애 조치 그룹 쇼](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 장애 조치 그룹 구성 검색 |
| [az sql 장애 조치 그룹 업데이트](/cli/azure/sql/failover-group#az-sql-failover-group-update) |장애 조치 그룹의 구성을 수정하거나 장애 조치 그룹에 하나 이상의 데이터베이스를 추가합니다.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 장애 조치 그룹의 장애 조치(failover)를 보조 서버로 트리거합니다. |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>관리되는 인스턴스를 사용하여 SQL 데이터베이스 장애 조치 그룹 관리

| 명령 | 설명 |
| --- | --- |
| [az sql 인스턴스-장애 조치-그룹 만들기](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | 이 명령은 장애 조치 그룹을 만들고 기본 인스턴스와 보조 인스턴스 모두에 등록합니다. |
| [az sql 인스턴스-장애 조치-그룹 업데이트](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | 장애 조치 그룹의 구성을 수정합니다.|
| [az SQL 인스턴스-장애 조치 그룹 표시](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | 장애 조치 그룹의 구성 검색|
| [az sql 인스턴스-장애 조치 그룹 집합-기본](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | 장애 조치 그룹의 장애 조치(failover)를 보조 인스턴스로 트리거합니다.|
| [az sql 인스턴스-장애 조치-그룹 삭제](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | 장애 조치(failover) 그룹을 제거합니다. |

* * *

> [!IMPORTANT]
> 샘플 스크립트는 [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)](scripts/sql-database-add-single-db-to-failover-group-powershell.md)를 참조하세요.

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: 단일 및 풀처리된 데이터베이스로 SQL 데이터베이스 장애 조치 그룹 관리

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 전체 데이터 동기화를 사용하여 현재 주 서버에서 보조 서버로 장애 조치(failover)를 트리거합니다.|
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | 데이터를 동기화하지 않고 현재 주 서버에서 보조 서버로 장애 조치(failover)를 트리거합니다. 이 작업을 수행하면 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 장애 조치 그룹의 구성을 검색합니다. |
| [서버별 장애 조치(failover) 그룹 나열](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 서버에서 장애 조치 그룹을 나열합니다. |
| [장애 조치(failover) 그룹 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 장애 조치 그룹의 구성을 업데이트합니다. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: 관리되는 인스턴스를 사용하는 장애 조치 그룹 관리

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 장애 조치 그룹의 구성을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 인스턴스에서 장애 조치 그룹 제거 |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 전체 데이터 동기화를 사용하여 현재 기본 인스턴스에서 이 인스턴스로 장애 조치(failover)를 트리거합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | 데이터를 동기화하지 않고 현재 기본 인스턴스에서 보조 인스턴스로 장애 조치(failover)를 트리거합니다. 이 작업을 수행하면 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 장애 조치 그룹의 구성을 검색합니다. |
| [장애 조치(failover) 그룹 나열 - 위치별 목록](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 위치에 있는 장애 조치(failover) 그룹을 나열합니다. |

## <a name="next-steps"></a>다음 단계

- 자세한 자습서는
    - [장애 조치 그룹에 단일 데이터베이스 추가](sql-database-single-database-failover-group-tutorial.md)
    - [장애 조치(failover) 그룹에 탄력적 풀 추가](sql-database-elastic-pool-failover-group-tutorial.md)
    - [장애 조치 그룹에 관리되는 인스턴스 추가](sql-database-managed-instance-failover-group-tutorial.md)
- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [PowerShell을 사용하여 Azure SQL Database에 단일 데이터베이스에 대한 활성 지역 복제 구성](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [PowerShell을 사용하여 Azure SQL Database에 풀링된 데이터베이스에 대한 활성 지역 복제 구성](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [PowerShell을 사용하여 Azure SQL Database 단일 데이터베이스를 장애 조치(failover) 그룹에 추가](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
- Azure SQL Database 자동화된 백업에 대해 자세히 알아보려면 [SQL Database 자동화된 백업을](sql-database-automated-backups.md)참조하십시오.
- 복구를 위해 자동화된 백업을 사용하는 방법에 대해 알아보려면 [서비스 시작 백업에서 데이터베이스 복원을](sql-database-recovery-using-backups.md)참조하십시오.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
