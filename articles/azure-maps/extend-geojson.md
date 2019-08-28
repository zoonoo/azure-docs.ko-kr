---
title: Azure Maps의 확장 된 GeoJSON 기 하 도형 Microsoft Docs
description: Azure Maps에서 GeoJSON 기하 도형을 확장하는 방법 알아보기
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881993"
---
# <a name="extended-geojson-geometries"></a>확장 된 GeoJSON 기 하 도형

Azure Maps에는 지리적 특징의 안팎을 검색할 수 있는 강력한 API 목록이 제공됩니다.
이러한 Api는 지리적 기능 (예: 상태 경계, 경로)을 나타내기 위해 [GeoJSON 사양을][1] 표준화 합니다.  

[GeoJSON spec][1] 은 다음 기 하 도형만 지원 합니다.

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* 다각형

일부 Azure Maps Api (예: [기 하 도형 내 검색](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) [GeoJSON 사양의][1]일부가 아닌 "Circle"과 같은 기 하 도형을 허용 합니다.

이 문서에서는 Azure Maps 특정 기 하 도형을 나타내는 [GeoJSON 사양을][1] 확장 하는 방법에 대 한 자세한 설명을 제공 합니다.

## <a name="circle"></a>원

[GeoJSON 사양][1]에서 기하도형을지원하지않습니다.`Circle` 개체를 `GeoJSON Point Feature` 사용 하 여 원을 나타냅니다.

`GeoJSON Feature` 개체를 사용하여 표현된 `Circle` 기하 도형은 다음 항목을 __반드시__ 포함해야 합니다.

- 가운데

    원 중심은 개체를 `GeoJSON Point` 사용 하 여 표현 됩니다.

- 반지름

    원의 `radius`는 `GeoJSON Feature`의 속성을 사용하여 표현됩니다. 반지름 값은 미터(_meters_) 단위이고 `double` 형식이어야 합니다.

- 하위 유형

    원 기하 도형에는 `subType` 속성도 포함되어야 합니다. 이 속성은 `GeoJSON Feature`의 속성에 포함 되 고 해당 값은 _원_ 이어야 합니다.

#### <a name="example"></a>예제

다음은 중심으로 원을 표시 하는 방법입니다 (위도: 47.639754, 경도:-122.126986), 반지름은 100 미터와 같으며 개체를 `GeoJSON Feature` 사용 합니다.

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

[GeoJSON 사양][1]에서 기하도형을지원하지않습니다.`Rectangle` `GeoJSON Polygon Feature` 개체를 사용 하 여 사각형을 나타냅니다. 사각형 확장은 웹 SDK의 그리기 도구 모듈에서 주로 사용 됩니다.

`GeoJSON Polygon Feature` 개체를 사용하여 표현된 `Rectangle` 기하 도형은 다음 항목을 __반드시__ 포함해야 합니다.

- 방향

    사각형의 모퉁이는 `GeoJSON Polygon` 개체의 좌표를 사용 하 여 표시 됩니다. 각 모퉁이 마다 하나씩, 다각형 링을 닫는 1과 동일한 다섯 번째 좌표 등 5 개의 좌표가 있어야 합니다. 이러한 좌표는 개발자가 원하는 대로 정렬 하 고 회전 하는 것으로 간주 됩니다.

- 하위 유형

    사각형 기 하 도형에도 `subType` 속성이 포함 되어야 합니다. 이 속성은 `GeoJSON Feature`의 속성에 포함 되 고 해당 값은 _사각형_ 이어야 합니다.

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

Azure Maps에서 데이터 GeoJSON에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [지 오 GeoJSON 형식](geofence-geojson.md)

Azure Maps 및 위치 인텔리전스 응용 프로그램과 관련 된 일반적인 기술 용어를 검토 합니다.

> [!div class="nextstepaction"]
> [Azure Maps 용어집](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
