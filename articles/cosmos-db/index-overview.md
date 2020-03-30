---
title: Azure Cosmos DB의 인덱싱
description: Azure Cosmos DB에서 인덱싱이 어떻게 작동하는지, 범위, 공간, 복합 인덱스와 같은 다양한 종류의 인덱스가 지원되는지 이해합니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873627"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB의 인덱싱 - 개요

Azure Cosmos DB는 스키마 또는 인덱스 관리를 처리하지 않고도 응용 프로그램을 반복할 수 있는 스키마에 구애받지 않는 데이터베이스입니다. 기본적으로 Azure Cosmos DB는 스키마를 정의하거나 보조 인덱스를 구성할 필요 없이 [컨테이너의](databases-containers-items.md#azure-cosmos-containers) 모든 항목에 대한 모든 속성을 자동으로 인덱싱합니다.

이 문서에서는 Azure Cosmos DB의 데이터 인덱싱 방법과 인덱스를 사용하여 쿼리 성능을 개선하는 방법을 설명합니다. [인덱싱 정책을](index-policy.md)사용자 지정하는 방법을 검토하기 전에 이 섹션을 검토하는 것이 좋습니다.

## <a name="from-items-to-trees"></a>항목에서 나무로

항목이 컨테이너에 저장될 때마다 해당 콘텐츠는 JSON 문서로 투영된 다음 트리 표현으로 변환됩니다. 즉, 해당 항목의 모든 속성이 트리의 노드로 표시됩니다. 의사 루트 노드는 항목의 모든 첫 번째 수준 속성에 대한 부모로 만들어집니다. 리프 노드에는 항목이 수행하는 실제 스칼라 값이 포함됩니다.

예를 들어 다음 항목을 고려하십시오.

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

다음 트리로 표시됩니다.

![트리로 표시된 이전 항목](./media/index-overview/item-as-tree.png)

트리에서 배열이 인코딩되는 방법: 배열의 모든 항목은 배열 내의 해당 항목의 인덱스로 레이블이 지정된 중간 노드를 가져옵니다(0, 1 등).

## <a name="from-trees-to-property-paths"></a>나무에서 속성 경로로

Azure Cosmos DB가 항목을 트리로 변환하는 이유는 해당 트리 내의 경로에서 속성을 참조할 수 있기 때문입니다. 속성에 대한 경로를 얻으려면 루트 노드에서 해당 속성으로 트리를 통과하고 각 트래버스 노드의 레이블을 연결할 수 있습니다.

다음은 위에서 설명한 예제 항목의 각 속성에 대한 경로입니다.

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

항목이 작성되면 Azure Cosmos DB는 각 속성의 경로와 해당 값을 효과적으로 인덱싱합니다.

## <a name="index-kinds"></a>인덱스 종류

Azure 코스모스 DB는 현재 세 가지 종류의 인덱스를 지원합니다.

### <a name="range-index"></a>범위 인덱스

**범위** 인덱스는 정렬된 트리와 같은 구조를 기반으로 합니다. 범위 인덱스 종류는 다음에 사용됩니다.

- 같음 쿼리:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   배열 요소에서 같음 일치
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- 범위 쿼리:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (에 `>`대해 `<` `>=`작동합니다 `!=`. " , `<=`

- 속성의 존재 여부를 확인합니다.

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 문자열 접두사 일치(CONTAINS 키워드는 범위 인덱스를 활용하지 않음)입니다.

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`쿼리:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`쿼리:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

범위 인덱스는 스칼라 값(문자열 또는 숫자)에 사용할 수 있습니다.

### <a name="spatial-index"></a>공간 인덱스

**공간** 인덱스를 사용하면 점, 선, 다각형 및 다중다각곤과 같은 지리 공간 개체에 대한 효율적인 쿼리를 사용할 수 있습니다. 이러한 쿼리는 ST_DISTANCE ST_WITHIN ST_INTERSECTS 키워드를 사용합니다. 다음은 공간 인덱스 종류를 사용하는 몇 가지 예입니다.

- 지리 공간 거리 쿼리:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 쿼리 내의 지리 공간:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- 지리 공간 교차 쿼리:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

공간 인덱스는 올바르게 포맷된 [GeoJSON](geospatial.md) 오브젝트에 사용할 수 있습니다. 점, 선스트링, 다각형 및 다중Polygons는 현재 지원됩니다.

### <a name="composite-indexes"></a>복합 인덱스

**복합** 인덱스는 여러 필드에서 작업을 수행할 때 효율성을 높입니다. 복합 인덱스 종류는 다음에 사용됩니다.

- `ORDER BY`여러 속성에 대한 쿼리:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 필터 및 `ORDER BY`. 이러한 쿼리는 필터 속성이 절에 추가된 `ORDER BY` 경우 복합 인덱스를 사용할 수 있습니다.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 둘 이상의 속성에 필터가 있는 쿼리가 하나 이상의 속성이 같음 필터인 경우

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

하나의 필터 조건자 인덱스 종류에 사용 하는 한 쿼리 엔진은 나머지를 스캔 하기 전에 먼저 평가 합니다. 예를 들어 다음과 같은 SQL 쿼리가 있는 경우`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* 위의 쿼리는 먼저 인덱스를 사용하여 name = "Andrew"가 있는 항목을 필터링합니다. 그런 다음 후속 파이프라인을 통해 모든 이름 = "Andrew" 항목을 전달하여 CONTAINS 필터 조건자(CONTAINS 필터 조건자)를 평가합니다.

* 인덱스를 사용하지 않는 함수(예: CONTAINS)를 사용하는 경우 인덱스를 사용하는 필터 조건자 추가를 추가하여 쿼리 속도를 높이고 전체 컨테이너 검사를 피할 수 있습니다. 필터 절의 순서는 중요하지 않습니다. 쿼리 엔진은 어떤 술어가 더 선택적인지 파악하고 그에 따라 쿼리를 실행합니다.


## <a name="querying-with-indexes"></a>인덱스를 사용한 쿼리

데이터를 인덱싱할 때 추출된 경로를 사용하면 쿼리를 처리할 때 인덱스를 쉽게 조회할 수 있습니다. 쿼리 절을 `WHERE` 인덱싱된 경로 목록과 일치시키면 쿼리 조건자와 일치하는 항목을 매우 빠르게 식별할 수 있습니다.

예를 들어 다음 쿼리를 `SELECT location FROM location IN company.locations WHERE location.country = 'France'`고려합니다. 쿼리 조건자(해당 국가로 "프랑스"가 있는 항목에 대한 필터링)는 아래 빨간색으로 강조 표시된 경로와 일치합니다.

![트리 내의 특정 경로 일치](./media/index-overview/matching-path.png)

> [!NOTE]
> 단일 `ORDER BY` 속성으로 정렬하는 절은 *항상* 범위 인덱스가 필요하며 참조하는 경로에 인덱스가 없는 경우 실패합니다. 마찬가지로 여러 `ORDER BY` 속성별로 정렬하는 쿼리에는 *항상* 복합 인덱스가 필요합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 정책](index-policy.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
