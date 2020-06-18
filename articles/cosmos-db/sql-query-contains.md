---
title: Azure Cosmos DB 쿼리 언어에 포함
description: Azure Cosmos DB의 CONTAINS SQL 시스템 함수가 첫 번째 문자열 식에 두 번째 문자열 식이 포함되어 있는지 여부를 나타내는 부울을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a08fe47122d7e9ddd1c9038bb5f15ebbb0be30fa
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848977"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS(Azure Cosmos DB)

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

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다.

시스템 함수 내 속성의 카디널리티가 증가함에 따라 Contains의 RU 소비량이 증가합니다. 즉, 속성 값에 특정 문자열이 포함되어 있는지 여부를 확인하는 경우 쿼리 RU 요금은 해당 속성에 사용할 수 있는 값의 수에 따라 달라집니다.

예를 들어, 도시와 국가라는 두 가지 속성을 고려합니다. 도시의 카디널리티는 5,000이고 국가의 카디널리티는 200입니다. 다음은 두 가지 예제 쿼리입니다.

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

도시의 카디널리티가 국가보다 높기 때문에 첫 번째 쿼리가 두 번째 쿼리보다 더 많은 RU를 사용할 것입니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
