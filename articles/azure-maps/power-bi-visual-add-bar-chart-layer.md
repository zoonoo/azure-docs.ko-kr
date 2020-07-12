---
title: Azure Maps Power BI 시각적 개체에 가로 막대형 차트 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure 지도 시각적 개체에서 가로 막대형 차트 계층을 사용 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 34d2d96e28f90249ad25788f6994dac63f83b1f6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261951"
---
# <a name="add-a-bar-chart-layer"></a>가로 막대형 차트 계층 추가

**가로 막대형 차트 계층** 은 지도에서 위치 데이터의 시각화를 3d 막대나 원통으로 허용 하 여 다음 차원으로 데이터를 가져오는 데 유용 합니다. 거품형 계층과 마찬가지로 나중에 가로 막대형 차트는 색 및 상대 높이를 사용 하 여 두 메트릭을 동시에 쉽게 시각화할 수 있습니다. 막대가 높이가 되도록 하려면 **필드** 창의 **크기** 버킷에 측정값을 추가 해야 합니다. 측정값이 제공 되지 않으면 **가로 막대형 셰이프** 옵션에 따라 높이가 없는 막대가 플랫 사각형 또는 원으로 표시 됩니다.

> [!div class="mx-imgBorder"]
> ![가로 막대형 차트 계층을 사용 하 여 점 데이터를 표시 하는 지도](media/power-bi-visual/bar-chart-layer-styled.png)

사용자는 지도를 기울기 및 회전 하 여 여러 관점에서 데이터를 볼 수 있습니다. 다음 방법 중 하나를 사용 하 여 지도를 기울임 또는 고주파음 할 수 있습니다.

-   **서식** 창의 **맵 설정** 에서 **탐색 컨트롤** 옵션을 설정 합니다. 그러면 지도를 기울이는 단추가 추가 됩니다.
-   마우스 오른쪽 단추를 클릭 하 고 마우스를 위아래로 끕니다.
-   터치 스크린을 사용하여 맵을 두 손가락으로 터치하고 위쪽 또는 아래쪽으로 함께 끕니다.
-   지도에 포커스가 있는 상태에서 **Shift** 키를 누른 상태에서 **위쪽** 또는 **아래쪽 화살표** 키를 누릅니다.

다음 방법 중 하나를 사용 하 여 맵을 회전할 수 있습니다.

-   **서식** 창의 **맵 설정** 에서 **탐색 컨트롤** 옵션을 설정 합니다. 지도를 회전 하는 단추를 추가 합니다.
-   마우스 오른쪽 단추를 누르고 마우스를 왼쪽 이나 오른쪽으로 끕니다.
-   터치 스크린을 사용하여 두 손가락으로 맵을 터치하고 회전합니다.
-   지도에 포커스가 있는 상태에서 **Shift** 키를 누른 채 **왼쪽** 또는 **오른쪽 화살표** 키를 누릅니다.

다음은 **가로 막대형 차트 계층** 섹션에서 사용할 수 있는 **서식** 창의 모든 설정입니다.

| 설정              | 설명      |
|----------------------|------------------|
| 가로 막대형 도형            | 3D 막대의 모양입니다.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box – 사각형 상자로 렌더링 됩니다.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 원통형 – 원통으로 렌더링 된 가로 막대형입니다. |
| 높이               | 각 막대의 높이입니다. 필드가 **필드** 창의 **크기** 버킷에 전달 되는 경우이 높이 값에 비례하여 막대가 배율 조정 됩니다. |
| 확대/축소 시 높이 조정 | 가로 막대의 높이가 확대/축소 수준에 따라 조정 되어야 하는지 여부를 지정 합니다. |
| 너비                | 각 막대의 너비입니다.  |
| 확대/축소 시 너비 조정  | 막대의 너비를 확대/축소 수준을 기준으로 조정할지 여부를 지정 합니다.  |
| 채우기 색           | 각 막대의 색입니다. **필드 창의** **범례** 버킷에 필드를 전달 하 고 별도의 **데이터 색** 섹션이 **서식** 창에 표시 되는 경우이 옵션은 숨겨집니다. |
| 투명성         | 각 막대의 투명도입니다. |
| 최소 확대/축소             | 최소 확대/축소 수준 타일을 사용할 수 있습니다. |
| 최대 확대/축소             | 최대 확대/축소 수준 타일을 사용할 수 있습니다. |
| 계층 위치       | 다른 지도 계층에 상대적인 계층의 위치를 지정 합니다. |

> [!NOTE]
> 막대가 작은 너비 값을 가지 며 **확대/축소 시 확대/축소** 옵션을 사용 하지 않도록 설정 하면 렌더링 된 너비의 크기가 픽셀 보다 작게 확대 될 때 사라질 수 있습니다. 그러나 **확대/축소 확대/축소** 옵션을 사용 하는 경우 확대/축소 수준을 변경 하면 큰 데이터 집합의 성능에 영향을 줄 수 있는 추가 계산이 수행 됩니다.

## <a name="next-steps"></a>다음 단계

맵에 컨텍스트를 더 추가 합니다.

> [!div class="nextstepaction"]
> [참조 계층 추가](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [타일 계층 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)

시각적 개체를 사용자 지정 합니다.

> [!div class="nextstepaction"]
> [Power BI의 색 서식을 위한 팁과 힌트](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [시각화 제목, 배경 및 범례 사용자 지정](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)