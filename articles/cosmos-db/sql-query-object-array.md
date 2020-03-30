---
title: Azure Cosmos DB의 배열 및 개체 작업
description: Azure Cosmos DB에서 배열 및 개체를 만드는 SQL 구문을 알아봅니다. 이 문서에서는 배열 개체에 대한 작업을 수행하는 몇 가지 예도 제공합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246553"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB의 배열 및 개체 작업

Azure Cosmos DB SQL API의 주요 기능은 배열 및 개체 생성입니다.

## <a name="arrays"></a>배열

다음 예제와 같이 배열을 생성할 수 있습니다.

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

[ARRAY 식을](sql-query-subquery.md#array-expression) 사용하여 하위 쿼리 결과에서 [배열을](sql-query-subquery.md) 생성할 수도 있습니다. 이 쿼리는 배열에서 자식의 모든 고유한 지정된 이름을 가져옵니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>반복

SQL API는 FROM 소스의 [IN 키워드를](sql-query-keywords.md#in) 통해 추가된 새 구문과 함께 JSON 배열을 반복하는 지원을 제공합니다. 다음 예제에서,

```sql
    SELECT *
    FROM Families.children
```

결과는 다음과 같습니다.

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

다음 쿼리는 컨테이너에서 `children` `Families` 반복을 수행합니다. 출력 배열은 앞의 쿼리와 다릅니다. 이 예제에서는 `children`결과를 분할하고 단일 배열로 병합합니다.  

```sql
    SELECT *
    FROM c IN Families.children
```

결과는 다음과 같습니다.

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

다음 예제와 같이 배열의 각 개별 항목에 대해 더 필터링할 수 있습니다.

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

결과는 다음과 같습니다.

```json
    [{
      "givenName": "Lisa"
    }]
```

배열 반복의 결과에 대해 집계할 수도 있습니다. 예를 들어 다음 쿼리는 모든 패밀리 중자 수를 계산합니다.

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [조인](sql-query-join.md)