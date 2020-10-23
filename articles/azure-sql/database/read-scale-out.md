---
title: 복제본에 대 한 쿼리 읽기
description: Azure SQL은 읽기 확장 이라는 읽기 작업을 위해 읽기 전용 복제본의 용량을 사용 하는 기능을 제공 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: fbde77de0ad8698ff82b80b440ae1d4bdcae1f36
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426993"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>읽기 전용 복제본을 사용 하 여 읽기 전용 쿼리 작업 오프 로드
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

고가용성 [아키텍처](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)의 일부로 프리미엄 및 중요 비즈니스용 서비스 계층의 각 단일 데이터베이스, 탄력적 풀 데이터베이스 및 관리 되는 인스턴스는 주 읽기-쓰기 복제본과 여러 보조 읽기 전용 복제본으로 자동으로 프로 비전 됩니다. 보조 복제본은 주 복제본과 동일한 계산 크기로 프로 비전 됩니다. 읽기 *확장* 기능을 사용 하면 읽기 전용 복제본에서 실행 하는 대신 읽기 전용 복제본 중 하나의 계산 용량을 사용 하 여 읽기 전용 작업을 오프 로드할 수 있습니다. 이러한 방식으로 일부 읽기 전용 작업은 읽기-쓰기 작업에서 격리 될 수 있으며 성능에 영향을 주지 않습니다. 이 기능은 분석과 같이 논리적으로 구분 된 읽기 전용 작업을 포함 하는 응용 프로그램을 위한 것입니다. 프리미엄 및 중요 비즈니스용 서비스 계층에서 응용 프로그램은 추가 비용 없이이 추가 용량을 사용 하 여 성능상의 이점을 얻을 수 있습니다.

*읽기 확장* 기능은 하나 이상의 보조 복제본이 생성 될 때 hyperscale 서비스 계층 에서도 사용할 수 있습니다. 하나의 보조 복제본에서 사용할 수 있는 것 보다 더 많은 리소스를 필요로 하는 로드 밸런싱 읽기 전용 작업에는 여러 개의 보조 복제본을 사용할 수 있습니다.

Basic, Standard 및 범용 서비스 계층의 고가용성 아키텍처에는 복제본이 포함 되지 않습니다. 이러한 서비스 계층에서는 *읽기 확장* 기능을 사용할 수 없습니다.

다음 다이어그램에서는이 기능을 보여 줍니다.

