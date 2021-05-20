---
title: SQL Managed Instance에서 수동으로 장애 조치(failover) 시작
description: Azure SQL Managed Instance에서 주 및 보조 복제본을 수동으로 장애 조치(failover)하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 02/27/2021
ms.openlocfilehash: 3c969c1898e67361e37a825d7976b1c52d08dd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101691147"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>SQL Managed Instance에서 사용자가 시작한 수동 장애 조치(failover)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 SQL Managed Instance GP(범용) 및 BC(중요 비즈니스용) 서비스 계층에서 주 노드를 수동으로 장애 조치(failover)하는 방법 및 BC 서비스 계층에서 보조 읽기 전용 복제본 노드를 수동으로 장애 조치(failover)하는 방법을 설명합니다.

## <a name="when-to-use-manual-failover"></a>수동 장애 조치(failover)를 사용하는 경우

[고가용성](../database/high-availability-sla.md)은 데이터베이스 애플리케이션에 대해 투명하게 작동하는 SQL Managed Instance 플랫폼의 기본 부분입니다. 노드 저하 또는 오류 감지 시 또는 정기적인 월간 소프트웨어 업데이트 중에 주 노드에서 보조 노드로 장애 조치(failover)는 Azure에서 SQL Managed Instance를 사용하는 모든 애플리케이션에 대한 예상된 상황입니다.

