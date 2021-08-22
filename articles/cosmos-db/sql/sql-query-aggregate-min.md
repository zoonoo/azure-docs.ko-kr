---
title: Azure Cosmos DB 쿼리 언어의 MIN
description: Azure Cosmos DB의 SQL 시스템 함수 Min (MIN)에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 26cbc99c83acba404784ceeb54c8937391b3ce2b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529918"
---
# <a name="min-azure-cosmos-db"></a>MIN(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 집계 함수는 식에서 값의 최소값을 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*scalar_expr*  
   는 스칼라 식입니다. 
  
## <a name="return-types"></a>반환 형식
  
스칼라 식을 반환합니다.  
  
## <a name="examples"></a>예
  
다음 예에서는 `propertyA`의 최소값을 반환합니다.
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다. `MIN`의 인수는 숫자, 문자열, 부울 또는 null일 수 있습니다. 정의되지 않은 값은 모두 무시됩니다.

서로 다른 형식의 데이터를 비교할 때, 다음 우선 순위 순서를 사용합니다 (오름차순):

- null
- boolean
- number
- 문자열

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수학 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB의 집계 함수](sql-query-aggregate-functions.md)