---
title: Azure Cosmos DB 쿼리 언어의 합계
description: Azure Cosmos DB의 Sum (SUM) SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553425"
---
# <a name="sum-azure-cosmos-db"></a>합계 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 집계 함수는 식의 값 합계를 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
숫자 식을 반환합니다.  
  
## <a name="examples"></a>예제
  
다음 예에서는의 합계를 반환 합니다 `propertyA` .
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다. 의 인수가 `SUM` string, boolean 또는 null 인 경우 전체 집계 시스템 함수는를 반환 `undefined` 합니다. 인수에 값이 있으면 `undefined` 계산에 영향을 주지 않습니다 `SUM` .

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수치 연산 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB 집계 함수](sql-query-aggregate-functions.md)