---
title: 지도에 그리기 도구 모음 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 그리기 도구 모음을 추가하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804676"
---
# <a name="drawing-tool-events"></a>그리기 도구 이벤트

맵에서 그리기 도구를 사용할 때는 사용자가 맵에 그릴 때 특정 이벤트에 반응하는 것이 유용합니다. 이 테이블에는 클래스에서 지원하는 `DrawingManager` 모든 이벤트가 나열됩니다.

| 이벤트 | Description |
|-------|-------------|
| `drawingchanged` | 셰이프의 좌표가 추가되거나 변경되었을 때 발생합니다. | 
| `drawingchanging` | 셰이프에 대한 미리 보기 좌표가 표시될 때 발생합니다. 예를 들어 좌표가 드래그될 때 이 이벤트가 여러 번 발생합니다. | 
| `drawingcomplete` | 셰이프가 그려지거나 편집 모드에서 제거되면 발생합니다. |
| `drawingmodechanged` | 드로잉 모드가 변경되면 발생합니다. 새 그리기 모드가 이벤트 처리기로 전달됩니다. |
| `drawingstarted` | 사용자가 셰이프 그리기를 시작하거나 셰이프를 편집 모드로 넣을 때 발생합니다.  |

다음 코드는 그리기 도구 모듈의 이벤트가 작동하는 방식을 보여 주며, 지도에 도형을 그리고 이벤트가 발사되는 것을 지켜봅을 본다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 도구 이벤트" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>그리기 도구 이벤트를</a> 참조하십시오.
</iframe>

<br/>

## <a name="examples"></a>예

드로잉 도구 이벤트를 사용하는 몇 가지 일반적인 시나리오를 살펴보겠습니다.

### <a name="select-points-in-polygon-area"></a>다각형 영역에서 점 선택

이 코드는 사용자 그리기 셰이프의 이벤트를 모니터링하는 방법을 보여 줍니다. 이 예제의 경우 코드는 다각형, 사각형 및 원의 모양을 모니터링합니다. 그런 다음 맵의 어떤 데이터 요소가 그려진 영역 내에 있는지 결정합니다. 이벤트는 `drawingcomplete` 선택 논리를 트리거하는 데 사용됩니다. 선택 논리에서 코드는 맵의 모든 데이터 포인트를 반복합니다. 점과 그려진 셰이프의 영역이 교차되어 있는지 확인합니다. 이 예제는 오픈 소스 [Turf.js](https://turfjs.org/) 라이브러리를 사용하여 공간 교차 계산을 수행합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그려진 다각형 영역에서 데이터 선택" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/XWJdeja'>그려진 다각형 영역에서</a> 펜 선택 데이터를 참조하십시오.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>다각형 영역에서 그리기 및 검색

이 코드는 사용자가 셰이프 그리기를 마친 후 셰이프 영역 내부의 관심 지점을 검색합니다. 프레임의 오른쪽 상단 모서리에 있는 '코드 펜 편집'을 클릭하여 코드를 수정하고 실행할 수 있습니다. 이벤트는 `drawingcomplete` 검색 논리를 트리거하는 데 사용됩니다. 사용자가 사각형 또는 다각형을 그리면 형상 내부의 검색이 수행됩니다. 원이 그려지면 반지름과 중심 위치가 관심 지점 검색을 수행하는 데 사용됩니다. 이 `drawingmodechanged` 이벤트는 사용자가 드로잉 모드로 전환하는 시기를 결정하는 데 사용되며 이 이벤트는 드로잉 캔버스를 지웁니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 영역에서 그리기 및 검색" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>펜 <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>그리기 및 다각형</a> 영역에서<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ()로 검색
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>측정 도구 만들기

아래 코드는 도면 이벤트를 사용하여 측정 도구를 만드는 방법을 보여 주며 있습니다. 는 `drawingchanging` 그려지는 모양을 모니터링하는 데 사용됩니다. 사용자가 마우스를 이동하면 셰이프의 치수가 계산됩니다. 이벤트는 `drawingcomplete` 그려진 후 셰이프에 대한 최종 계산을 수행하는 데 사용됩니다. 이 `drawingmodechanged` 이벤트는 사용자가 드로잉 모드로 전환하는 시기를 결정하는 데 사용됩니다. 또한 `drawingmodechanged` 이벤트는 드로잉 캔버스를 지우고 이전 측정 정보를 지웁니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="측정 도구" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>측정 도구를</a> 참조하십시오.
</iframe>

<br/>

## <a name="next-steps"></a>다음 단계

드로잉 도구 모듈의 추가 기능을 사용하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [도형 데이터 가져오기](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

서비스 모듈에 대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
> [서비스 모듈](how-to-use-services-module.md)

추가 코드 샘플을 확인해 보세요.

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
