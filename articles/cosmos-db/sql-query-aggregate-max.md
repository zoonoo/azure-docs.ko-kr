---
title: Azure Cosmos DB 쿼리 언어의 MAX
description: Azure Cosmos DB의 SQL 시스템 함수 최대(MAX)에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553412"
---
# <a name="max-azure-cosmos-db"></a>MAX(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 집계 함수는 식에 있는 값의 최댓값을 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>인수

*scalar_expr*  
   는 스칼라 식입니다. 
  
## <a name="return-types"></a>반환 형식
  
스칼라 식을 반환합니다.  
  
## <a name="examples"></a>예
  
다음 예에서는 `propertyA`의 최댓값을 반환합니다.
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다. `MAX`의 인수는 숫자, 문자열, 부울 또는 null일 수 있습니다. 정의되지 않은 값은 모두 무시됩니다.

서로 다른 형식 데이터를 비교하는 경우 다음과 같은 우선 순위 순서를 사용합니다(내림차순).

- 문자열
- 숫자
- boolean
- null

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수학 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB의 집계 함수](sql-query-aggregate-functions.md)