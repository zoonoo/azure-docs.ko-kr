---
title: 결과 집합 캐싱을 사용한 성능 튜닝 | Microsoft Docs
description: 기능 개요
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: 3e6af57840cf60516aba994a6b5728bfb7b35f09
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553535"
---
# <a name="performance-tuning-with-result-set-caching"></a>결과 집합 캐싱을 사용한 성능 조정  
결과 집합 캐싱을 사용 하도록 설정 하면 Azure SQL Data Warehouse에서 자동으로 쿼리 결과를 사용자 데이터베이스에 자동으로 캐시 하 여 반복 해 서 사용 합니다.  이렇게 하면 다시 계산 기능가 필요 하지 않도록 이후 쿼리 실행에서 지속형 캐시에서 직접 결과를 가져올 수 있습니다.   결과 집합 캐싱은 쿼리 성능을 향상 시키고 계산 리소스 사용량을 줄입니다.  또한 캐시 된 결과 집합을 사용 하는 쿼리는 동시성 슬롯을 사용 하지 않으므로 기존 동시성 제한에 대해 계산 되지 않습니다. 보안을 위해 사용자가 캐시 된 결과를 만드는 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시 된 결과에 액세스할 수 있습니다.  

## <a name="key-commands"></a>키 명령
[사용자 데이터베이스에 대 한 결과 집합 캐싱 설정/해제](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[세션에 대 한 결과 집합 캐싱 설정/해제](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[캐시 된 결과 집합의 크기 확인](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[캐시 정리](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>캐시 되지 않는 내용  

데이터베이스에 대해 결과 집합 캐싱이 설정 되 면 다음 쿼리를 제외 하 고 캐시가 가득 찼을 때까지 모든 쿼리에 대해 결과가 캐시 됩니다.
- DateTime. Now ()와 같은 비결 정적 함수를 사용 하 여 쿼리
- 사용자 정의 함수를 사용 하 여 쿼리
- 행 수준 보안 또는 열 수준 보안이 설정 된 테이블을 사용 하는 쿼리
- 행 크기가 64KB 보다 큰 데이터를 반환 하는 쿼리

결과 집합이 많은 쿼리 (예: > 100만 행)를 사용 하면 결과 캐시가 생성 될 때 처음 실행 하는 동안 성능이 저하 될 수 있습니다.

## <a name="when-cached-results-are-used"></a>캐시 된 결과가 사용 되는 경우

다음 요구 사항을 모두 충족 하는 경우 캐시 된 결과 집합이 쿼리에 다시 사용 됩니다.
- 쿼리를 실행 하는 사용자는 쿼리에서 참조 되는 모든 테이블에 액세스할 수 있습니다.
- 새 쿼리와 결과 집합 캐시를 생성 한 이전 쿼리 사이에 정확히 일치 하는 항목이 있습니다.
- 캐시 된 결과 집합이 생성 된 테이블에는 데이터 나 스키마가 변경 되지 않습니다.

이 명령을 실행 하 여 결과 캐시 적중 횟수 또는 누락으로 쿼리를 실행 했는지 확인 합니다. 캐시 적중이 있으면 result_cache_hit는 1을 반환 합니다.

```sql
SELECT request_id, command, result_cache_hit FROM sys.pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>캐시 된 결과 관리 

결과 집합 캐시의 최대 크기는 데이터베이스당 1tb입니다.  기본 쿼리 데이터가 변경 되 면 캐시 된 결과가 자동으로 무효화 됩니다.  

캐시 제거는이 일정에 따라 자동으로 Azure SQL Data Warehouse에서 관리 됩니다. 
- 결과 집합이 사용 되지 않았거나 무효화 된 경우 48 시간 마다 
- 결과 집합 캐시가 최대 크기에 도달 하는 경우

사용자는 다음 옵션 중 하나를 사용 하 여 전체 결과 집합 캐시를 수동으로 비울 수 있습니다. 
- 데이터베이스에 대 한 결과 집합 캐시 기능 해제 
- 데이터베이스에 연결 된 상태에서 DBCC DROPRESULTSETCACHE 실행

데이터베이스를 일시 중지 하면 캐시 된 결과 집합이 비어 있지 않습니다.  

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요. 
