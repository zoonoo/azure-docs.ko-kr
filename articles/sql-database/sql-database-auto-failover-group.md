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
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421379"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치(failover)를 사용할 수 있습니다.

Auto-failover groups is a SQL Database feature that allows you to manage replication and failover of a group of databases on a SQL Database server or all databases in a managed instance to another region. It is a declarative abstraction on top of the existing [active geo-replication](sql-database-active-geo-replication.md) feature, designed to simplify deployment and management of geo-replicated databases at scale. 수동으로 장애 조치(failover)를 시작하거나, 사용자 정의 정책에 따라 SQL Database 서비스에 위임할 수 있습니다. 사용자 정의 정책 옵션을 사용하면 치명적인 오류 또는 계획되지 않은 다른 이벤트가 발생하여 주 지역에서 SQL Database 서비스의 가용성이 완전히 또는 부분적으로 상실될 경우 보조 지역에서 여러 관련 데이터베이스를 자동으로 복구할 수 있습니다. A failover group can include one or multiple databases, typically used by the same application. 또한 읽을 수 있는 보조 데이터베이스를 사용하여 읽기 전용 쿼리 워크로드를 오프로드할 수 있습니다. 자동 장애 조치 그룹에 여러 데이터베이스가 포함되기 때문에 주 서버에서 이러한 데이터베이스를 구성해야 합니다. 자동 장애 조치 그룹은 그룹의 모든 데이터베이스를 다른 지역에 있는 하나의 보조 서버로만 복제할 수 있도록 지원합니다.

> [!NOTE]
> SQL Database 서버에서 단일 또는 풀링된 데이터베이스를 사용 중이며 동일하거나 다른 지역에 여러 개의 보조 데이터베이스를 만들려는 경우 [활성 지역 복제](sql-database-active-geo-replication.md)를 사용합니다. 

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 그룹 내 데이터베이스 중 하나 이상에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. Typically these are incidents that cannot be self-mitigated by the built-in automatic high availability operations. The examples of failover triggers include an incident caused by a SQL tenant ring or control ring being down due to an OS kernel memory leak on several compute nodes, or an incident caused by one or more tenant rings being down because a wrong network cable was cut during routine hardware decommissioning.  For more information, see [SQL Database High Availability](sql-database-high-availability.md).

