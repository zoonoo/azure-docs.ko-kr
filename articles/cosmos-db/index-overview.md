---
title: Azure Cosmos DB의 인덱싱
description: 인덱싱이 Azure Cosmos DB에서 작동하는 방식과, Range, Spatial, 지원되는 복합 인덱스 등, 다양한 유형의 인덱스에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: 4211f13324b9fda0b0823b2d035eb03863cb686d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339759"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB의 인덱싱 - 개요
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB는 스키마에 구애되지 않는 데이터베이스로, 스키마나 인덱스 관리를 처리하지 않고도 애플리케이션을 반복할 수 있습니다. 기본적으로 Azure Cosmos DB는 스키마 정의나 보조 인덱스 구성 없이도 [컨테이너](account-databases-containers-items.md#azure-cosmos-containers)의 전체 항목에 대해 모든 자산을 자동으로 인덱싱합니다.

이 문서에서는 Azure Cosmos DB의 데이터 인덱싱 방법과 인덱스를 사용하여 쿼리 성능을 개선하는 방법을 설명합니다. [인덱싱 정책](index-policy.md)을 사용자 지정하는 방법을 살펴보기 전에 이 섹션을 진행하는 것이 좋습니다.

## <a name="from-items-to-trees"></a>항목에서 트리로

항목이 컨테이너에 저장될 때마다 해당 콘텐츠는 JSON 문서에 프로젝션된 다음, 트리 표현으로 변환됩니다. 즉, 해당 항목의 모든 속성이 트리의 노드로 표시됩니다. 의사 루트 노드는 항목의 모든 1단계 속성을 부모로 하여 만들어집니다. 리프 노드에는 항목에서 전달하는 실제 스칼라 값이 포함됩니다.

다음 항목을 예로 들어 보겠습니다.

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

다음과 같은 트리로 표시됩니다.

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="트리로 표시된 이전 항목" border="false":::

배열이 트리에 인코딩되는 방법을 살펴보면, 배열의 모든 항목이 배열 내 해당 항목의 인덱스로 레이블이 지정된 중간 노드를 갖습니다(0, 1 등).

## <a name="from-trees-to-property-paths"></a>트리에서 속성 경로

Azure Cosmos DB가 항목을 트리로 변환하는 이유는 트리 내 경로에서 속성을 참조하기 위해서입니다. 속성의 경로를 가져오기 위해 루트 노드에서 해당 속성까지 트리를 트래버스하고 트래버스한 각 노드의 레이블을 연결할 수 있습니다.

위에서 설명한 예제 항목의 각 속성에 대한 경로는 다음과 같습니다.

- /locations/0/country: "독일"
- /locations/0/city: "베를린"
- /locations/1/country: "프랑스"
- /locations/1/city: "파리"
- /headquarters/country: "벨기에"
- /headquarters/employees: 250
- /exports/0/city: "모스크바"
- /exports/1/city: "아테네"

항목을 기록할 때 Azure Cosmos DB는 각 속성의 경로와 해당하는 값을 효과적으로 인덱싱합니다.

## <a name="index-kinds"></a>인덱스 종류

Azure Cosmos DB는 현재 3가지 인덱스 종류를 지원합니다.

### <a name="range-index"></a>범위 인덱스

**범위** 인덱스는 순서가 있는 트리형 구조를 기반으로 합니다. 범위 인덱스의 용도는 다음과 같습니다.

- 등호 쿼리:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   배열 요소에서 등호 일치
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- 범위 쿼리:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (`>`, `<`, `>=`, `<=`, `!=`에 적용)

- 속성이 있는지 확인:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 문자열 시스템 함수:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` 쿼리:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` 쿼리:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

범위 인덱스는 스칼라 값(문자열 또는 숫자)에 사용할 수 있습니다.

### <a name="spatial-index"></a>공간 인덱스

**공간** 인덱스는 점, 선, 다각형 및 다중 다각형 등의 지리 공간적 개체에 대한 효율적 쿼리를 구현합니다. 이러한 쿼리는 T_DISTANCE, ST_WITHIN, ST_INTERSECTS 키워드를 사용합니다. 공간 인덱스 종류를 사용하는 몇 가지 예는 다음과 같습니다.

- 지리 공간적 거리 쿼리:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 쿼리 내 지리 공간:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- 지리 공간적 교차 쿼리:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

공간 인덱스는 올바른 형식의 [GeoJSON](./sql-query-geospatial-intro.md) 개체에 사용할 수 있습니다. 현재 점, 선 문자열, 다각형 및 다중 다각형이 지원됩니다.

### <a name="composite-indexes"></a>복합 인덱스

**복합** 인덱스는 여러 필드에 대해 작업을 수행할 때 효율성을 높입니다. 복합 인덱스의 용도는 다음과 같습니다.

- 여러 속성에 대한 `ORDER BY` 쿼리:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 필터 및 `ORDER BY`를 사용하여 쿼리합니다. 이러한 쿼리는 필터 속성이 `ORDER BY` 절에 추가된 경우 복합 인덱스를 활용할 수 있습니다.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 하나 이상의 속성이 등호 필터인 둘 이상의 속성에 대해 필터를 사용하여 쿼리

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

한 필터 조건자가 인덱스 종류 중 하나를 사용한다면 쿼리 엔진이 나머지를 검사하기 전에 이 항목을 먼저 평가합니다. `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")` 같은 SQL 쿼리가 있는 경우를 예로 들어 보겠습니다.

* 위의 쿼리는 인덱스를 사용하여 firstName = "Andrew"인 항목에 대해 먼저 필터링합니다. 그런 다음, 모든 firstName = "Andrew" 항목을 후속 파이프라인을 통해 전달하여 CONTAINS 필터 조건자를 평가합니다.

* 인덱스를 사용하지 않는 함수(예: CONTAINS)를 사용할 때 인덱스를 사용하는 다른 필터 조건자를 추가하여 쿼리 속도를 높이고 전체 컨테이너 검사를 방지할 수 있습니다. 필터 절의 순서는 중요하지 않습니다. 쿼리 엔진이 더 선택적인 조건자를 파악하고 그에 따라 쿼리를 실행합니다.

## <a name="querying-with-indexes"></a>인덱스를 사용한 쿼리

데이터를 인덱싱하여 추출된 경로를 사용하면 쿼리를 처리할 때 인덱스를 쉽게 조화할 수 있습니다. 쿼리의 `WHERE` 절을 인덱싱된 경로 목록과 일치시켜 쿼리 조건자와 일치하는 항목을 매우 빠르게 식별할 수 있습니다.

`SELECT location FROM location IN company.locations WHERE location.country = 'France'` 쿼리를 예로 들어 보겠습니다. 쿼리 조건자(항목에 대한 필터링, 해당 국가/지역이 "프랑스"인 모든 위치)는 아래 빨간색으로 강조 표시된 경로와 일치합니다.

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="트리 내의 특정 경로 일치" border="false":::

> [!NOTE]
> 단일 속성을 기준으로 정렬하는 `ORDER BY` 절에는 *항상* 범위 인덱스가 필요하며 참조하는 경로에 이 항목이 없으면 실패합니다. 마찬가지로, 여러 속성을 기준으로 정렬하는 `ORDER BY` 쿼리는 *항상* 복합 인덱스가 필요합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 정책](index-policy.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)