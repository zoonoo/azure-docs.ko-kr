---
title: Azure Maps Power BI 시각적 개체의 레이어 이해 | Microsoft Azure 맵
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure 지도 시각적 개체에서 사용할 수 있는 다양 한 계층에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261846"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Azure Maps Power BI 시각적 개체의 레이어 이해

Azure Maps 시각적 개체에는 두 가지 유형의 계층을 사용할 수 있습니다. 첫 번째 형식은 시각적 개체의 **필드** 창에 전달 되는 렌더링 데이터를 중심으로 하 고 다음 계층으로 구성 됩니다. 이러한 데이터 렌더링 계층을 호출 하겠습니다.

:::row:::
    :::column span="":::
        **거품형 계층**

        지도에서 크기를 조정 된 원으로 요소를 렌더링 합니다.

        ![지도의 거품형 계층](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **가로 막대형 차트 계층**

        지도에 3D 막대로 요소를 렌더링 합니다.
        
        ![지도에 가로 막대형 차트 계층](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

계층의 두 번째 유형은 더 많은 컨텍스트를 제공 하 고 다음 계층으로 구성 될 데이터의 외부 원본을 추가 합니다.

:::row:::
    :::column span="":::
        **참조 계층**

        업로드 된 GeoJSON 파일을 지도 위에 오버레이 합니다.

        ![맵의 참조 계층](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **타일 계층**

        지도 위에 사용자 지정 타일 계층을 오버레이 합니다.
        
        ![지도에 타일 계층](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **트래픽 계층**

        지도에 실시간 트래픽 정보를 오버레이 합니다.
        
        ![지도에서 트래픽 계층](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

모든 데이터 렌더링 계층 및 **타일 계층**은 확대/축소 수준 범위를 지정 하는 데 사용 되는 최소 및 최대 확대/축소 수준 옵션을 포함 하며 이러한 레이어는에 표시 되어야 합니다. 이를 통해 하나의 확대/축소 수준에서 하나의 렌더링 계층을 사용 하 고 다른 확대/축소 수준에서 다른 렌더링 계층으로 전환할 수 있습니다.

이러한 계층에는 맵의 다른 계층을 기준으로 위치를 지정 하는 옵션도 있습니다. 여러 데이터 렌더링 레이어를 사용 하는 경우 맵에 추가 되는 순서에 따라 동일한 **계층 위치** 값이 있는 경우에는 해당 계층의 상대 계층화 순서가 결정 됩니다.

## <a name="general-layer-settings"></a>일반 계층 설정

**서식** 창의 일반 계층 섹션은 **필드** 창 (거품형 계층, 가로 막대형 차트)의 Power BI 데이터 집합에 연결 된 레이어에 적용 되는 공통 설정입니다.

| 설정     | 설명   |
|-------------|---------------|
| 선택 하지 않은 투명도 | 하나 이상의 셰이프를 선택 하는 경우 선택 되지 않은 셰이프의 투명도입니다.  |
| 0 표시              | 크기 값이 0 인 요소가 최소 반지름을 사용 하 여 맵에 표시 되어야 하는지 여부를 지정 합니다. |
| 네거티브 표시          | 음수 크기 값의 절대값을 그릴지 여부를 지정 합니다.   |
| Min 데이터 값          | 크기를 조정할 입력 데이터의 최소값입니다. 이상 값 클리핑에 적합 합니다.  |
| 최대 데이터 값          | 크기를 조정할 입력 데이터의 최대값입니다. 이상 값 클리핑에 적합 합니다.  |

## <a name="next-steps"></a>다음 단계

지도에 데이터가 표시 되는 방식을 변경 합니다.

> [!div class="nextstepaction"]
> [거품형 계층 추가](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [가로 막대형 차트 레이어 추가](power-bi-visual-add-bar-chart-layer.md)

맵에 컨텍스트를 더 추가 합니다.

> [!div class="nextstepaction"]
> [참조 레이어 추가](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [타일 레이어 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)