다음과 같은 이유로 SQL Managed Instance에서 [수동 장애 조치(failover)](../database/high-availability-sla.md#testing-application-fault-resiliency)를 실행하는 것을 고려할 수 있습니다.
- 프로덕션 환경에 배포하기 전에 장애 조치(failover) 복원력에 대한 애플리케이션 테스트
- 자동 장애 조치(failover) 시 오류 복원력을 위한 엔드투엔드 시스템 테스트
- 장애 조치(failover)가 기존 데이터베이스 세션에 미치는 영향 테스트
- 네트워크 대기 시간의 변경으로 인해 장애 조치(failover)에서 엔드투엔드 성능을 변경하는지 확인합니다.
- 일부 쿼리 성능 저하의 경우 수동 장애 조치(failover)를 통해 성능 문제를 완화할 수 있습니다.

> [!NOTE]
> 프로덕션 환경에 배포하기 전에 애플리케이션이 장애 조치(failover) 복원력 있는지 확인하면 프로덕션 환경에서 애플리케이션 오류의 위험을 완화하고 고객의 애플리케이션 가용성에 영향을 줍니다. [SQL Managed Instance를 사용하여 장애 조치(Failover) 복원력에 대한 앱 클라우드 준비 테스트](https://youtu.be/FACWYLgYDL8) 비디오 레코딩을 통해 클라우드 준비를 위한 애플리케이션 테스트에 대해 자세히 알아보세요.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>SQL Managed Instance에서 수동 장애 조치(failover) 시작

### <a name="azure-rbac-permissions-required"></a>Azure RBAC 사용 권한 필요

장애 조치(failover)를 시작하는 사용자에게는 다음 Azure 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- [Managed Instance 기여자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>PowerShell 사용

Az.Sql의 최소 버전은 [v2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)이어야 합니다. 항상 사용 가능한 최신 PowerShell 버전이 있는 Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하는 것이 좋습니다. 

사전 요구 사항으로 다음 PowerShell 스크립트를 사용하여 필요한 Azure 모듈을 설치합니다. 또한 장애 조치(failover)하려는 Managed Instance가 있는 구독을 선택합니다.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

다음 예제와 함께 PowerShell 명령 [Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover)를 사용하여 BC 및 GP 서비스 계층 모두에 적용되는 주 노드의 장애 조치(failover)를 시작합니다.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

다음 PS 명령을 사용하여 BC 서비스 계층에만 해당하는 읽기 보조 노드를 장애 조치(failover)할 수 있습니다.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>CLI 사용

최신 CLI 스크립트를 설치했는지 확인합니다.

다음 예제와 함께 az sql mi 장애 조치(failover) CLI 명령을 사용하여 BC 및 GP 서비스 계층 모두에 적용되는 주 노드의 장애 조치(failover)를 시작합니다.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

다음 CLI 명령을 사용하여 BC 서비스 계층에만 해당하는 읽기 보조 노드를 장애 조치(failover)할 수 있습니다.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Rest API 사용

연속 파이프라인 테스트 또는 자동화된 성능 완화기를 구현하기 위해 SQL Managed Instance의 장애 조치(failover)를 자동화해야 하는 고급 사용자의 경우 API 호출을 통해 장애 조치(failover)를 시작하여 이 함수를 수행할 수 있습니다. 자세한 내용은 [Managed Instance - 장애 조치(Failover) REST API](/rest/api/sql/managed%20instances%20-%20failover/failover)를 참조하세요.

REST API 호출을 사용하여 장애 조치(failover)를 시작하려면 먼저 선택한 API 클라이언트를 사용하여 인증 토큰을 생성합니다. 생성된 인증 토큰은 API 요청 헤더에서 권한 부여 속성으로 사용되며 필수입니다.

다음 코드는 호출할 API URI의 예제입니다.

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

API 호출에서 다음 속성을 전달해야 합니다.

| **API 속성** | **매개 변수** |
| --- | --- |
| subscriptionId | 관리형 인스턴스가 배포되는 구독 ID |
| resourceGroupName | 관리형 인스턴스를 포함하는 리소스 그룹 |
| managedInstanceName | 관리형 인스턴스의 이름 |
| replicaType | (선택 사항) (주 또는 ReadableSecondary) 이러한 매개 변수는 장애 조치(failover)될 복제본의 유형(주 또는 읽기 가능한 보조)을 나타냅니다. 지정하지 않으면 기본적으로 주 복제본에서 장애 조치(failover)가 시작됩니다. |
| api-version | 정적 값이며 현재 “2019-06-01-preview"여야 합니다. |

API 응답은 다음 두 가지 중 하나가 됩니다.

- 202 수락됨
- 400 요청 오류 중 하나입니다.

응답 헤더의 API 응답을 검토하여 작업 상태를 추적할 수 있습니다. 자세한 내용은 [비동기 Azure 작업의 상태](../../azure-resource-manager/management/async-operations.md)를 참조하세요.

## <a name="monitor-the-failover"></a>장애 조치(failover) 모니터링

BC 인스턴스에 대해 사용자가 시작한 장애 조치(failover)의 진행률을 모니터링하려면 SQL Managed Instance에서 즐겨 사용하는 클라이언트(예: SSMS)에서 다음 T-SQL 쿼리를 실행합니다. 시스템 뷰 sys.dm_hadr_fabric_replica_states를 읽고 인스턴스에 사용할 수 있는 복제본을 보고합니다. 수동 장애 조치(failover)를 시작한 후 동일한 쿼리를 새로 고칩니다.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

장애 조치(failover)를 시작하기 전에 출력은 AlwaysOn 가용성 그룹에서 1개의 주 및 3개의 보조 복제본이 포함된 BC 서비스 계층의 현재 주 복제본을 표시합니다. 장애 조치(failover)를 실행하면 이 쿼리를 다시 실행하여 주 노드의 변경을 나타내야 합니다.

GP 서비스 계층을 사용하여 BC에 대해 표시된 것과 동일한 출력을 볼 수 없습니다. 이는 GP 서비스 계층이 단일 노드만 기반으로 하기 때문입니다. GP 서비스 계층 인스턴스의 노드에서 SQL 프로세스가 시작된 시간을 보여 주는 대체 T-SQL 쿼리를 사용할 수 있습니다.

```T-SQL
SELECT sqlserver_start_time, sqlserver_start_time_ms_ticks FROM sys.dm_os_sys_info
```

장애 조치(failover) 중에 일반적으로 1분 이내에 지속되는 클라이언트 연결의 단기 손실은 서비스 계층에 관계 없이 장애 조치(failover) 실행을 나타냅니다.

> [!NOTE]
> **높은 강도** 워크로드의 경우에는 장애 조치(failover) 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 이는 장애 조치(failover)를 수행하기 전에 인스턴스 엔진이 주 서버의 모든 현재 트랜잭션을 처리하고 보조 노드에서 처리하기 때문입니다.

> [!IMPORTANT]
> 사용자가 시작한 수동 장애 조치(failover)의 기능 제한 사항은 다음과 같습니다.
> - **15분** 마다 동일한 Managed Instance에서 하나의(1) 장애 조치(failover)가 시작될 수 있습니다.
> - BC 인스턴스의 경우 장애 조치(failover) 요청이 허용되려면 복제본의 쿼럼이 있어야 합니다.
> - BC 인스턴스의 경우 장애 조치(failover)를 시작할 읽기 가능한 보조 복제본을 지정할 수 없습니다.
> - 자동화된 백업 시스템에서 새 데이터베이스에 대한 첫 번째 전체 백업이 완료될 때까지 장애 조치(failover)가 허용되지 않습니다.
> - 진행 중인 데이터베이스 복원이 있으면 장애 조치(failover)가 허용되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [SQL Managed Instance를 사용하여 장애 조치(Failover) 복원력에 대한 앱 클라우드 준비 테스트](https://youtu.be/FACWYLgYDL8) 비디오 레코딩을 통해 클라우드 준비를 위한 애플리케이션 테스트에 대해 자세히 알아보세요.
- [Azure SQL Managed Instance에 대한 관리형 인스턴스 고가용성](../database/high-availability-sla.md)의 고가용성에 대해 자세히 알아보세요.
- 개요는 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