![읽기 전용 복제본](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

*읽기 확장* 기능은 새 Premium, 중요 비즈니스용 및 hyperscale 데이터베이스에서 기본적으로 사용 하도록 설정 됩니다. Hyperscale의 경우 기본적으로 새 데이터베이스에 대해 하나의 보조 복제본이 생성 됩니다. 

> [!NOTE]
> 읽기 확장은 Managed Instance의 중요 비즈니스용 서비스 계층에서 항상 사용 하도록 설정 됩니다.

SQL 연결 문자열이로 구성 된 경우 `ApplicationIntent=ReadOnly` 해당 데이터베이스 또는 관리 되는 인스턴스의 읽기 전용 복제본으로 응용 프로그램이 리디렉션됩니다. 속성을 사용 하는 방법에 대 한 자세한 내용은 `ApplicationIntent` [응용 프로그램 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조 하세요.

SQL 연결 문자열의 설정에 관계 없이 응용 프로그램이 주 복제본에 연결 되도록 하려면 `ApplicationIntent` 데이터베이스를 만들 때 또는 구성을 변경할 때 읽기 확장을 명시적으로 해제 해야 합니다. 예를 들어 데이터베이스를 Standard 또는 범용 계층에서 Premium, 중요 비즈니스용 또는 Hyperscale 계층으로 업그레이드 하 고 모든 연결이 주 복제본으로 계속 진행 되도록 하려면 읽기 확장을 사용 하지 않도록 설정 합니다. 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은 [읽기 확장 사용 및 사용 안 함](#enable-and-disable-read-scale-out)을 참조 하세요.

> [!NOTE]
> 쿼리 저장소 및 SQL Profiler 기능은 읽기 전용 복제본에서 지원 되지 않습니다. 

## <a name="data-consistency"></a>데이터 일관성

복제본의 이점 중 하나는 복제본이 트랜잭션 측면에서 항상 일관된 상태로 있지만 다른 시점에 서로 다른 복제본 간에 약간의 대기 시간이 있을 수 있다는 것입니다. 읽기 확장은 세션 수준 일관성을 지원 합니다. 즉, 복제본을 사용할 수 없는 경우 발생 하는 연결 오류가 발생 한 후 읽기 전용 세션이 다시 연결 되 면 읽기-쓰기 복제본으로 최신 100%가 아닌 복제본으로 리디렉션될 수 있습니다. 마찬가지로 애플리케이션이 읽기-쓰기 세션을 사용하여 데이터를 쓰고 읽기 전용 세션을 사용하여 즉시 읽는 경우 최신 업데이트가 복제본에 즉시 표시되지 않을 수 있습니다. 대기 시간은 비동기 트랜잭션 로그 다시 실행 작업으로 인해 발생합니다.

> [!NOTE]
> 영역 내의 복제 대기 시간이 낮으므로이 상황은 드물게 발생 합니다. 복제 대기 시간을 모니터링 하려면 [읽기 전용 복제본 모니터링 및 문제 해결](#monitoring-and-troubleshooting-read-only-replicas)을 참조 하세요.

## <a name="connect-to-a-read-only-replica"></a>읽기 전용 복제본에 연결

데이터베이스에 대해 읽기 확장을 사용 하도록 설정 하는 경우 `ApplicationIntent` 클라이언트에서 제공 하는 연결 문자열의 옵션은 연결이 쓰기 복제본 또는 읽기 전용 복제본으로 라우팅되도록 할지를 결정 합니다. 특히 `ApplicationIntent` 값이 `ReadWrite` (기본값) 이면 연결이 읽기/쓰기 복제본으로 전송 됩니다. 이는이 `ApplicationIntent` 연결 문자열에 포함 되지 않은 경우의 동작과 동일 합니다. `ApplicationIntent` 값이 `ReadOnly`이면 연결이 읽기 전용 복제본으로 라우팅됩니다.

예를 들어 다음 연결 문자열은 클라이언트를 읽기 전용 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 연결 문자열 중 하나는 클라이언트를 읽기/쓰기 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>읽기 전용 복제본에 대한 연결인지 확인

데이터베이스의 컨텍스트에서 다음 쿼리를 실행 하 여 읽기 전용 복제본에 연결 되어 있는지 여부를 확인할 수 있습니다. 읽기 전용 복제본에 연결 된 경우 READ_ONLY 반환 됩니다.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> 프리미엄 및 중요 비즈니스용 서비스 계층에서는 지정 된 시간에 읽기 전용 복제본 중 하나만 액세스할 수 있습니다. Hyperscale은 여러 읽기 전용 복제본을 지원 합니다.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>읽기 전용 복제본 모니터링 및 문제 해결

읽기 전용 복제본에 연결 된 경우 Dmv (동적 관리 뷰)는 복제본의 상태를 반영 하며 모니터링 및 문제 해결을 위해 쿼리할 수 있습니다. 데이터베이스 엔진은 다양 한 모니터링 데이터를 표시 하기 위해 여러 뷰를 제공 합니다. 

일반적으로 사용 되는 보기는 다음과 같습니다.

| Name | 목적 |
|:---|:---|
|[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| CPU, 데이터 IO 및 서비스 목표 제한과 관련 된 로그 쓰기 사용률을 포함 하 여 지난 1 시간 동안 리소스 사용률 메트릭을 제공 합니다.|
|[sys.dm_os_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| 데이터베이스 엔진 인스턴스에 대 한 집계 대기 통계를 제공 합니다. |
|[sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| 복제본 성능 상태 및 동기화 통계를 제공 합니다. Redo queue size 및 redo rate는 읽기 전용 복제본의 데이터 대기 시간 표시기로 제공 됩니다. |
|[sys.dm_os_performance_counters](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| 데이터베이스 엔진 성능 카운터를 제공 합니다.|
|[sys.dm_exec_query_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| 실행 수, 사용한 CPU 시간 등 쿼리 별 실행 통계를 제공 합니다.|
|[sys.dm_exec_query_plan ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| 캐시 된 쿼리 계획을 제공 합니다. |
|[sys.dm_exec_sql_text ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| 캐시 된 쿼리 계획에 대 한 쿼리 텍스트를 제공 합니다.|
|[sys.dm_exec_query_profiles](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 쿼리가 실행 되는 동안 실시간 쿼리 진행률을 제공 합니다.|
|[sys.dm_exec_query_plan_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 쿼리의 런타임 통계를 포함 하 여 마지막으로 알려진 실제 실행 계획을 제공 합니다.|
|[sys.dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| 모든 데이터베이스 파일에 대 한 저장소 IOPS, 처리량 및 대기 시간 통계를 제공 합니다. |

> [!NOTE]
> `sys.resource_stats` `sys.elastic_pool_resource_stats` 논리적 master 데이터베이스의 및 dmv는 주 복제본의 리소스 사용률 데이터를 반환 합니다.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>확장 이벤트를 사용 하 여 읽기 전용 복제본 모니터링

읽기 전용 복제본에 연결 된 경우에는 확장 이벤트 세션을 만들 수 없습니다. 그러나 Azure SQL Database에서 주 복제본에서 만들어지고 변경 된 데이터베이스 범위 [확장 이벤트](xevent-db-diff-from-svr.md) 세션의 정의는 지역 복제본을 포함 하 여 읽기 전용 복제본에 복제 되 고 읽기 전용 복제본에 대 한 이벤트를 캡처합니다.

주 복제본의 세션 정의를 기반으로 하는 읽기 전용 복제본에서 확장 이벤트 세션을 시작 하 고 주 복제본과 독립적으로 중지할 수 있습니다. 주 복제본에서 확장 이벤트 세션을 삭제 하면 모든 읽기 전용 복제본에도 삭제 됩니다.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>읽기 전용 복제본에 대 한 트랜잭션 격리 수준

읽기 전용 복제본에서 실행 되는 쿼리는 항상 [snapshot](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) 트랜잭션 격리 수준에 매핑됩니다. Snapshot 격리는 판독기에서 작성기를 차단 하는 차단 시나리오를 방지 하기 위해 행 버전 관리를 사용 합니다.

드문 경우 지만 snapshot 격리 트랜잭션이 다른 동시 트랜잭션에서 수정 된 개체 메타 데이터에 액세스 하는 경우이 트랜잭션이 시작 된 후 다른 동시 트랜잭션의 DDL 문에 의해 '%. * l s ' 데이터베이스에서 스냅숏 격리 트랜잭션이 실패 했습니다. "라는 [3961](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)오류 메시지가 표시 될 수 있습니다. 메타데이터 버전이 관리되지 않기 때문에 허용되지 않습니다. 메타 데이터에 대 한 동시 업데이트를 사용 하면 스냅숏 격리와 혼합 하는 경우 불일치가 발생할 수 있습니다. "

### <a name="long-running-queries-on-read-only-replicas"></a>읽기 전용 복제본에 대 한 장기 실행 쿼리

읽기 전용 복제본에서 실행 되는 쿼리는 쿼리에서 참조 되는 개체의 메타 데이터 (테이블, 인덱스, 통계 등)에 액세스 해야 합니다. 드문 경우 지만 쿼리가 읽기 전용 복제본의 동일한 개체에 대 한 잠금을 유지 하는 동안 주 복제본에서 메타 데이터 개체를 수정 하면 쿼리는 주 복제본의 변경 내용을 읽기 전용 복제본에 적용 하는 프로세스를 [차단할](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) 수 있습니다. 이러한 쿼리를 오랜 시간 동안 실행 하는 경우 읽기 전용 복제본이 주 복제본과 동기화 되지 않을 수 있습니다. 

읽기 전용 복제본에 대 한 장기 실행 쿼리로 인해 이러한 종류의 차단이 발생 하면 자동으로 종료 되 고 세션에서 오류 1219, "높은 우선 순위 DDL 작업으로 인해 세션의 연결이 끊어져 있습니다."가 표시 됩니다.

> [!NOTE]
> 읽기 전용 복제본에 대해 쿼리를 실행할 때 오류 3961 또는 오류 1219이 발생 하면 쿼리를 다시 시도 하십시오.

> [!TIP]
> 프리미엄 및 중요 비즈니스용 서비스 계층에서 읽기 전용 복제본에 연결 된 경우 `redo_queue_size` `redo_rate` [sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV의 및 열을 사용 하 여 읽기 전용 복제본의 데이터 대기 시간 표시기로 제공 되는 데이터 동기화 프로세스를 모니터링할 수 있습니다.
> 

## <a name="enable-and-disable-read-scale-out"></a>읽기 확장 사용 및 사용 안 함

읽기 확장은 프리미엄, 중요 비즈니스용 및 Hyperscale 서비스 계층에서 기본적으로 사용 하도록 설정 되어 있습니다. 기본, 표준 또는 범용 서비스 계층에서는 읽기 확장을 사용 하도록 설정할 수 없습니다. 읽기 확장은 0 개 복제본으로 구성 된 Hyperscale 데이터베이스에서 자동으로 비활성화 됩니다.

다음 방법을 사용 하 여 프리미엄 또는 중요 비즈니스용 서비스 계층의 단일 데이터베이스 및 탄력적 풀 데이터베이스에서 읽기 확장을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 단일 데이터베이스 및 탄력적 풀 데이터베이스의 경우 읽기 확장을 사용 하지 않도록 설정 하는 기능은 이전 버전과의 호환성을 위해 제공 됩니다. 중요 비즈니스용 관리 되는 인스턴스에서는 읽기 확장을 사용 하지 않도록 설정할 수 없습니다.

### <a name="azure-portal"></a>Azure portal

데이터베이스 **구성** 블레이드에서 읽기 확장 설정을 관리할 수 있습니다.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. Azure Resource Manager 모듈은 12 월 2020 일까 때까지 버그 수정 사항을 계속 받게 됩니다.  Az module 및 Azure Resource Manager 모듈의 명령에 대 한 인수는 실제로 동일 합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

Azure PowerShell에서 읽기 확장을 관리 하려면 12 월 2016 Azure PowerShell 릴리스 이상이 필요 합니다. 최신 PowerShell 버전은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 참조하세요.

[AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet을 호출 하 고 `Enabled` `Disabled` 매개 변수에 대해 원하는 값 (또는)을 전달 하 여 Azure PowerShell에서 읽기 확장을 사용 하지 않도록 설정 하거나 다시 사용 하도록 설정할 수 있습니다 `-ReadScale` .

기존 데이터베이스에서 읽기 확장을 사용 하지 않도록 설정 하려면 다음을 수행 합니다 (꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제 함).

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

새 데이터베이스에서 읽기 확장을 사용 하지 않도록 설정 하려면 (꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호를 삭제 합니다.)

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

기존 데이터베이스에서 읽기 확장을 다시 사용 하도록 설정 하려면 다음을 수행 합니다 (꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제 함).

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

읽기 확장을 사용 하지 않도록 설정 된 데이터베이스를 만들거나 기존 데이터베이스에 대 한 설정을 변경 하려면 `readScale` `Enabled` `Disabled` 다음 샘플 요청과 같이 속성이 또는로 설정 된 다음 메서드를 사용 합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

자세한 내용은 [데이터베이스-만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 참조 하세요.

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>읽기 전용 `tempdb` 복제본에서 데이터베이스 사용

`tempdb`주 복제본의 데이터베이스가 읽기 전용 복제본으로 복제 되지 않습니다. 각 복제본에는 `tempdb` 복제본을 만들 때 생성 되는 자체 데이터베이스가 있습니다. 이를 통해를 `tempdb` 업데이트할 수 있고 쿼리를 실행 하는 동안 수정할 수 있습니다. 읽기 전용 작업에서 개체 사용에 의존 하는 경우 `tempdb` 쿼리 스크립트의 일부로 이러한 개체를 만들어야 합니다.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>지역에서 복제 된 데이터베이스에서 읽기 확장 사용

지리적으로 복제 된 보조 데이터베이스는 주 데이터베이스와 동일한 고가용성 아키텍처를 가집니다. 읽기 확장을 사용 하도록 설정 된 지역에서 복제 된 보조 데이터베이스에 연결 하는 경우에는를 사용 하는 세션이 `ApplicationIntent=ReadOnly` 기본 쓰기 가능 데이터베이스에서 라우팅되는 것과 같은 방식으로 고가용성 복제본 중 하나로 라우팅됩니다. `ApplicationIntent=ReadOnly`가 없는 세션은 역시 읽기 전용인 지역 복제된 보조 데이터베이스의 주 복제본으로 라우팅됩니다. 

이러한 방식으로 지역 복제본을 만드는 것은 읽기/쓰기 주 데이터베이스에 대 한 읽기 전용 복제본을 두 개 더 제공 하며,이는 총 세 개의 읽기 전용 복제본을 제공 합니다. 각 추가 지역 복제본은 또 다른 쌍의 읽기 전용 복제본을 제공 합니다. 지역 복제본은 주 데이터베이스의 지역을 포함 하 여 모든 Azure 지역에서 만들 수 있습니다.

> [!NOTE]
> 지리적으로 복제 된 보조 데이터베이스의 복제본 간에 자동 라운드 로빈 또는 다른 부하 분산 라우팅이 없습니다.

## <a name="next-steps"></a>다음 단계

- SQL Database 하이퍼 확장 제공에 대 한 자세한 내용은 [hyperscale service 계층](service-tier-hyperscale.md)을 참조 하세요.
