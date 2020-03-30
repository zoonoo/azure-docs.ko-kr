---
title: 간단한 데이터 계층 추가 | 마이크로소프트 Azure 지도
description: Azure Maps Web SDK에서 제공하는 공간 IO 모듈을 사용하여 간단한 데이터 계층을 추가하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7671d07a468a9f67a4851ec828fe18896d7a6c66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334274"
---
# <a name="add-a-simple-data-layer"></a>간단한 데이터 계층 추가

공간 IO 모듈은 `SimpleDataLayer` 클래스를 제공합니다. 이 클래스를 사용하면 맵에서 스타일이 있는 피처를 쉽게 렌더링할 수 있습니다. 스타일 속성이 있는 데이터 집합과 혼합 형상 유형이 포함된 데이터 집합을 렌더링할 수도 있습니다. 간단한 데이터 계층은 여러 렌더링 레이어를 래핑하고 스타일 표현식을 사용하여 이 기능을 구현합니다. 스타일 표현식은 이러한 래핑된 레이어 내부의 피처의 공통 스타일 속성을 검색합니다. 함수와 `atlas.io.read` `atlas.io.write` 함수는 이러한 속성을 사용하여 스타일을 지원되는 파일 형식으로 읽고 씁니다. 지원되는 파일 형식에 속성을 추가한 후 다양한 용도로 파일을 사용할 수 있습니다. 예를 들어 파일을 사용하여 맵에 스타일이 있는 피처를 표시할 수 있습니다.

스타일링 기능 외에도 팝업 `SimpleDataLayer` 템플릿이 내장된 팝업 기능을 제공합니다. 피처를 클릭하면 팝업이 표시됩니다. 원하는 경우 기본 팝업 기능을 사용하지 않도록 선택할 수 있습니다. 이 계층은 클러스터된 데이터도 지원합니다. 클러스터를 클릭하면 맵이 클러스터로 확대되어 개별 지점 및 하위 클러스터로 확장됩니다.

이 `SimpleDataLayer` 클래스는 형상 유형이 많고 피쳐에 많은 스타일이 적용된 대규모 데이터 집합에 사용됩니다. 이 클래스를 사용하면 스타일 표현식이 포함된 6개의 레이어의 오버헤드가 추가됩니다. 따라서 코어 렌더링 레이어를 사용하는 것이 더 효율적인 경우가 있습니다. 예를 들어 코어 레이어를 사용하여 피쳐에서 몇 가지 형상 유형과 몇 가지 스타일을 렌더링합니다.

## <a name="use-a-simple-data-layer"></a>간단한 데이터 계층 사용

클래스는 `SimpleDataLayer` 다른 렌더링 레이어가 사용되는 것처럼 사용됩니다. 아래 코드는 맵에서 간단한 데이터 레이어를 사용하는 방법을 보여 주며 다음과 같습니다.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

데이터 원본에 피처를 추가합니다. 그런 다음 간단한 데이터 계층에서 피처를 렌더링하는 가장 좋은 방법을 알아내게 됩니다. 개별 피쳐에 대한 스타일은 피쳐의 속성으로 설정할 수 있습니다. 다음 코드는 `color` 속성이 로 설정된 GeoJSON `red`점 피쳐를 보여 주며 있습니다. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

다음 코드는 간단한 데이터 계층을 사용하여 위의 점 기능을 렌더링합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="단순 데이터 계층 사용" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> <a href='https://codepen.io'>펜 코드펜의</a>Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>간단한 데이터 레이어 사용</a> 참조 .
</iframe>

간단한 데이터 계층의 진정한 힘은 다음과 같은 경우에 발생합니다.

- 데이터 원본에는 여러 가지 유형의 기능이 있습니다. 또는
- 데이터 집합의 피쳐에는 여러 스타일 속성이 개별적으로 설정되어 있습니다. 또는
- 데이터 집합에 정확히 무엇이 포함되어 있는지 확실하지 않습니다.

예를 들어 XML 데이터 피드를 구문 분석할 때 피쳐의 정확한 스타일과 형상 유형을 알 수 없습니다. 다음 샘플에서는 KML 파일의 기능을 렌더링하여 간단한 데이터 레이어의 기능을 보여 주며, 이 중 의 경우 또한 간단한 데이터 계층 클래스가 제공하는 다양한 옵션을 보여 줍니다.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="간단한 데이터 계층 옵션" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> <a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>단순 데이터 계층 옵션을</a> 참조하십시오.
</iframe>


