---
title: 결과 집합 캐싱을 사용한 성능 조정
description: Azure Synapse Analytics의 SQL Analytics에 대 한 결과 집합 캐싱 기능 개요
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 3d204605e68cf8cf33f69d73fb20f3cc08674e44
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200535"
---
# <a name="performance-tuning-with-result-set-caching"></a>결과 집합 캐싱을 사용한 성능 조정  
결과 집합 캐싱이 설정 된 경우 SQL Analytics는 반복적인 사용을 위해 쿼리 결과를 사용자 데이터베이스에 자동으로 캐시 합니다.  이렇게 하면 다시 계산 기능가 필요 하지 않도록 이후 쿼리 실행에서 지속형 캐시에서 직접 결과를 가져올 수 있습니다.   결과 집합 캐싱은 쿼리 성능을 향상 시키고 계산 리소스 사용량을 줄입니다.  또한 캐시 된 결과 집합을 사용 하는 쿼리는 동시성 슬롯을 사용 하지 않으므로 기존 동시성 제한에 대해 계산 되지 않습니다. 보안을 위해 사용자가 캐시 된 결과를 만드는 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시 된 결과에 액세스할 수 있습니다.  

## <a name="key-commands"></a>키 명령
[사용자 데이터베이스에 대 한 결과 집합 캐싱 설정/해제](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[세션에 대 한 결과 집합 캐싱 설정/해제](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[캐시 된 결과 집합의 크기 확인](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[캐시 정리](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>캐시 되지 않는 내용  

데이터베이스에 대해 결과 집합 캐싱이 켜진 경우, 다음 쿼리를 제외하고 캐시가 가득 찰 때까지 모든 쿼리에 대해 결과가 캐시됩니다.
- 비결정적 함수(예: DateTime.Now())를 사용하여 쿼리
- 사용자 정의 함수를 사용하여 쿼리
- 행 수준 보안 또는 열 수준 보안이 설정 된 테이블을 사용 하는 쿼리
- 행 크기가 64KB보다 큰 데이터를 반환하는 쿼리

> [!IMPORTANT]
> 캐시에서 결과 집합 캐시를 만들고 데이터를 검색 하는 작업은 SQL 분석 인스턴스의 control 노드에서 수행 됩니다.
> 결과 집합 캐싱이 켜져 있을 때 큰 결과 집합을 반환하는 쿼리(예: >100만개 행)를 실행하면 컨트롤 노드에서 CPU 사용량이 증가하고 인스턴스의 전체 쿼리 응답 속도가 느려질 수 있습니다.  이러한 쿼리는 일반적으로 데이터 탐색 또는 ETL 작업 중에 사용됩니다. 컨트롤 노드의 스트레스를 방지하고 성능 문제가 발생하는 것을 방지하려면 사용자는 해당 유형의 쿼리를 실행하기 전에 데이터베이스에서 결과 집합 캐싱을 해제해야 합니다.  

다음 쿼리를 실행 하 여 쿼리에 대 한 결과 집합 캐싱 작업을 수행 하는 시간에 대해 쿼리를 실행 합니다.

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

다음은 결과 집합 캐싱이 사용 하지 않도록 설정 된 상태로 실행 되는 쿼리에 대 한 예제 출력입니다.

![쿼리-rsc-사용 안 함](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

다음은 결과 집합 캐싱이 설정 된 상태로 실행 되는 쿼리에 대 한 예제 출력입니다.

![쿼리-rsc를 사용 하도록 설정 된 단계](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>캐시 된 결과가 사용 되는 경우

캐시된 결과 집합은 다음 요구 사항이 모두 충족되는 경우 쿼리에 다시 사용됩니다.
- 쿼리를 실행하는 사용자는 쿼리에서 참조된 모든 테이블에 액세스할 수 있습니다.
- 새 쿼리와 결과 집합 캐시를 생성한 이전 쿼리가 정확히 일치합니다.
- 캐시된 결과 집합이 생성된 테이블에는 데이터 또는 스키마 변경 내용이 없습니다.

이 명령을 실행하여 결과 캐시 적중 또는 누락으로 쿼리를 실행했는지 확인합니다. 캐시 적중이 있는 경우 result_cache_hit는 1을 반환합니다.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>캐시 된 결과 관리 

결과 세트 캐시의 최대 크기는 데이터베이스당 1TB입니다.  기본 쿼리 데이터가 변경 되 면 캐시 된 결과가 자동으로 무효화 됩니다.  

캐시 제거는 다음 일정에 따라 자동으로 SQL Analytics에서 관리 됩니다. 
- 결과 집합이 사용 되지 않았거나 무효화 된 경우 48 시간 마다 
- 결과 집합 캐시가 최대 크기에 도달 하는 경우

사용자는 다음 옵션 중 하나를 사용 하 여 전체 결과 집합 캐시를 수동으로 비울 수 있습니다. 
- 데이터베이스에 대한 결과 집합 캐시 기능을 해제 
- 데이터베이스에 연결 된 상태에서 DBCC DROPRESULTSETCACHE 실행

데이터베이스를 일시 중지 하면 캐시 된 결과 집합이 비어 있지 않습니다.  

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요. 
