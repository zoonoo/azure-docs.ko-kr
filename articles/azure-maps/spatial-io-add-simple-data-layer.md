---
title: 단순 데이터 계층 추가 | Microsoft Azure 맵
description: Azure Maps Web SDK에서 제공 하는 공간 IO 모듈을 사용 하 여 간단한 데이터 계층을 추가 하는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 61272bee350fc7c7dd7d0e17adc55c436f4706ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885956"
---
# <a name="add-a-simple-data-layer"></a>단순 데이터 레이어 추가

공간 IO 모듈은 클래스를 제공 합니다 `SimpleDataLayer` . 이 클래스를 사용 하면 지도의 스타일 기능을 쉽게 렌더링할 수 있습니다. 또한 혼합 된 기 하 도형 유형을 포함 하는 데이터 집합 및 스타일 속성을 포함 하는 데이터 집합을 렌더링할 수 있습니다. 단순 데이터 계층은 여러 렌더링 계층을 래핑하고 스타일 식을 사용 하 여이 기능을 달성 합니다. 스타일 식은 이러한 래핑된 계층 안에 있는 기능의 공용 스타일 속성을 검색 합니다. `atlas.io.read`함수 및 함수는 `atlas.io.write` 이러한 속성을 사용 하 여 지원 되는 파일 형식으로 스타일을 읽고 씁니다. 지원 되는 파일 형식에 속성을 추가한 후에는이 파일을 다양 한 용도로 사용할 수 있습니다. 예를 들어 파일을 사용 하 여 지도의 스타일이 지정 된 기능을 표시할 수 있습니다.

에서는 기능 스타일 지정 외에도 `SimpleDataLayer` 팝업 템플릿을 사용 하 여 기본 제공 팝업 기능을 제공 합니다. 기능이 클릭 되 면 팝업이 표시 됩니다. 원하는 경우 기본 팝업 기능을 사용 하지 않도록 설정할 수 있습니다. 이 계층은 클러스터 된 데이터도 지원 합니다. 클러스터를 클릭 하면 맵이 클러스터로 확대 되 고 개별 요소 및 하위 클러스터로 확장 됩니다.

`SimpleDataLayer`이 클래스는 여러 기 하 도형 형식과 기능에 적용 되는 많은 스타일을 포함 하는 대량 데이터 집합에서 사용 하기 위한 것입니다. 이 클래스는 사용 되는 경우 스타일 식이 포함 된 6 개 계층의 오버 헤드를 추가 합니다. 따라서 핵심 렌더링 계층을 사용 하는 것이 더 효율적일 수 있습니다. 예를 들어 핵심 계층을 사용 하 여 기능에 몇 가지 geometry 형식 및 몇 가지 스타일을 렌더링 합니다.

## <a name="use-a-simple-data-layer"></a>단순 데이터 계층 사용

`SimpleDataLayer`클래스는 다른 렌더링 계층이 사용 되는 것과 같은 방식으로 사용 됩니다. 아래 코드는 map에서 단순 데이터 계층을 사용 하는 방법을 보여 줍니다.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

데이터 원본에 기능을 추가 합니다. 그런 다음 단순 데이터 계층을 통해 기능을 렌더링 하는 방법을 파악할 수 있습니다. 개별 기능에 대 한 스타일을 기능 속성으로 설정할 수 있습니다. 다음 코드에서는 속성이로 설정 된 GeoJSON point 기능을 보여 줍니다 `color` `red` . 

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

다음 코드에서는 단순 데이터 계층을 사용 하 여 위의 point 기능을 렌더링 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="단순 데이터 계층 사용" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> CodePen에서 펜 Azure Maps ()를 사용 하 여 <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>간단한 데이터 계층 사용</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

간단한 데이터 계층의 실제 강력한 기능에는 다음이 제공 됩니다.

- 데이터 원본에는 여러 가지 유형의 기능이 있습니다. 디스크나
- 데이터 집합의 기능에는 개별적으로 설정 된 여러 가지 스타일 속성이 있습니다. 디스크나
- 데이터 집합에 정확 하 게 포함 된 내용이 확실 하지 않습니다.

예를 들어 XML 데이터 피드를 구문 분석할 때 기능의 정확한 스타일 및 기 하 도형 유형을 모를 수 있습니다. 다음 샘플에서는 KML 파일의 기능을 렌더링 하 여 간단한 데이터 계층의 강력한 기능을 보여 줍니다. 또한 단순 데이터 계층 클래스에서 제공 하는 다양 한 옵션을 보여 줍니다.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="단순 데이터 계층 옵션" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>단순 데이터 계층 옵션</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>


