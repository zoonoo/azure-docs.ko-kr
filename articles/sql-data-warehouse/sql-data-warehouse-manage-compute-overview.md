---
title: "Azure SQL Data Warehouse의 계산 능력 관리(개요) | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스의 성능 확장 기능입니다. 또는 DWU를 조정하거나 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2017
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: f4a79413bc5e660504b4b6b48fcf496fb0f08ade
ms.lasthandoff: 03/29/2017


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Azure SQL 데이터 웨어하우스의 계산 능력 관리(개요)
> [!div class="op_single_selector"]
> * [개요](sql-data-warehouse-manage-compute-overview.md)
> * [포털](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST (영문)](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

SQL 데이터 웨어하우스는 저장소와 계산을 분리하여 각각의 성능을 독립적으로 조정할 수 있습니다. 따라서 데이터 양에 관계 없이 성능 요구 사항에 맞게 계산을 확장할 수 있습니다. 이 아키텍처의 자연스러운 결과는 계산 및 저장소에 대한 [청구][billed]가 분리되어 있다는 것입니다. 

이 개요에서는 SQL Data Warehouse에서 규모 확장이 작동하는 방법과 SQL Data Warehouse의 일시 중지, 다시 시작 및 확장 기능을 활용하는 방법에 대해 설명합니다. DWU(데이터 웨어하우스 단위)와 성능 간의 관계에 대해 알아 보려면 [DWU][data warehouse units (DWUs)] 페이지를 참조하세요. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>SQL Data Warehouse에서 계산 관리 작업이 작동하는 방법
SQL Data Warehouse의 아키텍처는 제어 노드, 계산 노드 및 60개 배포판으로 분산된 저장소 계층으로 구성됩니다. 

SQL Data Warehouse의 정상적인 활성 세션 동안 메타데이터를 관리하는 시스템의 헤드 노드에는 분산된 쿼리 최적화 프로그램이 있습니다. 이 헤드 노드 아래에는 계산 노드와 저장소 계층이 있습니다. 400DWU의 경우 시스템에는 하나의 헤드 노드, 네 개의 계산 노드 및 60개의 배포판으로 구성된 저장소 계층이 있습니다. 

크기 조정 또는 일시 중지 작업을 수행하는 경우 시스템에서 먼저 들어오는 모든 쿼리를 종료한 다음 트랜잭션을 롤백하여 일관된 상태를 유지합니다. 크기 조정 작업의 경우 이 트랜잭션 롤백을 완료한 후에만 크기 조정이 수행됩니다. 강화 작업의 경우 시스템에서 추가로 원하는 계산 노드 수를 프로비전한 다음 계산 노드를 저장소 계층에 다시 연결합니다. 규모 축소 작업의 경우 불필요한 노드가 해제되고 나머지 계산 노드는 적절한 수의 배포판에 다시 연결됩니다. 일시 중지 작업의 경우 모든 계산 노드가 해제되고 시스템에서 다양한 메타데이터 작업을 수행하여 최종 시스템을 안정된 상태로 유지합니다.

| DWU  | 계산 노드 수 \# | 노드당 배포 수 \# |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

계산 관리를 위한 세 가지 주요 기능은 다음과 같습니다.

1. 일시 중지
2. 다시 시작
3. 확장

이러한 작업을 각각 완료하는 데에는 몇 분이 걸릴 수 있습니다. 자동으로 크기 조정/일시 중지/다시 시작하는 경우 다른 작업을 진행하기 전에 특정 작업이 완료되었는지 확인하는 논리를 구현하는 것이 좋습니다. 

다양한 끝점을 통해 데이터베이스 상태를 확인하는 경우 이러한 작업의 자동화를 올바르게 구현할 수 있습니다. 포털에서는 작업 완료 시의 알림과 데이터베이스 현재 상태를 제공하지만 프로그래밍 방식으로 상태를 확인할 수는 없습니다. 

>  [!NOTE]
>
>  모든 끝점에 대한 계산 관리 기능은 존재하지 않습니다.
>
>  

|              | 일시 중지/다시 시작 | 확장 | 데이터베이스 상태 확인 |
| ------------ | ------------ | ----- | -------------------- |
| Azure 포털 | 예          | 예   | **아니요**               |
| PowerShell   | 예          | 예   | 예                  |
| REST API     | 예          | 예   | 예                  |
| T-SQL        | **아니요**       | 예   | 예                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>계산 조정

SQL Data Warehouse의 성능은 CPU, 메모리 및 I/O 대역폭과 같은 계산 리소스를 추상화한 측정값인 [DWU(데이터 웨어하우스 단위)][data warehouse units (DWUs)]로 측정됩니다. 시스템 성능을 조정하려는 사용자는 포털, T-SQL 및 REST API와 같은 다양한 방법을 통해 시스템 성능을 조정할 수 있습니다. 

### <a name="how-do-i-scale-compute"></a>계산 크기는 어떻게 조정합니까?
DWU 설정을 변경하여 SQL Data Warehouse에 대한 계산 성능을 관리합니다. 특정 작업에 대해 DWU를 더 많이 추가하면 성능이 [선형적으로][linearly] 향상됩니다.  시스템을 강화하거나 축소할 때 성능이 눈에 띄게 변경되는 DWU 제품을 제공합니다. 

DWU를 조정할 경우 다음과 같은 개별 방법을 사용할 수 있습니다.

* [Azure Portal을 사용하여 계산 능력 조정][Scale compute power with Azure portal]
* [PowerShell을 사용하여 계산 능력 조정][Scale compute power with PowerShell]
* [REST API를 사용하여 계산 능력 조정][Scale compute power with REST APIs]
* [TSQL을 사용하여 계산 능력 조정][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>얼마나 많은 DWU를 사용해야 합니까?

사용자에게 이상적인 DWU가 무엇인지 파악하기 위해서는 규모를 키우거나 줄이고 데이터를 로드한 후에 몇 가지 쿼리를 실행해 봅니다. 크기 조정이 빠르기 때문에 1시간 이내에 다양한 성능 수준을 시험해 볼 수 있습니다. 

> [!Note] 
> SQL Data Warehouse는 많은 양의 데이터를 처리하도록 설계되었습니다. 특히 대규모 DWU에서 크기 조정에 대한 실제 기능을 확인하려면 1TB에 가깝거나 초과하는 대용량 데이터 집합을 사용합니다.

워크로드에 가장 적합한 DWU를 찾는 방법에 대한 권장 사항:

1. 개발 중인 데이터 웨어하우스의 경우 먼저 낮은 DWU 성능 수준을 선택합니다.  적합한 시작 지점은 DW400 또는 DW200입니다.
2. 응용 프로그램 성능을 모니터링하여 선택한 DWU 수와 관찰한 성능을 비교합니다.
3. 선형 크기 조정을 가정하여 요구 사항에 맞는 최적 성능 수준을 달성하기 위해 성능이 얼마나 더 빠르거나 느려야 하는지 확인합니다.
4. 원하는 워크로드 성능에 비례하여 DWU 수를 늘리거나 줄입니다. 
5. 비즈니스 요구 사항에 맞는 최적 성능 수준에 도달할 때까지 계속 조정합니다.

> [!NOTE]
>
> 계산 노드 간에 작업을 분할할 수 있는 경우 더 많은 병렬 처리를 통해 쿼리 성능만 증가합니다. 크기 조정으로 성능이 변경되지 않는다고 확인되면 성능 튜닝 관련 문서를 참조하여 데이터가 균일하게 분산되어 있지 않은지 또는 많은 양의 데이터 이동이 있는지 확인합니다. 

### <a name="when-should-i-scale-dwus"></a>언제 DWU를 조정해야 하나요?
DWU 크기 조정은 다음과 같은 중요한 시나리오를 변경합니다.

1. 검색, 집계 및 CTAS 문에 대한 시스템 성능의 선형적 변경
2. PolyBase로 로드할 때 판독기 및 기록기 수 증가
3. 동시 쿼리 및 동시성 슬롯의 최대 수

DWU 성능 조정 시기에 대한 권장 사항:

1. 대량의 데이터 로딩 또는 변환 작업을 수행하기 전에 데이터를 더욱 빠르게 사용할 수 있도록 DWU를 확장합니다.
2. 가장 바쁜 업무 시간에서 많은 수의 동시 쿼리를 수용할 수 있도록 확장합니다. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>계산 일시 중지
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 다음과 같은 개별 방법을 사용합니다.

* [Azure Portal을 사용하여 계산 일시 중지][Pause compute with Azure portal]
* [PowerShell을 사용하여 계산 일시 중지][Pause compute with PowerShell]
* [REST API를 사용하여 계산 일시 중지][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>계산 다시 시작
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 다시 시작하려면 다음과 같은 개별 방법을 사용합니다.

* [Azure Portal을 사용하여 계산 다시 시작][Resume compute with Azure portal]
* [PowerShell을 사용하여 계산 다시 시작][Resume compute with PowerShell]
* [REST API를 사용하여 계산 다시 시작][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>데이터베이스 상태 확인 

데이터베이스를 다시 시작하려면 다음과 같은 개별 방법을 사용합니다.

- [T-SQL을 사용하여 데이터베이스 상태 확인][Check database state with T-SQL]
- [PowerShell을 사용하여 데이터베이스 상태 확인][Check database state with PowerShell]
- [REST API를 사용하여 데이터베이스 상태 확인][Check database state with REST APIs]

## <a name="permissions"></a>권한

데이터베이스 크기를 조정하려면 [ALTER DATABASE][ALTER DATABASE]에서 설명하는 권한이 필요합니다.  일시 중지 및 다시 시작을 수행하려면 [SQL DB 참가자][SQL DB Contributor] 권한, 특히 Microsoft.Sql/servers/databases/action이 필요합니다.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>다음 단계
일부 추가적인 주요 성능 개념을 이해하는 데 도움이 되는 다음 문서를 참조하세요.

* [워크로드 및 동시성 관리][워크로드 및 동시성 관리]
* [테이블 디자인 개요][Table design overview]
* [테이블 배포][Table distribution]
* [테이블 인덱싱][Table indexing]
* [테이블 분할][Table partitioning]
* [테이블 통계][Table statistics]
* [모범 사례][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[billed]: https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/
[linearly]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/

