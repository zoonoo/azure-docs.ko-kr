---
title: Azure Cosmos DB 쿼리 언어에 포함
description: Azure Cosmos DB의 CONTAINS SQL 시스템 함수가 첫 번째 문자열 식에 두 번째 문자열 식이 포함되어 있는지 여부를 나타내는 부울을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ae5f786fe75d96bd0dc1aa0afab7c5a8965c826
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537979"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

첫 번째 문자열 식이 두 번째를 포함하는지를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   검색할 문자열 식입니다.  
  
*str_expr2*  
   찾을 문자열 식입니다.  

*bool_expr* 대/소문자를 무시하기 위한 선택적 값입니다. true로 설정하면 CONTAINS는 대/소문자를 구분하지 않는 검색을 수행합니다. 지정되지 않은 경우 이 값은 false입니다.
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 "abc"에 "ab"가 포함되어 있고 "abc"에 "A"가 포함되어 있는지 확인합니다.  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>설명

이 [문자열 시스템 함수에서 인덱스를 사용하는 방법](sql-query-string-functions.md)을 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
