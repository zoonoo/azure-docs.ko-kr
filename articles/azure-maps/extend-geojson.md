---
title: 확장된 GeoJSON 기하 도형 | Microsoft Azure Maps
description: Azure Maps가 GeoJSON 사양을 확장하여 추가 기하학적 도형을 포함하는 방법에 대해 알아봅니다. 맵에서 사용할 원과 사각형을 설정 하는 예제를 봅니다.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895786"
---
# <a name="extended-geojson-geometries"></a>확장된 GeoJSON 기하 도형

Azure Maps는 지리적 기능에 따라 내부를 검색할 수 있는 강력한 API 목록을 제공됩니다. 이러한 API는 지리적 기능을 대표하는 표준 [GeoJSON 사양][1]을 준수합니다.  

[GeoJSON 사양][1]에서 지원하는 기하 도형은 다음으로 제한됩니다.

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

일부 Azure Maps API는 [GeoJSON 사양][1]의 일부가 아닌 기하 도형을 수락합니다. 예를 들어 [기하 도형 API 내에서 검색](/rest/api/maps/search/postsearchinsidegeometry)은 원과 다각형을 허용합니다.

이 문서에서는 Azure Maps가 [GeoJSON 사양][1]을 확장하여 특정 기하 도형을 나타내는 방법에 대한 자세한 설명을 제공합니다.

## <a name="circle"></a>Circle

이 `Circle`기하 도형은 [GeoJSON 사양][1]에서 지원하지 않습니다. 원을 사용하여 `GeoJSON Point Feature` 개체를 나타냅니다.

`GeoJSON Feature` 개체를 사용하여 표현된 `Circle` 기하 도형은 다음 좌표와 속성을 __반드시__ 포함해야 합니다.

- Center

    원의 중심은 `GeoJSON Point` 개체를 사용하여 표현합니다.

- 반지름

    원의 `radius`는 `GeoJSON Feature`의 속성을 사용하여 표현됩니다. 반지름 값은 미터(_meters_) 단위이고 `double` 형식이어야 합니다.

- 하위 유형

    원 기하 도형에는 `subType` 속성도 포함되어야 합니다. 이 속성은 `GeoJSON Feature`의 속성의 한 부분이어야 하며 해당 값은 _원_ 이어야 합니다

#### <a name="example"></a>예제

`GeoJSON Feature` 개체를 사용하여 원을 표시하는 방법은 다음과 같습니다. 위도: 47.639754 및 경도:-122.126986 지점을 원의 중심으로 지정하고 반지름을 100미터로 할당하겠습니다.

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

## <a name="rectangle"></a>사각형

이 `Rectangle`기하 도형은 [GeoJSON 사양][1]에서 지원하지 않습니다. `GeoJSON Polygon Feature` 개체를 사용하여 사각형을 나타냅니다. 사각형 확장은 웹 SDK의 그리기 도구 모듈에서 주로 사용됩니다.

`GeoJSON Polygon Feature` 개체를 사용하여 표현된 `Rectangle` 기하 도형은 다음 좌표와 속성을 __반드시__ 포함해야 합니다.

- 모퉁이

    사각형의 모퉁이는 `GeoJSON Polygon` 개체의 좌표를 사용하여 표시 됩니다. 각 모퉁이마다 하나씩, 5개의 좌표가 있어야 합니다. 그리고 첫 번째 좌표와 동일한 다섯 번째 좌표로 다각형 링을 닫습니다. 이러한 좌표의 정렬을 맞추고 개발자가 원하는 대로 회전할 수 있다고 가정합니다.

- 하위 유형

    사각형 기하 도형 또한 `subType` 속성을 포함해야 합니다. 이 속성은 `GeoJSON Feature` 속성의 한 부분이어야 하며, 해당 값은 _사각형_ 이어야 합니다

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

Azure Maps에서 GeoJSON 데이터에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [지오펜스 GeoJSON 형식](geofence-geojson.md)

Azure Maps 및 위치 인텔리전스 애플리케이션과 관련된 일반적인 기술 용어를 검토합니다.

> [!div class="nextstepaction"]
> [Azure Maps 용어집](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946