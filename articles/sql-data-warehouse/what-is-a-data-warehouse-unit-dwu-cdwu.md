---
title: Azure SQL Data Warehouse의 데이터 웨어하우스 단위(DWU, cDWU) | Microsoft Docs
description: 가격 및 성능을 최적화하기 위한 이상적인 데이터 웨어하우스 단위(DWU, cDWU) 수 선택에 대한 권장 사항 및 단위 수를 변경하는 방법
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: d20a600951a0fe586e981adf12127072df1b744c
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428018"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>DWU(데이터 웨어하우스 단위) 및 cDWU(컴퓨팅 데이터 웨어하우스 단위)

가격 및 성능을 최적화하기 위한 이상적인 데이터 웨어하우스 단위(DWU, cDWU) 수 선택에 대한 권장 사항 및 단위 수를 변경하는 방법

## <a name="what-are-data-warehouse-units"></a>데이터 웨어하우스 단위 란 무엇 인가요

Azure SQL Data Warehouse CPU, 메모리 및 IO는 Dwu (데이터 웨어하우스 단위) 라는 계산 크기 단위로 제공 됩니다. DWU는 계산 리소스 및 성능의 추상적이고 정규화된 측정값을 나타냅니다. 서비스 수준에 대 한 변경, 성능 및 시스템의 비용을 조정 하는 시스템에 사용할 수 있는 Dwu 수를 변경 합니다.

성능 향상을 위해 데이터 웨어하우스 단위 수를 늘릴 수 있습니다. 성능을 낮추려면 데이터 웨어하우스 단위를 줄입니다. 스토리지 및 계산 비용은 별도로 청구되므로 데이터 웨어하우스 단위를 변경해도 스토리지 비용에 영향을 미치지 않습니다.

데이터 웨어하우스 단위의 성능은 다음과 같은 데이터 웨어하우스 워크로드 메트릭을 기반으로 합니다.

