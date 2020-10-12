---
title: Azure Maps Power BI 시각적 개체에 거품형 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure 지도 시각적 개체에서 거품형 계층을 사용 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4443b0f479079a4722a5d62fea40afcb4a58632d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261936"
---
# <a name="add-a-bubble-layer"></a>거품형 레이어 추가

**거품형 계층** 은 지도에서 크기 조정 된 원으로 위치 데이터를 렌더링 합니다.

> [!div class="mx-imgBorder"]
> ![거품형 계층을 사용 하 여 점 데이터를 표시 하는 지도](media/power-bi-visual/bubble-layer-with-legend-color.png)

처음에는 모든 거품의 채우기 색이 동일 합니다. 필드가 **필드** 창의 **범례** 버킷으로 전달 되는 경우에는 해당 분류에 따라 거품이 색이 지정 됩니다. 거품 윤곽선은 흰색 이지만 새 색으로 변경 하거나 고대비 윤곽선 옵션을 사용 하도록 설정할 수 있습니다. 고대비 **윤곽선** 옵션은 채우기 색의 고대비 변형 인 윤곽선 색을 동적으로 할당 합니다. 이렇게 하면 지도의 스타일에 관계 없이 거품이 명확 하 게 표시 되도록 할 수 있습니다. 다음은 **거품형 계층** 섹션에서 사용할 수 있는 **서식** 창의 기본 설정입니다.

| 설정               | 설명    |
|-----------------------|----------------|
| 크기                  | 각 거품의 크기입니다. 이 옵션은 필드 창의 **크기** **버킷에 필드를** 전달할 때 숨겨집니다. 추가 옵션은이 문서의 아래쪽에 있는 [거품형 크기 조정](#bubble-size-scaling) 토픽에 설명 된 대로 표시 됩니다. |
| 채우기 색            | 각 거품의 색입니다. **필드 창의** **범례** 버킷에 필드를 전달 하 고 별도의 **데이터 색** 섹션이 **서식** 창에 표시 되는 경우이 옵션은 숨겨집니다. |
| 채우기 투명도     | 각 거품의 투명도입니다. |
| 고대비 윤곽선 | 채우기 색의 고대비 변형을 사용 하 여 접근성을 개선 하기 위해 윤곽선 색을 채우기 색과 대조 합니다. |
| 윤곽선 색         | 거품을 윤곽선으로 나타낸 색입니다. 고대비 **윤곽선** 옵션을 사용 하는 경우이 옵션은 숨겨집니다. |
| 윤곽선 투명도  | 윤곽선의 투명도입니다. |
| 윤곽선 두께         | 윤곽선의 너비 (픽셀)입니다. |
| 흐리게                  | 윤곽선에 적용 되는 흐림 효과의 양입니다. 값이 1 이면 중심 지점만 투명도가 없도록 거품을 흐리게 합니다. 값 0은 모든 흐림 효과를 적용 합니다. |
| 피치 맞춤       | 지도가 표시 될 때 거품의 모양을 지정 합니다. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Viewport-거품이 뷰포트를 기준으로 지도의 가장자리에 표시 됩니다. (기본값)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 지도-거품이 지도의 표면에서 평면으로 렌더링 됩니다. |
| 확대/축소 배율            | 확대/축소 수준에 비례하여 거품의 크기를 조정 해야 하는 양입니다. 확대/축소 눈금은 크기 조정이 없음을 의미 합니다. 값이 클수록 확대 될 때 거품이 확대 되 고 확대 되 면 더 작게 됩니다. 이를 통해 축소 된 경우에는 지도를 축소 하는 데 도움이 되지만, 확대 시에는 점수가 더 복잡해 집니다. 값 1은 크기 조정을 적용 하지 않습니다. |
| 최소 확대/축소              | 최소 확대/축소 수준 타일을 사용할 수 있습니다. |
| 최대 확대/축소              | 최대 확대/축소 수준 타일을 사용할 수 있습니다. |
| 계층 위치        | 다른 지도 계층에 상대적인 계층의 위치를 지정 합니다. |

## <a name="bubble-size-scaling"></a>거품 크기 조정

**필드 창의** **크기** 버킷에 필드를 전달 하는 경우 거품은 각 데이터 요소의 측정값 값에 상대적으로 크기가 조정 됩니다. **서식** 창의 **거품형 계층** 섹션에서 **크기** 옵션은 필드를 **크기** 버킷에 전달할 때 사라집니다. 그러면 거품의 반지름이 min과 max 값 사이에서 크기가 조정 됩니다. **크기** 버킷에 지정 된 필드가 있는 경우 **서식** 창의 **거품형 계층** 섹션에 다음 옵션이 표시 됩니다.

| 설정             | 설명  |
|---------------------|--------------|
| 최소 크기            | 데이터 크기를 조정할 때 최소 거품 크기입니다.|
| 최대 크기            | 데이터 크기를 조정할 때의 최대 거품 크기입니다.|
| 크기 조정 방법 | 상대적 거품형 크기를 결정 하는 데 사용 되는 크기 조정 알고리즘입니다.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• 선형-최소 및 최대 크기에 선형으로 매핑되는 입력 데이터의 범위입니다. (기본값)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 로그-최소 및 최대 크기에 매핑되는 입력 데이터 로그의 범위입니다.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier-Cubic-Bezier 곡선의 X1, Y1, X2, Y2 값을 지정 하 여 사용자 지정 크기 조정 메서드를 만듭니다. |

**크기 조정 방법이** **Log**로 설정 된 경우 다음 옵션을 사용할 수 있습니다.

| 설정   | 설명      |
|-----------|------------------|
| 로그 눈금 | 거품의 크기를 계산할 때 적용할 로그 눈금 간격입니다. |

**크기 조정 방법을** **입방-Bezier**로 설정 하면 크기 조정 곡선을 사용자 지정 하기 위해 다음 옵션을 사용할 수 있게 됩니다.

| 설정 | 설명                           |
|---------|---------------------------------------|
| X      | 입방 형 3 차원 곡선의 X1 매개 변수입니다. |
| Y1      | 입방 형 3 차원 곡선의 X2 매개 변수입니다. |
| X2      | 입방 형 3 차원 곡선의 Y1 매개 변수입니다. |
| Y2      | 입방 형 3 차원 곡선의 Y2 매개 변수입니다. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) 에는 Cubic-Bezier 곡선에 대 한 매개 변수를 만드는 데 유용한 도구가 있습니다.

## <a name="next-steps"></a>다음 단계

지도에 데이터가 표시 되는 방식을 변경 합니다.

> [!div class="nextstepaction"]
> [가로 막대형 차트 레이어 추가](power-bi-visual-add-bar-chart-layer.md)

맵에 컨텍스트를 더 추가 합니다.

> [!div class="nextstepaction"]
> [참조 레이어 추가](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [타일 레이어 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)

시각적 개체를 사용자 지정 합니다.

> [!div class="nextstepaction"]
> [Power BI의 색 서식을 위한 팁과 힌트](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [시각화 제목, 배경 및 범례 사용자 지정](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)