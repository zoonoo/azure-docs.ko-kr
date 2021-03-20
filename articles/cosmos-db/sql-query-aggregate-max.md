---
title: Azure Cosmos DB 최대 쿼리 언어
description: Azure Cosmos DB의 최대 (MAX) SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553412"
---
# <a name="max-azure-cosmos-db"></a>최대 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 집계 함수는 식에 있는 값의 최대값을 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>인수

*scalar_expr*  
   스칼라 식입니다. 
  
## <a name="return-types"></a>반환 형식
  
스칼라 식을 반환 합니다.  
  
## <a name="examples"></a>예제
  
다음 예에서는의 최대값을 반환 합니다 `propertyA` .
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다. 의 인수는 `MAX` 숫자, 문자열, 부울 또는 null 일 수 있습니다. 정의 되지 않은 값은 모두 무시 됩니다.

서로 다른 형식 데이터를 비교 하는 경우 다음과 같은 우선 순위 순서를 내림차순으로 사용 합니다.

- 문자열
- 숫자
- boolean
- null

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수치 연산 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB 집계 함수](sql-query-aggregate-functions.md)