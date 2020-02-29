---
title: SQL 풀의 계산 리소스 관리
description: Azure Synapse Analytics SQL 풀의 성능 확장 기능에 대해 알아봅니다. DWU를 조정하여 확장하거나 데이터 웨어하우스를 일시 중지하여 비용을 절약합니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ef860fb607a4f241e6428b714b022058a4697228
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197286"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Azure Synapse Analytics 데이터 웨어하우스의 계산 관리

Azure Synapse Analytics SQL 풀에서 계산 리소스를 관리 하는 방법에 대해 알아봅니다. SQL 풀을 일시 중지 하 여 비용을 절감 하거나 성능 요구에 맞게 데이터 웨어하우스의 크기를 조정 합니다. 

## <a name="what-is-compute-management"></a>컴퓨팅 관리란?

데이터 웨어하우스의 아키텍처는 저장소와 계산을 분리 하 여 각각을 독립적으로 확장할 수 있도록 합니다. 이에 따라 데이터 스토리지와는 관계없이 성능 요구 사항에 맞게 컴퓨팅의 크기를 조정할 수 있습니다. 또한 컴퓨팅 리소스를 일시 중지했다가 다시 시작할 수도 있습니다. 이 아키텍처의 자연스러운 결과는 컴퓨팅 및 스토리지에 대한 [청구](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)가 분리되어 있다는 것입니다. 한동안 데이터 웨어하우스를 사용할 필요가 없으면 컴퓨팅을 일시 중지하여 컴퓨팅 비용을 절약할 수 있습니다. 

## <a name="scaling-compute"></a>컴퓨팅 크기 조정

SQL 풀의 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md) 설정을 조정 하 여 계산을 확장 하거나 축소할 수 있습니다. 데이터 웨어하우스 단위를 더 추가함에 따라 로드 및 쿼리 성능이 선형적으로 증가할 수 있습니다. 

확장 단계는 [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md) 또는 [T-SQL](quickstart-scale-compute-tsql.md) 빠른 시작을 참조하세요. 또한 확장 작업은 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)를 통해 수행할 수도 있습니다.

크기 조정 작업을 수행 하기 위해 SQL 풀은 먼저 들어오는 모든 쿼리를 종료 한 다음 트랜잭션을 롤백하여 일관 된 상태를 유지 합니다. 크기 조정은 트랜잭션 롤백이 완료된 후에만 수행됩니다. 크기 조정 작업의 경우 시스템은 계산 노드에서 저장소 계층을 분리 하 고, 계산 노드를 추가한 다음, 저장소 계층을 계산 계층에 다시 연결 합니다. 각 SQL 풀은 계산 노드에 균등 하 게 분산 되는 60 배포로 저장 됩니다. 계산 노드를 더 추가 하면 계산 전력이 늘어납니다. 계산 노드 수가 늘어나면 계산 노드당 배포 수가 감소 하 여 쿼리에 더 많은 계산 능력을 제공 합니다. 마찬가지로, 데이터 웨어하우스 단위를 줄이면 계산 노드의 수가 줄어들어 쿼리에 대 한 계산 리소스를 줄일 수 있습니다.

다음 표에서는 데이터 웨어하우스 단위를 변경할 때 컴퓨팅 노드당 배포 수가 변경되는 방식을 보여 줍니다.  DW30000c는 60 계산 노드를 제공 하 고 DW100c 보다 훨씬 더 높은 쿼리 성능을 제공 합니다. 

| DWU(데이터 웨어하우스 단위)  | 계산 노드의 \# | 노드당 배포 수 \# |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>올바른 데이터 웨어하우스 단위 크기 찾기

확장, 특히 더 큰 데이터 웨어하우스 단위의 성능상 이점을 알아보기 위해 최소한 1TB의 데이터 집합을 사용하려고 합니다. SQL 풀에 가장 적합 한 데이터 웨어하우스 단위 수를 찾으려면 확장 및 축소를 시도 합니다. 데이터를 로드한 후 서로 다른 데이터 웨어하우스 단위 수를 사용하여 몇 가지 쿼리를 실행합니다. 크기 조정이 빠르기 때문에 1시간 이내에 다양한 성능 수준을 시험해 볼 수 있습니다. 

