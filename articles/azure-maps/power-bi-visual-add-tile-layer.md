---
title: Azure Maps Power BI 시각적 개체에 타일 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure 지도 시각적 개체에서 타일 계층을 사용 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261941"
---
# <a name="add-a-tile-layer"></a>타일 레이어 추가

참조 계층 기능과 같은 타일 계층 기능을 사용 하면 더 많은 컨텍스트를 제공 하기 위해 지도에 추가 데이터를 중첩 시킬 수 있습니다. 타일 계층을 사용 하면 Azure Maps 기본 지도 타일 위에 이미지를 superimpose 수 있습니다. 이 방법은 드 론 또는 수백만 개의 데이터 행에서 이미지와 같은 크고 복잡 한 데이터 집합을 오버레이 하는 좋은 방법입니다.

> [!div class="mx-imgBorder"]
> ![Azure Maps에서 현재 적외선 날씨 데이터를 표시 하는 타일 계층 위의 거품형 계층을 표시 하는 지도입니다.](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

타일 계층이 서버에서 타일에 로드 됩니다. 이러한 이미지는 미리 렌더링 되거나 동적으로 렌더링 될 수 있습니다. 미리 렌더링 된 이미지는 타일 계층에서 인식 하는 명명 규칙을 사용 하 여 서버의 다른 이미지와 마찬가지로 저장 됩니다. 동적으로 렌더링 된 이미지는 서비스를 사용 하 여 실시간으로 가까운 이미지를 로드 합니다. 타일 계층은 지도에서 큰 데이터 집합을 시각화할 수 있는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 수 있을 뿐만 아니라 벡터 데이터도 타일 계층으로 렌더링 될 수 있습니다.

타일 서비스를 사용할 수 있는 경계 상자 및 확대/축소 범위는 타일이 요청 되는 위치를 제한 하는 설정으로 전달 될 수 있습니다. 이는 시각적 개체와 타일 서비스 모두에 대 한 성능 향상입니다. 다음은 **타일 계층** 섹션에서 사용할 수 있는 **서식** 창에서 사용할 수 있는 모든 설정에 대 한 개요입니다.

| 설정        | Description   |
|----------------|---------------|
| Url            | 타일 서비스를 가리키는 형식이 지정 된 URL입니다.  |
| 타일 크기      | 타일의 너비 및 높이 크기를 모두 지정 하는 정수 값입니다.   |
| 북 바운드    | 타일을 사용할 수 있는 경계 상자의 북쪽 위도입니다. |
| 남 바운드    | 타일을 사용할 수 있는 경계 상자의 남부 위도입니다. |
| 동부 bound     | 타일을 사용할 수 있는 경계 상자의 동부 경도입니다.  |
| 서 부 바인딩     | 타일을 사용할 수 있는 경계 상자의 서유럽어 경도입니다.   |
| 투명성   | 타일 계층의 투명도입니다.   |
| TMS         | 타일 지도 서비스-타일 계층의 Y 좌표 축에 반대 하는 사양입니다. |
| 최소 확대/축소       | 최소 확대/축소 수준 타일을 사용할 수 있습니다. |
| 최대 확대/축소       | 최대 확대/축소 수준 타일을 사용할 수 있습니다.  |
| 계층 위치 | 다른 지도 계층에 상대적인 계층의 위치를 지정 합니다. |

## <a name="tile-url-formatting"></a>타일 URL 서식 지정

Azure Maps 시각적 개체는 다음과 같은 세 가지 타일 서비스 명명 규칙을 지원 합니다.

-   **X, Y, Zoom 표기법** -X는 열이 고 Y는 타일 그리드에서 타일의 행 위치 이며 확대/축소는 확대/축소 수준을 기반으로 하는 값입니다.
-   **Quadkey notation** -x, y 및 zoom 정보를 단일 문자열 값으로 결합 합니다. 이 문자열 값은 단일 타일에 대 한 고유 식별자가 됩니다.
-   **경계 상자** -경계 상자 좌표 형식으로 이미지를 지정 합니다. `{west},{south},{east},{north}` 이 형식은 일반적으로 [WMS (웹 매핑 서비스)](https://www.opengeospatial.org/standards/wms)에서 사용 됩니다.

타일 url은 다음 매개 변수를 사용 하는 타일 URL 템플릿에 대 한 https URL을 사용 합니다.

-   `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
-   `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
-   `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
-   `{quadkey}` - `quadkey` Bing 지도 타일 시스템 명명 규칙을 기반으로 하는 타일 식별자입니다.
-   `{bbox-epsg-3857}` - `{west},{south},{east},{north}` EPSG 3857 공간 참조 시스템에서 형식의 경계 상자 문자열입니다.

예를 들어 다음은 Azure Maps의 [날씨 레이더 타일 서비스](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 에 대 한 서식이 지정 된 타일 URL입니다. 는 `[subscription-key]` Azure Maps 구독 키에 대 한 자리 표시자입니다.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

바둑판식 배열 시스템 Azure Maps에 대 한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

맵에 컨텍스트를 더 추가 합니다.

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)
