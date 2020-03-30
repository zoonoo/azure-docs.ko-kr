---
title: 확장된 GeoJSON 지오메트리 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps가 GeoJSON 사양을 확장하여 특정 형상을 나타내는 방법에 대해 알아봅니다.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276401"
---
# <a name="extended-geojson-geometries"></a>확장된 GeoJSON 지오메트리

Azure Maps는 지리적 기능 내부 및 따라 검색할 수 있는 강력한 API 목록을 제공합니다. 이러한 API는 지리적 기능을 나타내는 표준 [GeoJSON 사양을][1] 준수합니다.  

[GeoJSON 사양은][1] 다음 형상만 지원합니다.

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

일부 Azure Maps API는 [GeoJSON 사양에][1]속하지 않는 형상을 허용합니다. 예를 [들어, 지오메트리 내부 검색](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) API는 원과 다각형을 허용합니다.

이 문서에서는 Azure Maps가 [GeoJSON 사양][1]을 확장하여 특정 기하 도형을 나타내는 방법에 대한 자세한 설명을 제공합니다.

## <a name="circle"></a>Circle

`Circle` 지오메트리는 [GeoJSON 사양에서][1]지원되지 않습니다. `GeoJSON Point Feature` 우리는 원을 나타내는 개체를 사용합니다.

객체를 `Circle` 사용하여 표현되는 `GeoJSON Feature` 형상에는 다음 좌표와 속성이 __포함되어야 합니다.__

- 중심

    원의 중심은 객체를 `GeoJSON Point` 사용하여 표현됩니다.

- 반지름

    원의 `radius`는 `GeoJSON Feature`의 속성을 사용하여 표현됩니다. 반지름 값은 미터(_meters_) 단위이고 `double` 형식이어야 합니다.

- 하위 유형

    원 기하 도형에는 `subType` 속성도 포함되어야 합니다. 이 속성은 `GeoJSON Feature`속성의 일부여야 하며 해당 값은 _Circle이어야_ 합니다.

#### <a name="example"></a>예제

`GeoJSON Feature` 개체를 사용하여 원을 나타내는 방법은 다음과 같습니다. 위도에서 원을 가운데에 중심을 두자: 47.639754 및 경도: -122.126986, 100미터와 같은 반지름을 할당합니다.

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>직사각형

`Rectangle` 지오메트리는 [GeoJSON 사양에서][1]지원되지 않습니다. 개체를 `GeoJSON Polygon Feature` 사용하여 사각형을 나타냅니다. 사각형 확장은 주로 Web SDK의 그리기 도구 모듈에서 사용됩니다.

객체를 `Rectangle` 사용하여 표현되는 `GeoJSON Polygon Feature` 형상에는 다음 좌표와 속성이 __포함되어야 합니다.__

- 모서리

    사각형의 모서리는 `GeoJSON Polygon` 오브젝트의 좌표를 사용하여 표시됩니다. 각 모서리마다 하나씩 5개의 좌표가 있어야 합니다. 그리고, 첫 번째 좌표와 동일한 다섯 번째 좌표는 다각형 링을 닫습니다. 이러한 좌표가 정렬되고 개발자가 원하는 대로 회전할 수 있다고 가정합니다.

- 하위 유형

    사각형 형상에도 속성이 `subType` 포함되어야 합니다. 이 속성은 `GeoJSON Feature`'s 속성의 일부여야 하며 해당 값은 _사각형이어야_ 합니다.

### <a name="example"></a>예제

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>다음 단계

Azure 지도에서 GeoJSON 데이터에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [지오펜스 지오JSON 형식](geofence-geojson.md)

Azure 지도 및 위치 인텔리전스 응용 프로그램과 관련된 일반적인 기술 용어의 용어집을 검토합니다.

> [!div class="nextstepaction"]
> [Azure 지도 용어집](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
