---
title: Azure Cosmos DB에서 배열 및 개체 사용
description: Azure Cosmos DB에서 배열 및 개체를 만드는 SQL 구문에 대해 알아봅니다. 또한 이 문서에서는 배열 개체에 대한 작업을 수행하는 몇 가지 예제를 제공합니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: ca62bbb86134e3553c9ebe822096419b8051a872
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529771"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB에서 배열 및 개체 사용
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API의 주요 기능은 배열 및 개체 만들기입니다. 이 문서에서는 [Family 데이터 세트](sql-query-getting-started.md#upload-sample-data)를 사용하여 재현할 수 있는 예제를 사용합니다.

이 데이터 세트의 항목 예제는 다음과 같습니다.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>배열

다음 예제에 표시된 대로 배열을 생성할 수 있습니다.

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

[ARRAY 식](sql-query-subquery.md#array-expression)을 사용하여 [하위 쿼리](sql-query-subquery.md)의 결과에서 배열을 생성할 수도 있습니다. 이 쿼리는 배열에서 자식의 고유한 이름을 모두 가져옵니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>반복

SQL API는 FROM 원본의 [IN 키워드](sql-query-keywords.md#in)를 사용하여 JSON 배열에 대한 반복을 지원합니다. 다음 예제에서는

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

다음 쿼리는 `Families` 컨테이너에서 `children`에 대해 반복을 수행합니다. 출력 배열은 앞의 쿼리와 다릅니다. 이 예제에서는 `children`을 분할하고 결과를 단일 배열로 평면화합니다.  

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

다음 예제에 표시된 대로 배열의 각 개별 항목을 추가로 필터링할 수 있습니다.

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

배열 반복의 결과를 집계할 수도 있습니다. 예를 들어 다음 쿼리는 모든 가족의 자식 수를 계산합니다.

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

결과는 다음과 같습니다.

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> 반복에 IN 키워드를 사용하는 경우 배열 외부의 속성을 필터링하거나 프로젝션할 수 없습니다. 대신 [JOIN](sql-query-join.md)을 사용해야 합니다.

추가 예제는 [Azure Cosmos DB에서 배열 사용에 대한 블로그 게시물](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [조인](sql-query-join.md)
