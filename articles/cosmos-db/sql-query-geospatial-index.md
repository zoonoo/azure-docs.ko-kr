---
title: Azure Cosmos DB를 사용하여 지리 공간적 데이터 인덱싱
description: Azure Cosmos DB를 사용하여 공간 데이터 인덱싱
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336217"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용하여 지리 공간적 데이터 인덱싱
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB의 데이터베이스 엔진은 스키마 제약 없이 JSON을 최고 수준으로 지원하도록 설계되었습니다. Azure Cosmos DB의 쓰기 최적화된 데이터베이스 엔진은 GeoJSON 표준으로 표시된 공간 데이터를 기본적으로 이해합니다.

간단히 말해, 기하 도형이 측지 좌표에서 2D 평면으로 프로젝션된 다음 **quadtree** 를 사용하여 셀에 점진적으로 나뉩니다. 이러한 셀은 점의 위치를 유지하는 **힐버트 공간 채움 곡선** 내의 셀 위치에 따라 1D에 매핑됩니다. 또한 위치 데이터는 인덱싱될 때 **공간 분할** 이라는 프로세스를 거칩니다. 즉, 위치와 교차하는 모든 셀이 식별되고 Azure Cosmos DB 인덱스에 키로 저장됩니다. 쿼리 시 점 및 다각형과 같은 인수도 관련 셀 ID 범위를 추출하기 위해 공간 분할된 다음 인덱스에서 데이터를 검색하는 데 사용됩니다.

`/*`(모든 경로)에 대한 공간 인덱스를 포함하는 인덱싱 정책을 지정하면 효율적인 공간 쿼리를 위해 컨테이너에 있는 모든 데이터가 인덱싱됩니다.

> [!NOTE]
> Azure Cosmos DB는 Point, LineString, Polygon, MultiPolygon의 인덱싱을 지원합니다. 이 형식 중 하나를 인덱싱하면 다른 모든 형식도 자동으로 인덱싱됩니다. 즉, Polygon을 인덱싱하면 Point, LineString, MultiPolygon도 인덱싱됩니다. 새 공간 형식을 인덱싱해도 해당 형식의 유효한 GeoJSON 데이터가 없는 한, 쓰기 RU 요금이나 인덱스 크기는 영향을 받지 않습니다.

## <a name="modifying-geospatial-configuration"></a>지리 공간적 구성 수정

컨테이너에서 **지리 공간적 구성** 은 공간 데이터를 인덱싱하는 방법을 지정합니다. 컨테이너당 하나의 **지리 공간적 구성**(지리 또는 기하 도형)을 지정합니다.

