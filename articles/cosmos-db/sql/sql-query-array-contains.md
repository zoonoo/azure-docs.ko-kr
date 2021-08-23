---
title: Azure Cosmos DB 쿼리 언어의 ARRAY_CONTAINS
description: Azure Cosmos DB의 Array Contains SQL 시스템 함수가 배열에 지정된 값이 포함되어 있는지 여부를 나타내는 부울을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 027d4b5a846f95bc16ae0592790ebfd4492d0de5
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537850"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

지정된 값이 배열에 포함되는지를 나타내는 부울 값을 반환합니다. 명령 내에서 부울 식을 사용하여 개체의 부분 또는 전체 일치를 확인할 수 있습니다. 

## <a name="syntax"></a>구문
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   검색할 배열 식입니다.  
  
*expr*  
   찾을 식입니다.  

*bool_expr*  
   부울 식입니다. 'true'로 평가되고 지정된 검색 값이 개체인 경우 명령은 부분 일치(검색 개체가 개체 중 하나의 하위 집합임)를 확인합니다. 'false'로 평가되면 명령은 배열 내의 모든 개체에 대해 전체 일치를 확인합니다. 지정하지 않으면 기본값 false입니다. 
  
## <a name="return-types"></a>반환 형식
  
  부울 값을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `ARRAY_CONTAINS`을 사용하여 배열의 멤버 자격을 확인하는 방법을 보여 줍니다.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"b1": true, "b2": false}]  
```  

다음 예제에서는 ARRAY_CONTAINS를 사용하여 JSON의 부분 일치를 확인하는 방법을 보여 줍니다.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [배열 함수 Azure Cosmos DB](sql-query-array-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
