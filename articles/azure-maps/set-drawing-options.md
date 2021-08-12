---
title: 그리기 도구 모듈 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용하여 그리기 옵션 데이터를 설정하는 방법을 알아봅니다
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 95a04d763fa5982181cc1c797bce969d9857ae4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890635"
---
# <a name="use-the-drawing-tools-module"></a>그리기 도구 모듈 사용

Azure Maps 웹 SDK는 *그리기 도구 모듈* 을 제공합니다. 이 모듈을 사용하면 마우스나 터치 화면 같은 입력 장치를 사용하여 맵에서 도형을 쉽게 그리거나 편집할 수 있습니다. 이 모듈의 핵심 클래스는 [그리기 관리자](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)입니다. 그리기 관리자는 맵에서 도형을 그리거나 편집하는 데 필요한 모든 기능을 제공합니다. 이는 직접 사용할 수 있으며, 사용자 지정 도구 모음 UI와 통합됩니다. 또한 기본 제공 [그리기 도구 모음](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) 클래스를 사용할 수 있습니다. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>웹 페이지에서 그리기 도구 모듈 로드

1. 새 HTML 파일을 만들고 [평소와 같이 맵을 구현](./how-to-use-map-control.md)합니다.
2. Azure Maps 그리기 도구 모듈을 로드합니다. 다음 두 가지 방법 중 한 가지로 로드할 수 있습니다.
    - Azure Maps 서비스 모듈의 전역적으로 호스트된 Azure Content Delivery Network 버전을 사용합니다. 파일의 `<head>` 요소에서 JavaScript 및 CSS 스타일시트에 대한 참조를 추가합니다.

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - 또는 [azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm 패키지를 사용하여 Azure Maps 웹 SDK 소스 코드의 그리기 도구 모듈을 로컬로 로드한 다음 앱을 사용하여 호스트할 수 있습니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 다음 명령을 실행합니다.
    
        > **npm install azure-maps-drawing-tools**
    
        그런 다음 파일의 `<head>` 요소에서 JavaScript 및 CSS 스타일시트에 대한 참조를 추가합니다.

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>그리기 관리자 직접 사용

그리기 도구 모듈이 응용 프로그램에 로드되면 [그리기 관리자](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)를 통해 그리기와 편집 기능을 사용할 수 있습니다. 그리기 관리자를 인스턴스화하거나 `drawingManager.setOptions()` 함수를 대안으로 사용하는 동안 그리기 관리자 옵션을 지정할 수 있습니다.

### <a name="set-the-drawing-mode"></a>그리기 모드 설정

다음 코드는 그리기 관리자의 인스턴스를 만들고, 그리기 **모드** 옵션을 설정합니다. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

아래 코드는 그리기 관리자의 그리기 모드를 설정하는 방법에 대한 전체 실행 예시입니다. 맵을 클릭하여 다각형 그리기를 시작합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps로 펜 <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>다각형 그리기</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 <a href='https://codepen.io'>CodePen</a>에서 수행하는 방법을 참조하세요.
</iframe>


### <a name="set-the-interaction-type"></a>상호 작용 유형 설정

그리기 관리자는 맵과 상호 작용하여 도형을 그릴 수 있는 세 가지 다른 방법을 지원합니다.

* `click` - 마우스 또는 터치스크린을 클릭하면 좌표가 추가됩니다.
* `freehand ` - 맵에서 마우스 또는 터치스크린을 끌면 좌표가 추가됩니다. 
* `hybrid` - 마우스 또는 터치스크린을 클릭하거나 끌면 좌표가 추가됩니다.

다음 코드는 다각형 그리기 모드를 사용하고, 그리기 관리자가 `freehand`를 준수해야 하는 그리기 상호 작용 유형을 설정합니다. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 이 코드 샘플은 맵에 다각형을 그리는 기능을 구현합니다. 마우스 왼쪽 단추를 누르고 자유롭게 끌면 됩니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="자유롭게 그리기" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps로 펜 <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>자유롭게 그리기</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 <a href='https://codepen.io'>CodePen</a>에서 수행하는 방법을 참조하세요.
</iframe>


### <a name="customizing-drawing-options"></a>그리기 옵션 사용자 지정

이전 예제에서는 그리기 관리자를 인스턴스화하면서 그리기 옵션을 사용자 지정하는 방법을 설명했습니다. `drawingManager.setOptions()` 함수를 사용하여 그리기 관리자 옵션도 설정할 수 있습니다. 다음은 setOptions 함수를 사용하여 그리기 관리자 옵션의 모든 사용자 지정 항목을 테스트하는 도구입니다.

<br/>

<iframe height="685" title="그리기 관리자 사용자 지정" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps으로 펜 <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>도형 데이터 가져오기</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [그리기 도구 모음 추가](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [셰이프 데이터 가져오기](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [그리기 이벤트에 반응](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [그리기 관리자](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [그리기 도구 모음](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)