최적의 데이터 웨어하우스 단위 수를 찾는 데 권장되는 사항은 다음과 같습니다.

- 개발 중인 SQL 풀의 경우 더 적은 수의 데이터 웨어하우스 단위를 선택 하 여 시작 합니다.  좋은 출발점은 DW400c 또는 DW200c입니다.
- 애플리케이션 성능을 모니터링하여 선택한 데이터 웨어하우스 단위 수와 관찰한 성능을 비교합니다.
- 선형 크기 조정을 가정하고 데이터 웨어하우스 단위를 늘리거나 줄이는 데 필요한 크기를 결정합니다. 
- 비즈니스 요구 사항에 맞는 최적 성능 수준에 도달할 때까지 계속 조정합니다.

## <a name="when-to-scale-out"></a>확장하는 경우

데이터 웨어하우스 단위를 확장하는 경우 성능 측면에 미치는 영향은 다음과 같습니다.

- 검색, 집계 및 CTAS 문에 대한 시스템 성능을 선형적으로 향상시킵니다.
- 데이터를 로드하기 위해 판독기 및 기록기의 수를 늘립니다.
- 동시 쿼리 및 동시성 슬롯의 최대 수

데이터 웨어하우스 단위를 확장하는 경우 권장 사항은 다음과 같습니다.

- 대량의 데이터 로드 또는 변환 작업을 수행하기 전에 데이터를 더 빠르게 사용할 수 있도록 확장합니다.
- 가장 바쁜 업무 시간에 많은 수의 동시 쿼리를 수용할 수 있도록 확장합니다. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>확장으로도 성능이 향상되지 않는 경우

