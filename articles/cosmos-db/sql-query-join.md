---
title: Azure 코스모스 DB에 대한 SQL JOIN 쿼리
description: Azure Cosmos DB에서 여러 테이블을 조인하여 데이터를 쿼리하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871145"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure 코스모스 DB에 합류

관계형 데이터베이스에서 테이블 간 조인은 정규화된 스키마를 디자인하는 논리적 결과입니다. 반대로 SQL API는 *자체 조인과*논리적으로 동등한 스키마없는 항목의 비정규화된 데이터 모델을 사용합니다.

내부 조인은 조인에 참여하는 집합의 완전한 교차곱을 만듭니다. N 방향 조인의 결과는 N 요소 튜플의 집합이며, 여기서 튜플의 각 값은 조인에 참여하는 별칭 지정된 집합과 연결되며 다른 절에서 해당 별칭을 참조하여 액세스할 수 있습니다.

## <a name="syntax"></a>구문

언어는 구문을 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`지원합니다. 이 쿼리는 값이 있는 `N` tuples 집합을 반환합니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다. 

`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` FROM 절을 살펴보겠습니다.  
  
 각 원본에서 `input_alias1, input_alias2, …, input_aliasN`을 정의하도록 합니다. 이 FROM 절은 N 튜플(N개 값이 포함된 튜플)의 집합을 반환합니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다.  
  
**예제 1** - 2개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- input_alias1을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `<from_source1> JOIN <from_source2>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**예제 2** - 3개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- `input_alias1`을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `input_alias2`을 참조하는 문서 범위로 `<from_source3>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias2 = 1,`의 경우 {100, 200}  
  
    `input_alias2 = 3,`의 경우 {300}  
  
- `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > `input_alias1`, `input_alias2`의 다른 값에 대한 튜플이 없으므로 `<from_source3>`에서 값을 반환하지 않았습니다.  
  
**예제 3** - 3개 원본  
  
- 컨테이너 범위로 <from_source1>을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- input_alias1을 참조하는 문서 범위로 <from_source2>를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `<from_source3>`을 `input_alias1`로 범위 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias2 = A,`의 경우 {100, 200}  
  
    `input_alias2 = C,`의 경우 {300}  
  
- `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > 이렇게 하면 `<from_source2>`와 `<from_source3>`의 범위를 모두 동일한 `<from_source1>`로 지정했기 때문에 두 원본의 교차곱을 만들고,  이로 인해 A 값이 있는 4개(2x2) 튜플, B 값이 있는 0개(1x0) 튜플 및 C 값이 있는 2개(2x1) 튜플을 만들었습니다.  
  
## <a name="examples"></a>예

다음 예제는 JOIN 절의 작동 방식을 보여 줍니다. 이러한 예제를 실행하기 전에 샘플 [패밀리 데이터를](sql-query-getting-started.md#upload-sample-data)업로드합니다. 다음 예제에서는 소스및 빈 집합에서 각 항목의 교차 제품이 비어 있기 때문에 결과가 비어 있습니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

결과는 다음과 같습니다.

```json
    [{
    }]
```

다음 예제에서 조인은 항목 루트와 `id` 하위 루트라는 두 JSON 개체 간의 교차 곱입니다. `children` 배열이라는 `children` 사실은 `children` 배열인 단일 루트를 다루기 때문에 조인에서 효과적이지 않습니다. 배열이 있는 각 항목의 교차 곱이 정확히 하나의 항목만 생성하기 때문에 결과에는 두 개의 결과만 포함됩니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

다음 예제에서는 보다 기본적인 조인을 보여 줍니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 절의 FROM 소스는 이터레이터입니다. 따라서 앞의 예제의 흐름은 다음과 같은 것입니다.  

1. 배열의 각 `c` 자식 요소를 확장합니다.
2. 첫 번째 단계가 평평해진 각 `f` 자식 `c` 요소와 함께 항목의 루트가 있는 교차 제품을 적용합니다.
3. 마지막으로 루트 개체 `f` `id` 속성만 투영합니다.

첫 번째 `AndersenFamily`항목, 의 `children` 은 하나의 요소만 포함하므로 결과 집합에는 단일 개체만 포함됩니다. 두 번째 `WakefieldFamily`항목인 `children`" 에 두 개의 이 가 포함되어 `children` 있으므로 교차 제품은 각 요소에 대해 하나씩 두 개의 개체를 생성합니다. 교차곱에서 예상한 대로 두 항목의 루트 필드는 동일합니다.

JOIN 절의 실제 유용성은 투영하기 어려운 모양으로 교차 제품에서 투플을 형성하는 것입니다. 아래 예제는 튜플의 조합에 대한 필터로, 사용자는 튜플 전체에 만족하는 조건을 선택할 수 있습니다.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

결과는 다음과 같습니다.

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

다음 예제의 다음 확장은 이중 조인을 수행합니다. 교차 제품을 다음과 같은 의사 코드로 볼 수 있습니다.

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`한 명의 애완동물을 가진 아이가 있으므로, 크로스 제품은\*\*이 가족으로부터 한 행(1 1 1)을 산출합니다. `WakefieldFamily`두 명의 자녀가 있는데, 그 중 한 명만 애완동물을 가지고 있지만, 그 아이는 두 마리의 애완동물을 가지고 있습니다. 이 제품군의 교차 제품은\*\*1 1 1 2 = 2 행을 생성합니다.

다음 예제에서는 애완 동물 이름이 `pet`없는 `Shadow`모든 tuples를 제외 하는 추가 필터가 있습니다. 배열에서 튜플을 빌드하고 튜플의 요소를 필터링하고 요소의 조합을 투영할 수 있습니다.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

결과는 다음과 같습니다.

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [하위 쿼리](sql-query-subquery.md)