---
title: Azure Maps |에서 그리기 옵션 설정 Microsoft Docs
description: Azure Maps 웹 SDK를 사용 하 여 그리기 옵션 데이터를 설정 하는 방법
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309767"
---
# <a name="set-drawing-options"></a>그리기 옵션 설정

이 문서에서는 [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) 의 여러 옵션으로 사용자 환경을 변경 하는 방법을 보여 줍니다. 드로잉 관리자에 대 한 옵션을 인스턴스화하는 동안 지정 하거나 **setOptions ()** 함수를 사용 하 여 옵션을 설정할 수 있습니다.


## <a name="set-drawing-mode"></a>그리기 모드 설정

다음 코드는 그리기 관리자의 인스턴스를 만들고 그리기 **모드** 옵션을 설정 합니다. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

아래 코드는 그리기 관리자의 그리기 모드를 설정 하는 방법의 전체 실행 예입니다. 지도를 클릭 하 여 다각형 그리기를 시작 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>다각형 그리기</a> 를 참조 하세요.
</iframe>


## <a name="set-interaction-type"></a>상호 작용 유형 설정

다음 코드는 그리기 관리자가 준수 해야 하는 그리기 상호 작용의 유형을 설정 합니다. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

왼쪽 마우스 단추를 누른 채 마우스로 끌어 지도에 자유롭게 그릴 수 있는 기능을 구현 하는 코드 샘플은 다음과 같습니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="자유 그리기" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>자유 그리기</a> 를 참조 하세요.
</iframe>


## <a name="customizing-drawing-options"></a>그리기 옵션 사용자 지정

이전 예제에서는 드로잉 관리자를 인스턴스화하는 동안 그리기 옵션을 사용자 지정 하는 방법을 보여 주었습니다. **SetOptions ()** 함수를 사용 하 여 드로잉 관리자 옵션을 설정할 수도 있습니다 drawingManager. 다음은 setOptions 함수를 사용 하 여 드로잉 관리자에 대 한 모든 옵션의 사용자 지정을 테스트 하는 도구입니다.

<br/>

<iframe height="685" title="드로잉 관리자 사용자 지정" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Pen <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>셰이프 데이터 가져오기</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [그리기 도구 모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