데이터 웨어하우스 단위를 추가하면 병렬 처리가 증가합니다. 작업이 컴퓨팅 노드 간에 균등하게 분할되면 추가 병렬 처리로 인해 쿼리 성능이 향상됩니다. 확장을 통해 성능이 변경되지 않는 경우 이러한 상황이 발생할 수 있는 몇 가지 이유가 있습니다. 데이터가 배포를 통해 왜곡되거나 쿼리에서 대량의 데이터 이동이 발생할 수 있습니다. 쿼리 성능 문제를 조사하려면 [성능 문제 해결](sql-data-warehouse-troubleshoot.md#performance)을 참조하세요. 

## <a name="pausing-and-resuming-compute"></a>컴퓨팅 일시 중지 및 다시 시작

컴퓨팅을 일시 중지하면 스토리지 계층이 컴퓨팅 노드에서 분리됩니다. 계산 리소스는 계정에서 해제 됩니다. 컴퓨팅이 일시 중지된 동안에는 컴퓨팅 비용이 청구되지 않습니다. 컴퓨팅을 다시 시작하면 스토리지를 컴퓨팅 노드에 다시 연결하여 컴퓨팅 비용에 대한 청구를 다시 시작합니다. SQL 풀을 일시 중지 하는 경우:

* Compute 및 메모리 리소스가 데이터 센터에서 사용 가능한 리소스의 풀에 반환됩니다.
* 일시 중지 기간 동안에는 데이터 웨어하우스 단위 비용이 0입니다.
* 데이터 스토리지에는 영향이 없으며 데이터는 그대로 유지됩니다. 
* 실행 중이거나 큐에 대기 중인 모든 작업은 취소 됩니다.

SQL 풀을 다시 시작 하는 경우:

* SQL 풀은 데이터 웨어하우스 단위 설정에 대해 계산 및 메모리 리소스를 가져옵니다.
* 데이터 웨어하우스 단위에 대한 컴퓨팅 비용 청구가 다시 시작됩니다.
* 데이터를 사용할 수 있게 됩니다.
* SQL 풀이 온라인 상태 이면 작업 쿼리를 다시 시작 해야 합니다.

항상 SQL 풀에 액세스할 수 있게 하려면 일시 중지 하지 말고 가장 작은 크기로 규모를 조정 하는 것이 좋습니다. 

일시 중지 및 다시 시작 단계는 [Azure Portal](pause-and-resume-compute-portal.md) 또는 [PowerShell](pause-and-resume-compute-powershell.md) 빠른 시작을 참조하세요. 또한 [일시 중지 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) 또는 [다시 시작 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)를 사용할 수도 있습니다.

## <a name="drain-transactions-before-pausing-or-scaling"></a>일시 중지 또는 크기 조정 전 트랜잭션 비우기

일시 중지 또는 크기 조정 작업을 시작하기 전에 기존 트랜잭션이 완료되도록 허용하는 것이 좋습니다.

SQL 풀을 일시 중지 하거나 크기를 조정 하는 경우 백그라운드에서 일시 중지 또는 크기 조정 요청을 시작할 때 쿼리가 취소 됩니다. 간단한 SELECT 쿼리를 취소하는 것은 빠른 작업이며 인스턴스를 일시 중지하거나 규모를 조정하는 데 소요되는 시간에 거의 영향을 주지 않습니다.  하지만 트랜잭션 쿼리는 데이터 또는 데이터 구조를 수정하므로 신속하게 중지되지 않을 수 있습니다. **기본적으로 트랜잭션 쿼리는 전체를 완료하거나 변경 사항을 롤백해야 합니다.** 트랜잭션 쿼리로 완료된 작업을 롤백하는 데는 쿼리가 적용된 원래 변경 사항보다 시간이 훨씬 더 오래 걸릴 수 있습니다. 예를 들어 행을 삭제하고 1시간 동안 실행 중인 쿼리를 취소하는 경우 시스템에서 삭제된 행을 다시 삽입하는 데 1시간이 소요될 수 있습니다. 트랜잭션이 진행 중인 동안 일시 중지 또는 크기 조정을 실행할 경우 일시 중지 및 크기 조정 시 롤백이 완료될 때까지 기다린 후 진행할 수 있으므로 시간이 오래 소요되는 것처럼 보일 수 있습니다.

[트랜잭션 이해](sql-data-warehouse-develop-transactions.md) 및 [트랜잭션 최적화](sql-data-warehouse-develop-best-practices-transactions.md)도 참조하세요.

## <a name="automating-compute-management"></a>컴퓨팅 관리 자동화

컴퓨팅 관리 작업을 자동화하려면 [Azure Functions를 사용하여 컴퓨팅 관리](manage-compute-with-azure-functions.md)를 참조하세요.

각각의 확장, 일시 중지 및 다시 시작 작업을 완료하는 데 몇 분이 걸릴 수 있습니다. 자동으로 크기 조정, 일시 중지 또는 다시 시작하는 경우, 다른 작업을 진행하기 전에 특정 작업이 완료되었는지 확인하는 논리를 구현하는 것이 좋습니다. 다양 한 끝점을 통해 SQL 풀 상태를 확인 하면 이러한 작업의 자동화를 올바르게 구현할 수 있습니다. 

SQL 풀 상태를 확인 하려면 [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) 또는 [t-sql](quickstart-scale-compute-tsql.md#check-data-warehouse-state) 빠른 시작을 참조 하세요. [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)를 사용 하 여 SQL 풀 상태를 확인할 수도 있습니다.


## <a name="permissions"></a>권한

SQL 풀 크기를 조정 하려면 [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)에 설명 된 권한이 필요 합니다.  일시 중지하고 다시 시작하려면 [SQL DB 참가자](../role-based-access-control/built-in-roles.md#sql-db-contributor) 권한, 특히 Microsoft.Sql/servers/databases/action이 필요합니다.


## <a name="next-steps"></a>다음 단계
계산 리소스 관리의 다른 측면을 [계산](manage-compute-with-azure-functions.md) 하는 방법 가이드에서 개별 쿼리에 대해 서로 다른 계산 리소스를 할당 하는 방법을 참조 하세요. 자세한 내용은 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
