---
title: Azure Maps에 드로잉 도구 모음 추가 | Microsoft Docs
description: Azure Maps 웹 SDK를 사용 하 여 지도에 드로잉 도구 모음을 추가 하는 방법
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6bc754c9a4f333da85e57c5ad9780da8df93e895
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309752"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>지도에 드로잉 도구 모음 추가

이 문서에서는 그리기 도구 모듈을 사용 하 고 지도에 드로잉 도구 모음을 표시 하는 방법을 보여 줍니다. [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 컨트롤은 지도에 그리기 도구 모음을 추가 합니다. 하나 및 모든 그리기 도구를 사용 하 여 지도를 만드는 방법과 드로잉 관리자에서 그리기 셰이프의 렌더링을 사용자 지정 하는 방법을 배웁니다.

## <a name="add-drawing-toolbar"></a>그리기 도구 모음 추가

다음 코드는 그리기 관리자의 인스턴스를 만들고 해당 도구 모음을 맵에 표시 합니다.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 도구 모음 추가" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 기준으로 펜 <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>그리기 추가 도구 모음</a> 을 참조 하세요.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>표시 된 도구 모음 옵션 제한

다음 코드는 그리기 관리자의 인스턴스를 만들고 지도에 다각형 그리기 도구를 사용 하 여 도구 모음을 표시 합니다. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기 도구 추가" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 통해 펜 <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>polygon 그리기 도구 추가</a> 를 참조 하세요.
</iframe>


## <a name="change-drawing-rendering-style"></a>그리기 렌더링 스타일 변경

다음 코드는 그리기 관리자에서 렌더링 계층을 가져오고 드로잉의 렌더링 스타일을 변경 하는 옵션을 수정 합니다. 이 경우 점은 파란색 표식 아이콘으로 렌더링 되 고 선은 빨강 및 4 픽셀 너비 이며, 다각형은 녹색 채우기 색과 주황색 윤곽선을 갖습니다.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 렌더링 스타일 변경" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 펜으로 <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>그리기 렌더링 스타일 변경</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [그리기 도구 모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)