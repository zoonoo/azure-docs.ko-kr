---
title: Azure 코스모스 DB의 ORDER BY 절
description: Azure Cosmos DB에 대한 SQL ORDER BY 절에 대해 알아봅니다. SQL을 Azure 코스모스 DB JSON 쿼리 언어로 사용합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188741"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure 코스모스 DB의 ORDER BY 절

선택적 ORDER BY 절은 쿼리에서 반환되는 결과에 대한 정렬 순서를 지정합니다.

## <a name="syntax"></a>구문
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>인수
  
- `<sort_specification>`  
  
   쿼리 결과 집합을 정렬할 속성이나 식을 지정합니다. 정렬 열은 이름 또는 속성 별칭으로 지정할 수 있습니다.  
  
   여러 속성을 지정할 수 있습니다. 속성 이름은 고유해야 합니다. ORDER BY 절의 정렬 속성 시퀀스는 정렬된 결과 집합의 구성을 정의합니다. 즉 결과 집합이 첫 번째 속성으로 정렬된 다음 정렬된 해당 목록이 두 번째 속성으로 정렬되는 등등입니다.  
  
   ORDER BY 절에서 참조되는 속성 이름은 선택 목록의 속성 또는 모호함 없이 FROM 절에 지정된 컬렉션에 정의된 속성에 해당해야 합니다.  
  
- `<sort_expression>`  
  
   쿼리 결과 집합을 정렬할 하나 이상의 속성 또는 식을 지정합니다.  
  
- `<scalar_expression>`  
  
   자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  
  
- `ASC | DESC`  
  
   지정된 열의 값이 오름차순으로 정렬되는지 내림차순으로 정렬되는지를 지정합니다. ASC는 오름차순으로 정렬하고, DESC는 내림차순으로 정렬합니다. ASC가 기본 정렬 순서입니다. Null 값은 가능한 가장 작은 값으로 취급됩니다.  
  
## <a name="remarks"></a>설명  
  
   이 `ORDER BY` 절에서는 인덱싱 정책에 정렬중인 필드에 대한 인덱스가 포함되어야 합니다. Azure Cosmos DB 쿼리 런타임은 계산된 속성이 아닌 속성 이름에 대한 정렬을 지원합니다. Azure 코스모스 DB는 여러 `ORDER BY` 속성을 지원합니다. ORDER BY 속성이 여러 개인 쿼리를 실행하려면 정렬중인 필드의 [복합 인덱스를](index-policy.md#composite-indexes) 정의해야 합니다.

> [!Note]
> 정렬중인 속성이 일부 문서에 대해 정의되지 않고 ORDER BY 쿼리에서 검색하려는 경우 인덱스에 이 경로를 명시적으로 포함해야 합니다. 기본 인덱싱 정책은 sort 속성이 정의되지 않은 문서를 검색하는 것을 허용하지 않습니다. [누락된 필드가 있는 문서에 대한 예제 쿼리를 검토합니다.](#documents-with-missing-fields)

## <a name="examples"></a>예

예를 들어, 다음은 거주 도시 이름의 오름차순으로 패밀리를 검색하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

다음 쿼리는 항목 `id`생성 날짜 순으로 패밀리 s를 검색합니다. 항목은 `creationDate` 1970년 1월 1일 이후의 *시간*또는 경과 시간을 초단위로 나타내는 숫자입니다.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

또한 여러 속성으로 주문할 수 있습니다. 여러 속성으로 정렬하는 쿼리에는 [복합 인덱스가](index-policy.md#composite-indexes)필요합니다. 다음과 같은 쿼리를 고려해 보세요.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

이 쿼리는 도시 `id` 이름의 오름차순으로 패밀리를 검색합니다. 여러 항목의 도시 이름이 동일한 경우 쿼리는 `creationDate` 내림차순으로 정렬됩니다.

## <a name="documents-with-missing-fields"></a>필드가 없는 문서

`ORDER BY` 기본 인덱싱 정책이 있는 컨테이너에 대해 실행되는 쿼리는 sort 속성이 정의되지 않은 문서를 반환하지 않습니다. sort 속성이 정의되지 않은 문서를 포함하려면 이 속성을 인덱싱 정책에 명시적으로 포함해야 합니다.

예를 들어 다음과 같은 외에 `"/*"`명시적으로 경로를 포함하지 않는 인덱싱 정책이 있는 컨테이너가 있습니다.

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

절에 포함 `lastName` 되는 쿼리를 실행 하는 경우 속성정의 된 `lastName` 문서만 포함 됩니다. `Order By` a가 없는 `lastName` 문서가 쿼리 `lastName` 결과에 나타나지 않도록 명시적 포함 경로를 정의하지 않았습니다.

다음은 정의된 문서가 없는 `lastName` 두 개의 문서에서 정렬하는 쿼리입니다. `lastName`

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과에는 정의된 `lastName`문서만 포함됩니다.

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

에 대한 `lastName`경로를 명시적으로 포함하도록 컨테이너의 인덱싱 정책을 업데이트하는 경우 쿼리 결과에 정의되지 않은 정렬 속성이 있는 문서가 포함됩니다. 이 스칼라 값을 넘어서는 것이 아니라 이 스칼라 값으로 이어질 경로를 명시적으로 정의해야 합니다. 인덱싱 `?` 정책의 경로 정의에 있는 문자를 사용하여 속성을 명시적으로 인덱싱하고 그 `lastName` 외에 중첩된 경로를 추가하지 않도록 해야 합니다.

다음은 쿼리 결과에 정의되지 `lastName` 않은 문서가 표시되도록 하는 샘플 인덱싱 정책입니다.

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

동일한 쿼리를 다시 실행하면 누락된 `lastName` 문서가 쿼리 결과에 먼저 표시됩니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

정렬 순서를 수정하는 `DESC`경우 에서 `lastName` 누락된 문서가 쿼리 결과에 마지막으로 나타납니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB의 인덱싱 정책](index-policy.md)
- [오프셋 제한 절](sql-query-offset-limit.md)
