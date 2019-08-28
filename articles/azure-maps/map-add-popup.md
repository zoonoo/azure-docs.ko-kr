---
title: Azure Maps로 팝업 추가 | Microsoft Docs
description: Azure Maps 웹 SDK에 팝업을 추가 하는 방법입니다.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cde6c745034d0963bd372e36e6e5a046113c202b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976561"
---
# <a name="add-a-popup-to-the-map"></a>맵에 팝업 추가

이 문서에서는 맵에 있는 지점에 팝업을 추가하는 방법을 보여 줍니다.

## <a name="understand-the-code"></a>코드 이해

다음 코드에서는 기호 계층을 사용 하 여 `name` 및 `description` 속성이 있는 point 기능을 맵에 추가 합니다. [Popup 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) 의 인스턴스가 만들어졌지만 표시 되지 않습니다. 마우스 이벤트가 기호 계층에 추가 되어 마우스로 기호 마커를 가리키거나 끌 때 팝업을 트리거하고 팝업을 닫습니다. 표식 기호가 가리킴 `position` 이면 팝업의 속성이 표식의 위치로 업데이트 되 `content` 고 옵션은 마우스가 가리키는 point 기능의 및 `description` 속성을 `name` 래핑하는 일부 HTML로 업데이트 됩니다. 그런 다음 `open` 함수를 사용 하 여 맵에 팝업을 표시 합니다.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='Azure Maps를 사용하여 팝업 추가' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)가 제공하는 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps를 사용하여 팝업 추가</a> 펜을 참조하세요.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>여러 점을 사용하여 팝업을 다시 사용

많은 수의 점이 있고 한 번에 하나의 popup만 표시 하려는 경우 가장 좋은 방법은 하나의 popup을 만들고 각 요소 기능에 대해 popup을 만드는 대신 다시 사용 하는 것입니다. Popup을 다시 사용 하면 응용 프로그램에서 만든 DOM 요소의 수가 크게 줄어 성능이 향상 될 수 있습니다. 다음 샘플에서는 3 포인트 기능을 만듭니다. 그 중 하나를 클릭하면 해당 점 기능에 대한 콘텐츠와 함께 팝업이 표시됩니다.

<br/>

<iframe height='500' scrolling='no' title='여러 점을 사용하여 팝업을 다시 사용' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>여러 점을 사용하여 팝업을 다시 사용</a>을 참조하세요.
</iframe>

## <a name="customizing-a-popup"></a>팝업 사용자 지정

기본적으로 popup에는 흰색 배경, 아래쪽에는 포인터 화살표, 오른쪽 위 모서리에 닫기 단추가 있습니다. 다음 샘플에서는 팝업의 `fillColor` 옵션을 사용 하 여 배경색을 검은색으로 변경 합니다. `shoCloseButton` 옵션을 false로 설정 하 여 닫기 단추를 제거 합니다. 팝업의 HTML 콘텐츠는 팝업의 가장자리에서 패딩 된 10 픽셀을 사용 하 고 텍스트는 흰색으로 표시 되어 검은색 배경에 잘 표시 됩니다.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="사용자 지정 된 팝업" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>사용자 지정 팝업</a> 을 참조 하세요.
</iframe>

## <a name="popup-events"></a>Popup 이벤트

팝업은 열고, 닫고, 끌 수 있습니다. Popup 클래스는 개발자가 이러한 작업에 반응 하는 데 도움이 되는 이벤트를 제공 합니다. 다음 샘플에서는 팝업을 열거나 닫거나 끌 때 발생 하는 이벤트를 강조 표시 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup 이벤트" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>팝업 이벤트</a> 를 참조 하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

전체 코드 샘플에 대한 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)