- 속도 표준 데이터 웨어하우징 쿼리 수는 많은 수의 행을 검색 및 복잡 한 집계를 수행 합니다. 이 작업은 I/O 및 CPU를 많이 사용합니다.
- 얼마나 빨리 데이터 웨어하우스 Azure Data Lake 또는 Azure Storage Blob에서 데이터를 수집할 수 있습니다. 이 작업은 네트워크 및 CPU를 많이 사용합니다.
- 속도 [ `CREATE TABLE AS SELECT` ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 명령이 테이블을 복사할 수 있습니다. 이 작업에는 데이터를 저장소에서 읽어오기, 어플라이언스의 노드 전체에 배포하기, 저장소에 다시 쓰기가 포함됩니다. 이 작업은 CPU, IO 및 네트워크를 많이 사용합니다.

DWU 늘리기:

- 검색, 집계 및 CTAS 문에 대한 시스템 성능을 선형적으로 변경합니다.
- PolyBase 로드 작업용 판독기 및 작성기 수를 늘립니다.
- 동시 쿼리 및 동시성 슬롯의 최대 수를 늘립니다.

## <a name="service-level-objective"></a>서비스 수준 목표

SLO(서비스 수준 목표)는 데이터 웨어하우스의 비용 및 성능 수준을 결정하는 확장성 설정입니다. Gen2에 대한 서비스 수준은 cDWU(계산 데이터 웨어하우스 단위)로 측정됩니다(예: DW2000c). Gen1 서비스 수준은 DWU로 측정됩니다(예: DW2000).
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2는 최근에 100cDWU만큼 낮은 컴퓨팅 계층을 지원하기 위해 크기 조정 기능을 추가했습니다. 현재 Gen1에 있는 기존 데이터 웨어하우스는 하위 컴퓨팅 계층이 필요한 경우 이제 추가 비용 없이 현재 사용 가능한 지역에서 Gen2로 업그레이드할 수 있습니다.  해당 지역이 아직 지원되지 않는 경우에도 지원되는 지역으로 업그레이드할 수 있습니다. 자세한 내용은 [Gen2로 업그레이드](upgrade-to-latest-generation.md)를 참조하세요.

T-SQL에서 SERVICE_OBJECTIVE 설정은 서비스 수준 및 데이터 웨어하우스에 대 한 성능 계층을 결정합니다.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>성능 계층 및 데이터 웨어하우스 단위

각 성능 계층은 약간 다른 데이터 웨어하우스의 측정 단위를 사용합니다. 이러한 차이는 크기 단위를 직접 요금 청구로 변환하므로 송장에 반영됩니다.

- Gen1 데이터 웨어하우스는 DWU(데이터 웨어하우스 단위)로 측정됩니다.
- Gen2 데이터 웨어하우스는 cDWU(컴퓨팅 데이터 웨어하우스 단위)로 측정됩니다.

DWU 및 cDWU 모두 계산을 확장 또는 축소할 수 있고 데이터 웨어하우스를 사용할 필요가 없는 경우 계산을 일시 중지할 수 있습니다. 이러한 작업은 모두 주문형 작업입니다. Gen2는 계산 노드에서 로컬 디스크 기반 캐시를 사용하여 성능을 향상시킵니다. 시스템의 크기를 조정하거나 시스템을 일시 중지할 경우 캐시가 무효화되므로 최적의 성능을 얻으려면 캐시 준비 시간이 필요합니다.  

데이터 웨어하우스 단위를 늘리면 계산 리소스가 선형적으로 증가됩니다. Gen2 최상의 쿼리 성능과 최대 규모의 확장성을 제공합니다. 또한 Gen2 시스템 캐시는 대부분의 사용을 확인합니다.

### <a name="capacity-limits"></a>용량 제한

각 SQL Server(예: myserver.database.windows.net)에는 특정 데이터 웨어하우스 단위 수를 허용하는 [DTU(데이터베이스 트랜잭션 단위)](../sql-database/sql-database-what-is-a-dtu.md) 할당량이 지정되어 있습니다. 자세한 내용은 [워크로드 관리 용량 제한](sql-data-warehouse-service-capacity-limits.md#workload-management)을 참조하세요.

## <a name="how-many-data-warehouse-units-do-i-need"></a>얼마나 많은 데이터 웨어하우스 단위 필요 합니까

이상적인 데이터 웨어하우스 단위 수는 워크로드 및 시스템에 로드한 데이터 양에 따라 매우 다릅니다.

워크로드에 가장 적합한 DWU를 찾는 방법에 대한 단계:

1. 더 작은 DWU를 선택하여 시작합니다.
2. 시스템으로 로드하는 데이터를 테스트할 때 애플리케이션 성능을 모니터링하여 선택한 DWU 수와 관찰한 성능을 비교합니다.
3. 정기적으로 작업량이 많은 기간에 필요한 추가 요구 사항을 식별합니다. 최고 사용률이 크게 표시 하는 워크 로드 및 활동 캡처에서 크기가 자주 조정 해야 할 수 있습니다.

SQL Data Warehouse는 데이터 양 조정이 가능한 대량의 계산 및 쿼리를 프로비전할 수 있는 스케일 아웃 시스템입니다. 특히 큰 DWU에서 진정한 크기 조정 기능을 확인하려면 데이터에 충분한 CPU가 할당되도록 데이터 집합의 크기를 조정하는 것이 좋습니다. 크기 조정 테스트의 경우 1TB 이상을 사용하는 것이 좋습니다.

> [!NOTE]
>
> 계산 노드 간에 작업을 분할할 수 있는 경우 더 많은 병렬 처리를 통해 쿼리 성능만 증가합니다. 크기를 조정해도 성능이 변경되지 않는 경우 테이블 디자인 및/또는 쿼리를 튜닝해야 할 수 있습니다. 쿼리 튜닝 지침에 대해서는 [사용자 쿼리 관리](sql-data-warehouse-overview-manage-user-queries.md)를 참조하세요.

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

### <a name="azure-portal"></a>Azure portal

DWU 또는 cDWU를 변경하려면

1. [Azure Portal](https://portal.azure.com)을 열고 데이터베이스를 연 다음 **크기 조정**을 클릭합니다.

2. **크기 조정**에서 슬라이더를 왼쪽 또는 오른쪽으로 이동해 DWU 설정을 변경합니다.

3. **저장**을 클릭합니다. 확인 메시지가 표시됩니다. **예**를 클릭하여 확인하거나 **아니요**를 클릭하여 취소합니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dwu 또는 Cdwu를 변경 하려면 사용 합니다 [집합 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet. 다음 예제에서는 MyServer에서 호스트되는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

자세한 내용은 [SQL Data Warehouse용 PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)을 참조하세요.

### <a name="t-sql"></a>T-SQL

T-SQL을 사용하여 현재 DWU 또는 cDWU 설정을 보고, 설정을 변경하고, 진행 상황을 확인할 수 있습니다.

DWU 또는 cDWU를 변경하려면:

1. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 문을 사용합니다. 다음 예제에서는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

DWU를 변경하려면 [데이터베이스 생성 또는 업데이트](/rest/api/sql/databases/createorupdate) REST API를 사용합니다. 다음 예제에서는 myserver에서 호스팅되는 MySQLDW 데이터베이스에 대 한를 dw1000으로 서비스 수준 목표를 설정 합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
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
2. 다음 쿼리를 제출하여 데이터베이스 상태를 확인합니다.

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

이 DMV는 작업 및 하거나 IN_PROGRESS 또는 COMPLETED 작업의 상태와 같은 SQL Data Warehouse에 대해 다양 한 관리 작업에 대 한 정보를 반환 합니다.

## <a name="the-scaling-workflow"></a>크기 조정 워크플로

크기 조정 작업을 시작 하면 시스템은 먼저 열려 있는 모든 세션, 일관 된 상태를 확인 하려면 열린 트랜잭션이 롤백 중단 합니다. 크기 조정 작업의 경우 이 트랜잭션 롤백을 완료한 후에만 크기 조정이 수행됩니다.  

- 스케일 업 작업의 경우 시스템 모든 계산 노드를 프로 비전 추가 계산 노드를 분리 하 고 저장소 계층에 다시 연결 합니다.
- 규모 축소 작업의 경우 시스템 모든 계산 노드에서 분리 되었다가 다음 저장소 계층에 필요한 노드만 있습니다.

## <a name="next-steps"></a>다음 단계

성능 관리에 대한 자세한 내용은 [워크로드 관리에 대한 리소스 클래스](resource-classes-for-workload-management.md) 및 [메모리와 동시성 제한](memory-and-concurrency-limits.md)을 참조하세요.
