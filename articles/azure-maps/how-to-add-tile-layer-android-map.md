---
title: Android maps에 타일 계층 추가 | Microsoft Azure 맵
description: 지도에 타일 계층을 추가 하는 방법에 대해 알아봅니다. Azure Maps Android SDK를 사용 하 여 날씨 레이더 오버레이를 지도에 추가 하는 예제를 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679302"
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

## <a name="prerequisites"></a>필수 조건

이 문서의 프로세스를 완료 하려면 맵을 로드 하기 위해 [Azure Maps Android SDK](how-to-use-android-map-control-library.md) 를 설치 해야 합니다.

## <a name="add-a-tile-layer-to-the-map"></a>지도에 타일 계층 추가

이 샘플에서는 타일 집합을 가리키는 타일 계층을 만드는 방법을 보여 줍니다. 이 샘플에서는 "x, y, zoom" 배열 시스템을 사용 합니다. 이 타일 계층의 소스는 [OpenSeaMap 프로젝트](https://openseamap.org/index.php)이며,이 프로젝트에는 다양 한 원본 해리 차트가 포함 되어 있습니다. 타일 계층을 볼 때 종종 지도의 도시 레이블을 명확 하 게 볼 수 있는 것이 좋습니다. 이 동작은 지도 레이블 계층 아래에 타일 계층을 삽입 하 여 수행할 수 있습니다.

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

다음 스크린샷은 진한 회색조 스타일을 포함 하는 지도에서 해리 정보의 타일 계층을 표시 하는 위의 코드를 보여 줍니다.

![타일 계층을 표시 하는 Android 맵](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>다음 단계

지도 스타일을 설정 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [지도 스타일 변경](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer-android.md)
