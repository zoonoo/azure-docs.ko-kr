---
title: Azure Cosmos DB의 StringEquals 쿼리 언어
description: Azure Cosmos DB의 StringEquals SQL 시스템 함수가 첫 번째 문자열 식이 두 번째와의 일치 여부를 나타내는 부울을 반환하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 57d124421082e1c38fab4d982687a8e6c970505e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83853695"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS(Azure Cosmos DB)

 첫 번째 문자열 식이 두 번째 문자열과 일치하는지를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   비교할 첫 번째 문자열 식입니다.  
  
*str_expr2*  
   비교할 두 번째 문자열 식입니다.  

*bool_expr* 대/소문자를 무시하는 선택적 값입니다. true로 설정하면 StringEquals는 대/소문자를 구분하지 않는 검색을 수행합니다. 지정되지 않은 경우 이 값은 false입니다.
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 "abc"가 "abc"와 일치하는지, "abc"가 "ABC"와 일치하는지 확인합니다.  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
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

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 활용합니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