> [!NOTE]
> 이 간단한 데이터 계층은 [팝업 템플릿](map-add-popup.md#add-popup-templates-to-the-map) 클래스를 사용하여 KML 풍선 또는 피쳐 속성을 테이블로 표시합니다. 기본적으로 팝업에서 렌더링되는 모든 콘텐츠는 보안 기능으로 iframe 내부에 샌드박스됩니다. 그러나 다음과 같은 제한 사항이 있습니다.
>
> - 모든 스크립트, 양식, 포인터 잠금 및 상위 탐색 기능은 사용할 수 없습니다. 링크를 클릭하면 새 탭에서 열 수 있습니다. 
> - iframe에서 매개 변수를 `srcdoc` 지원하지 않는 이전 브라우저는 소량의 콘텐츠를 렌더링하는 것으로 제한됩니다.
> 
> 팝업에 로드되는 데이터를 신뢰하고 팝업에 로드된 이러한 스크립트가 응용 프로그램에 액세스할 수 있도록 하려면 팝업 템플릿 `sandboxContent` 옵션을 false로 설정하여 이 옵션을 비활성화할 수 있습니다. 

## <a name="default-supported-style-properties"></a>기본 지원 스타일 속성

앞에서 설명한 것처럼 간단한 데이터 계층은 버블, 기호, 선, 다각형 및 돌출 다각형과 같은 여러 핵심 렌더링 레이어를 래핑합니다. 그런 다음 식을 사용하여 개별 피처에서 유효한 스타일 속성을 검색합니다.

Azure Maps 및 GitHub 스타일 속성은 지원되는 속성 이름의 두 가지 주요 집합입니다. 다른 Azure 맵 레이어 옵션의 대부분의 속성 이름은 단순 데이터 레이어에서 피처의 스타일 속성으로 지원됩니다. GitHub에서 일반적으로 사용되는 스타일 속성 이름을 지원하기 위해 일부 레이어 옵션에 식이 추가되었습니다. 이러한 속성 이름은 [GitHub의 GeoJSON 맵 지원에](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)의해 정의되며 플랫폼 내에 저장되고 렌더링되는 GeoJSON 파일의 스타일을 지정하는 데 사용됩니다. GitHub의 모든 스타일 속성은 `marker-symbol` 스타일 지정 속성을 제외한 간단한 데이터 계층에서 지원됩니다.

판독기가 덜 일반적인 스타일 속성을 가로질러 오면 가장 가까운 Azure Maps 스타일 속성으로 변환됩니다. 또한 간단한 데이터 계층의 `getLayers` 함수를 사용하고 레이어의 옵션을 업데이트하여 기본 스타일 표현식을 재정의할 수 있습니다.

다음 섹션에서는 단순 데이터 계층에서 지원하는 기본 스타일 속성에 대한 세부 정보를 제공합니다. 지원되는 속성 이름의 순서도 속성의 우선 순위입니다. 동일한 레이어 옵션에 대해 두 스타일 속성이 정의된 경우 목록의 첫 번째 스타일 속성의 우선 순위가 더 높습니다.

### <a name="bubble-layer-style-properties"></a>버블 레이어 스타일 특성

피쳐가 a `Point` 또는 `MultiPoint`a이고 피쳐에 점을 `image` 기호로 렌더링하기 위한 사용자 지정 아이콘으로 사용되는 속성이 없는 경우 피쳐는 `BubbleLayer`로 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1,</sup> `marker-size` <sup>2,</sup> `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` 및 `scale` 값은 스칼라 값으로 간주되며 값에 곱해지됩니다.`8`

\[2\] GitHub `marker-size` 옵션을 지정하면 반지름에 다음 값이 사용됩니다.

| 표식 크기 | 반지름 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

클러스터는 거품 레이어를 사용하여 렌더링됩니다. 기본적으로 클러스터의 반지름은 `16`로 설정됩니다. 클러스터의 색상은 아래에 정의된 대로 클러스터의 포인트 수에 따라 달라집니다.

| 포인트 의 # | 색    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>기호 스타일 속성

피쳐가 a `Point` 또는 `MultiPoint`a이고 피쳐가 `image` 있고 점을 기호로 렌더링하기 위한 사용자 지정 아이콘으로 사용되는 속성이 있는 `SymbolLayer`경우 피쳐는 로 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] GitHub `marker-size` 옵션을 지정하면 아이콘 크기 옵션에 다음 값이 사용됩니다.

| 표식 크기 | 기호 크기 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

점 피쳐가 클러스터인 `point_count_abbreviated` 경우 속성은 텍스트 레이블로 렌더링됩니다. 이미지가 렌더링되지 않습니다.

### <a name="line-style-properties"></a>선 스타일 속성

피쳐가 `LineString`"? `MultiLineString` `Polygon` `MultiPolygon` `LineLayer`

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>다각형 스타일 속성

피쳐가 a `Polygon` 또는 `MultiPolygon`a이고 피쳐에 `height` 속성이 없거나 `height` 속성이 0이면 피쳐가 `PolygonLayer`로 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>돌출 된 다각형 스타일 속성

피쳐가 a `Polygon` 또는 `MultiPolygon`a이고 `height` 값이 0보다 큰 속성이 있는 경우 `PolygonExtrusionLayer`피쳐는 로 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [단순 데이터 레이어](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [단순 데이터 레이어 옵션](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC 맵 레이어 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 운영 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
