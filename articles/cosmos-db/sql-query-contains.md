---
title: Azure Cosmos DB 쿼리 언어에 포함
description: Azure Cosmos DB의 CONTAINS SQL 시스템 함수가 첫 번째 문자열 식에 두 번째 문자열 식이 포함되어 있는지 여부를 나타내는 부울을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339175"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

포함 된의 속성 크기가 일부 문서에 대해 1kb 보다 큰 경우 쿼리 엔진은 해당 문서를 로드 해야 합니다. 이 경우 쿼리 엔진은 인덱스를 사용 하 여 Contains를 완전히 평가할 수 없습니다. 속성 크기가 1kb 이상인 문서 수가 많은 경우 Contains의 추가 요금은 높아집니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
