---
title: Android maps에 타일 계층 추가 | Microsoft Azure 맵
description: 지도에 타일 계층을 추가 하는 방법에 대해 알아봅니다. Azure Maps Android SDK를 사용 하 여 날씨 레이더 오버레이를 지도에 추가 하는 예제를 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608988"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>지도에 타일 계층 추가 (Android SDK)

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도에서 타일 계층을 렌더링 하는 방법을 보여 줍니다. 타일 계층을 사용하여 Azure Maps 기본 맵 타일의 위에 이미지를 겹칠 수 있습니다. Azure Maps의 타일 배치 체계에 대한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md) 설명서에서 볼 수 있습니다.

타일 계층이 서버에서 타일에 로드 됩니다. 이러한 이미지는 타일 계층에서 인식 하는 명명 규칙을 사용 하 여 미리 렌더링 하 고 서버의 다른 이미지와 마찬가지로 저장할 수 있습니다. 또는 이러한 이미지를 실시간으로 거의 이미지를 생성 하는 동적 서비스로 렌더링할 수 있습니다. Azure Maps TileLayer 클래스에서 지 원하는 세 가지 타일 서비스 명명 규칙은 다음과 같습니다.

* X, Y, 확대/축소 표기법-확대/축소 수준에 따라 타일 그리드에서 x는 열 위치이고 y는 행 위치입니다.
* Quadkey 표기법-x, y 조합은 정보를 타일의 고유 식별자인 단일 문자열 값으로 확대/축소합니다.
* 경계 상자-경계 상자 좌표는 `{west},{south},{east},{north}` [WMS (웹 매핑 서비스)](https://www.opengeospatial.org/standards/wms)에서 일반적으로 사용 되는 형식으로 이미지를 지정 하는 데 사용할 수 있습니다.

> [!TIP]
> TileLayer는 맵에서 큰 데이터 세트를 시각화하는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 있을 뿐 아니라 타일 계층으로 벡터 데이터도 렌더링할 수 있습니다. 지도 컨트롤은 벡터 데이터를 타일 계층으로 렌더링 하 여 타일을 로드 하기만 하면 됩니다. 이러한 타일은 파일 크기가 나타내는 벡터 데이터 보다 훨씬 작을 수 있습니다. 맵에서 데이터 행 수백만 개를 렌더링해야 하는 대부분의 사용자가 이 기술을 사용합니다.

타일 계층으로 전달된 타일 URL은 다음 매개 변수를 사용하는 TileJSON 리소스에 대한 http/https URL 또는 타일 URL 템플릿이어야 합니다.

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing Maps 타일 시스템 명명 규칙에 따라 타일 Quadkey 식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.
* `{subdomain}` -하위 도메인 값이 지정 된 경우 하위 도메인 값에 대 한 자리 표시자입니다.
* `azmapsdomain.invalid` -맵에 사용 되는 것과 동일한 값을 사용 하 여 도메인 및 타일 요청의 인증을 정렬 하는 자리 표시자입니다. Azure Maps에서 호스트 하는 타일 서비스를 호출할 때이를 사용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 프로세스를 완료 하려면 맵을 로드 하기 위해 [Azure Maps Android SDK](how-to-use-android-map-control-library.md) 를 설치 해야 합니다.

## <a name="add-a-tile-layer-to-the-map"></a>지도에 타일 계층 추가

이 샘플에서는 타일 집합을 가리키는 타일 계층을 만드는 방법을 보여 줍니다. 이 샘플에서는 "x, y, zoom" 배열 시스템을 사용 합니다. 이 타일 계층의 소스는 [OpenSeaMap 프로젝트](https://openseamap.org/index.php)이며,이 프로젝트에는 다양 한 원본 해리 차트가 포함 되어 있습니다. 타일 계층을 볼 때 종종 지도의 도시 레이블을 명확 하 게 볼 수 있는 것이 좋습니다. 이 동작은 지도 레이블 계층 아래에 타일 계층을 삽입 하 여 수행할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷은 진한 회색조 스타일을 포함 하는 지도에서 해리 정보의 타일 계층을 표시 하는 위의 코드를 보여 줍니다.

![타일 계층을 표시 하는 Android 맵](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC 웹 매핑 서비스 (WMS) 추가

WMTS (웹 매핑 서비스)는 지도 데이터의 이미지를 제공 하는 OGC (Open Geospatial Consortium) 표준입니다. 이 형식으로 사용할 수 있는 많은 개방형 데이터 집합은 Azure Maps에서 사용할 수 있습니다. 서비스가 `EPSG:3857` CRS (좌표 참조 시스템)를 지 원하는 경우이 유형의 서비스를 타일 계층과 함께 사용할 수 있습니다. WMS 서비스를 사용할 때 width 및 height 매개 변수를 서비스에서 지 원하는 것과 동일한 값으로 설정 합니다 .이 값은 옵션에서 동일 하 게 설정 해야 `tileSize` 합니다. 형식이 지정 된 URL에서 `BBOX` 서비스의 매개 변수를 자리 표시자로 설정 합니다 `{bbox-epsg-3857}` .

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷은 지도 위에 있는 [미국 Geological 설문 조사 (USGS)](https://mrdata.usgs.gov/) 의 geological 데이터의 웹 매핑 서비스를 레이블 아래에 겹치게 보여 줍니다.

![WMS 타일 계층을 표시 하는 Android 맵](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC 웹 매핑 타일 서비스 추가 (WMTS)

WMTS (웹 매핑 타일 서비스)는 맵의 타일 기반 오버레이를 제공 하기 위한 OGC (Open Geospatial Consortium) 표준입니다. 이 형식으로 사용할 수 있는 많은 개방형 데이터 집합은 Azure Maps에서 사용할 수 있습니다. 서비스가 `EPSG:3857` 또는 `GoogleMapsCompatible` CRS (좌표 참조 시스템)를 지 원하는 경우이 유형의 서비스를 타일 계층과 함께 사용할 수 있습니다. WMTS 서비스를 사용 하는 경우 width 및 height 매개 변수를 서비스에서 지 원하는 것과 동일한 값으로 설정 합니다 .이 값은 옵션에서 동일 하 게 설정 해야 `tileSize` 합니다. 형식이 지정 된 URL에서 다음 자리 표시자를 적절 하 게 바꿉니다.

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

다음 스크린샷은 지도 위쪽의 [미국 USGS () 국가 지도](https://viewer.nationalmap.gov/services/) 에서도로 및 레이블 아래에 있는 이미지의 웹 매핑 타일 서비스와 겹치지 않는 위의 코드를 보여 줍니다.

![WMTS 타일 계층을 표시 하는 Android 맵](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>다음 단계

지도에서 이미지를 오버레이 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [이미지 레이어](map-add-image-layer-android.md)
