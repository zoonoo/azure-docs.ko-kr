---
title: Azure Cosmos DB를 사용 하 여 지리 공간적 데이터 인덱싱
description: Azure Cosmos DB를 사용 하 여 공간 데이터 인덱싱
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566375"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하 여 지리 공간적 데이터 인덱싱

Azure Cosmos DB의 데이터베이스 엔진은 실제로 스키마를 알 수 없고 JSON에 대 한 첫 번째 클래스 지원을 제공 하도록 설계 되었습니다. Azure Cosmos DB의 쓰기 최적화 된 데이터베이스 엔진은 GeoJSON 표준으로 표시 된 공간 데이터를 기본적으로 이해 합니다.

간단히 말해, 기하 도형이 측지 좌표에서 2D 평면으로 프로젝션된 다음 **quadtree**를 사용하여 셀에 점진적으로 나뉩니다. 이러한 셀은 점의 위치를 유지하는 **힐버트 공간 채움 곡선** 내의 셀 위치에 따라 1D에 매핑됩니다. 또한 위치 데이터는 인덱싱될 때 **공간 분할**이라는 프로세스를 거칩니다. 즉, 위치와 교차하는 모든 셀이 식별되고 Azure Cosmos DB 인덱스에 키로 저장됩니다. 쿼리 시 점 및 다각형과 같은 인수도 관련 셀 ID 범위를 추출하기 위해 공간 분할된 다음 인덱스에서 데이터를 검색하는 데 사용됩니다.

/* (모든 경로)에 대해 공간 인덱스를 포함 하는 인덱싱 정책을 지정 하는 경우 효율적인 공간 쿼리에 대해 컨테이너에 있는 모든 데이터가 인덱싱됩니다.

> [!NOTE]
> Azure Cosmos DB는 요소, LineStrings, 다각형 및 MultiPolygons의 인덱싱을 지원 합니다.
>
>

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
>
>
>

Azure CLI, PowerShell 또는 SDK를 사용 하 여 [인덱싱 정책을 수정할](how-to-manage-indexing-policy.md) 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [Azure Cosmos DB 쿼리](sql-query-getting-started.md)에 대해 알아보기
* [Azure Cosmos DB를 사용 하 여 공간 데이터 쿼리](sql-query-geospatial-query.md) 에 대 한 자세한 정보
* [Azure Cosmos DB에서 지리 공간 및 GeoJSON 위치 데이터](sql-query-geospatial-intro.md) 에 대해 자세히 알아보세요.