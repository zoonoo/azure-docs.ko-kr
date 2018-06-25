---
title: Azure Maps에서 GeoJSON 기하 도형 추가 | Microsoft Docs
description: Azure Maps에서 GeoJSON 기하 도형을 확장하는 방법 알아보기
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659842"
---
# <a name="extending-geojson-geometries"></a>GeoJSON 기하 도형 확장

Azure Maps에는 지리적 특징의 안팎을 검색할 수 있는 강력한 API 목록이 제공됩니다.
이러한 API는 지리적 특징(예: 시/도 경계, 경로)을 나타내는 [GeoJSON 사양][1]을 표준화합니다.  

[GeoJSON 사양][1]은 다음과 같은 기하 도형만 지원합니다.

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

일부 Azure Maps API(예: [Search Inside Geometry](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry))는 [GeoJSON 사양][1]에 속하지 않는 "Circle"과 같은 기하 도형을 허용합니다.

이 문서에서는 Azure Maps가 [GeoJSON 사양][1]을 확장하여 특정 기하 도형을 나타내는 방법에 대한 자세한 설명을 제공합니다.

### <a name="circle"></a>원

`Circle` 기하 도형은 [GeoJSON 사양][1]에서 지원되지 않습니다. 여기서는 `GeoJSON Feature` 개체를 사용하여 원을 나타냅니다.

`GeoJSON Feature` 개체를 사용하여 표현된 `Circle` 기하 도형은 다음 항목을 __반드시__ 포함해야 합니다.

1. 중심
   >원의 중심은 `GeoJSON Point` 유형을 사용하여 표현됩니다.

2. 반지름
   >원의 `radius`는 `GeoJSON Feature`의 속성을 사용하여 표현됩니다. 반지름 값은 미터(_meters_) 단위이고 `double` 형식이어야 합니다.

3. 하위 유형
   >원 기하 도형에는 `subType` 속성도 포함되어야 합니다. 이 속성은 `GeoJSON Feature`의 속성에 속해야 하며 값은 _Circle_이어야 합니다.


#### <a name="example"></a>예

다음은 `GeoJSON Feature` 개체를 사용하여 반지름이 100미터이면서 위도: 47.639754, 경도: -122.126986에 중심을 둔 원을 나타내는 방법입니다.

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

[1]: https://tools.ietf.org/html/rfc7946
