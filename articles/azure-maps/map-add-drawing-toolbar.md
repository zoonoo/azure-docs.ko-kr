---
title: 지도에 그리기 도구 모음 추가 | 마이크로소프트 Azure 지도
description: Azure 지도 웹 SDK를 사용하여 맵에 그리기 도구 모음을 추가하는 방법
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334494"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>맵에 그리기 도구 도구 모음 추가

이 문서에서는 그리기 도구 모듈을 사용하고 맵에 그리기 도구 모음을 표시하는 방법을 보여 주었습니다. [그리기도구모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 컨트롤은 맵에 그리기 도구 모음을 추가합니다. 하나와 모든 그리기 도구만 사용하여 맵을 작성하는 방법과 도면 관리자에서 도면 셰이프 렌더링을 사용자 지정하는 방법을 배웁니다.

## <a name="add-drawing-toolbar"></a>그리기 도구 모음 추가

다음 코드는 도면 관리자의 인스턴스를 만들고 맵에 도구 모음을 표시합니다.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 도구 모음 추가" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>그리기 도구 모음 추가</a> 를 참조 하십시오.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>표시된 도구 모음 옵션 제한

다음 코드는 도면 관리자의 인스턴스를 작성하고 맵에 다각형 그리기 도구만 사용하여 도구 모음을 표시합니다. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기 도구 추가" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>다각형 그리기 도구 추가</a> 펜을 참조하십시오.
</iframe>


## <a name="change-drawing-rendering-style"></a>드로잉 렌더링 스타일 변경

그려진 셰이프의 스타일은 `drawingManager.getLayers()` 함수를 사용하여 도면 관리자의 기본 레이어를 검색한 다음 개별 레이어에 옵션을 설정하여 사용자 지정할 수 있습니다. 셰이프를 편집할 때 좌표에 나타나는 드래그 핸들은 HTML 마커입니다. 드래그 핸들의 스타일은 도면 관리자의 HTML 마커 `dragHandleStyle` 옵션과 `secondaryDragHandleStyle` 옵션에 전달하여 사용자 지정할 수 있습니다.  

다음 코드는 도면 관리자에서 렌더링 레이어를 가져옵니다 및 도면에 대 한 렌더링 스타일을 변경 하는 옵션을 수정 합니다. 이 경우 점은 파란색 마커 아이콘으로 렌더링됩니다. 선은 빨간색과 4픽셀 너비가 됩니다. 다각형은 녹색 채우기 색상과 주황색 윤곽선을 갖습니다. 그런 다음 드래그 핸들의 스타일을 정사각형 아이콘으로 변경합니다. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="드로잉 렌더링 스타일 변경" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>변경 그리기 렌더링 스타일을</a> 참조하십시오.
</iframe>


## <a name="next-steps"></a>다음 단계

드로잉 도구 모듈의 추가 기능을 사용하는 방법에 대해 알아봅니다.

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
> [그리기 도구 모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
