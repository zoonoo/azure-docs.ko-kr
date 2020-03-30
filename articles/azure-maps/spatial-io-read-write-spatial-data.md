---
title: 공간 데이터 읽기 및 쓰기 | 마이크로소프트 Azure 지도
description: Azure Maps Web SDK에서 제공하는 공간 IO 모듈을 사용하여 데이터를 읽고 쓰는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334148"
---
# <a name="read-and-write-spatial-data"></a>공간 데이터 읽기 및 쓰기

아래 표에는 Spatial IO 모듈을 통해 작업을 읽고 쓰는 데 지원되는 공간 파일 형식이 나열되어 있습니다.

| 데이터 형식       | 읽기 | 쓰기 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| 지오RSS            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| 공간 CSV       | ✓  |  ✓  |
| 잘 알려진 텍스트   | ✓  |  ✓  |

다음 섹션에서는 공간 IO 모듈을 사용하여 공간 데이터를 읽고 쓰기 위한 모든 다른 도구에 대해 간략하게 설명합니다.

## <a name="read-spatial-data"></a>공간 데이터 읽기

이 `atlas.io.read` 기능은 KML, GPX, GeoRSS, GeoJSON 및 CSV 파일과 같은 일반적인 공간 데이터 형식을 공간 데이터로 읽는 데 사용되는 주요 기능입니다. 이 함수는 압축 된 버전의 압축 된 버전을 zip 파일 또는 KMZ 파일로 읽을 수도 있습니다. KMZ 파일 형식은 이미지와 같은 자산을 포함할 수 있는 KML의 압축 버전입니다. 또는 읽기 함수는 이러한 형식의 파일을 가리키는 URL을 사용할 수 있습니다. URL은 CORS 사용 엔드포인트에서 호스팅되어야 하거나 읽기 옵션에 프록시 서비스를 제공해야 합니다. 프록시 서비스는 CORS를 사용할 수 없는 도메인에 리소스를 로드하는 데 사용됩니다. 읽기 함수는 맵에 이미지 아이콘을 추가하겠다는 약속을 반환하고 UI 스레드에 미치는 영향을 최소화하기 위해 데이터를 비동기적으로 처리합니다.

압축 된 파일을 읽을 때, zip 또는 KMZ로, 그것은 압축을 풀고 첫 번째 유효한 파일에 대 한 스캔 됩니다. 예를 들어 doc.kml 또는 .kml, .xml, geojson, .json, .csv, .tsv 또는 .txt와 같은 다른 유효한 확장자가 있는 파일입니다. 그런 다음 KML 및 GeoRSS 파일에서 참조되는 이미지가 미리 로드되어 액세스할 수 있습니다. 액세스할 수 없는 이미지 데이터는 대체 대체 이미지를 로드하거나 스타일에서 제거될 수 있습니다. KMZ 파일에서 추출한 이미지는 데이터 URI로 변환됩니다.

읽기 함수의 결과는 개체입니다. `SpatialDataSet` 이 개체는 GeoJSON 기능 컬렉션 클래스를 확장합니다. 그것은 쉽게 지도에 `DataSource` 해당 기능을 렌더링 하는 있는 사람으로 전달 될 수 있습니다. 기능 `SpatialDataSet` 정보뿐만 아니라 KML 접지 오버레이, 처리 메트릭 및 다음 표에 설명된 기타 세부 정보도 포함될 수 있습니다.

| 속성 이름 | Type | Description | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 데이터 집합의 모든 데이터의 경계 상자입니다. |
| `features` | `Feature[]` | 데이터 집합 내의 GeoJSON 기능입니다. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML 그라운드 오버레이의 배열. |
| `icons` | 레코드&lt;문자열, 문자열&gt; | 아이콘 URL 집합입니다. 키 = 아이콘 이름, 값 = URL. |
| properties | any | 공간 데이터 세트의 문서 수준에서 제공되는 속성 정보입니다. |
| `stats` | `SpatialDataSetStats` | 공간 데이터 세트의 콘텐츠 및 처리 시간에 대한 통계입니다. |
| `type` | `'FeatureCollection'` | 읽기 전용 GeoJSON 형식 값입니다. |

