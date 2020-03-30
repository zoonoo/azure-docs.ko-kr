---
title: 지도의 도형에서 데이터 얻기 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 셰이프 데이터를 그리는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334405"
---
# <a name="get-shape-data"></a>도형 데이터 가져오기

이 문서에서는 맵에 그려진 셰이프의 데이터를 얻는 방법을 보여 주며, 이 문서에서는 우리는 드로잉 [관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)내부 **의 drawingManager.getSource()** 기능을 사용합니다. 그려진 모양의 geojson 데이터를 추출하고 다른 곳에서 사용하려는 경우 다양한 시나리오가 있습니다.  


## <a name="get-data-from-drawn-shape"></a>그려진 모양에서 데이터 가져옵니다.

다음 함수는 그려진 셰이프의 원본 데이터를 가져온 다음을 화면에 출력합니다. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

다음은 기능을 테스트하기 위해 셰이프를 그릴 수 있는 전체 실행 중인 코드 샘플입니다.

<br/>

<iframe height="686" title="도형 데이터 가져오기" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>펜 모양 데이터 보기</a>
</iframe>


## <a name="next-steps"></a>다음 단계

드로잉 도구 모듈의 추가 기능을 사용하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [그리기 이벤트에 반응](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [지도](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [그리기 도구 모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
