---
title: 맵에 대한 데이터 원본 만들기 | 마이크로소프트 Azure 지도
description: 이 문서에서는 데이터 원본을 만들고 Microsoft Azure Maps Web SDK를 사용하여 맵에 추가하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190225"
---
# <a name="create-a-data-source"></a>데이터 소스 만들기

Azure Maps 웹 SDK는 데이터 원본에 데이터를 저장합니다. 데이터 원본을 사용하면 쿼리 및 렌더링을 위한 데이터 작업이 최적화됩니다. 현재 두 가지 유형의 데이터 원본이 있습니다.

**GeoJSON 데이터 소스**

GeoJSON 기반 데이터 원본로드 및 클래스를 `DataSource` 사용 하 여 로컬로 데이터를 저장 합니다. GeoJSON 데이터는 [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) 네임스페이스의 도우미 클래스를 사용하여 수동으로 만들거나 만들 수 있습니다. 클래스는 `DataSource` 로컬 또는 원격 GeoJSON 파일을 가져오는 기능을 제공합니다. 원격 GeoJSON 파일은 COR이 활성화된 엔드포인트에서 호스팅되어야 합니다. 클래스는 `DataSource` 포인트 데이터를 클러스터링하는 기능을 제공합니다. 또한 데이터를 쉽게 추가, 제거 및 `DataSource` 클래스로 업데이트할 수 있습니다.


> [!TIP]
> 의 모든 데이터를 덮어쓰려는 `DataSource`경우 를 가정해 보시면 됩니다. 다음 `clear` `add` 함수를 호출하면 맵이 두 번 다시 렌더링되어 약간의 지연이 발생할 수 있습니다. 대신 데이터 `setShapes` 원본의 모든 데이터를 제거하고 대체하고 맵의 단일 다시 렌더링만 트리거하는 함수를 사용합니다.

**벡터 타일 소스**

벡터 타일 소스는 벡터 타일 레이어에 액세스하는 방법을 설명합니다. [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) 클래스를 사용하여 벡터 타일 소스를 인스턴스화합니다. 벡터 타일 레이어는 타일 레이어와 유사하지만 동일하지는 않습니다. 타일 레이어는 래스터 이미지입니다. 벡터 타일 레이어는 PBF 형식으로 압축된 파일입니다. 이 압축된 파일에는 벡터 맵 데이터와 하나 이상의 레이어가 포함되어 있습니다. 파일은 각 레이어의 스타일에 따라 클라이언트에서 렌더링및 스타일을 만들 수 있습니다. 벡터 타일의 데이터에는 점, 선 및 다각형 의 형태로 지리적 피처가 포함되어 있습니다. 래스터 타일 레이어 대신 벡터 타일 레이어를 사용하는 데는 몇 가지 장점이 있습니다.

 - 벡터 타일의 파일 크기는 일반적으로 동등한 래스터 타일보다 훨씬 작습니다. 따라서 대역폭이 적게 사용됩니다. 즉, 대기 시간이 단축되고 맵이 빨라지며 사용자 환경이 향상됩니다.
 - 벡터 타일은 클라이언트에서 렌더링되므로 표시되는 장치의 해상도에 맞게 조정됩니다. 결과적으로 렌더링된 맵은 수정처럼 선명한 레이블로 더 잘 정의된 것처럼 보입니다.
 - 벡터 맵에서 데이터 스타일을 변경해도 클라이언트에 새 스타일을 적용할 수 있기 때문에 데이터를 다시 다운로드할 필요가 없습니다. 반대로 래스터 타일 레이어의 스타일을 변경하려면 일반적으로 서버에서 타일을 로드한 다음 새 스타일을 적용해야 합니다.
 - 데이터는 벡터 형식으로 전달되므로 데이터를 준비하는 데 필요한 서버 측 처리가 줄어듭니다. 따라서 최신 데이터를 더 빠르게 사용할 수 있습니다.

벡터 소스를 사용하는 모든 레이어는 `sourceLayer` 값을 지정해야 합니다.

생성되면 `map.sources` [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)입니다 속성을 통해 데이터 원본을 맵에 추가할 수 있습니다. 다음 코드는 를 `DataSource` 만들고 맵에 추가하는 방법을 보여 주며, 이 코드를 보여 주시면 됩니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure 맵은 열린 표준인 [맵박스 벡터 타일 사양을](https://github.com/mapbox/vector-tile-spec)준수합니다.

## <a name="connecting-a-data-source-to-a-layer"></a>데이터 원본을 레이어에 연결

렌더링 레이어를 사용하여 맵에서 데이터가 렌더링됩니다. 단일 데이터 원본은 하나 이상의 렌더링 레이어에서 참조할 수 있습니다. 다음 렌더링 레이어에는 데이터 원본이 필요합니다.

- [버블 레이어](map-add-bubble-layer.md) - 맵에서 포인트 데이터를 배율 조정된 원으로 렌더링합니다.
- [심볼 레이어](map-add-pin.md) - 포인트 데이터를 아이콘 또는 텍스트로 렌더링합니다.
- [히트 맵 레이어](map-add-heat-map-layer.md) - 포인트 데이터를 밀도 히트 맵으로 렌더링합니다.
- [선 레이어](map-add-shape.md) - 선을 렌더링하거나 다각형의 윤곽선을 렌더링합니다. 
- [다각형 레이어](map-add-shape.md) - 단색 또는 이미지 패턴으로 다각형 영역을 채웁니다.

다음 코드는 데이터 원본을 만들고 맵에 추가하고 거품 레이어에 연결하는 방법을 보여 주며 있습니다. 그런 다음 원격 위치에서 데이터 원본으로 GeoJSON 포인트 데이터를 가져옵니다. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

이러한 데이터 원본에 연결하지 않는 추가 렌더링 레이어가 있지만 렌더링을 위해 데이터를 직접 로드합니다. 

- [이미지 레이어](map-add-image-layer.md) - 맵 위에 단일 이미지를 오버레이하고 모서리를 지정된 좌표 집합에 바인딩합니다.
- [타일 레이어](map-add-tile-layer.md) - 맵 위에 래스터 타일 레이어를 중첩합니다.

## <a name="one-data-source-with-multiple-layers"></a>여러 레이어가 있는 하나의 데이터 원본

여러 계층을 단일 데이터 원본에 연결할 수 있습니다. 이 옵션이 유용한 여러 가지 시나리오가 있습니다. 예를 들어 사용자가 다각형을 그리는 시나리오를 생각해 보십시오. 사용자가 맵에 점을 추가함에 따라 다각형 영역을 렌더링하고 채우아야 합니다. 다각형의 윤곽을 그리는 스타일선을 추가하면 사용자가 그릴 때 다각형의 가장자리를 더 쉽게 볼 수 있습니다. 다각형의 개별 위치를 편리하게 편집하기 위해 각 위치 위에 핀이나 마커와 같은 핸들을 추가할 수 있습니다.

![단일 데이터 원본에서 데이터를 렌더링하는 여러 레이어를 보여주는 맵](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

대부분의 매핑 플랫폼에서는 다각형 개체, 선 오브젝트 및 다각형의 각 위치에 대한 핀이 필요합니다. 다각형이 수정되면 선과 핀을 수동으로 업데이트해야 하므로 빠르게 복잡해질 수 있습니다.

Azure Maps를 사용하면 아래 코드와 같이 데이터 원본의 단일 다각형만 있으면 됩니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [데이터 소스 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [벡터타일 소스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [벡터타일소스옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [기호 레이어 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [거품 레이어 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)