또한 자동 장애 조치 그룹은 장애 조치하는 동안 변경되지 않는 읽기-쓰기 및 읽기 전용 수신기 끝점을 제공합니다. 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계 없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치(failover)가 완료되면 엔드포인트를 새 지역으로 리디렉션하도록 DNS 레코드가 자동으로 업데이트됩니다. 특정 RPO 및 RTO 데이터에 대해서는 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 SQL Database 서버 또는 관리되는 인스턴스의 데이터베이스에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 다음을 사용하여 자동 장애 조치(failover) 그룹을 관리할 수 있습니다.

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: 그룹 장애 조치](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: 장애 조치(failover) 그룹](https://docs.microsoft.com/rest/api/sql/failovergroups)

장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 스토리지 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="auto-failover-group-terminology-and-capabilities"></a>자동 장애 조치(failover) 그룹 용어 및 기능

- **Failover group (FOG)**

  A failover group is a named group of databases managed by a single SQL Database server or within a single managed instance that can fail over as a unit to another region in case all or some primary databases become unavailable due to an outage in the primary region. When created for managed instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  
  > [!IMPORTANT]
  > The name of the failover group must be globally unique within the `.database.windows.net` domain.

- **SQL Database 서버**

     SQL Database 서버를 사용하면 단일 SQL Database 서버의 사용자 데이터베이스를 일부 또는 모두 장애 조치(failover) 그룹에 배치할 수 있습니다. 또한 SQL Database 서버는 단일 SQL Database 서버에서 여러 개의 장애 조치(failover) 그룹을 지원합니다.

- **주**

  The SQL Database server or managed instance that hosts the primary databases in the failover group.

- **보조**

  The SQL Database server or managed instance that hosts the secondary databases in the failover group. 보조 서버는 주 서버와 동일한 지역에 있을 수 없습니다.

- **장애 조치(failover) 그룹에 단일 데이터베이스 추가**

  동일한 SQL Database 서버에 있는 여러 개의 단일 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 장애 조치(failover) 그룹에 단일 데이터베이스를 추가하면 동일한 버전과 컴퓨팅 크기를 사용하는 보조 데이터베이스가 보조 서버에 자동으로 만들어집니다.  장애 조치(failover) 그룹을 만들 때 해당 서버를 지정했습니다. 보조 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 새 보조 데이터베이스가 보조 서버에 만들어집니다.

  > [!IMPORTANT]
  > Make sure that the secondary server doesn't have a database with the same name unless it is an existing secondary database. In failover groups for managed instance all user databases are replicated. 장애 조치(failover) 그룹에서 복제를 위해 사용자 데이터베이스 하위 집합을 선택할 수 없습니다.

- **장애 조치(failover) 그룹에 탄력적 풀의 데이터베이스 추가**

  탄력적 풀에 있는 전체 또는 여러 개의 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 탄력적 풀에 주 데이터베이스가 있는 경우 동일한 이름을 가진 보조 데이터베이스가 탄력적 풀에 자동으로 만들어집니다(보조 풀). 장애 조치(failover) 그룹에서 만들 보조 데이터베이스를 호스트하기에 충분한 여유 용량과 정확히 동일한 이름을 가진 탄력적 풀이 보조 서버에 있는지 확인해야 합니다. 보조 풀에 보조 데이터베이스가 이미 있는 데이터베이스를 풀에 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 보조 데이터베이스가 서버에 이미 있는 데이터베이스를 추가하는 경우 새 보조 데이터베이스가 보조 풀에 만들어집니다.
  
- **DNS 영역**

  A unique ID that is automatically generated when a new instance is created. A multi-domain (SAN) certificate for this instance is provisioned to authenticate the client connections to any instance in the same DNS zone. The two managed instances in the same failover group must share the DNS zone.
  
  > [!NOTE]
  > A DNS zone ID is not required for failover groups created for SQL Database servers.

- **장애 조치 그룹 읽기-쓰기 수신기**

  A DNS CNAME record that points to the current primary's URL. It is created automatically when the failover group is created and allows the read-write SQL workload to transparently reconnect to the primary database when the primary changes after failover. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.database.windows.net`.

- **장애 조치 그룹 읽기 전용 수신기**

  보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기를 가리키는 DNS CNAME 레코드가 생성됩니다. It is created automatically when the failover group is created and allows the read-only SQL workload to transparently connect to the secondary using the specified load-balancing rules. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.secondary.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.secondary.database.windows.net`.

- **자동 장애 조치 정책**

  기본적으로 장애 조치(failover) 그룹은 자동 장애 조치(failover) 정책을 사용하여 구성됩니다. SQL Database 서비스는 오류가 검색되고 유예 기간이 만료된 후에 장애 조치(failover)를 트리거합니다. 영향의 규모로 인해 [SQL Database 서비스의 기본 제공 고가용성 인프라](sql-database-high-availability.md)를 통해 중단을 완화할 수 없음을 시스템에서 확인해야 합니다. 애플리케이션에서 장애 조치 워크플로를 제어하려는 경우 자동 장애 조치를 해제할 수 있습니다.
  
  > [!NOTE]
  > Because verification of the scale of the outage and how quickly it can be mitigated involves human actions by the operations team, the grace period cannot be set below one hour.  This  limitation applies to all databases in the failover group regardless of their data synchronization state. 

- **읽기 전용 장애 조치 정책**

  기본적으로 읽기 전용 수신기에 대한 장애 조치가 사용되지 않습니다. 이렇게 하면 보조 서버가 오프라인 상태일 때 주 서버의 성능이 영향을 받지 않습니다. 그러나 보조 서버가 복구될 때까지 읽기 전용 세션이 연결할 수 없음을 의미합니다. If you cannot tolerate downtime for the read-only sessions and are OK to temporarily use the primary for both read-only and read-write traffic at the expense of the potential performance degradation of the primary, you can enable failover for the read-only listener by configuring the `AllowReadOnlyFailoverToPrimary` property. In that case, the read-only traffic will be automatically redirected to the primary if the secondary is not available.

- **계획된 장애 조치**

   계획된 장애 조치(failover)는 보조 역할이 주 역할로 전환되기 전에 주 데이터베이스와 보조 데이터베이스 간에 전체 동기화를 수행합니다. 이렇게 하면 데이터 손실이 발생하지 않습니다. 계획된 장애 조치(failover)는 다음과 같은 시나리오에서 사용합니다.

  - 데이터 손실이 허용되지 않을 때 프로덕션에서 DR(재해 복구) 드릴 수행
  - 데이터베이스를 다른 지역으로 재배치
  - 중단이 완화된 후 데이터베이스를 주 지역으로 반환합니다(장애 복구(failback)).

- **계획되지 않은 장애 조치**

   계획되지 않은 장애 조치 또는 강제 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 보조 역할을 주 역할로 즉시 전환합니다. 이 작업으로 인해 데이터가 손실됩니다. 계획되지 않은 장애 조치(failover)는 주 데이터베이스에 액세스할 수 없는 중단 중에 복구 방법으로 사용됩니다. When the original primary is back online, it will automatically reconnect without synchronization and become a new secondary.

- **수동 장애 조치**

  자동 장애 조치 구성에 관계 없이 언제든지 장애 조치를 수동으로 시작할 수 있습니다. 자동 장애 조치(failover) 정책이 구성되지 않은 경우 장애 조치 그룹의 데이터베이스를 보조 데이터베이스에 복구하려면 수동 장애 조치(failover)를 수행해야 합니다. 강제 장애 조치 또는 친숙한 장애 조치(전체 데이터 동기화 사용)를 시작할 수 있습니다. 친숙한 장애 조치를 사용하면 주 데이터베이스를 보조 지역에 재배치할 수 있습니다. 장애 조치(failover)가 완료되면 새로운 주 데이터베이스에 대한 연결을 보장하기 위해 DNS 레코드가 자동으로 업데이트됩니다.

- **데이터 손실이 있는 유예 기간**

  주 및 보조 데이터베이스가 비동기 복제를 사용하여 동기화되기 때문에 장애 조치로 인해 데이터가 손실될 수 있습니다. 애플리케이션의 데이터 손실 허용 오차를 반영하도록 자동 장애 조치 정책을 사용자 지정할 수 있습니다. By configuring `GracePeriodWithDataLossHours`, you can control how long the system waits before initiating the failover that is likely to result data loss.

- **여러 장애 조치 그룹**

  동일한 서버 쌍에 대해 여러 장애 조치 그룹을 구성하여 장애 조치의 크기를 제어할 수 있습니다. 각 그룹은 독립적으로 장애 조치됩니다. 다중 테넌트 애플리케이션에서 탄력적 풀을 사용하는 경우 이 기능을 사용하여 각 풀에 주 및 보조 데이터베이스를 혼합할 수 있습니다. 이렇게 하면 테넌트의 절반에 대해서만 가동 중단에 따른 영향을 줄일 수 있습니다.

  > [!NOTE]
  > Managed Instance는 여러 개의 장애 조치(failover) 그룹을 지원하지 않습니다.
  
## <a name="permissions"></a>권한

Permissions for a failover group are managed via [role-based access control (RBAC)](../role-based-access-control/overview.md). The [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) role has all the necessary permissions to manage failover groups.

### <a name="create-failover-group"></a>Create failover group

To create a failover group, you need RBAC write access to both the primary and secondary servers, and to all databases in the failover group. For a managed instance, you need RBAC write access to both the primary and secondary managed instance, but permissions on individual databases are not relevant since individual managed instance databases cannot be added to or removed from a failover group. 

### <a name="update-a-failover-group"></a>Update a failover group

To update a failover group, you need RBAC write access to the failover group, and all databases on the current primary server or managed instance.  

### <a name="failover-a-failover-group"></a>Failover a failover group

To fail over a failover group, you need RBAC write access to the failover group on the new primary server or managed instance.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀로 장애 조치(failover) 그룹을 사용하는 방법의 모범 사례

자동 장애 조치(failover) 그룹은 주 SQL Database 서버에서 구성되어야 하며 다른 Azure 지역의 보조 SQL Database 서버에 연결됩니다. 그룹에는 이러한 서버에 있는 데이터베이스가 모두 또는 일부 포함될 수 있습니다. 다음 다이어그램은 여러 데이터베이스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> See [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a single database to a failover group.

비즈니스 연속성을 고려하여 서비스를 설계하는 경우 다음과 같은 일반 지침을 따르세요.

- **하나 이상의 장애 조치 그룹을 사용하여 여러 데이터베이스의 장애 조치 관리**

  다른 하위 지역의 두 서버(기본 및 보조 서버) 간에 하나 이상의 장애 조치 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다. 장애 조치(failover) 그룹을 사용하는 경우 주 데이터베이스와 서비스 목표가 동일한 지역 보조 데이터베이스가 작성됩니다. 장애 조치(failover) 그룹에 기존의 지역 복제 관계를 추가하는 경우 보조 데이터베이스가 주 데이터베이스와 동일한 서비스 계층 및 컴퓨팅 크기로 구성되었는지 확인합니다.
  
  > [!IMPORTANT]
  > Creating failover groups between two servers in different subscriptions is not currently supported for single databases and elastic pools. If you move the primary or secondary server to a different subscription after the failover group has been created, it could result in failures of the failover requests and other operations.

- **OLTP 워크로드에 읽기-쓰기 수신기 사용**

  OLTP 작업을 수행할 때 `<fog-name>.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새 주 데이터베이스로 리디렉션됩니다.

- **읽기 전용 워크로드에 읽기 전용 수신기 사용**

  특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 읽기 전용 세션의 경우 `<fog-name>.secondary.database.windows.net`을 서버 URL로 사용하면 자동으로 보조 데이터베이스에 연결됩니다. It is also recommended that you indicate in connection string read intent by using `ApplicationIntent=ReadOnly`. If you want to ensure that the read-only workload can reconnect after failover or in case the secondary server goes offline, make sure to configure the `AllowReadOnlyFailoverToPrimary` property of the failover policy.

- **성능 저하에 대한 대비**

  SQL 장애 조치 결정은 애플리케이션의 나머지 부분 또는 사용되는 다른 서비스에서 독립적입니다. 애플리케이션은 한 지역의 일부 구성 요소 및 나머지 지역의 일부 구성 요소와 "혼합"될 수 있습니다. 성능 저하를 방지하려면 DR 지역에서 중복 애플리케이션 배포를 확인하고 이러한 [네트워크 보안 지침](#failover-groups-and-network-security)을 따르세요.

  > [!NOTE]
  > DR 지역의 애플리케이션이 다른 연결 문자열을 사용하지 않아도 됩니다.  

- **데이터 손실에 대비**

  If an outage is detected, SQL waits for the period you specified by `GracePeriodWithDataLossHours`. 기본값은 1시간입니다. If you cannot afford data loss, make sure to set `GracePeriodWithDataLossHours` to a sufficiently large number, such as 24 hours. 수동 그룹 장애 조치(failover)를 사용하여 보조 데이터베이스에서 주 데이터베이스로 장애 복구할 수 있습니다.

  > [!IMPORTANT]
  > 지역 복제를 사용하는 800개 이하의 DTU 및 250개 초과의 데이터베이스가 있는 탄력적 풀에는 계획된 장애 조치 지연 및 성능 저하가 포함된 문제가 발생할 수 있습니다.  이러한 문제는 지역에서 복제 엔드포인트가 지리적으로 광범위하게 분리되어 있거나 여러 보조 엔드포인트가 각 데이터베이스에 대해 사용되는 경우 쓰기 집약적 작업에 발생할 가능성이 높습니다.  이러한 문제의 증상은 지역에서 복제 지연이 시간에 따라 증가하는 경우에 표시됩니다.  이러한 지연은 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 사용하여 모니터링할 수 있습니다.  이러한 문제가 발생하는 경우 완화 방안에는 풀 DTU의 수를 늘리거나 동일한 풀에 지역에서 복제된 데이터베이스 수를 줄이는 방법이 있습니다.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Best practices of using failover groups with managed instances

자동 장애 조치(failover) 그룹은 주 인스턴스에서 구성되어야 하며 다른 Azure 지역의 보조 인스턴스에 연결됩니다.  인스턴스의 모든 데이터베이스가 보조 인스턴스에 복제됩니다.

다음 다이어그램은 관리되는 인스턴스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> See [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a managed instance to use failover group.

If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

- **주 인스턴스와 동일한 DNS 영역에 보조 인스턴스 만들기**

  장애 조치(failover) 후 주 인스턴스에 대한 무중단 연결을 보장하려면 주 인스턴스와 보조 인스턴스가 동일한 DNS 영역에 있어야 합니다. It will guarantee that the same multi-domain (SAN) certificate can be used to authenticate the client connections to either of the two instances in the failover group. 애플리케이션이 프로덕션 배포에 사용할 준비가 되면 다른 지역에 보조 인스턴스를 만들고 주 인스턴스와 DNS 영역을 공유하는지 확인합니다. You can do it by specifying a `DNS Zone Partner` optional parameter using the Azure portal, PowerShell, or the REST API.

> [!IMPORTANT]
> First instance created in the subnet determines DNS zone for all subsequent instances in the same subnet. This means that two instances from the same subnet cannot belong to different DNS zones.

  For more information about creating the secondary instance in the same DNS zone as the primary instance, see [Create a secondary managed instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **두 인스턴스 간에 복제 트래픽 사용**

  각 인스턴스가 자체 VNet에 격리되므로 이러한 VNet 간의 양방향 트래픽을 허용해야 합니다. [Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

- **Create a failover group between managed instances in different subscriptions**

  You can create a failover group between managed instances in two different subscriptions. When using PowerShell API you can do it by  specifying the `PartnerSubscriptionId` parameter for the secondary instance. When using REST API, each instance ID included in the `properties.managedInstancePairs` parameter can have its own subscriptionID.
  
  > [!IMPORTANT]
  > Azure Portal does not support failover groups across different subscriptions.

- **장애 조치(failover) 그룹을 구성하여 전체 인스턴스의 장애 조치(failover) 관리**

  장애 조치(failover) 그룹은 인스턴스에 있는 모든 데이터베이스의 장애 조치(failover)를 관리합니다. 그룹을 만들면 인스턴스의 각 데이터베이스가 자동으로 보조 인스턴스에 지역 복제됩니다. 장애 조치(failover) 그룹을 사용하여 데이터베이스 하위 집합의 부분 장애 조치(failover)를 시작할 수 없습니다.

  > [!IMPORTANT]
  > 주 인스턴스에서 데이터베이스를 제거하면 지역 보조 인스턴스에서도 자동으로 삭제됩니다.

- **OLTP 워크로드에 읽기-쓰기 수신기 사용**

  OLTP 작업을 수행할 때 `<fog-name>.zone_id.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새로운 주 데이터베이스로 리디렉션됩니다. Because the secondary instance shares the DNS zone with the primary, the client application will be able to reconnect to it using the same SAN certificate.

- **읽기 전용 쿼리를 위해 지역 복제된 보조 데이터베이스에 직접 연결**

  특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 지역 복제된 보조 데이터베이스에 직접 연결하려는 경우 `server.secondary.zone_id.database.windows.net`을 서버 URL로 사용하면 지역 복제된 보조 데이터베이스에 직접 연결됩니다.

  > [!NOTE]
  > 특정 서비스 계층에서 Azure SQL Database는 [읽기 전용 복제본](sql-database-read-scale-out.md)을 사용하여 읽기 전용 복제본의 용량 및 연결 문자열의 `ApplicationIntent=ReadOnly` 매개 변수를 통해 읽기 전용 쿼리 워크로드를 부하를 분산할 수 있도록 지원합니다. 지역 복제된 보조 데이터베이스를 구성한 경우 이 기능을 사용하여 주 위치 또는 지역 복제된 위치에 있는 읽기 전용 복제본에 연결할 수 있습니다.
  > - 주 위치의 읽기 전용 복제본에 연결하려면 `<fog-name>.zone_id.database.windows.net`을 사용합니다.
  > - To connect to a read-only replica in the secondary location, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **성능 저하에 대한 대비**

  SQL 장애 조치 결정은 애플리케이션의 나머지 부분 또는 사용되는 다른 서비스에서 독립적입니다. 애플리케이션은 한 지역의 일부 구성 요소 및 나머지 지역의 일부 구성 요소와 "혼합"될 수 있습니다. 성능 저하를 방지하려면 DR 지역에서 중복 애플리케이션 배포를 확인하고 이러한 [네트워크 보안 지침](#failover-groups-and-network-security)을 따르세요.

- **데이터 손실에 대비**

  가동 중단이 감지되는 경우 SQL은 중요한 지식에 대한 데이터 손실이 없으면 읽기 쓰기 장애 조치를 자동으로 트리거합니다. 그렇지 않으면 `GracePeriodWithDataLossHours`에서 지정한 기간 동안 대기합니다. `GracePeriodWithDataLossHours`를 지정한 경우 데이터 손실을 준비합니다. 일반적으로 중단 시에 Azure에서는 가용성을 우선으로 합니다. 데이터 손실을 방지하려는 경우 GracePeriodWithDataLossHours를 24시간과 같은 충분히 큰 숫자로 설정해야 합니다.

  읽기/쓰기 수신기의 DNS 업데이트는 장애 조치(failover)가 시작된 후 즉시 발생합니다. 이 작업으로 인한 데이터 손실은 없습니다. 그러나 일반적인 조건에서 데이터베이스 역할을 전환하는 데 최대 5분 정도 걸릴 수 있습니다. 완료될 때까지 새로운 주 인스턴스의 일부 데이터베이스는 계속 읽기 전용입니다. If failover is initiated using PowerShell, the entire operation is synchronous. If it is initiated using the Azure portal, the UI will indicate completion status. REST API를 사용하여 시작하는 경우, 표준 Azure Resource Manager의 폴링 메커니즘을 사용하여 완료되었는지 모니터링합니다.

  > [!IMPORTANT]
  > 수동 그룹 장애 조치(failover)를 사용하여 주 데이터베이스를 원래 위치로 다시 이동할 수 있습니다. 장애 조치(failover)를 유발한 중단이 완화되면 주 데이터베이스를 원래 위치로 이동할 수 있습니다. 이렇게 하려면 그룹의 수동 장애 조치(failover)를 시작해야 합니다.

- **Acknowledge known limitations of failover groups**

  Database rename is not supported for instances in failover group. You will need to temporarily delete failover group to be able to rename a database.

## <a name="failover-groups-and-network-security"></a>장애 조치 그룹 및 네트워크 보안

For some applications the security rules require that the network access to the data tier is restricted to a specific component or components such as a VM, web service etc. This requirement presents some challenges for business continuity design and the use of the failover groups. Consider the following options when implementing such restricted access.

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

1. [공용 IP를 만듭니다.](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [공용 부하 분산 장치를 만들고](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) 여기에 공용 IP를 할당합니다.
3. 프런트 엔드 구성 요소에 대한 [가상 네트워크 및 가상 머신을 만듭니다.](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers)
4. [네트워크 보안 그룹을 만들고](../virtual-network/security-overview.md) 인바운드 연결을 구성합니다.
5. 'Sql' [서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하여 아웃바운드 연결이 Azure SQL 데이터베이스에 대해 열려 있는지 확인합니다.
6. [SQL 데이터베이스 방화벽 규칙](sql-database-firewall-configure.md)을 만들어서 1단계에서 만든 공용 IP 주소에서 인바운드 트래픽을 허용합니다.

아웃바운드 액세스를 구성하는 방법 및 방화벽 규칙에서 사용할 IP에 대한 자세한 내용은 [부하 분산 장치 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md)을 참조하세요.

위의 구성에서는 자동 장애 조치가 프런트 엔드 구성 요소에서 연결을 차단하지 않는지 확인하고 애플리케이션이 프런트 엔드와 데이터 계층 간의 긴 대기 시간을 허용할 수 있다고 가정합니다.

> [!IMPORTANT]
> 지역 중단에 대한 비즈니스 연속성을 보장하기 위해 프런트 엔드 구성 요소와 데이터베이스 모두에 대한 지리적 복제를 확인해야 합니다.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Enabling geo-replication between managed instances and their VNets

When you set up a failover group between primary and secondary managed instances in two different regions, each instance is isolated using an independent virtual network. To allow replication traffic between these VNets ensure these prerequisites are met:

1. The two managed instances need to be in different Azure regions.
1. The two managed instances need to be the same service tier, and have the same storage size.
1. Your secondary managed instance must be empty (no user databases).
1. The virtual networks used by the managed instances need to be connected through a [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) or Express Route. When two virtual networks connect through an on-premises network, ensure there is no firewall rule blocking ports 5022, and 11000-11999. 전역 VNet 피어링은 지원되지 않습니다.
1. The two managed instance VNets cannot have overlapping IP addresses.
1. You need to set up your Network Security Groups (NSG) such that ports 5022 and the range 11000~12000 are open inbound and outbound for connections from the other managed instanced subnet. 이는 인스턴스 간의 복제 트래픽을 허용하기 위한 것입니다.

   > [!IMPORTANT]
   > NSG 보안 규칙을 잘못 구성하면 데이터베이스 복사 작업이 중단됩니다.

1. The secondary instance is configured with the correct DNS zone ID. DNS zone is a property of a managed instance and virtual cluster, and its ID is included in the host name address. The zone ID is generated as a random string when the first managed instance is created in each VNet and the same ID is assigned to all other instances in the same subnet. Once assigned, the DNS zone cannot be modified. Managed instances included in the same failover group must share the DNS zone. You accomplish this by passing the primary instance's zone ID as the value of DnsZonePartner parameter when creating the secondary instance. 

   > [!NOTE]
   > For a detailed tutorial on configuring failover groups with managed instance, see [add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>주 데이터베이스 업그레이드 또는 다운그레이드

보조 데이터베이스와의 연결을 끊지 않고도 주 데이터베이스를 다른 컴퓨팅 크기(동일한 서비스 계층 내, 범용 및 중요 비즈니스용 사이 아님)로 업그레이드하거나 다운그레이드할 수 있습니다. When upgrading, we recommend that you upgrade all of the secondary databases first, and then upgrade the primary. When downgrading, reverse the order: downgrade the primary first, and then downgrade all of the secondary databases. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

This sequence is recommended specifically to avoid the problem where the secondary at a lower SKU gets overloaded and must be re-seeded during an upgrade or downgrade process. You could also avoid the problem by making the primary read-only, at the expense of impacting all read-write workloads against the primary.

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. Calling `sp_wait_for_database_copy_sync` blocks the calling thread until the last committed transaction has been transmitted to the secondary database. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. `sp_wait_for_database_copy_sync` is scoped to a specific continuous copy link. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> `sp_wait_for_database_copy_sync` prevents data loss after failover, but does not guarantee full synchronization for read access. The delay caused by a `sp_wait_for_database_copy_sync` procedure call can be significant and depends on the size of the transaction log at the time of the call.

## <a name="failover-groups-and-point-in-time-restore"></a>장애 조치(failover) 그룹 및 지정 시간 복원

장애 조치(failover) 그룹과 함께 지정 시간 복원을 사용하는 방법에 대한 자세한 내용은 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="programmatically-managing-failover-groups"></a>프로그래밍 방식으로 장애 조치(failover) 그룹 관리

앞에서 설명한 대로, 자동 장애 조치(failover) 그룹과 활성 지역 복제는 Azure PowerShell 및 REST API를 사용하여 프로그래밍 방식으로 관리할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)을 참조하세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 서버에서 장애 조치 그룹을 제거하고 그룹에 포함된 모든 보조 데이터베이스를 삭제합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 장애 조치 그룹 구성을 검색합니다. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |장애 조치 그룹의 구성을 수정합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Azure SQL Database 장애 조치(failover) 그룹에 하나 이상의 데이터베이스 추가|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 구성을 수정합니다.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |장애 조치 그룹 구성을 검색합니다.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 장애 조치(failover) 그룹을 제거합니다.|

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| 명령 | 설명 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 서버에서 장애 조치 그룹을 제거하고 그룹에 포함된 모든 보조 데이터베이스를 삭제합니다. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 장애 조치 그룹 구성을 검색합니다. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifies the configuration of the failover group and/or adds one or more databases to an Azure SQL Database failover group|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다. |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| 명령 | 설명 |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | 장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | 장애 조치 그룹의 구성을 수정합니다.|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | 장애 조치 그룹 구성을 검색합니다.|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | 장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다.|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | 장애 조치(failover) 그룹을 제거합니다. |

* * *

> [!IMPORTANT]
> 샘플 스크립트는 [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)](scripts/sql-database-add-single-db-to-failover-group-powershell.md)를 참조하세요.

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Manage SQL database failover groups with single and pooled databases

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 현재 주 서버에서 이 서버로 장애 조치(failover)합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |현재 주 서버에서 이 서버로 장애 조치(failover)합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 장애 조치(failover) 그룹을 가져옵니다. |
| [서버별 장애 조치(failover) 그룹 나열](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 서버에 있는 장애 조치(failover) 그룹을 나열합니다. |
| [장애 조치(failover) 그룹 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 장애 조치(failover) 그룹을 업데이트합니다. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Manage failover groups with Managed Instances

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 현재 주 서버에서 이 서버로 장애 조치(failover)합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |현재 주 서버에서 이 서버로 장애 조치(failover)합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 장애 조치(failover) 그룹을 가져옵니다. |
| [장애 조치(failover) 그룹 나열 - 위치별 목록](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 위치에 있는 장애 조치(failover) 그룹을 나열합니다. |

## <a name="next-steps"></a>다음 단계

- For detailed tutorials, see
    - [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Add elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [Use PowerShell to configure active geo-replication for a single database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use PowerShell to configure active geo-replication for a pooled database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use PowerShell to add an Azure SQL Database single database to a failover group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
