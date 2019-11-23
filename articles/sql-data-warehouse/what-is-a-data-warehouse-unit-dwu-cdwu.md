---
title: Data Warehouse Units (DWUs) in Azure Synapse Analytics (formerly SQL DW)
description: Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7cd6a037f339f193f63cbe152f0ea9964679c231
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420485"
---
# <a name="data-warehouse-units-dwus"></a>Data Warehouse Units (DWUs)

Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.

## <a name="what-are-data-warehouse-units"></a>What are Data Warehouse Units

A [SQL pool](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) represents a collection of analytic resources that are being provisioned when using [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Analytic resources are defined as a combination of CPU, memory and IO. These three resources are bundled into units of compute scale called Data Warehouse Units (DWUs). DWU는 컴퓨팅 리소스 및 성능의 추상적이고 정규화된 측정값을 나타냅니다. A change to your service level alters the number of DWUs that are available to the system, which in turn adjusts the performance, and the cost, of your system.

For higher performance, you can increase the number of data warehouse units. For less performance, reduce data warehouse units. 스토리지 및 컴퓨팅 비용은 별도로 청구되므로 데이터 웨어하우스 단위를 변경해도 스토리지 비용에 영향을 미치지 않습니다.

Performance for data warehouse units is based on these workload metrics:

- How fast a standard data warehousing query can scan a large number of rows and then perform a complex aggregation. 이 작업은 I/O 및 CPU를 많이 사용합니다.
- How fast the data warehouse can ingest data from Azure Storage Blobs or Azure Data Lake. 이 작업은 네트워크 및 CPU를 많이 사용합니다.
- How fast the [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL command can copy a table. 이 작업에는 데이터를 스토리지에서 읽어오기, 어플라이언스의 노드 전체에 배포하기, 스토리지에 다시 쓰기가 포함됩니다. 이 작업은 CPU, IO 및 네트워크를 많이 사용합니다.

DWU 늘리기:

- 검색, 집계 및 CTAS 문에 대한 시스템 성능을 선형적으로 변경합니다.
- PolyBase 로드 작업용 판독기 및 작성기 수를 늘립니다.
- 동시 쿼리 및 동시성 슬롯의 최대 수를 늘립니다.

## <a name="service-level-objective"></a>서비스 수준 목표

SLO(서비스 수준 목표)는 데이터 웨어하우스의 비용 및 성능 수준을 결정하는 확장성 설정입니다. The service levels for Gen2 SQL pool are measured in data warehouse units (DWU), for example DW2000c.

In T-SQL, the SERVICE_OBJECTIVE setting determines the service level for your SQL pool.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>용량 제한

각 SQL Server(예: myserver.database.windows.net)에는 특정 데이터 웨어하우스 단위 수를 허용하는 [DTU(데이터베이스 트랜잭션 단위)](../sql-database/sql-database-what-is-a-dtu.md) 할당량이 지정되어 있습니다. 자세한 내용은 [워크로드 관리 용량 제한](sql-data-warehouse-service-capacity-limits.md#workload-management)을 참조하세요.

## <a name="how-many-data-warehouse-units-do-i-need"></a>How many data warehouse units do I need

이상적인 데이터 웨어하우스 단위 수는 워크로드 및 시스템에 로드한 데이터 양에 따라 매우 다릅니다.

워크로드에 가장 적합한 DWU를 찾는 방법에 대한 단계:

1. 더 작은 DWU를 선택하여 시작합니다.
2. 시스템으로 로드하는 데이터를 테스트할 때 애플리케이션 성능을 모니터링하여 선택한 DWU 수와 관찰한 성능을 비교합니다.
3. 정기적으로 작업량이 많은 기간에 필요한 추가 요구 사항을 식별합니다. Workloads that show significant peaks and troughs in activity may need to be scaled frequently.

SQL Analytics is a scale-out system that can provision vast amounts of compute and query sizeable quantities of data. 특히 큰 DWU에서 진정한 크기 조정 기능을 확인하려면 데이터에 충분한 CPU가 할당되도록 데이터 집합의 크기를 조정하는 것이 좋습니다. 크기 조정 테스트의 경우 1TB 이상을 사용하는 것이 좋습니다.

> [!NOTE]
>
> 컴퓨팅 노드 간에 작업을 분할할 수 있는 경우 더 많은 병렬 처리를 통해 쿼리 성능만 증가합니다. 크기를 조정해도 성능이 변경되지 않는 경우 테이블 디자인 및/또는 쿼리를 튜닝해야 할 수 있습니다. 쿼리 튜닝 지침에 대해서는 [사용자 쿼리 관리](sql-data-warehouse-overview-manage-user-queries.md)를 참조하세요.

## <a name="permissions"></a>권한

데이터 웨어하우스 단위를 변경하려면 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)에 설명된 권한이 필요합니다.

SQL DB 참가자 및 SQL Server 참가자와 같은 Azure 리소스에 대한 기본 제공 역할은 DWU 설정을 변경할 수 있습니다.

## <a name="view-current-dwu-settings"></a>현재 DWU 설정 보기

현재 DWU 설정을 보려면:

1. Visual Studio에서 SQL Server 개체 탐색기를 엽니다.
2. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.
3. sys.database_service_objectives 동적 관리 뷰에서 선택합니다. 다음은 예제입니다.

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>데이터 웨어하우스 단위 변경

### <a name="azure-portal"></a>Azure Portal

To change DWUs:

1. [Azure Portal](https://portal.azure.com)을 열고 데이터베이스를 연 다음 **크기 조정**을 클릭합니다.

2. **크기 조정**에서 슬라이더를 왼쪽 또는 오른쪽으로 이동해 DWU 설정을 변경합니다.

3. 페이지 맨 아래에 있는 **저장**을 참조하세요. 확인 메시지가 표시됩니다. **예**를 클릭하여 확인하거나 **아니요**를 클릭하여 취소합니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To change the DWUs, use the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet. The following example sets the service level objective to DW1000c for the database MySQLDW that is hosted on server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

자세한 내용은 [SQL Data Warehouse용 PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)을 참조하세요.

### <a name="t-sql"></a>T-SQL

With T-SQL you can view the current DWU settings, change the settings, and check the progress.

DWU를 변경하려면

1. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 문을 사용합니다. The following example sets the service level objective to DW1000c for the database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API

DWU를 변경하려면 [데이터베이스 생성 또는 업데이트](/rest/api/sql/databases/createorupdate) REST API를 사용합니다. The following example sets the service level objective to DW1000c for the database MySQLDW, which is hosted on server MyServer. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

추가 REST API 예제를 보려면 [SQL Data Warehouse용 REST API](sql-data-warehouse-manage-compute-rest-api.md)를 참조하세요.

## <a name="check-status-of-dwu-changes"></a>DWU 변경 상태 확인

DWU 변경을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 자동으로 크기를 조정하는 경우 다른 작업을 진행하기 전에 특정 작업이 완료되었는지 확인하는 논리를 구현하는 것이 좋습니다.

다양한 엔드포인트를 통해 데이터베이스 상태를 확인하면 자동화를 올바르게 구현할 수 있습니다. 포털에서는 작업 완료 시의 알림과 데이터베이스 현재 상태를 제공하지만 프로그래밍 방식으로 상태를 확인할 수는 없습니다.

Azure Portal에서 스케일 아웃 작업에 대한 데이터베이스 상태를 확인할 수 없습니다.

DWU 변경 상태를 확인하려면:

1. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.

1. 다음 쿼리를 제출하여 데이터베이스 상태를 확인합니다.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. 다음 쿼리를 제출하여 작업 상태를 확인합니다.

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
This DMV returns information about various management operations on your SQL pool such as the operation and the state of the operation, which is either IN_PROGRESS or COMPLETED.

## <a name="the-scaling-workflow"></a>크기 조정 워크플로

When you start a scale operation, the system first kills all open sessions, rolling back any open transactions to ensure a consistent state. 크기 조정 작업의 경우 이 트랜잭션 롤백을 완료한 후에만 크기 조정이 수행됩니다.  

- For a scale-up operation, the system detaches all compute nodes, provisions the additional compute nodes, and then reattaches to the storage layer.
- For a scale-down operation, the system detaches all compute nodes and then reattaches only the needed nodes to the storage layer.

## <a name="next-steps"></a>다음 단계

성능 관리에 대한 자세한 내용은 [워크로드 관리에 대한 리소스 클래스](resource-classes-for-workload-management.md) 및 [메모리와 동시성 제한](memory-concurrency-limits.md)을 참조하세요.
