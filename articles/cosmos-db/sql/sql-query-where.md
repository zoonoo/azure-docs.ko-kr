---
title: Azure Cosmos DB의 WHERE 절
description: Azure Cosmos DB의 SQL WHERE 절에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 67ce23d3ce039ae4a517f633aaeb2c8b4be49b29
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529847"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 WHERE 절
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

선택 사항인 WHERE 절(`WHERE <filter_condition>`)은 결과에 포함하기 위해 원본 JSON 항목이 쿼리를 충족해야 하는 조건(들)을 지정합니다. JSON 항목이 결과에서 고려되려면 지정된 조건이 `true`이여야 합니다. 결과에 포함될 수 있는 소스 항목의 가장 작은 하위 집합을 결정하기 위해 인덱스 계층에서 WHERE 절을 사용합니다.
  
## <a name="syntax"></a>구문
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>인수

- `<filter_condition>`  
  
   반환할 문서에 대해 충족하는 조건을 지정합니다.  
  
- `<scalar_expression>`  
  
   계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md)을 참조하세요.  
  
## <a name="remarks"></a>설명
  
  문서를 반환하려면 필터 조건으로 지정된 식을 true로 평가해야 합니다. `true` 부울 값만 조건을 충족하고, 다른 값(undefined, null, false, 숫자, 배열 또는 개체)은 조건을 충족하지 않습니다.

  일치 필터의 일부로 `WHERE`절에 파티션 키를 포함하는 경우, 쿼리는 관련 파티션에 대해서만 자동으로 필터링합니다.

## <a name="examples"></a>예

다음 쿼리는 값이 `AndersenFamily`인 `id` 속성을 포함하는 항목을 요청합니다. `id` 속성이 없거나 해당 값이 `AndersenFamily`와 일치하지 않는 항목은 제외됩니다.

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

앞의 예제는 단순한 같음 쿼리를 보여 주었습니다. SQL API는 여러 [스칼라 식](sql-query-scalar-expressions.md)도 지원합니다. 가장 일반적으로 사용되는 식은 이항 및 단항 식입니다. 소스 JSON 개체의 속성 참조도 유효한 식입니다.

다음과 같이 지원되는 이항 연산자를 사용할 수 있습니다.  

|**연산자 유형**  | **값** |
|---------|---------|
|산술 | +,-,*,/,% |
|비트    | \|, &, ^, <<, >>, >>>(0 채우기 오른쪽 시프트) |
|논리    | AND, OR, NOT      |
|비교 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\|(연결) |

다음 쿼리는 이항 연산자를 사용합니다.

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

다음 예제와 같이 단항 연산자 +,-, ~를 사용할 수도 있지만 쿼리에는 사용할 수 없습니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

쿼리에서 속성 참조를 사용할 수도 있습니다. 예를 들어, `SELECT * FROM Families f WHERE f.isRegistered`은 `true`과 동일한 값을 가지는 속성 `isRegistered`를 포함하는 JSON 항목을 반환합니다. `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, 또는 `<array>`과 같은 다른 값은 결과에서 해당 항목을 제외합니다. 또한 `IS_DEFINED` 형식 검사 함수를 사용하여 지정된 JSON 속성의 존재 유무를 기준으로 쿼리할 수 있습니다. 예를 들어, `SELECT * FROM Families f WHERE NOT IS_DEFINED(f.isRegistered)`은 `isRegistered`에 대한 값이 없는 모든 JSON 항목을 반환합니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [IN 키워드](sql-query-keywords.md#in)
- [FROM 절](sql-query-from.md)
