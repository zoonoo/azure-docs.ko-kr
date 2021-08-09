---
title: Azure Cosmos DB 쿼리 언어의 SUM
description: Azure Cosmos DB의 SQL 시스템 함수 합계(SUM)에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553425"
---
# <a name="sum-azure-cosmos-db"></a>SUM(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 집계 함수는 식의 값 합계를 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
다음 예에서는 `propertyA`의 합계를 반환합니다.
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다. `SUM`의 인수가 문자열, 부울 또는 null인 경우 전체 집계 시스템 함수는 `undefined`를 반환합니다. 인수에 `undefined` 값이 있으면 `SUM` 계산에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수학 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB의 집계 함수](sql-query-aggregate-functions.md)