## <a name="examples-of-reading-spatial-data"></a>공간 데이터 읽기의 예

다음 코드는 공간 데이터 집합을 읽고 클래스를 사용하여 맵에서 렌더링하는 방법을 보여 주어집니다. `SimpleDataLayer` 코드는 URL로 가리키는 GPX 파일을 사용합니다.

<br/>

<iframe height='500' scrolling='no' title='공간 데이터 로드 단순' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a>간단한 펜 <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>로드 공간 데이터를</a> 참조하십시오.
</iframe>

다음 코드 데모에서는 KML 또는 KMZ를 맵에 읽고 로드하는 방법을 보여 주며, 이 데모를 보여 주실 수 있습니다. KML은 `ImageLyaer` 또는 의 `OgcMapLayer`형태로 될 지상 오버레이를 포함 할 수 있습니다. 이러한 오버레이는 피처와 별도로 맵에 추가해야 합니다. 또한 데이터 세트에 사용자 지정 아이콘이 있는 경우 피처를 로드하기 전에 해당 아이콘을 맵 리소스에 로드해야 합니다.

<br/>

<iframe height='500' scrolling='no' title='KML 온맵 로드' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a> <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML 온투 Azure</a> 지도<a href='https://codepen.io/azuremaps'>@azuremaps</a>() 별 펜 로드를 참조하십시오.
</iframe>

CORS를 사용하도록 설정하지 않은 교차 도메인 자산에 액세스하기 위한 프록시 서비스를 선택적으로 제공할 수 있습니다. 읽기 함수는 먼저 CORS를 사용하여 다른 도메인의 파일에 액세스하려고 시도합니다. CORS를 사용하여 다른 도메인의 리소스에 처음 액세스하지 못하면 프록시 서비스가 제공된 경우에만 추가 파일을 요청합니다. 읽기 함수는 제공된 프록시 URL의 끝에 파일 URL을 더합니다. 이 코드 조각은 프록시 서비스를 읽기 함수에 전달하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 줄 수 있습니다.

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

아래 데모에서는 구분된 파일을 읽고 맵에서 렌더링하는 방법을 보여 주어집니다. 이 경우 코드는 공간 데이터 열이 있는 CSV 파일을 사용합니다.

<br/>

<iframe height='500' scrolling='no' title='구분된 파일 추가' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>구분된 파일 추가</a> 를 참조하세요.
</iframe>

## <a name="write-spatial-data"></a>공간 데이터 작성

공간 IO 모듈에는 두 가지 주요 쓰기 함수가 있습니다. 함수는 `atlas.io.write` 문자열을 생성하고 `atlas.io.writeCompressed` 함수는 압축된 zip 파일을 생성합니다. 압축된 zip 파일에는 공간 데이터가 포함된 텍스트 기반 파일이 포함됩니다. 이 두 함수 모두 파일에 데이터를 추가하겠다는 약속을 반환합니다. 그리고 둘 다 다음 데이터 중 `SpatialDataSet`어느 `DataSource` `ImageLayer`한 `OgcMapLayer`개도 쓸 수 있습니다: . 두 함수 중 하나를 사용하여 작성할 때 원하는 파일 형식을 지정할 수 있습니다. 파일 형식을 지정하지 않으면 데이터가 KML로 기록됩니다.

아래 도구는 `atlas.io.write` 함수와 함께 사용할 수 있는 쓰기 옵션의 대부분을 보여 줍니다.

<br/>

<iframe height='700' scrolling='no' title='공간 데이터 쓰기 옵션' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 펜 <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>공간 데이터 쓰기 옵션을</a> 참조하십시오.
</iframe>

## <a name="example-of-writing-spatial-data"></a>공간 데이터 작성 예

다음 샘플을 사용하면 지도에서 공간 파일을 드래그 앤 드롭한 다음 로드할 수 있습니다. 맵에서 GeoJSON 데이터를 내보내고 지원되는 공간 데이터 형식 중 하나에 문자열또는 압축 파일로 쓸 수 있습니다.

<br/>

