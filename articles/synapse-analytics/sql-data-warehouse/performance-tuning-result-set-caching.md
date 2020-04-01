---
title: 결과 집합 캐싱을 사용한 성능 조정
description: Azure 시냅스 분석에서 SQL 분석에 대한 결과 세트 캐싱 기능 개요
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: da476dc14949ebab1a054a9624d91acb25b9f2b4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474472"
---
# <a name="performance-tuning-with-result-set-caching"></a>결과 집합 캐싱을 사용한 성능 조정  
결과 집합 캐싱을 사용하도록 설정하면 SQL Analytics는 반복적으로 사용하기 위해 사용자 데이터베이스의 쿼리 결과를 자동으로 캐시합니다.  이렇게 하면 후속 쿼리 실행이 지속된 캐시에서 직접 결과를 얻을 수 있으므로 다시 계산할 필요가 없습니다.   결과 집합 캐싱은 쿼리 성능을 향상시키고 계산 리소스 사용량을 줄입니다.  또한 캐시된 결과 집합을 사용하는 쿼리는 동시성 슬롯을 사용하지 않으므로 기존 동시성 제한에 포함되지 않습니다. 보안을 위해 사용자는 캐시된 결과를 만드는 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시된 결과에 액세스할 수 있습니다.  

## <a name="key-commands"></a>주요 명령
[사용자 데이터베이스에 대한 켜기/끄기 결과 세트 캐싱 켜기](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[세션에 대한 켜기/끄기 결과 세트 캐싱 켜기](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[캐시된 결과 집합의 크기 확인](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[캐시 정리](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>캐시되지 않은 것  

데이터베이스에 대해 결과 집합 캐싱이 켜진 경우, 다음 쿼리를 제외하고 캐시가 가득 찰 때까지 모든 쿼리에 대해 결과가 캐시됩니다.
- 비결정적 함수(예: DateTime.Now())를 사용하여 쿼리
- 사용자 정의 함수를 사용하여 쿼리
- 행 수준 보안 또는 열 수준 보안이 활성화된 테이블을 사용하는 쿼리
- 행 크기가 64KB보다 큰 데이터를 반환하는 쿼리

> [!IMPORTANT]
> 결과 집합 캐시를 만들고 캐시에서 데이터를 검색하는 작업은 SQL Analytics 인스턴스의 제어 노드에서 발생합니다.
> 결과 집합 캐싱이 켜져 있을 때 큰 결과 집합을 반환하는 쿼리(예: >100만개 행)를 실행하면 컨트롤 노드에서 CPU 사용량이 증가하고 인스턴스의 전체 쿼리 응답 속도가 느려질 수 있습니다.  이러한 쿼리는 일반적으로 데이터 탐색 또는 ETL 작업 중에 사용됩니다. 컨트롤 노드의 스트레스를 방지하고 성능 문제가 발생하는 것을 방지하려면 사용자는 해당 유형의 쿼리를 실행하기 전에 데이터베이스에서 결과 집합 캐싱을 해제해야 합니다.  

쿼리에 대한 결과 집합 캐싱 작업이 수행된 시간에 대해 이 쿼리를 실행합니다.

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

다음은 결과 집합 캐싱을 사용하지 않도록 설정한 쿼리에 대한 예제 출력입니다.

![rsc를 사용할 수 있는 쿼리 단계](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

다음은 결과 집합 캐싱을 사용하도록 설정된 쿼리에 대한 예제 출력입니다.

![rsc를 이용한 쿼리 단계-사용 가능](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>캐시된 결과를 사용하는 경우

캐시된 결과 집합은 다음 요구 사항이 모두 충족되는 경우 쿼리에 다시 사용됩니다.
- 쿼리를 실행하는 사용자는 쿼리에서 참조된 모든 테이블에 액세스할 수 있습니다.
- 새 쿼리와 결과 집합 캐시를 생성한 이전 쿼리가 정확히 일치합니다.
- 캐시된 결과 집합이 생성된 테이블에는 데이터 또는 스키마 변경 내용이 없습니다.

이 명령을 실행하여 결과 캐시 적중 또는 누락으로 쿼리를 실행했는지 확인합니다. result_set_cache 열은 캐시 적중의 경우 1, 캐시 누락의 경우 0, 결과 집합 캐싱이 사용되지 않은 이유로 음수 값을 반환합니다. 자세한 내용은 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7) 확인하십시오.

```sql
SELECT request_id, command, result_set_cache FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>캐시된 결과 관리 

결과 세트 캐시의 최대 크기는 데이터베이스당 1TB입니다.  캐시된 결과는 기본 쿼리 데이터가 변경될 때 자동으로 무효화됩니다.  

캐시 제거는 다음 일정에 따라 SQL Analytics에서 자동으로 관리합니다. 
- 결과 집합을 사용하지 않았거나 무효화된 경우 48시간마다 
- 결과 집합 캐시가 최대 크기에 가까워지면

사용자는 다음 옵션 중 하나를 사용하여 전체 결과 집합 캐시를 수동으로 비울 수 있습니다. 
- 데이터베이스에 대한 결과 집합 캐시 기능을 해제 
- 데이터베이스에 연결된 상태에서 DBCC DROPRESULTSETCACHE 실행

데이터베이스를 일시 중지하면 캐시된 결과 집합이 비어 있지 않습니다.  

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요. 