> [!NOTE]
> 이 간단한 데이터 계층은 [popup 템플릿](map-add-popup.md#add-popup-templates-to-the-map) 클래스를 사용 하 여 KML 풍선을 표시 하거나 기능 속성을 표로 표시 합니다. 기본적으로 팝업에서 렌더링 된 모든 콘텐츠는 보안 기능으로 iframe 내에서 샌드 박싱 됩니다. 그러나 다음과 같은 제한 사항이 있습니다.
>
> - 모든 스크립트, 폼, 포인터 잠금 및 상위 탐색 기능을 사용할 수 없습니다. 링크를 클릭 하면 새 탭에서 링크를 열 수 있습니다. 
> - Iframe에서 매개 변수를 지원 하지 않는 이전 브라우저 `srcdoc` 는 적은 양의 콘텐츠를 렌더링 하도록 제한 됩니다.
> 
> 팝업에 로드 되는 데이터를 신뢰 하 고 잠재적으로 popup에 로드 된 이러한 스크립트가 응용 프로그램에 액세스할 수 있도록 하려면 popup template 옵션을 false로 설정 하 여이 기능을 사용 하지 않도록 설정할 수 있습니다 `sandboxContent` . 

## <a name="default-supported-style-properties"></a>지원 되는 기본 스타일 속성

앞에서 설명한 것 처럼 단순 데이터 계층은 거품형, 기호, 선, 다각형 및 돌출 다각형의 몇 가지 핵심 렌더링 계층을 래핑합니다. 그런 다음 식을 사용 하 여 개별 기능에 대해 유효한 스타일 속성을 검색 합니다.

Azure Maps 및 GitHub 스타일 속성은 지원 되는 속성 이름의 두 가지 주요 집합입니다. 여러 Azure maps 계층 옵션의 속성 이름은 대부분 단순 데이터 계층에서 기능의 스타일 속성으로 지원 됩니다. GitHub에서 일반적으로 사용 되는 스타일 속성 이름을 지원 하기 위해 일부 레이어 옵션에 식이 추가 되었습니다. 이러한 속성 이름은 [GitHub의 GeoJSON map 지원](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)으로 정의 되며 플랫폼 내에서 저장 되 고 렌더링 되는 GeoJSON 파일의 스타일을 지정 하는 데 사용 됩니다. 스타일 속성을 제외 하 고 모든 GitHub의 스타일 속성은 단순 데이터 계층에서 지원 됩니다 `marker-symbol` .

더 작은 공용 스타일 속성에서 판독기가 제공 되는 경우 가장 가까운 Azure Maps style 속성으로 변환 됩니다. 또한 `getLayers` 단순 데이터 계층의 함수를 사용 하 여 계층의 옵션을 업데이트 하면 기본 스타일 식이 재정의 될 수 있습니다.

다음 섹션에서는 단순 데이터 계층에서 지원 되는 기본 스타일 속성에 대해 자세히 설명 합니다. 지원 되는 속성 이름의 순서 역시 속성의 우선 순위입니다. 같은 계층 옵션에 대해 두 개의 스타일 속성이 정의 된 경우 목록의 첫 번째 속성이 우선 순위가 높습니다. 색은 임의의 CSS3 색 값일 수 있습니다. HEX, RGB, RGBA, HSL, HSLA 또는 명명 된 색 값입니다.

### <a name="bubble-layer-style-properties"></a>거품형 계층 스타일 속성

기능이 `Point` 또는이 `MultiPoint` 고 해당 기능에 `image` 사용자 지정 아이콘으로 사용 되는 속성이 없는 경우 해당 요소를 기호로 렌더링 하면이 기능이로 렌더링 됩니다 `BubbleLayer` .

| 계층 옵션 | 지원 되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] `size` 및 `scale` 값은 스칼라 값으로 간주 되며 다음에 곱해집니다.`8`

\[2 \] GitHub `marker-size` 옵션을 지정 하면 다음 값이 반경에 사용 됩니다.

| 표식 크기 | 반지름 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

클러스터도 거품형 계층을 사용 하 여 렌더링 됩니다. 기본적으로 클러스터의 radius는로 설정 됩니다 `16` . 클러스터의 색은 아래에 정의 된 대로 클러스터의 요소 수에 따라 달라 집니다.

| 점수 | 색상    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;5-10     | `green`  |

### <a name="symbol-style-properties"></a>기호 스타일 속성

기능이 또는이 고 및 `Point` 기능에 `MultiPoint` `image` 사용자 지정 아이콘으로 사용 되는 속성이 있는 경우이 기능은를 사용 하 여 렌더링 됩니다 `SymbolLayer` .

| 계층 옵션 | 지원 되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] GitHub 옵션을 `marker-size` 지정 하면 아이콘 크기 옵션에 다음 값이 사용 됩니다.

| 표식 크기 | 기호 크기 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Point 기능이 클러스터 인 경우 `point_count_abbreviated` 속성은 텍스트 레이블로 렌더링 됩니다. 이미지가 렌더링 되지 않습니다.

### <a name="line-style-properties"></a>선 스타일 속성

기능이,, 또는 이면 `LineString` 이 `MultiLineString` `Polygon` `MultiPolygon` 기능은를 사용 하 여 렌더링 됩니다 `LineLayer` .

| 계층 옵션 | 지원 되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Polygon 스타일 속성

기능이 `Polygon` 또는이 `MultiPolygon` 고 기능에 속성이 없거나 속성이 0 인 경우이 `height` `height` 기능은를 사용 하 여 렌더링 됩니다 `PolygonLayer` .

| 계층 옵션 | 지원 되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>돌출 다각형 스타일 속성

기능이 `Polygon` 또는이 `MultiPolygon` 고 `height` 값이 0 보다 큰 속성이 있는 경우이 기능은를 사용 하 여 렌더링 됩니다 `PolygonExtrusionLayer` .

| 계층 옵션 | 지원 되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC 지도 계층 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