Azure Portal에서 **지리** 및 **기하 도형** 공간 형식 간에 전환할 수 있습니다. 기하 도형 공간 형식으로 전환하기 전에 [경계 상자를 사용하여 유효한 공간 기하 도형 인덱싱 정책](#geometry-data-indexing-examples)을 만드는 것이 중요합니다.

Azure Portal 내 **데이터 탐색기** 에서 **지리 공간적 구성** 을 설정하는 방법은 다음과 같습니다.

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="지리 공간적 구성 설정":::

.NET SDK에서 `geospatialConfig`를 수정하여 **지리 공간적 구성** 을 조정할 수도 있습니다.

지정하지 않으면 `geospatialConfig`는 기본적으로 지리 데이터 형식으로 지정됩니다. `geospatialConfig`를 수정하면 컨테이너의 기존 지리 공간적 데이터가 모두 다시 인덱싱됩니다.

다음은 `geospatialConfig` 속성을 설정하고 **boundingBox** 를 추가하여 지리 공간적 데이터 형식을 `geometry`로 수정하는 예제입니다.

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>지리 데이터 인덱싱 예제

다음 JSON 코드 조각은 **지리** 데이터 형식에 대한 공간 인덱싱이 사용하도록 설정된 인덱싱 정책을 보여 줍니다. 이 정책은 지리 데이터 형식의 공간 데이터에 유효하며, 공간 쿼리를 위해 문서 내에 있는 GeoJSON Point, Polygon, MultiPolygon 또는 LineString을 모두 인덱싱합니다. Azure Portal을 사용하여 인덱싱 정책을 수정하는 경우 인덱싱 정책에 대해 다음과 같은 JSON을 지정하여 컨테이너에서 공간 인덱싱을 사용하도록 설정할 수 있습니다.

**지리 공간 인덱싱을 사용하는 컨테이너 인덱싱 정책 JSON**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> 문서 내의 위치 GeoJSON 값이 잘못되었거나 형식이 잘못된 경우 공간 쿼리를 위해 인덱싱되지 않습니다. ST_ISVALID 및 ST_ISVALIDDETAILED를 사용하여 위치 값의 유효성을 검사할 수 있습니다.

Azure CLI, PowerShell 또는 SDK를 사용하여 [인덱싱 정책을 수정](how-to-manage-indexing-policy.md)할 수도 있습니다.

## <a name="geometry-data-indexing-examples"></a>기하 도형 데이터 인덱싱 예제

지리 데이터 형식과 마찬가지로 **기하 도형** 데이터 형식에서도 관련 경로와 인덱싱할 형식을 지정해야 합니다. 특정 경로에 대해 인덱싱하려는 영역을 나타내기 위해 인덱싱 정책 내에서 `boundingBox`도 지정해야 합니다. 각 지리 공간적 경로에 고유한 `boundingBox`가 필요합니다.

경계 상자는 다음 속성으로 구성됩니다.

- **xmin**: 인덱싱된 최소 x 좌표
- **ymin**: 인덱싱된 최소 y 좌표
- **xmax**: 인덱싱된 최대 x 좌표
- **ymax**: 인덱싱된 최대 y 좌표

기하학적 데이터는 차지하는 평면이 무한할 수 있기 때문에 경계 상자가 필요합니다. 그러나 공간 인덱스에 필요한 공간은 유한합니다. **지리** 데이터 형식의 경우 지구가 경계이므로 경계 상자를 설정할 필요가 없습니다.

데이터를 모두(또는 대부분) 포함하는 경계 상자를 만듭니다. 완전히 경계 상자 내부에 있는 개체에서 컴퓨팅된 작업만 공간 인덱스를 활용할 수 있습니다. 경계 상자를 필요 이상으로 크게 만들면 쿼리 성능이 저하됩니다.

다음은 **geospatialConfig** 를 `geometry`로 설정하여 **기하 도형** 데이터를 인덱싱하는 예제 인덱싱 정책입니다.

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

위의 인덱싱 정책에는 x 좌표가 (-10, 10), y 좌표가 (-20, 20)인 **boundingBox** 가 있습니다. 위의 인덱싱 정책을 사용하는 컨테이너는 완전히 이 지역 내에 있는 모든 Point, Polygon, MultiPolygon, LineString을 인덱싱합니다.

> [!NOTE]
> **boundingBox** 를 사용하는 인덱싱 정책을 `geography` 데이터 형식의 컨테이너에 추가하려고 하면 실패합니다. **boundingBox** 를 추가하기 전에 컨테이너의 **geospatialConfig** 를 `geometry`로 수정해야 합니다. 컨테이너에 대해 지리 공간적 데이터 형식을 선택하기 전이나 이후에 데이터를 추가하고 인덱싱 정책의 나머지(예: 경로, 형식)를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [Azure Cosmos DB 쿼리](sql-query-getting-started.md)에 대해 알아보기
* [Azure Cosmos DB를 사용하여 공간 데이터 쿼리](sql-query-geospatial-query.md)에 대해 알아보기
* [Azure Cosmos DB의 지리 공간적 및 GeoJSON 위치 데이터](sql-query-geospatial-intro.md)에 대해 알아보기
