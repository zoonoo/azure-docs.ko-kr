---
title: Azure 코스모스 DB의 WHERE 절
description: Azure 코스모스 DB에 대한 SQL WHERE 절에 대해 알아보기
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898768"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure 코스모스 DB의 WHERE 절

선택적 WHERE 절`WHERE <filter_condition>`() 은 소스 JSON 항목이 쿼리가 결과에 포함되도록 충족해야 하는 조건을 지정합니다. JSON 항목은 결과에 대해 고려할 `true` 지정된 조건을 평가해야 합니다. 인덱스 계층은 WHERE 절을 사용하여 결과의 일부가 될 수 있는 소스 항목의 가장 작은 하위 집합을 결정합니다.
  
## <a name="syntax"></a>구문
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>인수

- `<filter_condition>`  
  
   반환할 문서에 대해 충족하는 조건을 지정합니다.  
  
- `<scalar_expression>`  
  
   계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식을](sql-query-scalar-expressions.md) 참조하십시오.  
  
## <a name="remarks"></a>설명
  
  문서를 반환하려면 필터 조건으로 지정된 식을 true로 평가해야 합니다. 부울 값만 `true` 조건을 만족하며, 정의되지 않은 값, null, false, 번호, 배열 또는 Object는 조건을 충족하지 않습니다.

  같음 필터의 일부로 `WHERE` 절에 파티션 키를 포함하면 쿼리가 관련 파티션으로만 자동으로 필터링됩니다.

## <a name="examples"></a>예

다음 쿼리는 값이 있는 `id` 속성을 포함하는 `AndersenFamily`항목을 요청합니다. `id` 속성이 없거나 값이 일치하지 `AndersenFamily`않는 항목은 제외됩니다.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 절의 스칼라 식

앞의 예제는 단순한 같음 쿼리를 보여 주었습니다. SQL API는 다양한 [스칼라 식도](sql-query-scalar-expressions.md)지원합니다. 가장 일반적으로 사용되는 식은 이항 및 단항 식입니다. 소스 JSON 개체의 속성 참조도 유효한 식입니다.

다음과 같은 지원되는 이진 연산을 사용할 수 있습니다.  

|**연산자 유형**  | **값** |
|---------|---------|
|산술 | +,-,*,/,% |
|비트 단위    | \|, &, ^, <<, >>, >>>(0 채우기 오른쪽 시프트) |
|논리    | AND, OR, NOT      |
|비교 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\|(연결) |

다음 쿼리는 이진 연산을 사용합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

다음 예제와 같이 쿼리에서 unary 연산자 +,-, ~및 not not를 사용할 수도 있습니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

쿼리에서 속성 참조를 사용할 수도 있습니다. 예를 들어 `SELECT * FROM Families f WHERE f.isRegistered` 에 해당하는 값으로 속성을 `isRegistered` 포함하는 JSON 항목을 반환합니다. `true` 의 `false`다른 값(예: `Undefined` `<number>`" `<string>` `null` `<object>`. `<array>`" 및 ) 은 에서 항목을 제외합니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [IN 키워드](sql-query-keywords.md#in)
- [FROM 절](sql-query-from.md)