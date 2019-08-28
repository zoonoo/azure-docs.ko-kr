---
title: Azure Cosmos DB의 인덱싱
description: Azure Cosmos DB에서 인덱싱의 작동 방식을 파악하고
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: thweiss
ms.openlocfilehash: c8e21ea89f3e23709d636ab8af4716bff76d7217
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479280"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 인덱싱-개요

Azure Cosmos DB은 스키마를 포함 하지 않는 데이터베이스 이며 스키마 또는 인덱스 관리를 처리할 필요 없이 응용 프로그램에서 반복할 수 있습니다. 기본적으로 Azure Cosmos DB는 스키마를 정의 하거나 보조 인덱스를 구성 하지 않고도 [컨테이너](databases-containers-items.md#azure-cosmos-containers) 의 모든 항목에 대 한 모든 속성을 자동으로 인덱싱합니다.

이 문서의 목표는 Azure Cosmos DB 데이터를 인덱싱하는 방법과 인덱스를 사용 하 여 쿼리 성능을 개선 하는 방법을 설명 하는 것입니다. [인덱싱 정책을](index-policy.md)사용자 지정 하는 방법을 살펴보기 전에이 섹션을 진행 하는 것이 좋습니다.

## <a name="from-items-to-trees"></a>항목에서 트리로

항목이 컨테이너에 저장 될 때마다 해당 콘텐츠는 JSON 문서로 프로젝션 된 후 트리 표현으로 변환 됩니다. 즉, 해당 항목의 모든 속성은 트리의 노드로 표시 됩니다. 의사 루트 노드는 항목의 모든 첫 번째 수준 속성에 대 한 부모로 만들어집니다. 리프 노드에는 항목에서 전달 하는 실제 스칼라 값이 포함 됩니다.

예를 들어 다음 항목을 살펴보세요.

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

다음 트리로 표시 됩니다.

![트리로 표시 된 이전 항목입니다.](./media/index-overview/item-as-tree.png)

트리에서 배열을 인코딩하는 방법에 유의 하세요. 배열의 모든 항목은 배열 내에서 해당 항목의 인덱스 (0, 1 등)로 레이블이 지정 된 중간 노드를 가져옵니다.

## <a name="from-trees-to-property-paths"></a>트리에서 속성 경로로

Azure Cosmos DB 항목을 트리로 변환 하는 이유는 해당 트리 내에서 속성을 참조할 수 있기 때문입니다. 속성의 경로를 가져오기 위해 루트 노드에서 해당 속성에 대 한 트리를 트래버스 하 고 각 트래버스 노드의 레이블을 연결할 수 있습니다.

위에서 설명한 예제 항목의 각 속성에 대 한 경로는 다음과 같습니다.

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

항목을 쓸 때 Azure Cosmos DB는 각 속성의 경로와 해당 값을 효과적으로 인덱싱합니다.

## <a name="index-kinds"></a>인덱스 종류

현재 Azure Cosmos DB는 다음과 같은 세 가지 종류의 인덱스를 지원 합니다.

**범위** 인덱스 종류는 다음에 사용 됩니다.

- 같음 쿼리:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
    ```

- 범위 쿼리:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ``` 
  ( `>` ,`<`, ,`!=`,)에 대해 작동 합니다. `<=` `>=`

- `ORDER BY`쿼리

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`쿼리

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

범위 인덱스는 스칼라 값 (문자열 또는 숫자)에 사용할 수 있습니다.

**공간** 인덱스 종류는 다음에 사용 됩니다.

- 지리 공간적 거리 쿼리: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 쿼리 내의 지리 공간적: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

올바른 형식의 [GeoJSON](geospatial.md) 개체에 대해 공간 인덱스를 사용할 수 있습니다. Points, LineStrings 및 다각형이 현재 지원 됩니다.

**복합** 인덱스 종류는 다음에 사용 됩니다.

- `ORDER BY`여러 속성에 대 한 쿼리: 

   ```sql
   SELECT * FROM container c ORDER BY c.firstName, c.lastName
   ```

## <a name="querying-with-indexes"></a>인덱스를 사용한 쿼리

데이터를 인덱싱할 때 추출 된 경로를 통해 쿼리를 처리할 때 인덱스를 쉽게 조회할 수 있습니다. 쿼리의 `WHERE` 절과 인덱싱된 경로 목록을 일치 시켜 쿼리 조건자와 일치 하는 항목을 매우 빠르게 식별할 수 있습니다.

예를 들어 다음 쿼리 `SELECT location FROM location IN company.locations WHERE location.country = 'France'`를 살펴보세요. 쿼리 조건자 (항목에 대 한 필터링, 국가에 "프랑스"가 있는 위치)는 아래 빨간색으로 강조 표시 된 경로와 일치 합니다.

![트리 내의 특정 경로 일치](./media/index-overview/matching-path.png)

> [!NOTE]
> 단일 속성을 기준으로 정렬 하는  절은항상범위인덱스가필요하며참조하는경로에는이인덱스가없는경우실패`ORDER BY` 합니다. 마찬가지로 다중 `ORDER BY` 쿼리에는 *항상* 복합 인덱스가 필요 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 정책](index-policy.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
