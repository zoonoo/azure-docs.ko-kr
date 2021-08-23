---
title: Azure Cosmos DB 쿼리 언어의 CEILING
description: Azure Cosmos DB의 CEILING SQL 시스템 함수에서 지정된 숫자 식보다 크거나 같은 최소 정수 값을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d89f6126cf42a0ba4e249e70c6bc00e1a2e18d2f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529719"
---
# <a name="ceiling-azure-cosmos-db"></a>CEILING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정한 숫자 식보다 크거나 같은 가장 작은 정수 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `CEILING` 함수를 사용하여 양수, 음수, 0 값을 표시합니다.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
