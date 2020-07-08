---
title: Azure Cosmos DB의 ORDER BY 절
description: Azure Cosmos DB에 대 한 SQL ORDER BY 절에 대해 알아봅니다. Azure Cosmos DB JSON 쿼리 언어로 SQL을 사용 합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: c4ae66884602989284a427bdc33de7612bd9a8df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84484331"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 ORDER BY 절

선택적 `ORDER BY` 절은 쿼리에서 반환 되는 결과에 대 한 정렬 순서를 지정 합니다.

## <a name="syntax"></a>구문
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>인수
  
- `<sort_specification>`  
  
   쿼리 결과 집합을 정렬할 속성이나 식을 지정합니다. 정렬 열은 이름이 나 속성 별칭으로 지정할 수 있습니다.  
  
   여러 속성을 지정할 수 있습니다. 속성 이름은 고유 해야 합니다. 절에서 정렬 속성의 순서는 `ORDER BY` 정렬 된 결과 집합의 구성을 정의 합니다. 즉 결과 집합이 첫 번째 속성으로 정렬된 다음 정렬된 해당 목록이 두 번째 속성으로 정렬되는 등등입니다.  
  
   절에서 참조 되는 속성 이름은 `ORDER BY` select 목록의 속성 또는 해당 절에 지정 된 컬렉션에 정의 된 속성 (모호성 없이)과 일치 해야 합니다 `FROM` .  
  
- `<sort_expression>`  
  
   쿼리 결과 집합을 정렬할 하나 이상의 속성이 나 식을 지정 합니다.  
  
- `<scalar_expression>`  
  
   자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  
  
- `ASC | DESC`  
  
   지정된 열의 값이 오름차순으로 정렬되는지 내림차순으로 정렬되는지를 지정합니다. `ASC`가장 낮은 값에서 가장 높은 값 순으로 정렬 합니다. `DESC`가장 큰 값에서 가장 작은 값으로 정렬 합니다. `ASC`는 기본 정렬 순서입니다. Null 값은 가능한 가장 작은 값으로 취급됩니다.  
  
## <a name="remarks"></a>설명  
  
   `ORDER BY`절을 사용 하려면 정렬 중인 필드에 대 한 인덱스를 인덱싱 정책에 포함 해야 합니다. Azure Cosmos DB 쿼리 런타임은 계산 된 속성이 아닌 속성 이름에 대 한 정렬을 지원 합니다. Azure Cosmos DB는 여러 `ORDER BY` 속성을 지원 합니다. 여러 ORDER BY 속성을 사용 하 여 쿼리를 실행 하려면 정렬할 필드에 [복합 인덱스](index-policy.md#composite-indexes) 를 정의 해야 합니다.

> [!Note]
> 정렬 되는 속성이 일부 문서에 대해 정의 되지 않은 경우이를 ORDER BY 쿼리로 검색 하려면이 경로를 인덱스에 명시적으로 포함 해야 합니다. 기본 인덱싱 정책은 sort 속성이 정의 되지 않은 문서를 검색 하는 것을 허용 하지 않습니다. [일부 누락 된 필드가 있는 문서에 대 한 예제 쿼리를 검토](#documents-with-missing-fields)합니다.

## <a name="examples"></a>예

예를 들어 다음은 주 도시 이름의 오름차순으로 패밀리를 검색 하는 쿼리입니다.

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

다음 쿼리는 항목을 `id` 만든 날짜 순으로 가족을 검색 합니다. 항목 `creationDate` 은 *epoch 시간*또는 경과 된 시간 (1 월 1 일 이후 1970 초)을 나타내는 숫자입니다.

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

또한 여러 속성을 기준으로 순서를 지정할 수 있습니다. 여러 속성으로 정렬 하는 쿼리에는 [복합 인덱스가](index-policy.md#composite-indexes)필요 합니다. 다음 쿼리를 고려해 보세요.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

이 쿼리는 `id` 도시 이름의 오름차순으로 패밀리를 검색 합니다. 여러 항목의 도시 이름이 동일한 경우 쿼리는를 기준으로 내림차순으로 정렬 됩니다 `creationDate` .

## <a name="documents-with-missing-fields"></a>누락 된 필드가 있는 문서

`ORDER BY`기본 인덱싱 정책을 사용 하 여 컨테이너에 대해 실행 되는 쿼리는 sort 속성이 정의 되지 않은 문서를 반환 하지 않습니다. Sort 속성이 정의 되어 있지 않은 문서를 포함 하려는 경우에는 인덱싱 정책에이 속성을 명시적으로 포함 해야 합니다.

예를 들어 다음은 이외의 경로를 명시적으로 포함 하지 않는 인덱싱 정책이 있는 컨테이너입니다 `"/*"` .

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

절에를 포함 하는 쿼리를 실행 하는 경우 `lastName` `Order By` 속성을 정의 하는 문서만 결과에 포함 됩니다 `lastName` . 에 대 한 명시적 포함 경로를 정의 하지 않았으므로가 `lastName` 없는 모든 문서가 `lastName` 쿼리 결과에 표시 되지 않습니다.

다음은 두 문서를 기준으로 정렬 하는 쿼리로 `lastName` , 하나는 정의 되어 있지 않습니다 `lastName` .

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과는 다음과 같이 정의 된 문서만 포함 합니다 `lastName` .

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

에 대 한 경로를 명시적으로 포함 하도록 컨테이너의 인덱싱 정책을 업데이트 하는 경우 `lastName` 쿼리 결과에 정의 되지 않은 sort 속성이 있는 문서를 포함 합니다. 경로를 명시적으로 정의 하 여이 스칼라 값 (이외에는)을 지정 해야 합니다. `?`인덱싱 정책의 경로 정의에 있는 문자를 사용 하 여 속성을 명시적으로 인덱싱해야 하 `lastName` 고 그 밖의 중첩 된 경로를 추가로 사용 하지 않도록 해야 합니다. `Order By`쿼리에서 [복합 인덱스](index-policy.md#composite-indexes)를 사용 하는 경우 결과는 항상 쿼리 결과에 정의 되지 않은 sort 속성이 있는 문서를 포함 합니다.

다음은 정의 되지 않은 문서를 `lastName` 쿼리 결과에 표시 하는 데 사용할 수 있는 샘플 인덱싱 정책입니다.

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

동일한 쿼리를 다시 실행 하는 경우 누락 된 문서는 `lastName` 먼저 쿼리 결과에 표시 됩니다.

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

정렬 순서를로 수정 하는 경우 `DESC` 누락 된 문서는 `lastName` 쿼리 결과의 마지막에 표시 됩니다.

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

> [!Note]
> .NET SDK 버전 3.4.0 이상 에서만 혼합 형식의 ORDER BY를 지원 합니다. 따라서 정의 되지 않은 값 및 정의 된 값을 조합 하 여 정렬 하려면이 버전 이상을 사용 해야 합니다.

결과에 다른 형식이 표시 되는 순서를 제어할 수 없습니다. 위의 예제에서는 정의 되지 않은 값이 문자열 값 보다 먼저 정렬 된 방법을 보여 주었습니다. 예를 들어, 정의 되지 않은 값의 정렬 순서를 보다 세밀 하 게 제어 하려는 경우에는 정의 되지 않은 속성에 "aaaaaaaaa" 또는 "zzzzzzzz" 문자열 값을 할당 하 여 첫 번째 또는 마지막 인지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB의 인덱싱 정책](index-policy.md)
- [OFFSET LIMIT 절](sql-query-offset-limit.md)
