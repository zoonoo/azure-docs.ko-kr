---
title: 드로잉 도구 모듈 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용하여 그리기 옵션 데이터를 설정하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334304"
---
# <a name="use-the-drawing-tools-module"></a>그리기 도구 모듈 사용

Azure 지도 웹 SDK는 *그리기 도구 모듈을*제공합니다. 이 모듈을 사용하면 마우스 나 터치 스크린과 같은 입력 장치를 사용하여 지도에서 모양을 쉽게 그리고 편집 할 수 있습니다. 이 모듈의 핵심 클래스는 [그리기 관리자입니다.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) 도면 관리자는 맵에서 셰이프를 그리고 편집하는 데 필요한 모든 기능을 제공합니다. 직접 사용할 수 있으며 사용자 지정 도구 모음 UI와 통합됩니다. 기본 제공 그리기 도구 [모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 클래스를 사용할 수도 있습니다. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>웹 페이지에서 그리기 도구 모듈 로드

1. 새 HTML 파일을 만들고 [평소와 같이 맵을 구현합니다.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)
2. Azure 지도 그리기 도구 모듈을 로드합니다. 다음 두 가지 방법 중 하나로 로드할 수 있습니다.
    - Azure Maps 서비스 모듈의 전역 호스팅Azure 콘텐츠 배달 네트워크 버전을 사용합니다. 파일의 요소에 JavaScript 및 CSS `<head>` 스타일시트에 대한 참조를 추가합니다.

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - 또는 [Azure](https://www.npmjs.com/package/azure-maps-drawing-tools) 맵 그리기 도구 npm 패키지를 사용하여 Azure Maps Web SDK 소스 코드에 대한 그리기 도구 모듈을 로컬로 로드한 다음 앱으로 호스트할 수 있습니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 이 명령 사용:
    
        > **npm Azure 맵 그리기 도구 설치**
    
        그런 다음 파일 의 요소에 JavaScript 및 CSS 스타일 시트에 대한 참조를 `<head>` 추가합니다.

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>도면 관리자를 직접 사용

드로잉 도구 모듈이 응용 프로그램에 로드되면 [그리기 관리자를](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)사용하여 그리기 및 편집 기능을 활성화할 수 있습니다. 도면 관리자에 대한 옵션을 인스턴스화하는 동안 지정하거나 `drawingManager.setOptions()` 이 함수를 사용할 수 있습니다.

### <a name="set-the-drawing-mode"></a>드로잉 모드 설정

다음 코드는 도면 관리자의 인스턴스를 작성하고 도면 **모드** 옵션을 설정합니다. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

아래 코드는 도면 관리자의 도면 모드를 설정하는 방법에 대한 완전한 실행 예입니다. 맵을 클릭하여 다각형 그리기를 시작합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>다각형을 그리는</a> 펜을 참조하십시오.
</iframe>


### <a name="set-the-interaction-type"></a>상호 작용 유형 설정

드로잉 관리자는 맵과 상호 작용하여 셰이프를 그리는 세 가지 방법을 지원합니다.

* `click`- 좌표는 마우스 또는 터치를 클릭 할 때 추가됩니다.
* `freehand `- 좌표는 마우스 또는 터치가지도에 드래그 할 때 추가됩니다. 
* `hybrid`- 좌표는 마우스 또는 터치를 클릭하거나 드래그 할 때 추가됩니다.

다음 코드는 다각형 드로잉 모드를 활성화하고 도면 관리자가 준수해야 `freehand`하는 그리기 상호 작용 유형을 설정합니다. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 이 코드 샘플에서는 맵에서 다각형을 그리는 기능을 구현합니다. 왼쪽 마우스 버튼을 길게 누르고 자유롭게 드래그하면 됩니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="자유 도면" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 펜 <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>자유 도면을</a> 참조하십시오.
</iframe>


### <a name="customizing-drawing-options"></a>도면 옵션 사용자 지정

이전 예제에서는 도면 관리자를 인스턴스화하는 동안 도면 옵션을 사용자 지정하는 방법을 보여 주어 도면 옵션을 보여 주어 도면 옵션을 보여 주어 도면 옵션을 보여 주어 도면 옵션을 보여 주어 도면 옵션을 보여 주시면 됩니다. `drawingManager.setOptions()` 이 기능을 사용하여 그리기 관리자 옵션을 설정할 수도 있습니다. 다음은 setOptions 함수를 사용하여 도면 관리자에 대한 모든 옵션의 사용자 지정을 테스트하는 도구입니다.

<br/>

<iframe height="685" title="도면 관리자 사용자 지정" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>펜 모양 데이터 보기</a>
</iframe>


## <a name="next-steps"></a>다음 단계

드로잉 도구 모듈의 추가 기능을 사용하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [그리기 도구 모음 추가](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [도형 데이터 가져오기](map-get-shape-data.md)

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