<iframe height='700' scrolling='no' title='지도에 공간 파일을 드래그 앤 드롭' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>펜 드래그 및 공간 파일을 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () 별 <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>맵에 놓습니다.</a>
</iframe>

CORS를 사용하도록 설정하지 않은 교차 도메인 자산에 액세스하기 위한 프록시 서비스를 선택적으로 제공할 수 있습니다. 이 코드 조각은 프록시 서비스를 통합할 수 있다는 것을 보여줍니다.

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>잘 알려진 텍스트 읽기 및 쓰기(WKT)

[잘 알려진](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) 텍스트(WKT)는 공간 형상을 텍스트로 표현하기 위한 개방형 지리 공간 컨소시엄(OGC) 표준입니다. 많은 지리 공간 시스템은 PostGIS 플러그인을 사용하여 Azure SQL 및 Azure PostgreSQL과 같은 WKT를 지원합니다. 대부분의 OGC 표준과 마찬가지로 좌표는 "경도 위도"로 서식이 지정되어 "x y" 규칙에 맞게 정렬됩니다. 일례로, 경도 -110 및 위도(45)의 `POINT(-110 45)` 점은 WKT 형식을 사용하여 쓰여지을 수 있다.

잘 알려진 텍스트는 함수를 `atlas.io.ogc.WKT.read` 사용하여 읽을 수 `atlas.io.ogc.WKT.write` 있으며 함수를 사용하여 작성할 수 있습니다.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>잘 알려진 텍스트(WKT)를 읽고 쓰는 예

다음 코드는 잘 알려진 텍스트 문자열을 `POINT(-122.34009 47.60995)` 읽고 거품 레이어를 사용하여 맵에서 렌더링하는 방법을 보여 주어집니다.

<br/>

<iframe height='500' scrolling='no' title='잘 알려진 텍스트 읽기' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>잘 알려진 텍스트 읽기</a> 펜을 참조하십시오.
</iframe>

다음 코드는 잘 알려진 텍스트를 앞뒤로 읽고 쓰는 것을 보여 줍니다.

<br/>

<iframe height='700' scrolling='no' title='잘 알려진 텍스트 읽기 및 쓰기' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>펜 읽기 및 Azure 지도<a href='https://codepen.io/azuremaps'>@azuremaps</a>()별로 <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>잘 알려진 텍스트를 작성합니다.</a>
</iframe>

## <a name="read-and-write-gml"></a>GML 읽기 및 쓰기

GML은 다른 XML 사양의 확장자로 자주 사용되는 공간 XML 파일 사양입니다. GeoJSON 데이터는 `atlas.io.core.GmlWriter.write` 함수를 사용하여 GML 태그가 있는 XML로 작성할 수 있습니다. GML이 포함된 XML은 함수를 `atlas.io.core.GmlReader.read` 사용하여 읽을 수 있습니다. 읽기 함수에는 다음 두 가지 옵션이 있습니다.

- `isAxisOrderLonLat` 옵션 - 좌표의 축 순서 "위도, 경도" 또는 "경도, 위도"는 데이터 집합마다 다를 수 있으며 항상 잘 정의되지는 않습니다. 기본적으로 GML 판독기는 좌표 데이터를 "위도, 경도"로 읽지만 이 옵션을 true로 설정하면 "경도, 위도"로 읽힙니다.
- 옵션 `propertyTypes` - 이 옵션은 키가 데이터 집합의 속성 이름인 키 값 조회 테이블입니다. 값은 구문 분석 할 때 값을 캐스팅하는 개체 유형입니다. 지원되는 형식 값은 `number` `boolean`" `string` `date`및 . 속성이 조회 테이블에 없거나 형식이 정의되지 않은 경우 속성은 문자열로 구문 분석됩니다.

함수는 `atlas.io.read` 입력 데이터가 `atlas.io.core.GmlReader.read` XML임을 감지할 때 함수를 기본값으로 설정하지만 데이터는 다른 지원 공간 XML 형식 중 하나가 아닙니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [atlas.io 정적 함수](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [공간 데이터 세트](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [공간 데이터 세트 통계](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [Gml리더](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Gml라이터](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.io.ogc.WKT 기능](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [OGC 맵 레이어 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 운영 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
