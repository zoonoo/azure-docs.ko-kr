---
title: Azure Cosmos DB를 사용 하 여 지리 공간적 데이터 인덱싱
description: Azure Cosmos DB를 사용 하 여 공간 데이터 인덱싱
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79137906"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하 여 지리 공간적 데이터 인덱싱

Azure Cosmos DB의 데이터베이스 엔진은 실제로 스키마를 알 수 없고 JSON에 대 한 첫 번째 클래스 지원을 제공 하도록 설계 되었습니다. Azure Cosmos DB의 쓰기 최적화 된 데이터베이스 엔진은 GeoJSON 표준으로 표시 된 공간 데이터를 기본적으로 이해 합니다.

간단히 말해, 기하 도형이 측지 좌표에서 2D 평면으로 프로젝션된 다음 **quadtree**를 사용하여 셀에 점진적으로 나뉩니다. 이러한 셀은 점의 위치를 유지하는 **힐버트 공간 채움 곡선** 내의 셀 위치에 따라 1D에 매핑됩니다. 또한 위치 데이터는 인덱싱될 때 **공간 분할**이라는 프로세스를 거칩니다. 즉, 위치와 교차하는 모든 셀이 식별되고 Azure Cosmos DB 인덱스에 키로 저장됩니다. 쿼리 시 점 및 다각형과 같은 인수도 관련 셀 ID 범위를 추출하기 위해 공간 분할된 다음 인덱스에서 데이터를 검색하는 데 사용됩니다.

/* (모든 경로)에 대해 공간 인덱스를 포함 하는 인덱싱 정책을 지정 하는 경우 효율적인 공간 쿼리에 대해 컨테이너에 있는 모든 데이터가 인덱싱됩니다.

> [!NOTE]
> Azure Cosmos DB는 요소, LineStrings, 다각형 및 MultiPolygons의 인덱싱을 지원 합니다.
>
>

## <a name="modifying-geospatial-data-type"></a>지리 공간적 데이터 형식 수정

컨테이너에서는 `geospatialConfig` 지리 공간적 데이터를 인덱싱하는 방법을 지정 합니다. 컨테이너 (지리 또는 `geospatialConfig` 기 하 도형) 당 하나를 지정 해야 합니다. 지정 하지 않으면는 `geospatialConfig` 기본적으로 geography 데이터 형식으로 지정 됩니다. 를 수정 `geospatialConfig`하면 컨테이너의 모든 기존 지리 공간적 데이터가 인덱싱해야 됩니다.

> [!NOTE]
> 현재 Azure Cosmos DB는 3.6 이상 버전 에서만 .NET SDK의 geospatialConfig 수정 사항을 지원 합니다.
>

속성을 설정 하 고 **boundingBox**를 추가 하 여 지리 `geometry` 공간적 데이터 형식을로 수정 하는 예제는 다음과 같습니다. `geospatialConfig`

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

다음 JSON 코드 조각은 **geography** 데이터 형식에 대해 공간 인덱싱이 사용 되는 인덱싱 정책을 보여 줍니다. Geography 데이터 형식의 공간 데이터에 유효 하며 공간 쿼리를 위해 문서 내에 있는 모든 GeoJSON Point, Polygon, MultiPolygon 또는 LineString를 인덱싱합니다. Azure Portal를 사용 하 여 인덱싱 정책을 수정 하는 경우 인덱싱 정책에 대해 다음 JSON을 지정 하 여 컨테이너에서 공간 인덱싱을 사용 하도록 설정할 수 있습니다.

**지리 공간 인덱싱을 사용 하는 컨테이너 인덱싱 정책 JSON**

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

Azure CLI, PowerShell 또는 SDK를 사용 하 여 [인덱싱 정책을 수정할](how-to-manage-indexing-policy.md) 수도 있습니다.

## <a name="geometry-data-indexing-examples"></a>Geometry 데이터 인덱싱 예제

Geography 데이터 형식과 마찬가지로 **geometry** 데이터 형식을 사용 하 여 인덱싱할 관련 경로와 유형을 지정 해야 합니다. 또한 인덱싱 정책 내에서를 `boundingBox` 지정 하 여 특정 경로에 대해 인덱싱할 원하는 영역을 지정 해야 합니다. 각 지리 공간적 경로에는`boundingBox`고유한가 필요 합니다.

경계 상자는 다음 속성으로 구성 됩니다.

- **xmin**: 최소 인덱싱된 x 좌표입니다.
- **ymin**: 최소 인덱싱된 y 좌표입니다.
- **xmax**: 인덱싱된 최대 x 좌표
- **ymax**: 인덱싱된 최대 y 좌표

기하학적 데이터는 무한 할 수 있는 평면을 차지 하기 때문에 경계 상자가 필요 합니다. 그러나 공간 인덱스에는 유한 공간이 필요 합니다. **Geography** 데이터 형식의 경우 지구는 경계 이므로 경계 상자를 설정할 필요가 없습니다.

데이터의 모든 (또는 대부분)를 포함 하는 경계 상자를 만들어야 합니다. 경계 상자 내에 있는 개체에 대해 계산 된 작업만이 공간 인덱스를 활용할 수 있습니다. 경계 상자는 쿼리 성능에 부정적인 영향을 주므로 필요 이상으로 크게 설정 하면 안 됩니다.

**GeospatialConfig** 로 설정 된 `geometry` **geometry** 데이터를 인덱싱하는 인덱싱 정책 예제는 다음과 같습니다.

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

위의 인덱싱 정책은 x 좌표에 대해 (-10, 10) **boundingBox** (y 좌표의 경우-20, 20)입니다. 위의 인덱싱 정책이 있는 컨테이너는이 지역 내에 있는 모든 요소, 다각형, 다중 다각형 및 LineStrings를 인덱싱합니다.

> [!NOTE]
> **BoundingBox** 를 포함 하는 인덱싱 정책을 `geography` 데이터 형식의 컨테이너에 추가 하려고 하면 오류가 발생 합니다. **BoundingBox**를 추가 `geometry` 하기 전에 컨테이너의 **geospatialConfig** 을로 수정 해야 합니다. 컨테이너의 지리 공간적 데이터 형식을 선택 하기 전이나 후에 데이터를 추가 하 고 인덱싱 정책의 나머지를 수정할 수 있습니다 (예: 경로 및 형식).

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [Azure Cosmos DB 쿼리](sql-query-getting-started.md)에 대해 알아보기
* [Azure Cosmos DB를 사용 하 여 공간 데이터 쿼리](sql-query-geospatial-query.md) 에 대 한 자세한 정보
* [Azure Cosmos DB에서 지리 공간 및 GeoJSON 위치 데이터](sql-query-geospatial-intro.md) 에 대해 자세히 알아보세요.