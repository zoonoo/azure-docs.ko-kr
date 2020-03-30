---
title: Azure 코스모스 DB를 사용하여 지리 공간 데이터 색인
description: Azure 코스모스 DB를 사용하여 공간 데이터 인덱싱
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137906"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure 코스모스 DB를 사용하여 지리 공간 데이터 색인

Azure Cosmos DB의 데이터베이스 엔진을 진정으로 스키마에 구애받지 않고 JSON에 대한 일류 지원을 제공하도록 설계했습니다. Azure Cosmos DB의 쓰기 최적화된 데이터베이스 엔진은 기본적으로 GeoJSON 표준에 표시되는 공간 데이터를 이해합니다.

간단히 말해, 기하 도형이 측지 좌표에서 2D 평면으로 프로젝션된 다음 **quadtree**를 사용하여 셀에 점진적으로 나뉩니다. 이러한 셀은 점의 위치를 유지하는 **힐버트 공간 채움 곡선** 내의 셀 위치에 따라 1D에 매핑됩니다. 또한 위치 데이터는 인덱싱될 때 **공간 분할**이라는 프로세스를 거칩니다. 즉, 위치와 교차하는 모든 셀이 식별되고 Azure Cosmos DB 인덱스에 키로 저장됩니다. 쿼리 시 점 및 다각형과 같은 인수도 관련 셀 ID 범위를 추출하기 위해 공간 분할된 다음 인덱스에서 데이터를 검색하는 데 사용됩니다.

/* (모든 경로)에 대한 공간 인덱스를 포함하는 인덱싱 정책을 지정하면 컨테이너 내에 있는 모든 데이터가 효율적인 공간 쿼리를 위해 인덱싱됩니다.

> [!NOTE]
> Azure 코스모스 DB는 포인트, 라인스트링, 다각형 및 다중Polygon의 인덱싱을 지원합니다.
>
>

## <a name="modifying-geospatial-data-type"></a>지리 공간 데이터 형식 수정

컨테이너에서 지리 `geospatialConfig` 공간 데이터의 인덱싱 방법을 지정합니다. `geospatialConfig` 컨테이너당 지리 또는 형상을 지정해야 합니다. 지정하지 `geospatialConfig` 않으면 지리 데이터 형식이 기본값입니다. `geospatialConfig`을 수정하면 컨테이너의 모든 기존 지리 공간 데이터가 다시 인덱싱됩니다.

> [!NOTE]
> Azure Cosmos DB는 현재 3.6 이상의 버전에서만 .NET SDK의 지리 공간 구성에 대한 수정을 지원합니다.
>

다음은 `geospatialConfig` 속성을 설정하고 경계 **상자를**추가하여 지리 `geometry` 공간 데이터 형식을 수정하는 예제입니다.

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

다음 JSON 코드 조각은 **지리** 데이터 형식에 대해 공간 인덱싱이 활성화된 인덱싱 정책을 보여 주며 있습니다. 지리 데이터 형식이 있는 공간 데이터에 대해 유효하며 공간 쿼리를 위해 문서 내에 있는 GeoJSON 포인트, 다각형, 다각형 또는 LineString을 인덱싱합니다. Azure 포털을 사용하여 인덱싱 정책을 수정하는 경우 컨테이너에서 공간 인덱싱을 사용하도록 인덱싱 정책에 대해 다음 JSON을 지정할 수 있습니다.

**지리 공간 인덱싱을 갖춘 컨테이너 인덱싱 정책 JSON**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
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
       "excludedPaths":[]
    }
```

> [!NOTE]
> 문서 내의 위치 GeoJSON 값이 잘못되었거나 형식이 잘못된 경우 공간 쿼리를 위해 인덱싱되지 않습니다. ST_ISVALID 및 ST_ISVALIDDETAILED를 사용하여 위치 값의 유효성을 검사할 수 있습니다.

Azure CLI, PowerShell 또는 SDK를 사용하여 [인덱싱 정책을 수정할](how-to-manage-indexing-policy.md) 수도 있습니다.

## <a name="geometry-data-indexing-examples"></a>지오메트리 데이터 인덱싱 예제

지리 데이터 형식과 유사한 **지오메트리** 데이터 형식을 사용하여 인덱싱할 관련 경로 및 유형을 지정해야 합니다. 또한 인덱싱 정책 `boundingBox` 내에서 특정 경로에 대해 인덱싱할 원하는 영역을 지정해야 합니다. 각 지리 공간 경로에는 자체`boundingBox`가 필요합니다.

경계 상자는 다음 속성으로 구성됩니다.

- **xmin**: 최소 인덱싱 된 x 좌표
- **ymin**: 최소 인덱싱 y 좌표
- **xmax**: 최대 인덱싱 된 x 좌표
- **ymax**: 최대 인덱싱 된 y 좌표

기하학적 데이터가 무한할 수 있는 평면을 차지하기 때문에 경계 상자가 필요합니다. 그러나 공간 인덱스에는 유한한 공간이 필요합니다. **지리** 데이터 형식의 경우 지구는 경계이며 경계 상자를 설정할 필요가 없습니다.

데이터의 전부(또는 대부분)를 포함하는 경계 상자를 만들어야 합니다. 경계 상자 내부에 있는 객체에서 계산된 작업만 공간 인덱스를 사용할 수 있습니다. 쿼리 성능에 부정적인 영향을 미치기 때문에 경계 상자를 필요 이상으로 크게 만들지 않아야 합니다.

다음은 지오메트리 데이터를 **다음으로** 설정하여 **지오메트리** 데이터를 `geometry`인덱싱하는 예제 인덱싱 정책입니다.

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

위의 인덱싱 정책에는 x 좌표에 대해 경계 상자(-10, 10)와 y 좌표의 경우 (-20, 20)가 있습니다. **boundingBox** 위의 인덱싱 정책이 있는 컨테이너는 이 리전 내에 있는 모든 점, 다각형, 다각형 및 LineString을 인덱싱합니다.

> [!NOTE]
> 데이터 형식이 있는 컨테이너에 `geography` **경계가** 있는 인덱싱 정책을 추가하려고 하면 실패합니다. **경계 상자를**추가 `geometry` 하기 전에 컨테이너의 **지리 공간구성을** 수정 해야 합니다. 컨테이너의 지리 공간 데이터 형식을 선택하기 전이나 후에 데이터를 추가하고 인덱싱 정책의 나머지 부분(예: 경로 및 유형)을 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [Azure Cosmos DB 쿼리](sql-query-getting-started.md)에 대해 알아보기
* [Azure Cosmos DB를 사용하여 공간 데이터 쿼리에](sql-query-geospatial-query.md) 대해 자세히 알아보기
* [Azure Cosmos DB에서 지리 공간 및 GeoJSON 위치 데이터에](sql-query-geospatial-intro.md) 대해 자세히 알아보기